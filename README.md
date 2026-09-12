# ðŸ¥— Sistema de GestiÃ³n de Acceso Alimentario y Red de Heladeras Comunitarias

<p align="center">
  <img src="https://img.shields.io/badge/Language-Java%2017-ED8B00?logo=openjdk&logoColor=white" alt="Java 17" />
  <img src="https://img.shields.io/badge/Framework-Javalin%206.3-03c75a?logo=java&logoColor=white" alt="Javalin" />
  <img src="https://img.shields.io/badge/ORM-Hibernate%20%2F%20JPA-59666C?logo=hibernate&logoColor=white" alt="Hibernate" />
  <img src="https://img.shields.io/badge/Database-MySQL%20%7C%20HSQLDB-4479A1?logo=mysql&logoColor=white" alt="Database" />
  <img src="https://img.shields.io/badge/IoT-MQTT%20%2F%20Eclipse%20Paho-660066" alt="MQTT" />
  <img src="https://img.shields.io/badge/Templates-Handlebars-FF7D00" alt="Handlebars" />
  <img src="https://img.shields.io/badge/UTN%20FRBA-DiseÃ±o%20de%20Sistemas%202024-blueviolet" alt="UTN FRBA" />
</p>

Sistema integral orientado a mitigar la inseguridad alimentaria en contextos de vulnerabilidad socioeconÃ³mica. Desarrollado como Trabajo PrÃ¡ctico Anual para la cÃ¡tedra de **DiseÃ±o de Sistemas (2024) - UTN FRBA**. 

La plataforma articula y conecta a **donantes particulares, empresas, tÃ©cnicos y personas en situaciÃ³n de vulnerabilidad**, proveyendo trazabilidad en tiempo real sobre una red distribuida de **heladeras comunitarias sensorizadas (IoT)**.

---

## ðŸ›ï¸ Arquitectura del Sistema

La soluciÃ³n adopta una arquitectura **MVC con cliente liviano**, integrando procesamiento por lotes (Cronjobs), brokers de mensajerÃ­a IoT y APIs externas de terceros:

```mermaid
flowchart TD
    subgraph Client["Capa de PresentaciÃ³n"]
        UI["Navegador Web / Vistas Handlebars"]
    end

    subgraph Backend["Servidor Web (Javalin 6)"]
        Router[Router & Middlewares de AutenticaciÃ³n]
        Controllers["Controladores MVC / DTOs"]
        Services["Capa de Negocio & Service Locator"]
        Domain["Modelo de Dominio Rico"]
        Router --> Controllers --> Services --> Domain
    end

    subgraph IoT["Subsistema IoT & Sensores"]
        Broker["Broker MQTT / Eclipse Paho"]
        Sensores["Sensores de Temperatura / Apertura"]
        Sensores --> Broker --> Backend
    end

    subgraph Async["Procesamiento Batch & Alertas"]
        Cron["CronJobs / Tareas Programadas"]
        Notif["Notificaciones: Telegram Bot • Twilio WhatsApp • SendGrid"]
        Reports["Generador de Reportes PDF - iText"]
    end

    subgraph External["Servicios Externos & Datos"]
        Retrofit["Retrofit2 Client - Recomendador de Puntos"]
        DB[("Base de Datos MySQL / JPA Hibernate")]
    end

    UI <--> Router
    Backend <--> DB
    Backend <--> Retrofit
    Cron --> Domain
    Domain --> Notif
    Cron --> Reports
```

---

## ðŸš€ CaracterÃ­sticas Principales

### ðŸ§Š Red de Heladeras Comunitarias e IoT
- **TelemetrÃ­a y Sensores en Tiempo Real:** RecepciÃ³n y procesamiento de eventos de temperatura y apertura de puertas mediante protocolo **MQTT**.
- **GestiÃ³n Automatizada de Incidentes:** DetecciÃ³n de anomalÃ­as tÃ©rmicas y fallas de conexiÃ³n; disparo automÃ¡tico de alertas a colaboradores suscritos y tÃ©cnicos de la zona.
- **RecomendaciÃ³n GeogrÃ¡fica:** IntegraciÃ³n con servicio REST externo (mediante **Retrofit2**) para sugerir puntos Ã³ptimos de colocaciÃ³n de heladeras segÃºn densidad demogrÃ¡fica y necesidad comunitaria.

