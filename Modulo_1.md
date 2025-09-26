# CURSO DEVOPS SENIOR
## Módulo 1: DevOps Estratégico y GitOps - Evaluación Parcial

**Team Quasar**
**Fecha:** 23-09-2025  

---

## 1. ¿Qué es GitOps y cómo se diferencia de DevOps tradicional?

GitOps es una evolución natural de los principios de DevOps y la Infraestructura como Código (IaC), donde Git se utiliza como única fuente de verdad para la gestión de infraestructura, configuraciones de aplicaciones y políticas de seguridad. A través de este enfoque declarativo, reproducible y auditable, GitOps permite automatizar la entrega y operación de sistemas complejos en entornos distribuidos.

GitOps utiliza herramientas como ArgoCD o Flux que detectan desviaciones automáticamente y corrigen el estado real del clúster para mantenerlo alineado con lo definido en el repositorio. Mientras que DevOps moderno se enfoca en la cultura, la colaboración entre áreas y la entrega continua usando CI/CD, observabilidad e IaC, GitOps lleva esos principios a un modelo donde todo cambio pasa por commits y pull requests, estandarizando despliegues y asegurando consistencia.

### Diferencias clave:

- **DevOps tradicional:** Modelo push donde pipelines CI/CD empujan cambios al cluster, requiriendo exposición de credenciales.
- **GitOps:** Modelo pull donde agentes en el cluster monitorean constantemente el repositorio Git y sincronizan automáticamente cualquier diferencia, eliminando la necesidad de exponer credenciales del cluster en pipelines externos.

---

## 2. ¿Cuáles son los patrones comunes en GitOps?

Los patrones comunes en GitOps son cinco:

### Repository-per-Environment
- Un repositorio distinto para cada entorno (dev, QA, stage, prod)
- Mayor aislamiento entre entornos pero riesgo de duplicación de configuraciones

### Single Repository with Environment Folders
- Un único repositorio con carpetas separadas por entorno para simplificar gestión
- Gestión centralizada pero requiere estrictas políticas de ramas y validación automatizada

### Progressive Delivery Pattern
- Integración con feature flags, canary deployments o blue-green deployments para liberar cambios de forma controlada
- Comúnmente integrado con Argo Rollouts, Flagger o sistemas de control de tráfico

### Drift Detection and Reconciliation
- Uso de operadores como ArgoCD o FluxCD para comparar estado deseado vs real y corregir desviaciones
- Puede notificar al equipo (modo observación) o reconciliar automáticamente (modo forzado)

### Multi-Tenant GitOps
- Para entornos compartidos (multi-cluster, multi-equipo)
- Requiere control granular de acceso (RBAC por equipo), estrategias de segmentación y automatización de permisos

---

## 3. ¿Qué ventajas ofrece ArgoCD en la implementación de GitOps?

ArgoCD actúa como un componente central de control, seguridad y visibilidad del pipeline operativo, ofreciendo múltiples ventajas:

### Automatización y Control
- Motor de sincronización entre Git y Kubernetes, asegurando que el estado del clúster coincida con lo definido en el repositorio
- Políticas automatizadas como auto-sync, auto-prune y self-heal que reducen trabajo manual

### Observabilidad y Auditoría
- Logs detallados de sincronización, health checks personalizados y dashboards integrados con Prometheus/Grafana
- Seguimiento completo de cambios y estados de aplicaciones

### Arquitectura Avanzada
- **API Server:** Interfaz de usuario, CLI y API RESTful
- **Repository Server:** Acceso a repositorios Git y análisis de manifiestos
- **Application Controller:** Comparación y reconciliación de estados
- **Integración SSO:** Dex para autenticación corporativa (OIDC, LDAP)

### Funcionalidades Avanzadas
- **App of Apps pattern:** Jerarquía de aplicaciones desde repositorio raíz
- **ApplicationSets:** Generación automática de aplicaciones ArgoCD a partir de reglas dinámicas, ideal para entornos multicluster, creación masiva de apps y entornos temporales de prueba
- **Sync Waves y Hooks:** Control de orden de despliegue de recursos dentro de una aplicación
- **Automated Sync Policies:** Mantienen automáticamente el estado deseado sin intervención manual
- **Integración con OPA/Gatekeeper y RBAC:** Cumplimiento de estándares de seguridad y control granular de acceso

---

## 4. ¿Qué consideraciones de seguridad son clave en GitOps con ArgoCD?

### Control de Acceso y Autenticación
- Integración con OPA/Gatekeeper y RBAC para políticas de validación de manifiestos previas al despliegue
- Limitación de acciones según rol o proyecto (lectura, sincronización, eliminación)
- Control de acceso a repositorios con SSH keys o tokens con permisos mínimos

