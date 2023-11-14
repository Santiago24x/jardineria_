# CONSULTAS BASE DE DATOS JARDINERIA

## MODELO FISICO 
![ModeloFisico](modeloFisico.png)

## BASE DE DATOS `jardineria`
### Instrucciones de implementacion de la base de datos sobre la que se trabajaran las consultas

## 1. Conectar al Servidor de Base de Datos

Abre un cliente de línea de comandos o una interfaz gráfica que te permita conectarte a tu servidor de bases de datos MySQL o MariaDB.

`mysql -u tu_usuario -p`

Reemplaza tu_usuario con el nombre de usuario de tu base de datos.

## 2. Crear la Base de Datos

`CREATE DATABASE nombre_de_tu_base_de_datos;`

Reemplaza nombre_de_tu_base_de_datos con el nombre que desees para tu base de datos.

## 3. Seleccionar la Base de Datos

`USE nombre_de_tu_base_de_datos;`

Esto te permitirá ejecutar comandos dentro de esa base de datos.

## Ejecutar el Script SQL

Puedes copiar el script contenido dentro `jardineria.sql` y pegarlo en tu consola para generar la ejecucion.


## CONSULTAS

1. Devuelve un listado con el código de oficina y la ciudad donde hay oficinas.

   ```sql
   SELECT codigo_oficina, ciudad 
   FROM oficina;
   ```

2. Devuelve un listado con la ciudad y el teléfono de las oficinas de España.

   ```sql
   SELECT ciudad, telefono 
   FROM oficina 
   WHERE pais = 'España';
   ```

3. Devuelve un listado con el nombre, apellidos y email de los empleados cuyo jefe tiene un código de jefe igual a 7.

   ```sql
   SELECT concat_ws(' ', nombre, apellido1, apellido2) AS nombre, email
   FROM empleado
   WHERE codigo_jefe = 7
   ```

4. Devuelve el nombre del puesto, nombre, apellidos y email del jefe de la empresa.

   ```sql
   SELECT CONCAT_WS(' ',nombre,apellido1,apellido2) as nombre
   FROM empleado
   WHERE codigo_jefe IS NULL;
   
   
   ```

5. Devuelve un listado con el nombre, apellidos y puesto de aquellos empleados que no sean representantes de ventas.

   ```sql
   -- Opcion 1
   SELECT CONCAT_WS(' ', nombre, apellido1, apellido2) as nombre, puesto FROM empleado WHERE puesto != 'Representante Ventas';
   -- Opcion 2
   SELECT CONCAT_WS(' ', nombre, apellido1, apellido2) as nombre, puesto FROM empleado WHERE NOT(puesto = 'Representante Ventas');
   ```

6. Devuelve un listado con el nombre de los todos los clientes españoles.

   ```sql
   SELECT nombre_cliente,pais 
   FROM cliente
   WHERE pais = 'Spain';
   ```

7. Devuelve un listado con los distintos estados por los que puede pasar un pedido.

   ```sql
   SELECT DISTINCT estado FROM pedido;
   ```

8. Devuelve un listado con el código de cliente de aquellos clientes que realizaron algún pago en 2008. Tenga en cuenta que deberá eliminar aquellos códigos de cliente que aparezcan repetidos. Resuelva la consulta:

   - Utilizando la función `Year` de MySQL.

   ```sql
   SELECT DISTINCT codigo_cliente 
   FROM pago 
   WHERE YEAR(fecha_pago) = 2008;
   ```

   - Utilizando la función `DATE FORMAT` de MySQL.

   ```sql
   SELECT DISTINCT codigo_cliente FROM pago WHERE DATE_FORMAT(fecha_pago, '%Y') = 2008;
   ```

   - Sin utilizar ninguna de las funciones anteriores.

   ```sql
   SELECT DISTINCT codigo_cliente FROM pago
   WHERE fecha_pago BETWEEN '2008-01-01' AND '2008-12-31';
   ```

9. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos que no han sido entregados a tiempo.

   ```sql
   SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega
   FROM pedido
   WHERE fecha_entrega > fecha_esperada OR fecha_entrega IS NULL
   ORDER BY fecha_esperada ASC;
   ```

10. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos cuya fecha de entrega ha sido al menos dos días antes de la fecha esperada.

    - Utilizando la función `ADDDATE` de MySQL.

    ```sql
    SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega FROM pedido
    WHERE fecha_esperada >= ADDDATE(fecha_entrega, INTERVAL 2 DAY);
    ```

    - Utilizando la función `DATEDIFF` de MySQL.

    ```sql
    SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega FROM pedido
    WHERE DATEDIFF(fecha_esperada, fecha_entrega) >= 2;
    ```

    - ¿Sería posible resolver esta consulta utilizando el operador de suma `+` o resta `-`?

    ```sql
    SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega FROM pedido 
    WHERE fecha_entrega <= (fecha_esperada - INTERVAL 2 DAY);
    ```

11. Devuelve un listado de todos los pedidos que fueron **rechazados** en `2009`.

    ```sql
    SELECT * FROM pedido WHERE YEAR(fecha_pedido) = 2009 AND estado = 'Rechazado';
    ```

12. Devuelve un listado de todos los pedidos que han sido **entregados** en el mes de enero de cualquier año.

    ```sql
    SELECT * FROM pedido WHERE MONTH(fecha_entrega) = 01 AND estado = 'Entregado';
    ```

13. Devuelve un listado con todos los pagos que se realizaron en el año `2008` mediante `Paypal`. Ordene el resultado de mayor a menor.

    ```sql
    SELECT * FROM pago WHERE forma_pago = 'PayPal' AND YEAR(fecha_pago) = 2008
    ORDER BY total DESC;
    ```

14. Devuelve un listado con todas las formas de pago que aparecen en la tabla `pago` . Tenga en cuenta que no deben aparecer formas de pago repetidas.

    ```sql
    SELECT DISTINCT forma_pago FROM pago;
    ```

15. Devuelve un listado con todos los productos que pertenecen a la gama `Ornamentales` y que tienen más de `100` unidades en stock. El listado deberá estar ordenado por su precio de venta, mostrando en primer lugar los de mayor precio.

    ```sql
    SELECT * FROM producto WHERE gama = 'Ornamentales' AND cantidad_en_Stock > 100
    ORDER BY precio_venta DESC;
    ```

16. Devuelve un listado con todos los clientes que sean de la ciudad de `Madrid` y cuyo representante de ventas tenga el código de empleado `11` o `30`.

    ```sql
    SELECT * FROM cliente WHERE ciudad = 'Madrid'
    AND (codigo_empleado_rep_ventas = 11 OR codigo_empleado_rep_ventas = 30);
    ```

## Consultas multitabla (Composición interna)

1. Obtén un listado con el nombre de cada cliente y el nombre y apellido de su representante de ventas.

   ```sql
   SELECT c.nombre_cliente AS Cliente, CONCAT_WS(' ', e.nombre, e.apellido1, e.apellido2) AS RepresentanteVentas
   FROM cliente c 
   JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado;
   ```

2. Muestra el nombre de los clientes que hayan realizado pagos junto con el nombre de sus representantes de ventas.

   ```sql
   SELECT 
     c.nombre_cliente AS Cliente, 
     CONCAT_WS(' ', e.nombre, e.apellido1, e.apellido2) AS representanteVentas, 
     CASE 
       WHEN p.id_transaccion IS NULL THEN 'No hay pago'
       ELSE 'Realizo pago'
     END AS EstadoPago
   FROM 
     cliente c
   JOIN 
     empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
   LEFT JOIN 
     pago p ON c.codigo_cliente = p.codigo_cliente
   WHERE
   p.id_transaccion is not null;
   ```

3. Muestra el nombre de los clientes que **no** hayan realizado pagos junto con el nombre de sus representantes de ventas.

   ```sql
   SELECT 
     c.nombre_cliente AS Cliente, 
     CONCAT_WS(' ', e.nombre, e.apellido1, e.apellido2) AS representanteVentas, 
     CASE 
       WHEN p.id_transaccion IS NULL THEN 'No hay pago'
       ELSE 'Realizo pago'
     END AS EstadoPago
   FROM 
     cliente c
   JOIN 
     empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
   LEFT JOIN 
     pago p ON c.codigo_cliente = p.codigo_cliente
   WHERE
   p.id_transaccion is null;
   ```

