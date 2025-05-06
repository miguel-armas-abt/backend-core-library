# 🔎 Gestión de logs 

[← Regresar](./../README.md)

## 1. `LoggingType`
Enum que mapea los tipos de logs.

```java
public enum LoggingType {

  ERROR("error", "Error"),
  REST_SERVER_REQ("rest.server.req", "REST server request"),
  REST_SERVER_RES("rest.server.res", "REST server response"),
  REST_CLIENT_REQ("rest.client.req", "REST client request"),
  REST_CLIENT_RES("rest.client.res", "REST client response");

  private final String code;
  private final String message;
}
```
Por defecto están habilitados todos los tipos de logs, pero usted puede deshabilitarlos con la propiedad `configuration.logging.logging-type`.

```yaml
configuration:
  logging:
    logging-type:
        rest.server.req: false
        rest.server.res: false
```

---

## 2. Ofuscación de valores sensibles
Para ofuscar valores sensibles en los logs, ajuste la propiedad `configuration.logging.obfuscation`.

```yaml
configuration:
  logging:
      obfuscation:
        body-fields: ["example.body.field", "example.body.array[*].field"]
        headers: ["Authorization", "Subscription-Key"]
```