### Observabilidad y Auditoría
- **Audit logs:** Seguimiento detallado de sincronizaciones, fallas y cambios manuales
- **Health checks personalizados:** Estados saludables para recursos no estándar
- **Dashboards:** Integración con Prometheus/Grafana para métricas operativas

### Seguridad de Red y Compliance
- Network policies para restringir comunicación entre ArgoCD y clusters
- Verificación de commits firmados con GPG para garantizar integridad
- Integración con sistemas de seguridad y compliance

### Integraciones Empresariales
- Firmas de imágenes (cosign) para verificación de integridad
- Escáneres de vulnerabilidades (Trivy, Grype)
- Sistemas de aprobación externa para cambios críticos

---

## 5. ¿Qué herramientas se pueden usar para gestionar secretos en GitOps?

La gestión de secretos es crítica en GitOps ya que la configuración de infraestructura y aplicaciones requiere acceso a claves, tokens y secretos. Almacenar estos datos directamente en Git representa una vulnerabilidad de seguridad, incluso en repositorios privados, por lo que es vital implementar mecanismos especializados:

### Sealed Secrets (Bitnami)
- Convierte secretos de Kubernetes en manifiestos cifrados seguros para versionar en Git
- Cifrados con clave pública del controlador, solo desencriptables por el clúster destino

### Vault by HashiCorp
- Solución enterprise para almacenamiento, rotación y políticas de acceso de secretos
- Genera secretos dinámicos bajo demanda (credenciales temporales de bases de datos)
- Control de acceso basado en políticas (ACL) y auditoría avanzada

### External Secrets Operator (ESO)
- Sincroniza secretos almacenados en proveedores externos hacia Kubernetes
- Desacopla completamente los secretos del repositorio Git
- Compatible con AWS Secrets Manager, Azure Key Vault, Google Secret Manager, HashiCorp Vault

### SOPS (Secrets OPerationS)
- Herramienta de Mozilla para cifrar archivos YAML/JSON con claves GPG o KMS
- Compatible con Helm y GitOps para pipelines CI/CD que versionan plantillas con datos sensibles cifrados

### Secret Management con Helm y Kustomize
- Inyección de secretos vía plantillas con values.yaml en Helm Charts o secretGenerator en Kustomize
- Requiere integración con herramientas como SOPS o ESO para cumplir estándares de seguridad

---

## 6. ¿Cómo se puede implementar una estrategia de promoción entre ambientes en GitOps?

En GitOps, la promoción entre ambientes implica trasladar cambios aprobados desde un entorno hacia el siguiente, manteniendo control, trazabilidad y auditoría completa.

### Estrategias de Estructura
- **Environment per Branch:** Rama separada por entorno (dev, staging, main)
- **Environment per Directory:** Carpetas separadas en un mismo repositorio con control de acceso granular

### Consideraciones Estratégicas
- **Políticas de revisión:** Pull requests obligatorios con aprobaciones requeridas
- **Validación automatizada:** Linters, validadores de schema (Kustomize, Helm, JsonSchema)
- **Artefactos confiables:** Integración con pipelines CI que generen artefactos versionados y firmados
- **Control de acceso:** Automatización con OPA/Gatekeeper para auditoría de cambios

### Implementación Práctica
- **Promoción manual:** Approval gates con revisión humana para cambios críticos
- **Promoción automática:** Basada en tests exitosos y métricas de salud
- **Canary deployments:** Promoción gradual con monitoreo de métricas
- **Feature flags:** Control granular de funcionalidades por ambiente

### Observabilidad
- Alta madurez en IaC y monitoreo para visibilidad de estados deseados vs reales
- Métricas DORA para medir efectividad de promociones
- Dashboards de estado de promociones entre ambientes

---

## 7. ¿Cómo se sincronizan los cambios en ArgoCD y qué modos de sincronización existen?

ArgoCD ofrece múltiples modos de sincronización para diferentes escenarios operativos:

### Sincronización Automática
Argo CD monitorea el repositorio Git y aplica los cambios automáticamente al clúster, generando despliegues rápidos ideal en entornos de desarrollo/QA.

- **Sincronización automática + Self-Heal:** Corrige desviaciones si alguien modifica el clúster manualmente, garantizando que Git siempre es la fuente de la verdad.
- **Sincronización automática + Prune:** Elimina recursos que ya no están en Git, manteniendo el cluster limpio y ordenado.

### Sincronización Manual
Los usuarios deben realizar la sincronización manualmente, lo que permite un control total sobre los cambios y la configuración, siendo útil en producción o cambios críticos, pero a mayor intervención humana aumenta el riesgo de olvidar aplicar cambios.

### Sincronización con Confirmación
Para ciertos objetos, se requiere confirmación manual antes de la sincronización. Estos modos permiten a los usuarios gestionar y mantener el estado de sus aplicaciones en Kubernetes de manera efectiva, asegurando que siempre reflejen la configuración deseada en el repositorio Git.

---