4. Devuelve el nombre de los clientes que han hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

   ```sql
   SELECT DISTINCT
     c.nombre_cliente AS Cliente, 
     CONCAT_WS(' ', e.nombre, e.apellido1, e.apellido2) AS representanteVentas,o.ciudad,
     CASE 
       WHEN p.id_transaccion IS NULL THEN 'No hay pago'
       ELSE 'Realizo pago'
     END AS EstadoPago
   FROM 
     cliente c
   JOIN 
     empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
   LEFT JOIN 
     pago p ON c.codigo_cliente = p.codigo_cliente
   JOIN 
     oficina o ON e.codigo_oficina = o.codigo_oficina
   WHERE
   p.id_transaccion is not null;
   ```

5. Devuelve el nombre de los clientes que **no** hayan hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

   ```sql
   SELECT DISTINCT
     c.nombre_cliente AS Cliente, 
     CONCAT_WS(' ', e.nombre, e.apellido1, e.apellido2) AS representanteVentas,o.ciudad,
     CASE 
       WHEN p.id_transaccion IS NULL THEN 'No hay pago'
       ELSE 'Realizo pago'
     END AS EstadoPago
   FROM 
     cliente c
   JOIN 
     empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
   LEFT JOIN 
     pago p ON c.codigo_cliente = p.codigo_cliente
   JOIN 
     oficina o ON e.codigo_oficina = o.codigo_oficina
   WHERE
   p.id_transaccion is null;
   ```

6. Lista la dirección de las oficinas que tengan clientes en `Fuenlabrada`.

   ```sql
   /* Consulta #1 con cliente, ciudad de cliente y oficina */
   SELECT distinct
   c.nombre_cliente,c.ciudad,
   CONCAT_WS('  ', o.linea_direccion1, o.linea_direccion2) AS Oficina
   FROM 
     cliente c
   JOIN 
     empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
   JOIN 
     oficina o ON e.codigo_oficina = o.codigo_oficina
   WHERE
   c.ciudad = 'Fuenlabrada';
   
   /* Consulta #2 con solo oficinas*/
   
   SELECT distinct
   CONCAT_WS('  ', o.linea_direccion1, o.linea_direccion2) AS Oficina
   FROM 
   cliente c
   JOIN 
   empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
   JOIN 
   oficina o ON e.codigo_oficina = o.codigo_oficina
   WHERE
   c.ciudad = 'Fuenlabrada';
   ```

7. Devuelve el nombre de los clientes y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

   ```sql
   SELECT 
     c.nombre_cliente,
     CONCAT_WS(' ',e.nombre,'',e.apellido1) as nombre_representante,
     o.ciudad as oficina
   FROM cliente c
   JOIN empleado e
   ON c.codigo_empleado_rep_ventas = e.codigo_empleado
   JOIN oficina o
   ON e.codigo_oficina = o.codigo_oficina
   ```

8. Devuelve un listado con el nombre de los empleados junto con el nombre de sus jefes.

   ```sql
   SELECT 
     CONCAT_WS(' ', ec.nombre, ec.apellido1, ec.apellido2) AS nombre_empleado,
     CASE 
       WHEN IFNULL(ej.nombre, '') = '' THEN 'El jefe de jefes '
       ELSE CONCAT_WS(' ', ej.nombre, ej.apellido1, ej.apellido2)
     END AS nombre_jefe
   FROM 
     empleado ec
   LEFT JOIN 
     empleado ej ON ec.codigo_jefe = ej.codigo_empleado;
   ```

9. Devuelve un listado que muestre el nombre de cada empleados, el nombre de su jefe y el nombre del jefe de sus jefe.

   ```sql
   SELECT 
     CONCAT_WS(' ', ec.nombre, ec.apellido1, ec.apellido2) AS nombre_empleado,
     CASE 
       WHEN IFNULL(ej.nombre, '') = '' THEN 'Ninguno'
       ELSE CONCAT_WS(' ', ej.nombre, ej.apellido1, ej.apellido2)
     END AS nombre_jefe , 
     CASE 
       WHEN IFNULL(ejj.nombre, '') = '' THEN 'Ninguno'
       ELSE CONCAT_WS(' ', ejj.nombre, ejj.apellido1, ejj.apellido2)
     END AS nombre_jefe_del_jefe
   FROM 
     empleado ec
   LEFT JOIN 
     empleado ej ON ec.codigo_jefe = ej.codigo_empleado
   LEFT JOIN 
     empleado ejj ON ej.codigo_jefe = ejj.codigo_empleado;
   
   ```

