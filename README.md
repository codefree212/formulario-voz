<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Formulario Secuencial por Voz y Manual</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f0f4f8;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-image: url('https://example.com/fondo-suave.jpg');
            background-size: cover;
            background-position: center;
        }

        .formulario-container {
            background-color: #ffffff;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            max-width: 400px;
            width: 100%;
            text-align: center;
        }

        h1 {
            color: #2c3e50;
            margin-bottom: 20px;
        }

        form {
            margin: 0;
        }

        label {
            color: #34495e;
            font-weight: bold;
            text-align: left;
            display: block;
            margin-top: 10px;
        }

        input, textarea {
            width: 100%;
            padding: 10px;
            margin-top: 5px;
            border: 1px solid #ccc;
            border-radius: 5px;
            box-sizing: border-box;
        }

        input[type="submit"], button {
            background-color: #3498db;
            color: #fff;
            padding: 10px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
            width: 100%;
            margin-top: 20px;
            transition: background-color 0.3s ease;
        }

        input[type="submit"]:hover, button:hover {
            background-color: #2980b9;
        }

        button {
            margin-bottom: 10px;
        }
    </style>
</head>
<body>
    <div class="formulario-container">
        <h1>Formulario por Voz o Manual</h1>

        <form id="formulario" onsubmit="enviarFormulario(event)">
            <label for="nombre">Nombre:</label>
            <input type="text" id="nombre" name="nombre">

            <label for="email">Correo Electrónico:</label>
            <input type="email" id="email" name="email">

            <label for="telefono">Teléfono:</label>
            <input type="tel" id="telefono" name="telefono">

            <label for="direccion">Dirección:</label>
            <input type="text" id="direccion" name="direccion">

            <label for="precio">Precio:</label>
            <input type="number" id="precio" name="precio">

            <label for="mensaje">Mensaje:</label>
            <textarea id="mensaje" name="mensaje"></textarea>

            <button type="button" onclick="iniciarFormulario()">Iniciar Formulario por Voz</button>
            <input type="submit" value="Enviar" id="enviar">
        </form>
    </div>

    <script>
        // Lista de los campos del formulario
        const campos = ['nombre', 'email', 'telefono', 'direccion', 'precio', 'mensaje'];
        let indiceActual = 0;

        // Función para enviar los datos a Google Sheets
        function enviarFormulario(event) {
            event.preventDefault();  // Evita que se envíe el formulario de manera predeterminada

            const formData = new FormData(document.getElementById('formulario'));

            fetch('https://script.google.com/macros/s/AKfycbzag9XZ18lmEBljC0r9nk0SJSIs4WtlndbwZ85fhFvl8ZD-Nu8EV1YootlXVyvoNki06A/exec', {  // Reemplaza con la URL de tu script
                method: 'POST',
                body: formData
            }).then(response => {
                if (response.ok) {
                    alert('Datos enviados correctamente a Google Sheets');
                } else {
                    alert('Hubo un error al enviar los datos');
                }
            }).catch(error => {
                alert('Error de conexión: ' + error);
            });
        }

        // Función para iniciar el proceso del formulario por voz
        function iniciarFormulario() {
            pedirPorVoz(indiceActual);
        }

        function hablar(texto, callback) {
            const mensaje = new SpeechSynthesisUtterance(texto);
            mensaje.lang = 'es-ES';
            mensaje.onend = function() {
                callback();
            };
            window.speechSynthesis.speak(mensaje);
        }

        function pedirPorVoz(indice) {
            const campoID = campos[indice];
            const campo = document.getElementById(campoID);
            hablar('Por favor, dígame su ' + campoID, function() {
                grabarVoz(campoID, function() {
                    indiceActual++;
                    if (indiceActual < campos.length) {
                        pedirPorVoz(indiceActual);
                    } else {
                        alert('Formulario completo');
                    }
                });
            });
        }

        function grabarVoz(campoID, callback) {
            const campo = document.getElementById(campoID);
            if (!('webkitSpeechRecognition' in window)) {
                alert('Lo siento, tu navegador no soporta reconocimiento de voz.');
                return;
            }
            const reconocimiento = new webkitSpeechRecognition();
            reconocimiento.lang = 'es-ES';
            reconocimiento.onresult = function(event) {
                campo.value = event.results[0][0].transcript;
                callback();
            };
            reconocimiento.start();
        }
    </script>
</body>
</html>
