ObsBias Design
+++++++++++++++++++++

.. uml::

    @startuml

    package "ObsBiasPackage" {

      namespace oops.base #DDDDDD {

          class ObsAuxControls {
            {static} classname() : string
          }

          class ObsAuxIncrements <MODEL> {
            {static} classname() : string
            + ObsAuxIncrements(ObsSpaces &, Configuration &) : void
            + ObsAuxIncrements(ObsAuxIncrements &, bool) : void
            + ObsAuxIncrements(ObsAuxIncrements &, Configuration &) : void
            + ~ObsAuxIncrements() : void
            - [] aux: *ObsAuxIncrement
          }

          class ObsAuxCovariances {
            {static} classname() : string
          }

          ObsAuxIncrements --> "1" ObsAuxControls
          ObsAuxCovariances --> "1" ObsAuxControls
          ObsAuxCovariances --> "1" ObsAuxIncrements
  
      }  /' namespace oops.base '/

      namespace oops.interface #DDDDDD {
          
          class ObsAuxControl {
          }
          
          class ObsAuxIncrement <MODEL> {
            + ObsAuxIncrement(ObsSpace &, Configuration &) :void
            + ObsAuxIncrement(ObsAuxIncrement &, bool) : void
            + ObsAuxIncrement(ObsAuxIncrement &, Configuration &) : void
            + ~ObsAuxIncrement() : void
          }

          class ObsAuxCovariance {
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