10. Devuelve el nombre de los clientes a los que no se les ha entregado a tiempo un pedido.

    ```sql
    -- Consulta #1
    SELECT c.nombre_cliente, p.fecha_pedido, p.fecha_esperada, p.fecha_entrega, concat(p.fecha_entrega - p.fecha_esperada,' ', 'dias')  AS tiempo_esperado, p.estado, ifnull(p.comentarios, 'Se entrego tarde') as comentarios
    FROM cliente c
    JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
    WHERE p.fecha_entrega > p.fecha_esperada;
    
    -- Consulta #2
    SELECT distinct c.nombre_cliente
    FROM cliente c
    JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
    WHERE p.fecha_entrega > p.fecha_esperada;
    ```

11. Devuelve un listado de las diferentes gamas de producto que ha comprado cada cliente.

    ```sql
    SELECT c.nombre_cliente, GROUP_CONCAT(DISTINCT gp.gama) as gama
    FROM cliente c 
    JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
    JOIN detalle_pedido dp ON p.codigo_pedido = dp.codigo_pedido
    JOIN producto pr ON pr.codigo_producto = dp.codigo_producto
    JOIN gama_producto gp ON gp.gama = pr.gama
    GROUP BY c.nombre_cliente;
    ```



## Consultas multitabla (Composición externa)

1. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.

   ```sql
   SELECT c.codigo_cliente, p.codigo_cliente AS pagos
   FROM cliente c
   LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
   WHERE p.codigo_cliente is null;
   ```

2. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pedido.

   ```sql
   SELECT * FROM cliente c
   LEFT JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
   WHERE p.codigo_cliente IS NULL;
   ```

3. Devuelve un listado que muestre los clientes que no han realizado ningún pago y los que no han realizado ningún pedido.

   ```sql
   SELECT DISTINCT c.nombre_cliente FROM cliente c
   LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
   LEFT JOIN pedido pe ON c.codigo_cliente = pe.codigo_cliente
   WHERE p.codigo_cliente IS NULL OR pe.codigo_cliente IS NULL
   ORDER BY c.nombre_cliente;
   ```

4. Devuelve un listado que muestre solamente los empleados que no tienen una oficina asociada.

   ```sql
   SELECT * FROM empleado e
   LEFT JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
   WHERE o.codigo_oficina IS NULL;
   ```

5. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado.

   ```sql
   SELECT e.nombre FROM empleado e
   LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
   WHERE codigo_empleado_rep_ventas IS NULL
   ORDER BY e.nombre;
   ```

6. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado junto con los datos de la oficina donde trabajan.

   ```sql
     SELECT e.*, o.* FROM empleado e
     LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
     JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
     WHERE codigo_empleado_rep_ventas IS NULL
     ORDER BY e.codigo_empleado;
   ```

7. Devuelve un listado que muestre los empleados que no tienen una oficina asociada y los que no tienen un cliente asociado 

   ```sql
   SELECT e.* FROM empleado e
   LEFT JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
   LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
   WHERE o.codigo_oficina IS NULL OR c.codigo_empleado_rep_ventas IS NULL;
   ```

8. Devuelve un listado de los productos que nunca han aparecido en un pedido.

   ```sql
   SELECT p.codigo_producto, p.nombre FROM producto p
   LEFT JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
   WHERE dp.codigo_producto IS NULL
   ORDER BY p.nombre;
   ```

9. Devuelve un listado de los productos que nunca han aparecido en un pedido. El resultado debe mostrar el nombre, la descripción y la imagen del producto

   ```sql
   SELECT p.codigo_producto, p.nombre, p.descripcion, g.imagen FROM producto p
   LEFT JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
   JOIN gama_producto g ON p.gama = g.gama
   WHERE dp.codigo_producto IS NULL
   ORDER BY p.nombre;
   ```

10. Devuelve las oficinas donde no trabajan ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama Frutales.

    ```sql
    SELECT DISTINCT o.codigo_oficina FROM oficina o
    JOIN empleado e ON o.codigo_oficina = e.codigo_oficina
    JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
    JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    JOIN pedido pe ON c.codigo_cliente = pe.codigo_cliente
    JOIN detalle_pedido dp ON pe.codigo_pedido = dp.codigo_pedido
    JOIN producto pro ON dp.codigo_producto = pro.codigo_producto
    WHERE pro.gama != 'Frutales';
    ```

11. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.

    ```sql
    SELECT DISTINCT c.* FROM cliente c
    LEFT JOIN pedido pe ON c.codigo_cliente = pe.codigo_cliente
    LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    WHERE pe.codigo_cliente IS NOT NULL AND p.codigo_cliente IS NULL;
    ```

12. Devuelve un listado con los datos de los empleados que no tienen clientes asociados y el nombre de su jefe asociado.

    ```sql
    SELECT DISTINCT e.*, jefe.nombre AS jefe FROM empleado e
    LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
    JOIN empleado jefe ON e.codigo_jefe = jefe.codigo_empleado
    WHERE c.codigo_empleado_rep_ventas IS NULL;
    ```

## Consultas de resumen

1. ¿Cuántos empleados hay en la compañía?

   ```sql
   SELECT CONCAT_WS(' ',COUNT(codigo_empleado),'empleados') as cantidad_empleados from empleado
   ```

2. ¿Cuántos clientes tiene cada país?

   ```sql
   SELECT COUNT(codigo_cliente) as cantidad_clientes,pais
   FROM cliente
   GROUP BY pais;
   ```

3. ¿Cuál fue el pago medio en 2009?

   ```sql
   SELECT ROUND(AVG(total), 2) as promedio_2009
   FROM pago
   WHERE fecha_pago BETWEEN '2009-01-01' AND '2009-12-31';
   ```

4. ¿Cuántos pedidos hay en cada estado? Ordena el resultado de forma descendente por el número de pedidos.

   ```sql
   SELECT estado, COUNT(codigo_pedido) as cantidad_pedidos
   FROM pedido
   GROUP BY estado
   ORDER BY cantidad_pedidos DESC;
   ```

5. Calcula el precio de venta del producto más caro y más barato en una misma consulta.

   ```sql
   SELECT MAX(precio_venta) as precio_maximo, MIN(precio_venta) as precio_minimo
   FROM producto;
   ```

6. Calcula el número de clientes que tiene la empresa.

   ```sql
   SELECT CONCAT_WS(' ',COUNT(codigo_cliente),'empleados') as cantidad_empleados from cliente;
   ```

7. ¿Cuántos clientes existen con domicilio en la ciudad de Madrid?

   ```sql
   SELECT CONCAT_WS(' ',COUNT(codigo_cliente),'clientes') as cantidad_clientes, ciudad
   FROM cliente
   WHERE ciudad = 'Madrid';
   ```

8. ¿Calcula cuántos clientes tiene cada una de las ciudades que empiezan por `M`?

   ```sql
   SELECT CONCAT_WS(' ',COUNT(codigo_cliente),'clientes') as cantidad_clientes, ciudad
   FROM cliente
   WHERE ciudad LIKE 'M%'
   GROUP BY ciudad
   ```

9. Devuelve el nombre de los representantes de ventas y el número de clientes al que atiende cada uno.

   ```sql
   SELECT CONCAT_WS(' ',e.nombre,e.apellido1,e.apellido2) as nombre_representante,CONCAT_WS(' ',COUNT(c.codigo_cliente),'Clientes') as cantidad_clientes
   FROM empleado e
   JOIN cliente c
   ON e.codigo_empleado = c.codigo_empleado_rep_ventas
   GROUP BY e.codigo_empleado;
   ```

10. Calcula el número de clientes que no tiene asignado representante de ventas.

    ```sql
    SELECT CONCAT_WS(' ',COUNT(codigo_cliente),'clientes') as cantidad_clientes
    FROM cliente
    WHERE codigo_empleado_rep_ventas IS NULL;
    ```

11. Calcula la fecha del primer y último pago realizado por cada uno de los clientes. El listado deberá mostrar el nombre y los apellidos de cada cliente.

    ```sql
    SELECT CONCAT_WS(' ',c.nombre_contacto,c.apellido_contacto) as nombre_apellidos, MIN(p.fecha_pago) as fecha_primer_pago, MAX(p.fecha_pago) as fecha_ultimo_pago
    FROM cliente c
    JOIN pago p
    ON c.codigo_cliente = p.codigo_cliente
    GROUP BY c.codigo_cliente;
    ```

