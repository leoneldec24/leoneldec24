<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChangoMas - Sistema de Gestión de Supermercado</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 50px;
            color: #333;
        }
        h1 {
            text-align: center;
            font-size: 3.5em;
            color: #4CAF50;
            text-shadow: 5px 5px 2px rgba(0, 0, 0, 0.2);
            margin-bottom: 100px;
        }
        .container {
            max-width: 1000px;
            margin: auto;
            background: white;
            border-radius: 50px;
            box-shadow: 0 5px 10px rgba(0, 0, 0, 0.1);
            padding: 15px;
        }
        input[type="text"], input[type="number"] {
            width: calc(50% - 20px);
            padding: 6px;
            margin: 21px 0;
            border: 2.5px solid #4CB050;
            border-radius: 100px;
            transition: border-color 0.3s;
        }
        input[type="text"]:focus, input[type="number"]:focus {
            border-color: #4CAF50;
            outline: none;
        }
        button {
            background-color: #4CAF50;
            color: white;
            padding: 5px 15px;
            border: none;
            border-radius: 50px;
            cursor: pointer;
            transition: background-color 0.10s, transform 0.2s;
            width: 25%; /* Botones ocupan todo el ancho del contenedor */
        }
        button:hover {
            background-color: #45a049;
            transform: scale(1.15);
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin: 50px 0;
        }
        table, th, td {
            border: 10px solid #ddd;
            border-radius: 15px;
            overflow: hidden;
        }
        th, td {
            padding: 8px;
            text-align: left;
        }
        th {
            background-color: #4CAF50;
            color: white;
        }
        tr:hover {
            background-color: #f1f1f1;
        }
        /* Estilo del menú centrado */
        #menu-container {
            display: none;
            position: fixed;
            top: 20.5%;
            left: 60%;
            transform: translate(-49%, -50%);
            background-color: white;
            border: 2px solid #4CAF50;
            border-radius: 30px;
            box-shadow: 0 4px 50px rgba(0, 0, 0, 0.2);
            padding: 5px; /* Reducción del padding */
            z-index: 100;
            width: 350px; /* Ancho del menú */
            text-align: center; /* Centrar texto en el menú */
        }
        #menu-toggle {
            float: right; /* Ubica el botón a la derecha */
            margin-left: 10px;
            padding: 10px 10px; /* Tamaño reducido */
            font-size: 15px; /* Tamaño de fuente reducido */
        }
    </style>
</head>
<body>

<div class="container" id="main">
    <h1>ChangoMas</h1>
    
    <button id="menu-toggle" onclick="toggleMenu()">☰ Menú</button>

    <h2 style="display: inline;">Ventas</h2>
    <div style="clear: both;"></div> <!-- Clearfix para evitar problemas de flotado -->

    <div id="menu-container">
        <button onclick="mostrarSeccion('nuevos_precios')">Nuevos Precios</button>
        <button onclick="mostrarSeccion('agregar_producto')">Agregar Producto</button>
        <button onclick="toggleMenu()">Cerrar Menú</button>
    </div>

    <input type="text" id="productoVenta" placeholder="Nombre del producto">
    <input type="number" id="cantidadVenta" placeholder="Cantidad">
    <button onclick="agregarACompra()">Agregar a Compra</button>
    <button onclick="cerrarVenta()">Cerrar Venta</button>

    <h2>Productos en Compra</h2>
    <table id="tabla-compra">
        <tr>
            <th>Producto</th>
            <th>Cantidad</th>
            <th>Subtotal</th>
        </tr>
    </table>

    <h3>Total: <span id="totalCompra">0</span></h3>

    <h2>Ventas Realizadas</h2>
    <table id="tabla-ventas">
        <tr>
            <th>Producto</th>
            <th>Cantidad</th>
            <th>Total</th>
        </tr>
    </table>
</div>

<div class="container" id="nuevos_precios" style="display:none;">
    <h1>Actualizar Precios</h1>

    <h2>Nombre del Producto</h2>
    <input type="text" id="nombrePrecio" placeholder="Nombre del producto">
    <h2>Nuevo Precio</h2>
    <input type="number" id="nuevoPrecio" placeholder="Nuevo Precio">
    <button onclick="actualizarPrecio()">Actualizar Precio</button>
    <button onclick="volver()">Volver</button>
</div>

<div class="container" id="agregar_producto" style="display:none;">
    <h1>Agregar Producto</h1>

    <h2>Nombre del Producto</h2>
    <input type="text" id="nombre" placeholder="Nombre del producto">
    <h2>Precio</h2>
    <input type="number" id="precio" placeholder="Precio">
    <h2>Cantidad</h2>
    <input type="number" id="cantidad" placeholder="Cantidad">
    <button onclick="agregarProducto()">Agregar Producto</button>
    <button onclick="volver()">Volver</button>

    <h2>Inventario de Productos</h2>
    <table id="tabla-inventario">
        <tr>
            <th>Nombre</th>
            <th>Precio</th>
            <th>Cantidad</th>
        </tr>
    </table>
</div>

