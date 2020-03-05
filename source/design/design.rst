ObsBias Design
+++++++++++++++++++++

.. uml::

    @startuml

    package "ObsBiasPackage" {

      namespace oops.base {

          class ObsAuxControls {
          }

          class ObsAuxIncrements {
            -[] *auxs_;
          }

          class ObsAuxCovariances {
            {static} classname() : string
          }

          ObsAuxIncrements --> "1" ObsAuxControls
          ObsAuxCovariances --> "1" ObsAuxControls
          ObsAuxCovariances --> "1" ObsAuxIncrements
  
      }  /' namespace oops.base '/

      namespace oops.interface {
          
          class ObsAuxControl {
          }
          
          class ObsAuxIncrement {
          }

          class ObsAuxCovariance {
          }
        
          ObsAuxIncrement --> "1" ObsAuxControl
          ObsAuxCovariance --> "1" ObsAuxControl
          ObsAuxCovariance --> "1" ObsAuxIncrement

      }  /' namesapce oops.interface '/

      oops.base.ObsAuxControls --> "0..*" oops.interface.ObsAuxControl
      oops.base.ObsAuxIncrements --> "0..*" oops.interface.ObsAuxIncrement
      oops.base.ObsAuxCovariances --> "0..*" oops.interface.ObsAuxCovariance
      
      namespace ufo {
        
          class ObsBias {
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

    } /' package ObsBiasComponent '/

    @enduml