### ðŸ¤ Modelo de Colaboraciones y FidelizaciÃ³n
- **Diversidad de Formas de DonaciÃ³n:**
  - DonaciÃ³n de viandas (control de caducidad y trazabilidad).
  - DistribuciÃ³n / traslado de viandas entre heladeras con capacidad disponible.
  - Donaciones monetarias periÃ³dicas o Ãºnicas.
  - GestiÃ³n y mantenimiento de heladeras por parte de personas jurÃ­dicas.
- **Sistema de Puntos & CatÃ¡logo de Premios:** Reconocimiento y recompensas a colaboradores por sus aportes a la red.

### ðŸ’³ Acceso y Tarjetas para Personas Vulnerables
- Registro de titulares y menores a cargo.
- AsignaciÃ³n de tarjetas de apertura y control de cupos de retiro diario de viandas para evitar desabastecimiento.

### ðŸ“² Notificaciones Multicanal
- Soporte multicanal desacoplado con patrÃ³n Adapter:
  - **Telegram:** InteracciÃ³n bidireccional mediante `telegrambots`.
  - **WhatsApp / SMS:** IntegraciÃ³n vÃ­a **Twilio SDK**.
  - **Email:** Alertas transaccionales con **SendGrid API**.

### â±ï¸ Cronjobs y Reportes PeriÃ³dicos
- Tareas programadas desacopladas (`cronjobs`) para:
  - DetecciÃ³n de fallas de conexiÃ³n en sensores de heladeras.
  - GeneraciÃ³n periÃ³dica de reportes exportables a **PDF** con **iText** (fallas histÃ³ricas, movimientos de viandas, aportes por colaborador).
  - Mantenimiento y control de tarjetas.

---

## ðŸ› ï¸ Stack TecnolÃ³gico

| Capa / Componente | TecnologÃ­a |
|---|---|
| **Lenguaje** | Java 17 |
| **Framework Web** | Javalin 6.3.0 |
| **Plantillas / Frontend** | Handlebars.java, HTML5, CSS3, JavaScript |
| **Persistencia / ORM** | Hibernate, JPA Extras, MySQL 8, HSQLDB (testing) |
| **IoT / MensajerÃ­a** | Eclipse Paho MQTT |
| **Clientes HTTP / APIs** | Retrofit 2 + Gson |
| **Notificaciones** | Twilio SDK, Telegram Bots, SendGrid Java |
| **Testing & Calidad** | JUnit 5, Mockito, JaCoCo, SpotBugs, Checkstyle |
| **Build & Empaquetado** | Apache Maven, Maven Shade Plugin (Multi-JAR) |

---

## âš™ï¸ CompilaciÃ³n y EjecuciÃ³n

### Prerrequisitos
- **Java Development Kit (JDK) 17** o superior.
- **Apache Maven 3.8+**.
- Servidor **MySQL** en ejecuciÃ³n.

### Build del Proyecto
Para compilar y empaquetar la aplicaciÃ³n web y los cronjobs:
```bash
mvn clean package
```
Esto generarÃ¡ los artefactos JAR correspondientes en la carpeta `target/`:
- `app.jar`: AplicaciÃ³n principal Javalin.
- `heladeras.jar`, `falla-conexion.jar`, `reportes.jar`, `tarjetas.jar`: Tareas batch / cronjobs.

### EjecuciÃ³n de la AplicaciÃ³n Web
```bash
java -jar target/app.jar
```
La aplicaciÃ³n web quedarÃ¡ accesible en `http://localhost:8080` (o el puerto configurado en el archivo de entorno).

---

## ðŸ‘¥ Equipo de Desarrollo

| Integrante | GitHub |
|---|---|
| **Uriel Grifman** | [@uriGrif](https://github.com/uriGrif) |
| **Thomas Ariel Luca** | [@thomyluca](https://github.com/thomyluca) |
| **Manuel Martinez** | [@ManuMar28](https://github.com/ManuMar28) |
| **Gonzalo Turri** | [@GonTurri](https://github.com/GonTurri) |
| **Tobias Winik** | [@twinik](https://github.com/twinik) |

---

## ðŸ“‘ Enunciado
- [Trabajo PrÃ¡ctico Anual Integrador 2024 - DDS UTN FRBA](https://docs.google.com/document/d/13niiEppxrm8LjyrxmH5Pskrc7VVuPKWSFRi3WvhsXns/edit?tab=t.0)
