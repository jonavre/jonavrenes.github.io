<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculadora de Carga de Trabajo</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; display: flex; }
        .contenido { flex: 1; }
        .imagen { 
            width: 180px; /* Tamaño reducido */
            height: 150px; /* Mantiene la proporción */
            margin-top: -20px; /* Mueve la imagen hacia arriba */
            margin-left: 20px; /* Espacio a la izquierda */
        }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        th, td { border: 1px solid black; padding: 8px; text-align: left; }
        th { background-color: #f2f2f2; }
        input, select { margin: 5px; padding: 5px; }
        button { margin: 10px 0; padding: 8px; cursor: pointer; }
    </style>
</head>
<body>
    <div class="contenido">
        <h2>Calculadora de Carga de Trabajo</h2>
        <label>Tiempo total laborable anual (horas): </label>
        <input type="number" id="totalLaborable" value="1885" readonly>
        <br><br>
        
        <h3>Agregar Actividad</h3>
        <input type="text" id="actividad" placeholder="Nombre de la actividad">
        <input type="number" id="frecuencia" placeholder="Frecuencia anual" oninput="validarNumero(this)">
        <input type="number" id="tiempo" placeholder="Tiempo unitario" oninput="validarNumero(this)">
        <select id="unidad">
            <option value="Minutos">Minutos</option>
            <option value="Horas">Horas</option>
        </select>
        <button onclick="agregarActividad()">Agregar</button>
        
        <table>
            <thead>
                <tr>
                    <th>Actividad</th>
                    <th>Frecuencia</th>
                    <th>Tiempo</th>
                    <th>Unidad</th>
                    <th>Tiempo Total (horas)</th>
                    <th>Eliminar</th>
                </tr>
            </thead>
            <tbody id="tablaActividades"></tbody>
        </table>
        <br>
        <h3>Carga de Trabajo: <span id="cargaTrabajo">0</span>%</h3>
        <h3>Tiempo trabajado: <span id="tiempoTrabajado">0</span> horas</h3>
        <h3>Tiempo restante: <span id="tiempoRestante">1885</span> horas</h3>
        <h3 id="mensaje" style="color: green; display: none;">🎉 ¡Felicidades! Has alcanzado el tiempo total laborable anual. 🎉</h3>
    </div>
    <img class="imagen" src="imagenes/image001.png" alt="Logo de la empresa"> <!-- Aquí va la imagen de la empresa -->
    
    <script>
        let actividades = [];
        
        function validarNumero(input) {
            if (input.value < 0) {
                input.value = '';
                alert("Solo se permiten números positivos.");
            }
        }
        
        function agregarActividad() {
            const actividad = document.getElementById("actividad").value.trim();
            const frecuencia = parseFloat(document.getElementById("frecuencia").value);
            const tiempo = parseFloat(document.getElementById("tiempo").value);
            const unidad = document.getElementById("unidad").value;
            
            if (!actividad || isNaN(frecuencia) || isNaN(tiempo) || frecuencia <= 0 || tiempo <= 0) {
                alert("Por favor, complete todos los campos correctamente con valores numéricos.");
                return;
            }
            
            if (actividades.some(act => act.actividad.toLowerCase() === actividad.toLowerCase())) {
                alert("Esta actividad ya ha sido agregada.");
                return;
            }
            
            actividades.push({ actividad, frecuencia, tiempo, unidad });
            actualizarTabla();
        }
        
        function eliminarActividad(index) {
            actividades.splice(index, 1);
            actualizarTabla();
        }
        
        function actualizarTabla() {
            const tabla = document.getElementById("tablaActividades");
            tabla.innerHTML = "";
            let totalHorasTrabajadas = 0;
            
            actividades.forEach((act, index) => {
                let tiempoHoras = act.unidad === "Minutos" ? act.tiempo / 60 : act.tiempo;
                let totalActividad = tiempoHoras * act.frecuencia;
                totalHorasTrabajadas += totalActividad;
                
                const row = document.createElement("tr");
                row.innerHTML = `<td>${act.actividad}</td>
                                <td>${act.frecuencia}</td>
                                <td>${act.tiempo}</td>
                                <td>${act.unidad}</td>
                                <td>${totalActividad.toFixed(2)}</td>
                                <td><button onclick="eliminarActividad(${index})">❌</button></td>`;
                tabla.appendChild(row);
            });
            
            const totalLaborable = parseFloat(document.getElementById("totalLaborable").value);
            const cargaTrabajo = (totalHorasTrabajadas / totalLaborable) * 100;
            const tiempoRestante = totalLaborable - totalHorasTrabajadas;
            
            document.getElementById("cargaTrabajo").innerText = cargaTrabajo.toFixed(2);
            document.getElementById("tiempoTrabajado").innerText = totalHorasTrabajadas.toFixed(2);
            document.getElementById("tiempoRestante").innerText = tiempoRestante.toFixed(2);
            
            if (totalHorasTrabajadas >= totalLaborable) {
                document.getElementById("mensaje").style.display = "block";
            } else {
                document.getElementById("mensaje").style.display = "none";
            }
        }
    </script>
</body>
</html>