12. Calcula el número de productos diferentes que hay en cada uno de los pedidos.

    ```sql
    SELECT p.codigo_pedido,CONCAT_WS(' ', COUNT(DISTINCT dp.codigo_producto), 'productos') as cantidad_productos
    FROM pedido p
    JOIN detalle_pedido dp ON p.codigo_pedido = dp.codigo_pedido
    GROUP BY p.codigo_pedido;
    
    ```

13. Calcula la suma de la cantidad total de todos los productos que aparecen en cada uno de los pedidos.

    ```sql
    SELECT p.codigo_pedido,CONCAT_WS(' ', SUM(dp.cantidad), 'unidades') as cantidad_total
    FROM pedido p
    JOIN detalle_pedido dp ON p.codigo_pedido = dp.codigo_pedido
    GROUP BY p.codigo_pedido;
    ```

14. Devuelve un listado de los 20 productos más vendidos y el número total de unidades que se han vendido de cada uno. El listado deberá estar ordenado por el número total de unidades vendidas.

    ```sql
    SELECT 
      p.codigo_producto,p.nombre,
      CONCAT_WS(' ', SUM(dp.cantidad), 'unidades') as cantidad_total
    FROM producto p
    JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
    GROUP BY p.codigo_producto
    ORDER BY SUM(dp.cantidad) DESC
    LIMIT 20;
    ```

15. La facturación que ha tenido la empresa en toda la historia, indicando la base imponible, el IVA y el total facturado. La base imponible se calcula sumando el coste del producto por el número de unidades vendidas de la tabla `detalle_pedido`. El IVA es el 21 % de la base imponible, y el total la suma de los dos campos anteriores.

    ```sql
    SELECT
      CONCAT_WS(' ',ROUND(SUM(dp.cantidad*p.precio_venta),2),'$') as base_imponible,
      CONCAT_WS(' ',ROUND(SUM(dp.cantidad*p.precio_venta)*0.21,2),'$') as IVA,
      CONCAT_WS(' ',ROUND(SUM(dp.cantidad*p.precio_venta)+SUM(dp.cantidad*p.precio_venta)*0.21,2),'$') as total
    FROM producto p
    JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto;
    ```

16. La misma información que en la pregunta anterior, pero agrupada por código de producto.

    ```sql
    SELECT
    p.codigo_producto,p.nombre,
      CONCAT_WS(' ',ROUND(SUM(dp.cantidad*p.precio_venta),2),'$') as base_imponible,
      CONCAT_WS(' ',ROUND(SUM(dp.cantidad*p.precio_venta)*0.21,2),'$') as IVA,
      CONCAT_WS(' ',ROUND(SUM(dp.cantidad*p.precio_venta)+SUM(dp.cantidad*p.precio_venta)*0.21,2),'$') as total
    FROM producto p
    JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
    GROUP BY p.codigo_producto
    ;
    ```

17. La misma información que en la pregunta anterior, pero agrupada por código de producto filtrada por los códigos que empiecen por `OR`.

    ```sql
    SELECT
    p.codigo_producto,p.nombre,
      CONCAT_WS(' ',ROUND(SUM(dp.cantidad*p.precio_venta),2),'$') as base_imponible,
      CONCAT_WS(' ',ROUND(SUM(dp.cantidad*p.precio_venta)*0.21,2),'$') as IVA,
      CONCAT_WS(' ',ROUND(SUM(dp.cantidad*p.precio_venta)+SUM(dp.cantidad*p.precio_venta)*0.21,2),'$') as total
    FROM producto p
    JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
    WHERE p.codigo_producto LIKE 'OR%'
    GROUP BY p.codigo_producto
    ;
    ```

18. Lista las ventas totales de los productos que hayan facturado más de 3000 euros. Se mostrará el nombre, unidades vendidas, total facturado y total facturado con impuestos (21% IVA).

    ```sql
    SELECT 
      p.codigo_producto,p.nombre,
      CONCAT_WS(' ', SUM(dp.cantidad), 'unidades') as unidades_vendidas,
      CONCAT_WS(' ',ROUND(SUM(dp.cantidad*p.precio_venta),2),'$') as total_facturado,
      CONCAT_WS(' ',ROUND(SUM(dp.cantidad*p.precio_venta)*0.21,2),'$') as total_facturado_con_impuestos
    FROM producto p
    JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
    GROUP BY p.codigo_producto
    HAVING total_facturado > 3000
    ORDER BY total_facturado_con_impuestos DESC;
    ```

