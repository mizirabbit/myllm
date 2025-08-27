```mermaid
classDiagram
    direction TB

    subgraph "App Layer" {
        ClientApp
    }

    subgraph "Framework Layer" {
        ILlmService
        ILlmCallback
        LlmService
    }

    subgraph "HAL Layer" {
        IGenieHal
        IGenieHalCallback
        GenieHalService
    }

    class ClientApp {
        <<Android App>>
        +ILlmService llmService
    }

    class ILlmCallback {
        <<AIDL Interface>>
        +onLlmResponseReceived(String)*
        +onError(String)*
    }

    class ILlmService {
        <<AIDL Interface>>
        +createDialog(String, ILlmCallback)*
        +sendMessage(String)*
        +disconnect()*
    }

    class LlmService {
        <<Android Service>>
        -ILlmCallback mClientCallback
        -IGenieHal mHal
        +createDialog(String, ILlmCallback)
        +sendMessage(String)
    }

    class IGenieHalCallback {
        <<HAL AIDL Interface>>
        +onResponse(String)*
    }

    class IGenieHal {
        <<HAL AIDL Interface>>
        +createDialog(String, IGenieHalCallback)*
        +sendMessage(String)*
        +disconnect()*
    }

    class GenieHalService {
        <<Native Service>>
        +createDialog(String, IGenieHalCallback)
    }

    ClientApp ..|> ILlmCallback : implements
    ClientApp o--> ILlmService : binds to & uses

    LlmService ..|> ILlmService : implements
    LlmService --> ILlmCallback : calls back

    LlmService o--> IGenieHal : binds to & uses
    LlmService ..|> IGenieHalCallback : implements

    GenieHalService ..|> IGenieHal : implements
    GenieHalService --> IGenieHalCallback : calls back
