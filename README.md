# 🌐 Discovery Server - Iron Library

> Servidor de descubrimiento de servicios para la arquitectura distribuida de Iron Library

## 🎯 Descripción

Este microservicio actúa como el **servidor de registro y descubrimiento** para toda la arquitectura distribuida de Iron Library. Basado en Netflix Eureka Server, permite que todos los microservicios se registren automáticamente y se descubran entre sí, facilitando la comunicación inter-servicios y el balanceo de carga.

## 🚀 Características

- ✅ **Registro automático** de microservicios
- ✅ **Descubrimiento de servicios** en tiempo real
- ✅ **Health checking** de servicios registrados
- ✅ **Dashboard web** para monitoreo
- ✅ **Balanceo de carga** automático
- ✅ **Tolerancia a fallos** y auto-recuperación
- ✅ **API REST** para consultas programáticas
- ✅ **Configuración centralizada** de red

## 🛠️ Stack Tecnológico

- **Spring Boot** 3.4.6
- **Spring Cloud Netflix Eureka Server** - Service Discovery
- **Java** 21
- **Maven** 3.9.9

## 📡 Endpoints Principales

### Base URL: `http://localhost:8761`

| Endpoint | Descripción |
|----------|-------------|
| `/` | Dashboard web de Eureka |
| `/eureka/apps` | Lista de aplicaciones registradas (XML) |
| `/eureka/apps/{app}` | Información de aplicación específica |
| `/eureka/apps/{app}/{instanceId}` | Información de instancia específica |
| `/eureka/instances/{instanceId}` | Detalles de instancia por ID |

### Dashboard Web
- **URL**: http://localhost:8761
- **Funcionalidad**: Visualización en tiempo real de servicios registrados
- **Información mostrada**: Estado, instancias, metadatos de servicios

## 🔧 Configuración

### Variables de Entorno
```properties
# Configuración del servidor
spring.application.name=discovery-server
server.port=8761
```

### Configuración Completa (application.properties)
```properties
spring.application.name=discovery-server
server.port=8761

# Eureka Server Configuration
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
eureka.client.service-url.defaultZone=http://localhost:8761/eureka/

# Disable self-preservation in dev environment
eureka.server.enable-self-preservation=false
eureka.server.eviction-interval-timer-in-ms=2000
```

## 🚀 Instalación y Ejecución

### Prerrequisitos
- Java 21
- Maven 3.6+

### Pasos de Instalación
```bash
# Clonar el repositorio
git clone https://github.com/IronLibrary/discovery-server.git
cd discovery-server

# Instalar dependencias
./mvnw clean install

# Ejecutar el servidor
./mvnw spring-boot:run
```

### Verificar Instalación
```bash
# Verificar que el servidor está funcionando
curl http://localhost:8761/eureka/apps

# Acceder al dashboard web
# Ir a http://localhost:8761 en el navegador
```

## 🧪 Testing

### Ejecutar Tests
```bash
# Todos los tests
./mvnw test

# Test de contexto
./mvnw test -Dtest="*ApplicationTests"
```

### Verificar Registro de Servicios
```bash
# Ver servicios registrados (JSON)
curl -H "Accept: application/json" http://localhost:8761/eureka/apps

# Ver servicio específico
curl -H "Accept: application/json" http://localhost:8761/eureka/apps/USER-SERVICE
```

## 🔗 Servicios del Ecosistema

### Servicios de Iron Library
- **book-service** - Puerto 8081
- **user-service** - Puerto 8082  
- **loan-service** - Puerto 8083
- **gateway-service** - Puerto 8080

### Configuración de Clientes
Los servicios se registran automáticamente con esta configuración:
```properties
eureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/
spring.application.name=nombre-del-servicio
```

## 📊 Monitoreo y Administración

### Dashboard Web
- **URL**: http://localhost:8761
- **Información disponible**:
  - Instancias registradas
  - Estado de salud
  - Metadatos de servicios
  - Configuración del servidor

### API de Consulta
```bash
# Listar todas las aplicaciones
GET /eureka/apps

# Obtener aplicación específica
GET /eureka/apps/{appName}

# Obtener instancia específica
GET /eureka/apps/{appName}/{instanceId}
```

### Health Checks
Los servicios reportan su estado cada 30 segundos por defecto:
- **UP**: Servicio funcionando correctamente
- **DOWN**: Servicio no disponible
- **STARTING**: Servicio iniciándose
- **OUT_OF_SERVICE**: Servicio fuera de servicio

## 🚀 Arquitectura del Sistema

### Flujo de Registro
1. **Servicio inicia** → Se conecta a Discovery Server
2. **Registro automático** → Envía metadatos (IP, puerto, health URL)
3. **Heartbeat** → Envía señales cada 30 segundos
4. **Renovación** → Mantiene registro activo
5. **Cancelación** → Se desregistra al detenerse

### Flujo de Descubrimiento
1. **Cliente solicita servicio** → Consulta Discovery Server
2. **Discovery devuelve instancias** → Lista de IPs y puertos disponibles
3. **Load balancing** → Cliente elige instancia
4. **Comunicación directa** → Llamada entre servicios

## 📚 Documentación API

### Obtener Servicios Registrados
```bash
curl -X GET http://localhost:8761/eureka/apps \
  -H "Accept: application/json"
```

### Respuesta de Servicios
```json
{
  "applications": {
    "application": [
      {
        "name": "USER-SERVICE",
        "instance": [
          {
            "instanceId": "localhost:user-service:8082",
            "hostName": "localhost",
            "port": {
              "$": 8082,
              "@enabled": "true"
            },
            "status": "UP",
            "healthCheckUrl": "http://localhost:8082/actuator/health"
          }
        ]
      }
    ]
  }
}
```

## 🛠️ Troubleshooting

### Problemas Comunes

**Servicios no se registran**
```bash
# Verificar conectividad
curl http://localhost:8761/eureka/apps

# Revisar logs del servicio cliente
# Verificar configuración eureka.client.serviceUrl.defaultZone
```

**Dashboard no carga**
```bash
# Verificar que el puerto 8761 está libre
netstat -an | grep 8761

# Verificar logs del discovery server
./mvnw spring-boot:run
```

**Servicios aparecen como DOWN**
- Verificar health check endpoints
- Revisar configuración de firewall
- Confirmar que los servicios responden en sus puertos

## 🚀 Próximas Mejoras

- [ ] **Autenticación** - Seguridad básica para producción
- [ ] **HTTPS** - Comunicación segura
- [ ] **Clustering** - Múltiples instancias de Eureka
- [ ] **Métricas** - Integración con Micrometer
- [ ] **Alertas** - Notificaciones cuando servicios fallan
- [ ] **Backup** - Persistencia de registro de servicios