<script>
    let inventario = {};
    let ventas = [];
    let compraActual = {};

    function toggleMenu() {
        const menu = document.getElementById('menu-container');
        menu.style.display = (menu.style.display === 'block') ? 'none' : 'block';
    }

    function mostrarSeccion(seccion) {
        document.getElementById('main').style.display = 'none';
        document.getElementById('nuevos_precios').style.display = 'none';
        document.getElementById('agregar_producto').style.display = 'none';
        document.getElementById(seccion).style.display = 'block';
        if (seccion === 'agregar_producto') {
            mostrarInventario();
        }
        // Ocultar el menú después de seleccionar una opción
        document.getElementById('menu-container').style.display = 'none';
    }

    function volver() {
        mostrarSeccion('main');
    }

    function agregarACompra() {
        const nombre = document.getElementById('productoVenta').value.trim().toLowerCase();
        const cantidad = parseInt(document.getElementById('cantidadVenta').value);

        if (inventario[nombre] && inventario[nombre].cantidad >= cantidad) {
            const subtotal = inventario[nombre].precio * cantidad;
            compraActual[nombre] = { cantidad, subtotal };
            inventario[nombre].cantidad -= cantidad;
            mostrarCompra();
            document.getElementById('productoVenta').value = '';
            document.getElementById('cantidadVenta').value = '';
        } else {
            alert('No hay suficiente stock o producto no encontrado.');
        }
    }

    function mostrarCompra() {
        const tablaCompra = document.getElementById('tabla-compra');
        tablaCompra.innerHTML = `<tr><th>Producto</th><th>Cantidad</th><th>Subtotal</th></tr>`;
        let total = 0;
        for (const nombre in compraActual) {
            const item = compraActual[nombre];
            tablaCompra.innerHTML += `<tr><td>${nombre}</td><td>${item.cantidad}</td><td>${item.subtotal}</td></tr>`;
            total += item.subtotal;
        }
        document.getElementById('totalCompra').innerText = total;
    }

    function cerrarVenta() {
        if (Object.keys(compraActual).length > 0) {
            let totalVenta = 0;
            for (const nombre in compraActual) {
                const item = compraActual[nombre];
                ventas.push({ nombre, ...item });
                totalVenta += item.subtotal;
            }
            alert(`Venta cerrada con éxito. Total: $${totalVenta}`);
            compraActual = {};
            mostrarCompra();
            mostrarVentas();
        } else {
            alert('No hay productos en la compra.');
        }
    }

    function mostrarVentas() {
        const tablaVentas = document.getElementById('tabla-ventas');
        tablaVentas.innerHTML = `<tr><th>Producto</th><th>Cantidad</th><th>Total</th></tr>`;
        let totalGeneral = 0;
        for (const venta of ventas) {
            tablaVentas.innerHTML += `<tr><td>${venta.nombre}</td><td>${venta.cantidad}</td><td>${venta.subtotal}</td></tr>`;
            totalGeneral += venta.subtotal;
        }
        document.getElementById('totalCompra').innerText = totalGeneral;
    }

    function agregarProducto() {
        const nombre = document.getElementById('nombre').value.trim().toLowerCase();
        const precio = parseFloat(document.getElementById('precio').value);
        const cantidad = parseInt(document.getElementById('cantidad').value);

        if (!nombre || isNaN(precio) || isNaN(cantidad)) {
            alert('Por favor, completa todos los campos correctamente.');
            return;
        }

        if (!inventario[nombre]) {
            inventario[nombre] = { precio, cantidad };
            alert(`Producto ${nombre} agregado con éxito.`);
        } else {
            inventario[nombre].cantidad += cantidad;
            alert(`Cantidad de ${nombre} actualizada. Total: ${inventario[nombre].cantidad}`);
        }

        mostrarInventario();

        // Limpiar campos
        document.getElementById('nombre').value = '';
        document.getElementById('precio').value = '';
        document.getElementById('cantidad').value = '';
    }

    function mostrarInventario() {
        const tablaInventario = document.getElementById('tabla-inventario');
        tablaInventario.innerHTML = `<tr><th>Nombre</th><th>Precio</th><th>Cantidad</th></tr>`;
        for (const nombre in inventario) {
            const item = inventario[nombre];
            tablaInventario.innerHTML += `<tr><td>${nombre}</td><td>$${item.precio}</td><td>${item.cantidad}</td></tr>`;
        }
    }

    function actualizarPrecio() {
        const nombre = document.getElementById('nombrePrecio').value.trim().toLowerCase();
        const nuevoPrecio = parseFloat(document.getElementById('nuevoPrecio').value);

        if (inventario[nombre]) {
            inventario[nombre].precio = nuevoPrecio;
            alert(`Precio de ${nombre} actualizado a $${nuevoPrecio}`);
            document.getElementById('nombrePrecio').value = '';
            document.getElementById('nuevoPrecio').value = '';
        } else {
            alert('Producto no encontrado.');
        }
    }

    // Inicialmente, muestra la sección principal
    mostrarSeccion('main');
</script>

</body>
</html>
