# CONSULTAS BASE DE DATOS JARDINERIA

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
SELECT DISTINCT
  c.nombre_cliente AS Cliente, 
  CONCAT_WS(' ', e.nombre, e.apellido1, e.apellido2) AS representanteVentas,o.ciudad
FROM 
  cliente c
JOIN 
  empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
JOIN 
  oficina o ON e.codigo_oficina = o.codigo_oficina
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

```

```

2. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pedido.

```

```

3. Devuelve un listado que muestre los clientes que no han realizado ningún pago y los que no han realizado ningún pedido.

```

```

4. Devuelve un listado que muestre solamente los empleados que no tienen una oficina asociada.

```

```

5. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado.

```

```

6. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado junto con los datos de la oficina donde trabajan.

```

```

7. Devuelve un listado que muestre los empleados que no tienen una oficina asociada y los que no tienen un cliente asociado.

```

```

8. Devuelve un listado de los productos que nunca han aparecido en un pedido.

```

```

9. Devuelve un listado de los productos que nunca han aparecido en un pedido. El resultado debe mostrar el nombre, la descripción y la imagen del producto.

```

```

10. Devuelve las oficinas donde **no trabajan** ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama `Frutales`.

```

```

11. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.

```

```

12. Devuelve un listado con los datos de los empleados que no tienen clientes asociados y el nombre de su jefe asociado.

```

```