19. Muestre la suma total de todos los pagos que se realizaron para cada uno de los años que aparecen en la tabla `pagos`.

    ```sql
    SELECT YEAR(fecha_pago) as Año,SUM(total) as total_por_anio
    FROM pago
    GROUP BY YEAR(fecha_pago)
    ORDER BY YEAR(fecha_pago) ASC;
    ```

## Subconsultas

1. Devuelve el nombre del cliente con mayor límite de crédito.

   ```sql
   SELECT nombre_cliente FROM cliente WHERE limite_credito = (
       SELECT MAX(limite_credito) FROM cliente);
   ```

2. Devuelve el nombre del producto que tenga el precio de venta más caro.

   ```sql
   SELECT nombre FROM producto WHERE precio_venta = (SELECT MAX(precio_venta) FROM producto);
   ```

3. Devuelve el nombre del producto del que se han vendido más unidades. (Tenga en cuenta que tendrá que calcular cuál es el número total de unidades que se han vendido de cada producto a partir de los datos de la tabla `detalle_pedido`)

   ```sql
   SELECT nombre FROM producto WHERE codigo_producto = (
       SELECT codigo_producto FROM detalle_pedido
       GROUP BY codigo_producto
       ORDER BY sum(cantidad) DESC LIMIT 1);
   ```

4. Los clientes cuyo límite de crédito sea mayor que los pagos que haya realizado. (Sin utilizar `INNER JOIN`).

   ```sql
   SELECT * FROM cliente c WHERE c.limite_credito > (
       SELECT SUM(total) FROM pago p
       WHERE c.codigo_cliente = p.codigo_cliente);
   ```

5. Devuelve el producto que más unidades tiene en stock.

   ```sql
   SELECT * FROM producto WHERE cantidad_en_stock = (
       SELECT MAX(cantidad_en_stock) FROM producto);
   ```

6. Devuelve el producto que menos unidades tiene en stock.

   ```sql
   SELECT * FROM producto WHERE cantidad_en_stock = (
       SELECT MIN(cantidad_en_stock) FROM producto);
   ```

7. Devuelve el nombre, los apellidos y el email de los empleados que están a cargo de **Alberto Soria**.

   ```sql
   SELECT nombre, apellido1, apellido2, email FROM empleado WHERE codigo_jefe = (
       SELECT codigo_empleado FROM empleado
       WHERE LOWER(CONCAT(nombre,' ', apellido1)) = 'alberto soria');
   ```

## ALL y ANY

1. Devuelve el nombre del cliente con mayor límite de crédito.

   ```sql
   SELECT c.nombre_cliente, c.limite_credito FROM cliente c
   WHERE c.limite_credito >= ALL (
       SELECT limite_credito
       FROM cliente
   );
   ```

2. Devuelve el nombre del producto que tenga el precio de venta más caro.

   ```sql
   SELECT p.nombre FROM producto p WHERE p.precio_venta >= ALL (
       SELECT precio_venta
       FROM producto
   );
   ```

3. Devuelve el producto que menos unidades tiene en stock.

   ```sql
   SELECT * FROM producto p WHERE p.cantidad_en_stock <= ALL (
       SELECT cantidad_en_stock
       FROM producto
   );
   ```

### IN y NOT IN

1. Devuelve el nombre, apellido1 y cargo de los empleados que no representen a ningún cliente.

   ```sql
   SELECT nombre, apellido1, puesto FROM empleado
   WHERE codigo_empleado IN (
       SELECT codigo_empleado FROM empleado e
       LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
       WHERE c.codigo_empleado_rep_ventas IS NULL);
   ```

2. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.

   ```sql
   SELECT * FROM cliente WHERE codigo_cliente IN (
       SELECT c.codigo_cliente FROM cliente c
       LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
       WHERE p.codigo_cliente IS NULL);
   ```

3. Devuelve un listado que muestre solamente los clientes que sí han realizado algún pago.

   ```sql
   SELECT * FROM cliente WHERE codigo_cliente NOT IN (
       SELECT c.codigo_cliente FROM cliente c
       LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
       WHERE p.codigo_cliente IS NULL);
   ```

