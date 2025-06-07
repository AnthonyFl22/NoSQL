# 🏨 Introducción

## 🎯 Propósito del Proyecto

Este proyecto tiene como finalidad comparar el rendimiento y la aplicabilidad de diferentes tecnologías de bases de datos —relacionales y no relacionales— usando un caso práctico: un sistema de reservas para hoteles.

La comparación incluye:
- Bases de datos **relacionales**: MySQL
- Bases de datos **NoSQL de tipo documento**: MongoDB
- Bases de datos **NoSQL de tipo grafo**: Neo4j
- Bases de datos **analíticas en la nube**: BigQuery

## 🧩 Justificación

En el contexto actual, donde los sistemas requieren almacenar y procesar grandes volúmenes de datos con estructuras diversas, es fundamental entender:
- Cuál tecnología es más rápida para **insertar** y **consultar** información.
- Cuál se adapta mejor a estructuras complejas como JSON o relaciones múltiples.
- Cuál es más eficiente para escenarios analíticos.

Este proyecto muestra la diferencia entre estas tecnologías aplicadas a un mismo dominio de datos: **un sistema hotelero multinivel**, estructurado por país, cadena hotelera y hotel.

## 🗃️ Descripción de los Datos

Se generaron datos sintéticos y estructurados jerárquicamente en formato JSON para representar:

- Reservas
- Clientes
- Habitaciones
- Servicios

Estos datos se utilizaron para alimentar los distintos gestores de base de datos y medir el rendimiento en tiempo de carga e interrogación.

## 📚 Alcances del Proyecto

- Modelado e inserción de datos en SQL y NoSQL.
- Uso de Python para automatizar la carga.
- Medición de tiempos de inserción y consulta.
- Análisis comparativo entre tecnologías.


## 🧑‍💻 Autor

Luis Anthony Flores Portillo  
Estudiante de Ciencia de Datos y Actuaría  
GitHub: [AnthonyFl22](https://github.com/AnthonyFl22)
