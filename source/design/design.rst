ObsBias Design
+++++++++++++++++++++

.. uml::

    @startuml

      package "ObsBias" {

        package "oops" {
        }

        package "ufo" {
        }

        oops --> "1" ufo
      }

        abstract class ObsBias {
          - privateField
          + publicField
          # protectedField
          ~ packagePrivateField
          - privateMethod()
          + publicMethod()
          # protectedMethod()
          ~ packagePrivateMethod()
           }
    
        class Dummy {
          {static} staticID
          {abstract} void methods()
           }

        class Flight {
           flightNumber : Integer
           departureTime : Date
           }

        package "Classic Collections" {

           abstract class AbstractList
           abstract AbstractCollection
           interface List
           interface Collection

           List <|-- AbstractList
           Collection <|-- AbstractCollection

           Collection <|- List
           AbstractCollection <|- AbstractList
           AbstractList <|-- ArrayList

           class ArrayList {
             Object[] elementData
             size()
              } 
        }

        enum TimeUnit {
          DAYS
          HOURS
          MINUTES
        }


        class ObsSpace {
          Name
        }
        
        ObsSpace "0..*" -- "1..*" ObsOperator
        (ObsSpace, ObsOperator) .. ObsBias

        class ObsOperator {
          drop()
          cancel()
        }

    @enduml