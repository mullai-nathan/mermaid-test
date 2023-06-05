@startuml
!theme aws-orange
actor User

participant "DFC App"
participant "DFC UI"
participant i1D3Device
participant "SystemOS"


User -> "SystemOS": Connects i1D3Device
User -> "DFC App": Launch Application
"DFC UI" --> User: Prompts to connect i1D3Device
User -> "DFC App": Launch Application
"DFC App" -> "DFC App": Detect i1D3 spectrophotometer device
loop i1D3 detected
    "DFC App" -> i1D3Device: Establish communication link
    "DFC App" -> i1D3Device: Reads Expiry time & usage count
     i1D3Device --> "DFC App": Returns Expiry time & usage count
    "DFC App" -> "DFC App": Check calibration devices
alt Vaild usage
    "DFC App" -> i1D3Device: Establish communication link
    "DFC App" -> i1D3Device: Reads Expiry time & usage count
     i1D3Device --> "DFC App": Returns Expiry time & usage count

    "DFC App" -> "DFC App": Check calibration devices
else Expired time or Exceeded usage
    "DFC UI" --> User: Show Error
end
else i1D3 not detected
    "DFC App" -> "DFC UI": 
    "DFC UI" --> User: Prompt to connect i1D3 device
end
"DFC App" --> User: Calibration prepared
@enduml
