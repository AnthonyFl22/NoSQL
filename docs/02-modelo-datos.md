# 🧱 Modelo de Datos Relacional

Este documento describe la estructura del modelo relacional utilizado como base para comparar los motores de bases de datos SQL y NoSQL.

---

## 1 · Visión de Conjunto

| Nivel jerárquico | Entidad (tabla)       | Rol en el modelo                                                | Volumen simulado |
|------------------|------------------------|------------------------------------------------------------------|------------------|
| País             | `Country`              | Agrupa cadenas por jurisdicción (implica moneda / región)        | 10               |
| Cadena           | `HotelChain`           | Marca corporativa que opera varios hoteles                       | 25               |
| Hotel            | `Hotel`                | Propiedades físicas (sede de ingresos)                           | 150              |
| Habitación       | `Room`                 | Unidad mínima de inventario                                      | ~5 000           |
| Huésped          | `Guest`                | Cliente individual                                               | 8 000            |
| Servicio         | `Service`              | Catálogo de add-ons (spa, tour, etc.)                            | 12 constantes    |
| Reserva          | `Reservation`          | Tabla transaccional (hecho)                                      | 20 000           |
| (Puente)         | `ReservationService`   | M:N entre Reserva y Servicio                                     | ≈ 15 000         |

> Las relaciones `Country → HotelChain → Hotel → Room` reflejan ≥ 3 niveles jerárquicos, clave para transformar los datos a JSON multinivel y grafos de profundidad ≥ 4.

---

## 2 · Descripción Detallada de Tablas

### 2.1 · `Country`

| Columna     | Tipo          | Descripción                                          |
|-------------|---------------|------------------------------------------------------|
| country_id  | INT PK        | Identificador interno                                |
| name        | VARCHAR(80)   | Nombre oficial del país                              |
| iso_code    | CHAR(2) UNIQUE| Código ISO‑3166 alfa‑2 (MX, US…)                     |
| currency    | CHAR(3)       | Moneda local (MXN, USD…)                             |

**Relevancia:** Segmenta métricas por región/moneda. Útil en BigQuery (agrupación) y en Neo4j (vecindad geográfica).

---

### 2.2 · `HotelChain`

| Columna     | Tipo          | Descripción                                          |
|-------------|---------------|------------------------------------------------------|
| chain_id    | INT PK        | Identificador único de la cadena                     |
| name        | VARCHAR(120)  | Nombre comercial (ej. Ortega Hospitality)            |
| founded_year| SMALLINT      | Año de fundación                                     |
| hq_city     | VARCHAR(120)  | Ciudad sede                                          |
| country_id  | INT FK → Country | Relación con el país                            |

**Relevancia:** Nivel corporativo para análisis de marca. Segundo escalón jerárquico.

---

### 2.3 · `Hotel`

| Columna     | Tipo          | Descripción                                          |
|-------------|---------------|------------------------------------------------------|
| hotel_id    | INT PK        | Identificador único del hotel                        |
| chain_id    | INT FK → HotelChain | Relación con cadena hotelera                  |
| name        | VARCHAR(150)  | Nombre de la propiedad                               |
| city        | VARCHAR(120)  | Ciudad                                               |
| address     | VARCHAR(255)  | Dirección completa                                   |
| stars       | TINYINT (1–5) | Clasificación turística                              |

**Relevancia:** Unidad operativa. Apoya análisis geográficos y de calidad. Nodo clave en grafos.

---

### 2.4 · `Room`

| Columna     | Tipo              | Descripción                                      |
|-------------|-------------------|--------------------------------------------------|
| room_id     | INT PK            | Identificador único de la habitación             |
| hotel_id    | INT FK → Hotel    | Relación con el hotel                            |
| room_number | VARCHAR(10)       | Número visible al huésped                        |
| room_type   | ENUM              | Categoría (single, double, suite, deluxe)        |
| capacity    | TINYINT           | Capacidad máxima                                 |
| base_rate   | DECIMAL(10,2)     | Tarifa estándar por noche                        |

**Relevancia:** Inventario básico. Útil en simulaciones de ocupación y pricing dinámico.

---

### 2.5 · `Guest`

| Columna     | Tipo              | Descripción                                      |
|-------------|-------------------|--------------------------------------------------|
| guest_id    | INT PK            | Identificador único del huésped                  |
| first_name, last_name | VARCHAR | Identidad del cliente                            |
| email       | VARCHAR UNIQUE    | Correo único                                     |
| country_id  | INT FK → Country  | País de origen                                   |
| created_at  | DATETIME          | Fecha de registro en el sistema                  |

**Relevancia:** Dimensión cliente para análisis de procedencia, fidelidad y marketing.

---

### 2.6 · `Service`

| Columna     | Tipo              | Descripción                                      |
|-------------|-------------------|--------------------------------------------------|
| service_id  | INT PK            | Identificador único del servicio                 |
| name        | VARCHAR(120)      | Nombre comercial del servicio                    |
| category    | ENUM              | Categoría (alimentos, spa, transporte, otros)    |
| base_price  | DECIMAL(10,2)     | Tarifa estándar sugerida                         |

**Relevancia:** Catálogo de servicios adicionales. Amplía el modelo transaccional y permite análisis por categoría.

---

### 2.7 · `Reservation` (tabla de hechos)

| Columna      | Tipo                  | Descripción                                    |
|--------------|-----------------------|------------------------------------------------|
| reservation_id | INT PK              | ID único de la reserva                         |
| guest_id     | INT FK → Guest        | Huésped                                        |
| room_id      | INT FK → Room         | Habitación reservada                           |
| check_in, check_out | DATE           | Fechas de estancia                             |
| status       | ENUM                  | Estado: booked / checked_in / checked_out / cancelled |
| total_amount | DECIMAL(12,2)         | Total a pagar (habitación + extras)            |
| created_at   | DATETIME              | Fecha de creación de la reserva                |

**Relevancia:** Tabla central para benchmarking. Se usa para pruebas de carga, consultas de ingresos, ocupación, y grafos.

---

### 2.8 · `ReservationService` (tabla puente M:N)

| Columna      | Tipo                      | Descripción                                 |
|--------------|---------------------------|---------------------------------------------|
| reservation_id | INT FK → Reservation     | Reserva relacionada                         |
| service_id   | INT FK → Service           | Servicio contratado                          |
| quantity     | TINYINT                    | Cantidad                                     |
| price_unit   | DECIMAL(10,2)              | Precio aplicado por unidad (permite descuentos) |

**Relevancia:** Añade complejidad transaccional. Clave para simulaciones de ingresos adicionales y relación M:N.

📄 [Ver script SQL del modelo relacional](docs/Database.sql)

🧭 ![Diagrama ER](docs/Diagrama ER.png)


