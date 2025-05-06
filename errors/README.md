# ⚠️ Gestión de errores 

[← Regresar](./../README.md)

---

[1. ErrorDto](#1-errordto) <br>
[2. GenericException](#2-genericexception) <br>
[3. ErrorDictionary](#3-errordictionary) <br>
[4. ProjectType](#4-projecttype) <br>
[5. RestClientErrorHandler](#5-restclienterrorhandler) <br>

---

## 1. `ErrorDto`
La respuesta de error tiene la siguiente estructura:

```json
{
  "code": "01.02.01",
  "origin": "OWN",
  "message": "Invalid field. traceParent must not be empty"
}
```

| Origen        | Descripción                                                                                    |
|---------------|------------------------------------------------------------------------------------------------|
| `OWN`         | Error propio del backend.                                                                      |
| `PARTNER`     | Error que proviene de otro backend en el mismo sistema y que comparte la etructura `ErrorDto`. |
| `THIRD_PARTY` | Error que proviene de un backend externo al sistema y que tiene su propia estructura de error. |

---

## 2. `GenericException`
Cada excepción personalizada extenderá de `GenericException` y será registrada en `ErrorDictionary`.

---

## 3. `ErrorDictionary`
Enum que actúa como diccionario de errores del backend.

```java
public enum ErrorDictionary {

  INVALID_FIELD("01.02.01", "Invalid field.", BAD_REQUEST, InvalidFieldException.class),;
    
  private final String code;
  private final String message;
  private final HttpStatus httpStatus;
  private final Class<? extends GenericException> exceptionClass;
}
```

---

## 4. `ProjectType`
- Enum que establece el rol del backend (*Backend for frontend* o *Generic microservice*). 
- Se configura a través de la propiedad `configuration.project-type`.

```yaml
configuration:
  project-type: BFF # BFF or MS
```

### 📌 *Backend For Frontend (BFF)*
El backend de tipo BFF impide que el frontend reciba códigos y/o mensajes de error que comprometan la seguridad del sistema.
Con este propósito, sus respuestas de error tendrán un código y mensaje por defecto.

```json
{
  "type": "OWN",
  "code": "Default",
  "message": "No hemos podido realizar tu operación. Estamos trabajando para solucionar el inconveniente."
}
```

Es posible habilitar códigos y/o mensajes de error para los escenarios en que el frontend los necesite para adecuar sus unhappy paths.
Para ello, agregue aquellos códigos de error y sus correspondientes mensajes en la propiedad `configuration.error-messages`.

```yaml
configuration:
  error-messages:
    Default: No hemos podido realizar tu operación. Estamos trabajando para solucionar el inconveniente.
    "[10.01.03]": ${configuration.error-messages.Default}.
    "[10.01.04]": Por tu seguridad, tu cuenta ha sido bloqueada.
```

### 📌 *Generic microservice*
Por su parte, los servicios genéricos exponen en la respuesta de error el código y mensaje que fueron lanzados en la excepción.

---

## 5. `RestClientErrorHandler`
Se encarga de capturar el error del cliente REST, deserealizarlo en un objeto DTO y lanzar una excepción `RestClientException` con el código y mensaje recuperado.

Para que la deseralización sea exitosa, el método `handleError()` necesita como argumento a la clase que representa la estructura del error (*wrapper*).
Internamente este método seleccionará la implementación de `RestClientErrorExtractor` que corresponda para poder mapear el código y mensaje.
En este sentido, usted debe crear la clase *wrapper* y su correspondiente *errorExtractor*.

```java
private Mono<? extends Throwable> handleError(ClientResponse clientResponse) {
  return errorHandler.handleError(clientResponse, MoneyExchangeError.class, SERVICE_NAME);
}
```

Para los escenarios en que sea necesario propagar el error del cliente REST con un diferente código, mensaje o incluso código HTTP, es posible hacerlo configurando la propiedad `errors` de cada cliente REST.

```yaml
configuration:
  rest-clients:
    money-exchange:
      errors:
        invalid-currency: # Código de error original
          code: 02.05.01 # Código de error personalizado (Opcional)
          message: Mensaje personalizado de ejemplo. # Mensaje - (Opcional)
          httpCode: 400 # Código HTTP - (Opcional)
```


