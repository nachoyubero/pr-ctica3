# Práctica 3 PAT: Carrito de la Compra (Persistencia y Servicios)

## Objetivo
El objetivo de esta entrega es evolucionar la aplicación del Carrito de la Compra incorporando persistencia en base de datos e introduciendo una **Capa de Servicios** para gestionar la lógica de negocio.

## Arquitectura y Justificación de Decisiones

Para garantizar la mantenibilidad y escalabilidad, el sistema se ha estructurado separando responsabilidades:

* **Entidades (Modelo de Datos):** Se establece una relación 1:N (`@ManyToOne`) para mantener la integridad referencial.
    * `Carrito`: Entidad maestra que almacena el identificador y el precio total. 
    * `LineaCarrito`: Representa el detalle de la compra. Conceptualmente, esta separación permite que un solo carrito agrupe múltiples productos distintos sin generar redundancia de datos.
* **Capa de Servicios (`ServicioCarritos`):** Toda la lógica operacional se ha extraído de los controladores.  Porque el controlador solo debe enrutar peticiones HTTP. Al centralizar cálculos  en el servicio, evitamos código duplicado, facilitamos el testing y respetamos el principio de responsabilidad única.
* **Gestión de Transacciones (`@Transactional`):** Se utiliza en los métodos del servicio para asegurar la consistencia. Si se añade una línea, pero falla la actualización del carrito principal, la base de datos revierte la operación entera, evitando datos corruptos o descuadres de precios.

## Funcionalidad y End-points
De acuerdo con el enunciado de la práctica, los controladores expondrán los siguientes end-points principales haciendo uso de los servicios implementados:

* **Añadir línea de carrito:** Permite agregar un artículo (`LineaCarrito`) a un `Carrito` existente. El sistema calcula automáticamente el `costeLinea` y actualiza el `precio` total del carrito maestro de forma transparente.
* **Borrar línea de carrito:** Elimina una línea específica y descuenta su importe del total del carrito, manteniendo la coherencia financiera.