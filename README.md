<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Terminal Simulado</title>
    <style>
        body {
            font-family: monospace;
            background-color: black;
            color: #0f0;
            margin: 0;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        #terminal {
            width: 100%;
            max-width: 800px;
            height: 400px;
            background-color: black;
            border: 2px solid #0f0;
            padding: 10px;
            overflow-y: auto;
            white-space: pre-wrap;
        }
        .line {
            display: flex;
            align-items: center;
        }
        #input-line {
            color: #0f0;
        }
        #cursor {
            display: inline-block;
            width: 10px;
            height: 16px;
            background-color: #0f0;
            margin-left: 5px;
            animation: blink 0.7s infinite;
        }
        @keyframes blink {
            50% { opacity: 0; }
        }
        #hidden-input {
            position: absolute;
            opacity: 0;
        }
    </style>
</head>
<body>

    <div id="terminal" onclick="focusOnInput()">
        <p>Bem-vindo ao Terminal Simulado! Digite "help" para ver os comandos.</p>
        <div class="line">
            <span id="input-line">user@terminal:~$</span>
            <span id="user-input"></span>
            <span id="cursor"></span>
        </div>
    </div>

    <input type="text" id="hidden-input" autofocus oninput="updateInput()"/>

    <script>
        const terminal = document.getElementById("terminal");
        const hiddenInput = document.getElementById("hidden-input");
        const userInput = document.getElementById("user-input");
        let commandHistory = [];
        let historyIndex = 0;
        let userName = "user";

        function focusOnInput() {
            hiddenInput.focus();
        }

        function printToTerminal(text, speed = 20) {
            let newLine = document.createElement("p");
            terminal.appendChild(newLine);
            terminal.scrollTop = terminal.scrollHeight;

            let i = 0;
            function type() {
                if (i < text.length) {
                    newLine.innerText += text[i];
                    i++;
                    setTimeout(type, speed);
                }
            }
            type();
        }

        function processCommand(command) {
            if (command.trim() === "") return;
            printToTerminal(userName + "@terminal:~$ " + command, 5);
            commandHistory.push(command);
            historyIndex = commandHistory.length;

            let args = command.split(" ");
            let cmd = args.shift().toLowerCase();

            switch (cmd) {
                case "help":
                    printToTerminal("Comandos disponíveis:\n- help: Mostra esta lista\n- clear: Limpa a tela\n- echo [texto]: Repete o texto digitado\n- about: Sobre este terminal\n- date: Mostra a data atual\n- random: Gera um número aleatório\n- exit: Fecha o terminal\n- whoami: Mostra o nome do usuário\n- color [red/blue/green]: Muda a cor do terminal\n- banner: Exibe um banner estilizado");
                    break;
                case "clear":
                    terminal.innerHTML = "";
                    break;
                case "about":
                    printToTerminal("Terminal Simulado v4.0\nCriado por hehepaulo12");
                    break;
                case "date":
                    printToTerminal("Data atual: " + new Date().toLocaleString());
                    break;
                case "random":
                    printToTerminal("Número aleatório: " + Math.floor(Math.random() * 100));
                    break;
                case "whoami":
                    printToTerminal("Usuário: " + userName);
                    break;
                case "exit":
                    printToTerminal("Encerrando terminal...");
                    setTimeout(() => window.close(), 1000);
                    break;
                case "echo":
                    printToTerminal(args.join(" "));
                    break;
                case "color":
                    changeColor(args[0]);
                    break;
                case "banner":
                    printToTerminal("████████████████████████████████\n█   BEM-VINDO AO TERMINAL    █\n████████████████████████████████");
                    break;
                default:
                    printToTerminal("Comando não reconhecido. Digite 'help' para ver os comandos.");
            }
        }

        function changeColor(color) {
            let colors = {
                red: "#f00",
                blue: "#00f",
                green: "#0f0"
            };
            if (colors[color]) {
                document.body.style.color = colors[color];
                document.getElementById("cursor").style.backgroundColor = colors[color];
                document.getElementById("terminal").style.borderColor = colors[color];
                printToTerminal("Cor do terminal alterada para " + color);
            } else {
                printToTerminal("Cor inválida. Use 'color red', 'color blue' ou 'color green'.");
            }
        }

        function updateInput() {
            userInput.innerText = hiddenInput.value;
        }

        document.addEventListener("keydown", function(event) {
            if (event.key === "Enter") {
                event.preventDefault();
                let command = hiddenInput.value.trim();
                hiddenInput.value = "";
                userInput.innerText = "";
                processCommand(command);
            } else if (event.key === "ArrowUp") {
                if (historyIndex > 0) {
                    historyIndex--;
                    hiddenInput.value = commandHistory[historyIndex];
                    updateInput();
                }
            } else if (event.key === "ArrowDown") {
                if (historyIndex < commandHistory.length - 1) {
                    historyIndex++;
                    hiddenInput.value = commandHistory[historyIndex];
                } else {
                    hiddenInput.value = "";
                }
                updateInput();
            }
        });

        focusOnInput();
    </script>

</body>
</html>
