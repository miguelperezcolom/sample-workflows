# sample-workflows

Colección de definiciones de workflows de ejemplo para [eventconductor](https://github.com/miguelperezcolom/eventconductor).

## Workflows de ejemplo

| Archivo | Descripción | Funcionalidades |
|---------|-------------|-----------------|
| `hello-world.yaml` | Workflow básico | Pasos secuenciales |
| `approval.yaml` | Proceso de aprobación | Pasos secuenciales |
| `onboarding.yaml` | Alta de nuevo usuario | Pasos secuenciales |
| `order-processing.yaml` | Procesamiento de pedido | Pasos secuenciales |
| `document-processing.yaml` | Procesamiento de documento | Pasos en paralelo |
| `loan-approval.yaml` | Aprobación de préstamo | Pasos condicionales |
| `payment-processing.yaml` | Procesamiento de pago | Reintentos, timeout |
| `travel-booking.yaml` | Reserva de viaje | Compensación / rollback |
| `expense-approval.yaml` | Aprobación de gastos | Pasos condicionales, `USER_TASK` |
| `new-employee-setup.yaml` | Alta de empleado | Pasos en paralelo, `PROCESS` |
| `order-fulfillment.yaml` | Fulfillment de pedido | Topic Kafka, paralelo, rollback |
| `insurance-claim.yaml` | Reclamación de seguro | **Todas las funcionalidades** |
| `customer-support-ticket.yaml` | Ticket de soporte al cliente | Condicional, `USER_TASK`, topic |
| `employee-offboarding.yaml` | Baja de empleado | Paralelo, `USER_TASK`, topic |
| `content-publishing.yaml` | Publicación de contenido | Paralelo, condicional, `USER_TASK`, `PROCESS` |
| `kyc-verification.yaml` | Verificación KYC | Paralelo, condicional, `PROCESS`, `USER_TASK` |
| `incident-management.yaml` | Gestión de incidencias IT | Condicional, paralelo, `USER_TASK`, rollback |

## Formato

Los workflows se definen en YAML siguiendo el esquema de `eventconductor`.

### Campos de workflow

| Campo | Descripción |
|-------|-------------|
| `id` | Identificador único del workflow |
| `name` | Nombre descriptivo |
| `version` | Versión del workflow |
| `status` | Estado (`ACTIVE`, etc.) |
| `steps` | Lista de pasos |

### Tipos de paso

| Tipo | Descripción |
|------|-------------|
| `ACTION` | Publica un evento en un topic Kafka |
| `USER_TASK` | Espera a que un usuario complete un formulario |
| `PROCESS` | Ejecuta un workflow hijo y espera su resultado |
| `END` | Finaliza el workflow |

### Campos de paso

| Campo | Descripción |
|-------|-------------|
| `id` | Identificador único del paso |
| `name` | Nombre descriptivo |
| `type` | Tipo de paso (ver tabla anterior) |
| `preconditionStepId` | Paso que debe completarse antes de ejecutar éste |
| `preconditionExpression` | Expresión JEXL sobre variables del proceso; omite el paso si devuelve `false` |
| `parallel` | `true` para ejecutar en paralelo con otros pasos con el mismo `preconditionStepId` |
| `topic` | Topic Kafka al que se publica el evento (pasos `ACTION`) |
| `formId` | Formulario asociado (pasos `USER_TASK`) |
| `childWorkflowDefinitionId` | Workflow hijo a ejecutar (pasos `PROCESS`) |
| `timeout` | Tiempo máximo de ejecución en ISO 8601 (p. ej. `PT30S`, `P2D`) |
| `retries` | Reintentos automáticos en caso de fallo (por defecto `0`) |
| `rollbackable` | `true` para activar compensación si el paso falla |
| `compensationStepId` | Paso a ejecutar como compensación (rollback) |

## Ejemplo

```yaml
id: hello-world
name: Hello World
version: 1
status: ACTIVE
steps:
  - id: greet
    type: ACTION
    name: Greet the user
  - id: farewell
    type: ACTION
    name: Say goodbye
    preconditionStepId: greet
  - id: end
    type: END
    name: Done
    preconditionStepId: farewell
```

## Schema

El schema completo está disponible en:
`https://raw.githubusercontent.com/miguelperezcolom/eventconductor/main/modules/workflow-engine/src/main/resources/workflow-definition-schema.json`
