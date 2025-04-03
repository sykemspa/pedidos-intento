# pedidos-intento
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gestión de Pedidos</title>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore-compat.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
            background-color: #f5f5f5;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        .container {
            width: 90%;
            max-width: 600px;
            padding: 20px;
            background: white;
            border-radius: 10px;
            box-shadow: 2px 2px 10px rgba(0,0,0,0.3);
            margin-bottom: 20px;
        }
        input, button {
            margin: 10px 0;
            padding: 10px;
            width: 100%;
        }
        button {
            background-color: #28a745;
            color: white;
            border: none;
            cursor: pointer;
        }
        button:hover {
            background-color: #218838;
        }
    </style>
</head>
<body>
    <div class="container" id="authContainer">
        <h2>Acceso</h2>
        <input type="email" id="email" placeholder="Correo electrónico">
        <input type="password" id="password" placeholder="Contraseña">
        <button onclick="registrarUsuario()">Registrarse</button>
        <button onclick="iniciarSesion()">Iniciar Sesión</button>
    </div>
    
    <div class="container" id="appContainer" style="display:none;">
        <h2>Gestión de Pedidos</h2>
        <button onclick="cerrarSesion()">Cerrar Sesión</button>
        <h3>Nuevo Pedido</h3>
        <input type="text" id="cliente" placeholder="Nombre del Cliente">
        <input type="text" id="descripcion" placeholder="Descripción del Pedido">
        <button onclick="agregarPedido()">Agregar Pedido</button>
        <h3>Pedidos Registrados</h3>
        <div id="listaPedidos"></div>
    </div>
    
    <script>
       const firebaseConfig = {
    apiKey: "AIzaSyDxxxxxxxxxxxxxxxxxx",
    authDomain: "tu-proyecto.firebaseapp.com",
    projectId: "tu-proyecto",
    storageBucket: "tu-proyecto.appspot.com",
    messagingSenderId: "123456789012",
    appId: "1:123456789012:web:abcdef123456"
};

        firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();
        const db = firebase.firestore();

        function registrarUsuario() {
            let email = document.getElementById("email").value;
            let password = document.getElementById("password").value;
            auth.createUserWithEmailAndPassword(email, password)
                .then(userCredential => alert("Usuario registrado con éxito"))
                .catch(error => alert(error.message));
        }

        function iniciarSesion() {
            let email = document.getElementById("email").value;
            let password = document.getElementById("password").value;
            auth.signInWithEmailAndPassword(email, password)
                .then(userCredential => {
                    document.getElementById("authContainer").style.display = "none";
                    document.getElementById("appContainer").style.display = "block";
                    cargarPedidos();
                })
                .catch(error => alert(error.message));
        }

        function cerrarSesion() {
            auth.signOut().then(() => {
                document.getElementById("authContainer").style.display = "block";
                document.getElementById("appContainer").style.display = "none";
            });
        }

        function agregarPedido() {
            let user = auth.currentUser;
            if (!user) return alert("Inicia sesión primero");
            
            let pedido = {
                cliente: document.getElementById("cliente").value,
                descripcion: document.getElementById("descripcion").value,
                usuario: user.uid
            };
            db.collection("pedidos").add(pedido).then(() => {
                cargarPedidos();
            });
        }

        function cargarPedidos() {
            let user = auth.currentUser;
            if (!user) return;
            
            db.collection("pedidos").where("usuario", "==", user.uid)
                .onSnapshot(snapshot => {
                    let lista = document.getElementById("listaPedidos");
                    lista.innerHTML = "";
                    snapshot.forEach(doc => {
                        let pedido = doc.data();
                        let div = document.createElement("div");
                        div.textContent = `${pedido.cliente}: ${pedido.descripcion}`;
                        lista.appendChild(div);
                    });
                });
        }
    </script>
</body>
</html>
