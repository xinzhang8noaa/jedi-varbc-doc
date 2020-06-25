VarBC Design
+++++++++++++++++++++

.. uml::

    @startuml

    package "ObsBiasPackage" {

      namespace oops.base #DDDDDD {

          class ObsAuxControls <MODEL> {
            {static} classname() : const string
            + ObsAuxControls(const ObsSpaces_<MODEL> &, const eckit::Configuration &) : void
            + ObsAuxControls(const ObsAuxControls &, const bool = true) : void
            + ~ObsAuxControls() : void
            + size() const : size_t 
            + operator[](const size_t) const : const ObsAuxControl_ &
            + operator[](const size_t) : ObsAuxControl_ &
            + read(const Configuration &) : void
            + write(const Configuration &) const : void
            + norm() const : double
            + operator=(const ObsAuxControls &) : ObsAuxControls &
            
            - print(ostream &) const : void
            - auxs_ : ObsAuxControl * [] 
          }

          class ObsAuxIncrements <MODEL> {
            {static} classname() : const string
            + ObsAuxIncrements(const ObsSpaces_ &, const Configuration &) : void
            + ObsAuxIncrements(const ObsAuxIncrements &, const bool = true) : void
            + ObsAuxIncrements(const ObsAuxIncrements &, const Configuration &) : void
            + ~ObsAuxIncrements() : void
            + size() const : size_t
            + operator[](const size_t) const : const ObsAuxIncrement_ &
            + operator[](const size_t) : ObsAuxIncrement_ &
            + diff(const ObsAuxControls_ &, const ObsAuxControls_ &) : void
            + zero() : void
            + operator=(const ObsAuxIncrements &) : ObsAuxIncrements &
            + operator+=(const ObsAuxIncrements &) : ObsAuxIncrements &
            + operator-=(const ObsAuxIncrements &) : ObsAuxIncrements & 
            + operator*=(const double &) : ObsAuxIncrements & 
            + axpy(const double &, const ObsAuxIncrements &) : void
            + dot_product_with(const ObsAuxIncrements &) const : double
            + read(const Configuration &) : void
            + write(const Configuration &) const : void
            + norm() const : double
            + serialSize() const : size_t
            + serialize(double [] &) const : void
            + deserialize(const double [], size_t &) : void

            - print(ostream &) const : void
            - aux: ObsAuxIncrement * []
          }

          class ObsAuxCovariances <MODEL> {
            {static} classname() : const string
            + ObsAuxCovariances(const ObsSpaces<MODEL> &, const Configuration &);
            + ~ObsAuxCovariances();
            + linearize(const ObsAuxControls_ &) : void
            + multiply(const ObsAuxIncrements_ &, ObsAuxIncrements_ &) const : void 
            + inverseMultiply(const ObsAuxIncrements_ &, ObsAuxIncrements_ &) const : void
            + randomize(ObsAuxIncrements_ &) const : void
            + config() const : const LocalConfiguration &
            + obspaces() const : const ObsSpaces_ &

            - print(ostream &) const : void
            - cov_ : ObsAuxCovariance_ * []
            - odb_ : const ObsSpaces_
            - conf_ : const LocalConfiguration
          }

          ObsAuxIncrements --> "1" ObsAuxControls
          ObsAuxCovariances --> "1" ObsAuxControls
          ObsAuxCovariances --> "1" ObsAuxIncrements
  
      }  /' namespace oops.base '/

      namespace oops.interface #DDDDDD {
          
          class ObsAuxControl <MODEL> {
            {static} const classname() : string
            + ObsAuxControl(ObsSpace<MODEL> &, const Configuration &) : void
            + ObsAuxControl(const ObsAuxControl &, const bool = true) : void
            + ~ObsAuxControl() : void
            + obsauxcontrol() const : const ObsAuxControl_ &
            + obsauxcontrol() : ObsAuxControl_ &
            + read(const Configuration &) : void
            + write(const Configuration &) const : void
            + norm() const : double
            + requiredGeoVaLs() const : const Variables & 
            + requiredHdiagnostics() const : const Variables &
            + operator=(const ObsAuxControl &) : ObsAuxControl &
            - print(std::ostream &) const : void
            - aux_ : ObsAuxControl_ *
          }
          
          class ObsAuxIncrement <MODEL> {
            {static} classname() : string
            + ObsAuxIncrement(ObsSpace<MODEL> &, Configuration &) :void
            + ObsAuxIncrement(ObsAuxIncrement &, bool) : void
            + ObsAuxIncrement(ObsAuxIncrement &, Configuration &) : void
            + ~ObsAuxIncrement() : void
            + obsauxincrement() const : ObsAuxIncrement_ &
            + obsauxincrement() : ObsAuxIncrement_ &
            + diff(const ObsAuxControl_ &, const ObsAuxControl_ &) : void
            + zero() : void
            + operator=(ObsAuxIncrement &) : ObsAuxIncrement &
            + operator+=(ObsAuxIncrement &) : ObsAuxIncrement &
            + operator-=(ObsAuxIncrement &) : ObsAuxIncrement &
            + operator*=(double &) : ObsAuxIncrement &
            + axpy(double &, ObsAuxIncrement &) : void
            + double dot_product_with(const ObsAuxIncrement &) const;
            + read(Configuration &) : void
            + write(Configuration &) : void
            + norm() : double
            + serialSize() : size_t
            + serialize(double [] &) : void
            + deserialize(double [] &, size_t &) : void
            - print(ostream &) : void
            - aux_ : ObsAuxIncrement_ *
          }

          class ObsAuxCovariance <MODEL> {
            {static} classname() : string
            + ObsAuxCovariance(ObsSpace<MODEL> &, const Configuration &) : void
            + ~ObsAuxCovariance() : void
            + linearize(ObsAuxControl_ &) : void
            + multiply(ObsAuxIncrement_ &, ObsAuxIncrement_ &) : void
            + inverseMultiply(ObsAuxIncrement_ &, ObsAuxIncrement_ &) : void
            + randomize(ObsAuxIncrement_ &) : void
            + config() : Configuration
            - print(ostream &) : void
            - > cov_ : ObsAuxCovariance_ *
          }
        
          ObsAuxIncrement --> "1" ObsAuxControl
          ObsAuxCovariance --> "1" ObsAuxControl
          ObsAuxCovariance --> "1" ObsAuxIncrement

      }  /' namesapce oops.interface '/

      oops.base.ObsAuxControls --> "1..*" oops.interface.ObsAuxControl
      oops.base.ObsAuxIncrements --> "1..*" oops.interface.ObsAuxIncrement
      oops.base.ObsAuxCovariances --> "1..*" oops.interface.ObsAuxCovariance
      
      namespace ufo #03fcf0 {
        
          class ObsBias {
            + {static} classname() : string
            + ObsBias(ObsSpace &, Configuration &) : void
            + ObsBias(ObsBias &, bool) : void
            + ~ObsBias() : void

            + operator+=(ObsBiasIncrement &) : ObsBias
            + operator=(ObsBias &) : ObsBias
            + operator[](int) : double
            + operator bool() : bool

            + read(Configuration &) : void
            + write(Configuration &) : void
            + norm() : double
            + size() : size_t
            + computeObsBias(:ObsVector [] &, ObsDataVector [] &, ioda::ObsDataVector [] &) : void
            + computeObsBiasPredictors(GeoVaLs &, ObsDiagnostics &, ObsDataVector [] &) : void

            + requiredGeoVaLs() : Variable
            + requiredHdiagnostics() : Variable
            + predNames() : Variable
            + config() : Configuration
            + obspace() : ObsSpace


            - print(std::ostream &) : void
            - biasbase_ : ObsBiasBase *
            - conf_ : LocalConfiguration
            - geovars_ : Variable
            - hdiags_ : Variable
            - predNames_ : Variable
          }
          
          class ObsBiasIncrement {
            + ObsBiasIncrement(ObsSpace &,  Configuration &) : void
            + ObsBiasIncrement(ObsBiasIncrement &,  bool) : void
            + ObsBiasIncrement(ObsBiasIncrement &,  Configuration &) : void
            + ~ObsBiasIncrement() : void
            - print(ostream &) : void
            - biasbase_ : *LinearObsBiasBase
            - conf_: LocalConfiguration
          }

          class ObsBiasCovariance {
          }
        
          ObsBiasIncrement --> "1" ObsBias
          ObsBiasCovariance --> "1" ObsBias
          ObsBiasCovariance --> "1" ObsBiasIncrement
        
      }  /' namespace ufo '/
      
      oops.interface.ObsAuxControl --> "1" ufo.ObsBias
      oops.interface.ObsAuxIncrement --> "1" ufo.ObsBiasIncrement
      oops.interface.ObsAuxCovariance --> "1" ufo.ObsBiasCovariance

    } /' package ObsBiasPackage '/

    @enduml