4. Devuelve un listado de los productos que nunca han aparecido en un pedido.

   ```sql
   SELECT * FROM producto WHERE codigo_producto IN (
       SELECT p.codigo_producto FROM producto p
       LEFT JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
       WHERE dp.codigo_producto IS NULL);
   ```

5. Devuelve el nombre, apellidos, puesto y teléfono de la oficina de aquellos empleados que no sean representante de ventas de ningún cliente.

   ```sql
   SELECT nombre, apellido1, puesto, (
       SELECT o.telefono FROM empleado e
       JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
       WHERE e.codigo_empleado = empleado.codigo_empleado) AS telefono_oficina
   FROM empleado WHERE codigo_empleado IN (
       SELECT codigo_empleado FROM empleado e
       LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
       WHERE c.codigo_empleado_rep_ventas IS NULL);
   ```

6. Devuelve las oficinas donde **no trabajan** ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama `Frutales`.

   ```sql
   SELECT * FROM oficina WHERE codigo_oficina IN (
       SELECT DISTINCT o.codigo_oficina FROM oficina o
       JOIN empleado e ON o.codigo_oficina = e.codigo_oficina
       JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
       JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
       JOIN detalle_pedido dp ON p.codigo_pedido = dp.codigo_pedido
       JOIN producto pro ON dp.codigo_producto = pro.codigo_producto
       WHERE pro.gama != 'Frutales');
   ```

7. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.

   ```sql
   SELECT * FROM cliente WHERE codigo_cliente IN (
       SELECT DISTINCT c.codigo_cliente FROM cliente c
       LEFT JOIN pedido pe ON c.codigo_cliente = pe.codigo_cliente
       LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
       WHERE pe.codigo_cliente IS NOT NULL AND p.codigo_cliente IS NULL);
   ```

## EXISTS y NOT EXISTS

1. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.

   ```sql
   SELECT nombre_cliente
   FROM cliente
   WHERE NOT EXISTS (
       SELECT codigo_cliente
       FROM pago
       WHERE cliente.codigo_cliente = pago.codigo_cliente)
   ```

2. Devuelve un listado que muestre solamente los clientes que sí han realizado algún pago.

   ```sql
   SELECT nombre_cliente
   FROM cliente
   WHERE EXISTS (
       SELECT codigo_cliente
       FROM pago
       WHERE cliente.codigo_cliente = pago.codigo_cliente)
   ```

3. Devuelve un listado de los productos que nunca han aparecido en un pedido.

   ```sql
   SELECT *
   FROM producto
   WHERE NOT EXISTS (
       SELECT *
       FROM detalle_pedido
       WHERE detalle_pedido.codigo_producto = producto.codigo_producto
   )
   ```

4. Devuelve un listado de los productos que han aparecido en un pedido alguna vez.

   ```sql
   SELECT *
   FROM producto
   WHERE EXISTS (
       SELECT *
       FROM detalle_pedido
       WHERE detalle_pedido.codigo_producto = producto.codigo_producto
   )
   ```

## Consultas variadas

1. Devuelve el listado de clientes indicando el nombre del cliente y cuántos pedidos ha realizado. Tenga en cuenta que pueden existir clientes que no han realizado ningún pedido.

   ```sql
   
   ```

2. Devuelve un listado con los nombres de los clientes y el total pagado por cada uno de ellos. Tenga en cuenta que pueden existir clientes que no han realizado ningún pago.

   ```sql
   
   ```

3. Devuelve el nombre de los clientes que hayan hecho pedidos en 2008 ordenados alfabéticamente de menor a mayor.

   ```sql
   
   ```

4. Devuelve el nombre del cliente, el nombre y primer apellido de su representante de ventas y el número de teléfono de la oficina del representante de ventas, de aquellos clientes que no hayan realizado ningún pago.

   ```sql
   
   ```

5. Devuelve el listado de clientes donde aparezca el nombre del cliente, el nombre y primer apellido de su representante de ventas y la ciudad donde está su oficina.

   ```sql
   
   ```

6. Devuelve el nombre, apellidos, puesto y teléfono de la oficina de aquellos empleados que no sean representante de ventas de ningún cliente.

   ```sql
   
   ```

7. Devuelve un listado indicando todas las ciudades donde hay oficinas y el número de empleados que tiene.

   ```sql
   
   ```

   