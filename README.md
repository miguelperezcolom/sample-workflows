# sample-workflows

Colección de definiciones de workflows de ejemplo para [eventconductor](https://github.com/miguelperezcolom/eventconductor).

## Estructura

```
workflows/
  hello-world.yaml      # Workflow básico de ejemplo
  approval.yaml         # Proceso de aprobación
  onboarding.yaml       # Alta de nuevo usuario
  order-processing.yaml    # Procesamiento de pedido
  document-processing.yaml # Procesamiento de documento con pasos en paralelo
  loan-approval.yaml       # Aprobación de préstamo con pasos condicionales
  payment-processing.yaml  # Procesamiento de pago con reintentos y timeout
  travel-booking.yaml      # Reserva de viaje con compensación (rollback)
  expense-approval.yaml    # Aprobación de gastos con tarea de usuario (USER_TASK)
  new-employee-setup.yaml  # Alta de empleado con subprocesos (PROCESS)
```

## Formato

Los workflows se definen en YAML siguiendo el esquema de `eventconductor`. Cada archivo incluye:

| Campo | Descripción |
|-------|-------------|
| `id` | Identificador único del workflow |
| `name` | Nombre descriptivo |
| `version` | Versión del workflow |
| `status` | Estado (`ACTIVE`, etc.) |
| `steps` | Lista de pasos (`ACTION`, `END`, …) |

### Campos de paso

| Campo | Descripción |
|-------|-------------|
| `id` | Identificador único del paso |
| `name` | Nombre descriptivo |
| `type` | Tipo de paso (`ACTION`, `END`, `USER_TASK`, `PROCESS`) |
| `preconditionStepId` | Paso que debe completarse antes de ejecutar éste |
| `preconditionExpression` | Expresión JEXL sobre variables del proceso; omite el paso si devuelve `false` |
| `parallel` | `true` para ejecutar en paralelo con otros pasos con el mismo `preconditionStepId` |
| `timeout` | Tiempo máximo de ejecución (duración ISO 8601, p. ej. `PT30S`) |
| `retries` | Número de reintentos automáticos en caso de fallo (por defecto `0`) |
| `rollbackable` | `true` para activar compensación si el paso falla |
| `compensationStepId` | Paso a ejecutar como compensación (rollback) |
| `formId` | Identificador del formulario asociado (requerido en pasos `USER_TASK`) |
| `childWorkflowDefinitionId` | Identificador del workflow hijo a ejecutar (requerido en pasos `PROCESS`) |

## Workflows de ejemplo por funcionalidad

| Funcionalidad | Workflow |
|---------------|----------|
| Pasos secuenciales básicos | `hello-world.yaml`, `approval.yaml`, `onboarding.yaml`, `order-processing.yaml` |
| Pasos en paralelo (`parallel`) | `document-processing.yaml`, `new-employee-setup.yaml` |
| Pasos condicionales (`preconditionExpression`) | `loan-approval.yaml`, `expense-approval.yaml` |
| Reintentos y timeout (`retries`, `timeout`) | `payment-processing.yaml` |
| Compensación / rollback (`rollbackable`, `compensationStepId`) | `travel-booking.yaml` |
| Tarea de usuario (`USER_TASK`, `formId`) | `expense-approval.yaml` |
| Subproceso (`PROCESS`, `childWorkflowDefinitionId`) | `new-employee-setup.yaml` |

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
