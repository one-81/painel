<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Painel Admin - PlayDigital</title>
    <style>
        body { font-family: Arial, sans-serif; background-color: #0b0c10; display: flex; justify-content: center; align-items: center; min-height: 100vh; margin: 0; color: #fff; flex-direction: column; }
        .container { background: #1f2833; padding: 30px; border-radius: 12px; box-shadow: 0 10px 40px rgba(255, 0, 85, 0.15); text-align: center; max-width: 400px; width: 90%; border: 1px solid #ff0055; }
        h2 { color: #ff0055; margin-bottom: 10px; text-transform: uppercase; letter-spacing: 2px; }
        p { color: #c5c6c7; font-size: 14px; margin-bottom: 20px; }
        input { width: 90%; padding: 14px; margin: 10px 0; border: 2px solid #ff0055; border-radius: 8px; font-size: 18px; background: #0b0c10; color: #fff; outline: none; text-align: center; font-weight: bold; }
        input:focus { border-color: #ff0055; box-shadow: 0 0 10px rgba(255, 0, 85, 0.5); }
        .btn-gerar { background: #ff0055; color: #fff; border: none; padding: 16px 20px; font-size: 18px; font-weight: bold; border-radius: 8px; cursor: pointer; width: 100%; transition: 0.3s; margin-top: 15px; text-transform: uppercase; }
        .btn-gerar:hover { background: #ff3377; box-shadow: 0 0 15px rgba(255, 0, 85, 0.8); }
        .message { margin-top: 15px; font-weight: bold; color: #ff4c4c; }
        .chave-nova { display: none; background: #0b0c10; border: 2px dashed #4cff61; padding: 15px; margin-top: 20px; font-size: 32px; color: #4cff61; letter-spacing: 5px; font-weight: bold; border-radius: 8px; }
    </style>
</head>
<body>
    <div class="container">
        <h2>🔒 Painel Admin</h2>
        <p>Acesso restrito. Digite a senha mestra para gerar novas chaves automáticas.</p>
        
        <input type="password" id="senhaInput" placeholder="Sua Senha Mestra">
        <button class="btn-gerar" id="btnGerar" onclick="gerarNovaChave()">Gerar Nova Chave Vip</button>
        
        <p class="message" id="msg"></p>
        
        <div class="chave-nova" id="chaveNovaContainer"></div>
    </div>

    <script>
        // SUA URL DO GOOGLE (Já deixei certinha)
        const URL_SERVIDOR = "https://script.google.com/macros/s/AKfycbzYLDcDpNyk3pkprI5uMSLgIzYowwia-n5NoEQsoA-KoSleUJe2WUhWrCnKifYb8JIg/exec"; 

        async function gerarNovaChave() {
            const senha = document.getElementById("senhaInput").value.trim();
            const msg = document.getElementById("msg");
            const btn = document.getElementById("btnGerar");
            const chaveNovaContainer = document.getElementById("chaveNovaContainer");

            if(!senha) {
                msg.textContent = "Digite a senha mestra.";
                return;
            }

            msg.textContent = "Lançando chave na planilha...";
            msg.style.color = "#c5c6c7";
            chaveNovaContainer.style.display = "none";
            btn.disabled = true;

            try {
                // Chama o comando "gerar" passando a senha
                const response = await fetch(URL_SERVIDOR + "?acao=gerar&senha=" + encodeURIComponent(senha), { redirect: 'follow' });
                const data = await response.json();

                if(data.status === "sucesso") {
                    msg.textContent = "Chave criada e adicionada como LIVRE na planilha!";
                    msg.style.color = "#c5c6c7";
                    
                    chaveNovaContainer.textContent = data.chave;
                    chaveNovaContainer.style.display = "block";
                    
                    // Copia pro teclado do seu celular/PC automático
                    navigator.clipboard.writeText(data.chave);
                    alert("A chave " + data.chave + " foi copiada! Só colar no WhatsApp do cliente.");
                } else {
                    msg.textContent = data.mensagem;
                    msg.style.color = "#ff4c4c";
                }
            } catch(e) {
                msg.textContent = "Erro de conexão.";
                msg.style.color = "#ff4c4c";
            }
            
            btn.disabled = false;
        }
    </script>
</body>
</html>
