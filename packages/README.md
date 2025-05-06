# 📦 Arquitectura de paquetes

[← Regresar](./../README.md)

---

## Paquete `commons.core`
Encapsula las clases de configuración y herramientas base para todos los proyectos.

```sh
    com.demo.poc
    │───`commons`
    .   └───core
    .       ├───config              # Configuración e inyección de beans
    .       ├───constants           # Constantes cross
    .       ├───errors              # Administración de errores
    .       ├───interceptor         # Intercepción de eventos (errores, peticiones REST, etc)
    .       ├───logging             # Población de logs
    .       ├───properties          # Estructura base de las properties
    .       ├───restclient          # Configuración del cliente REST (RestTemplate, Retrofit, WebClient, etc)
    .       ├───restserver          # Utilidades de input y ouput del servidor
    .       ├───serialization       # Utilidades de serialización/deserialización
    .       ├───tracing             # Utilidades de trazabilidad (traceParent, traceId, parentId)
    .       └───validations         # Validación de inputs
```

---

## Paquete `commons.custom`
Aquí se establecen las propiedades, excepciones y configuraciones específicas de cada proyecto. 

```sh
    com.demo.poc
    │───`commons`
    .   └───custom
    .       ├───config 
    .       ├───exceptions                  # *Exception                  
    .       └───properties                  # ApplicationProperties
```

---

## Paquete `entrypoint`

Este paquete adopta el enfoque de screaming architecture, en el cual cada subpaquete representa un caso de uso o componente funcional identificable.

La implementación variará según las necesidades de cada proyecto, pero deben tomar el siguiente modelo como referencia:

```sh
    com.demo.poc
    │───`commons`
    │   ├───core
    │   └───custom
    │
    └───`entrypoint`
        └───`<bounded-context>`
            │
            ├───constants                   # *Constants
            │
            ├───dto
            │   ├───request                 # *RequestDto
            │   └───response                # *ResponseDto
            │
            ├───params                      # *Param
            │
            ├───enums                       # *Type | *Category | *Enum
            │
            ├───event
            │   └───`<data-model>`          # *Producer | *Consumer
            │       └───message             # *InboundMessage | *OutboundMessage 
            │
            ├───mapper                      # *RequestMapper | *ResponseMapper
            │
            ├───repository
            │   └───`<data-model>`          # *Repository
            │       └───entity | wrapper    # *Entity | *Document | *RequestWrapper | *ResponseWrapper
            │
            ├───rest                        # *RestService
            │
            └───service                     # *Service
```

