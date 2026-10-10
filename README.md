<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Painel de Revenda</title>
    <style>
        body { font-family: Arial, sans-serif; background-color: #0b0c10; display: flex; justify-content: center; align-items: center; min-height: 100vh; margin: 0; color: #fff; flex-direction: column; }
        .container { background: #1f2833; padding: 30px; border-radius: 12px; box-shadow: 0 10px 40px rgba(0, 255, 204, 0.15); text-align: center; max-width: 400px; width: 90%; border: 1px solid #45a29e; }
        h2 { color: #66fcf1; margin-bottom: 5px; text-transform: uppercase; letter-spacing: 2px; }
        p { color: #c5c6c7; font-size: 14px; margin-bottom: 20px; }
        input { width: 90%; padding: 14px; margin: 10px 0; border: 2px solid #45a29e; border-radius: 8px; font-size: 18px; background: #0b0c10; color: #fff; outline: none; text-align: center; font-weight: bold; }
        input:focus { border-color: #66fcf1; box-shadow: 0 0 10px rgba(102, 252, 241, 0.5); }
        .btn-acao { background: #45a29e; color: #0b0c10; border: none; padding: 16px 20px; font-size: 18px; font-weight: bold; border-radius: 8px; cursor: pointer; width: 100%; transition: 0.3s; margin-top: 15px; text-transform: uppercase; }
        .btn-acao:hover { background: #66fcf1; box-shadow: 0 0 15px rgba(102, 252, 241, 0.8); }
        .message { margin-top: 15px; font-weight: bold; color: #ff4c4c; }
        .chave-nova { display: none; background: #0b0c10; border: 2px dashed #66fcf1; padding: 15px; margin-top: 20px; font-size: 32px; color: #66fcf1; letter-spacing: 5px; font-weight: bold; border-radius: 8px; }
        
        /* Sistema de Abas Visuais */
        #telaLogin { display: block; }
        #telaPainel { display: none; }
        
        .saldo-box { background: #0b0c10; border: 1px solid #45a29e; padding: 15px; border-radius: 8px; margin-bottom: 20px; }
        .saldo-valor { display: block; font-size: 36px; color: #66fcf1; font-weight: bold; margin-top: 5px; }
    </style>
</head>
<body>
    <!-- TELA DE LOGIN DO REVENDEDOR -->
    <div class="container" id="telaLogin">
        <h2>🔒 Área Restrita</h2>
        <p>Acesse com sua senha de Vendedor.</p>
        
        <input type="password" id="senhaInput" placeholder="Sua Senha">
        <button class="btn-acao" onclick="fazerLogin()">Entrar no Painel</button>
        
        <p class="message" id="msgLogin"></p>
    </div>

    <!-- TELA DE DENTRO DO PAINEL -->
    <div class="container" id="telaPainel">
        <h2 id="nomeUsuario">Olá, Vendedor</h2>
        
        <div class="saldo-box">
            <span style="color: #c5c6c7; font-size: 12px; text-transform: uppercase;">Seus Créditos Atuais</span>
            <span class="saldo-valor" id="saldoCreditos">0</span>
        </div>

        <button class="btn-acao" id="btnGerar" onclick="gerarChaveRevenda()">Gerar Chave Vip</button>
        
        <p class="message" id="msgPainel"></p>
        <div class="chave-nova" id="chaveNovaContainer"></div>
    </div>

    <script>
        // A SUA URL NOVA JÁ ESTÁ AQUI:
        const URL_SERVIDOR = "https://script.google.com/macros/s/AKfycbyd5YHL5kbbnldswLaFu5rr97eACsZduAJJMepOBC7MU53j-c8d6eVZeUFKNdqA8aeK/exec"; 
        
        let senhaLogada = "";

        // FUNÇÃO DE ENTRAR NO SITE
        async function fazerLogin() {
            const senha = document.getElementById("senhaInput").value.trim();
            const msg = document.getElementById("msgLogin");

            if(!senha) {
                msg.textContent = "Digite sua senha."; return;
            }

            msg.textContent = "Verificando...";
            msg.style.color = "#c5c6c7";

            try {
                const response = await fetch(URL_SERVIDOR + "?acao=login&senha=" + encodeURIComponent(senha), { redirect: 'follow' });
                const data = await response.json();

                if(data.status === "sucesso") {
                    senhaLogada = senha;
                    document.getElementById("telaLogin").style.display = "none";
                    document.getElementById("telaPainel").style.display = "block";
                    
                    document.getElementById("nomeUsuario").textContent = "Olá, " + data.nome;
                    document.getElementById("saldoCreditos").textContent = data.creditos;
                    
                    if(data.creditos <= 0) {
                        document.getElementById("btnGerar").style.background = "#ff4c4c";
                        document.getElementById("btnGerar").textContent = "Créditos Esgotados";
                    }
                } else {
                    msg.textContent = data.mensagem;
                    msg.style.color = "#ff4c4c";
                }
            } catch(e) {
                msg.textContent = "Erro ao conectar. Tente novamente.";
                msg.style.color = "#ff4c4c";
            }
        }

        // FUNÇÃO DE GERAR CHAVE LÁ DENTRO
        async function gerarChaveRevenda() {
            const msg = document.getElementById("msgPainel");
            const btn = document.getElementById("btnGerar");
            const chaveNovaContainer = document.getElementById("chaveNovaContainer");

            msg.textContent = "Criando chave na planilha...";
            msg.style.color = "#c5c6c7";
            chaveNovaContainer.style.display = "none";
            btn.disabled = true;

            try {
                const response = await fetch(URL_SERVIDOR + "?acao=gerar_revenda&senha=" + encodeURIComponent(senhaLogada), { redirect: 'follow' });
                const data = await response.json();

                if(data.status === "sucesso") {
                    msg.textContent = "Chave criada com sucesso e copiada!";
                    msg.style.color = "#c5c6c7";
                    
                    chaveNovaContainer.textContent = data.chave;
                    chaveNovaContainer.style.display = "block";
                    document.getElementById("saldoCreditos").textContent = data.creditos_restantes;
                    
                    if(data.creditos_restantes <= 0) {
                        btn.style.background = "#ff4c4c";
                        btn.textContent = "Créditos Esgotados";
                    }

                    navigator.clipboard.writeText(data.chave);
                } else {
                    msg.textContent = data.mensagem;
                    msg.style.color = "#ff4c4c";
                }
            } catch(e) {
                msg.textContent = "Erro ao conectar.";
                msg.style.color = "#ff4c4c";
            }
            
            btn.disabled = false;
        }
    </script>
</body>
</html>
