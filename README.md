<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Login - Área de Downloads</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="login-container">
        <h2>🔐 Área Restrita</h2>
        <p>Por favor, faça login para acessar os arquivos.</p>

        <form id="loginForm">
            <input type="text" id="username" placeholder="Usuário" required>
            <input type="password" id="password" placeholder="Senha" required>
            <button type="submit">Entrar</button>
        </form>

        <p id="errorMessage" class="error"></p>
    </div>

    <script src="script.js"></script>
</body>
</html><!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>📥 Área de Downloads</title>
    <link rel="stylesheet" href="style.css">
    <style>
        .nome-usuario {
            user-select: none;
            -webkit-user-select: none;
            cursor: default;
        }
    </style>
</head>
<body>
    <div class="dashboard-container">
        <h2>📁 Bem-vindo, <span class="nome-usuario">Administrador</span>!</h2>
        <p style="margin:10px 0; color:#555;">📁 <strong>Arquivos registrados:</strong> <span id="contagemArquivos">0</span></p>

        <a href="arquivos/seu-arquivo.pdf" class="download-btn" download>⬇️ Baixar Arquivo Principal</a>

        <div id="area-publicacao"></div>

        <a href="lista-publica.html" class="download-btn" style="background:#ffc107; margin:10px 0;">🌍 Lista Pública de Arquivos</a>
        <br><br>
        <button id="logoutBtn">Sair</button>
    </div>

    <script>
        if (!sessionStorage.getItem('isLoggedIn')) {
            window.location.href = 'index.html';
        }

        const usuarioLogado = sessionStorage.getItem('usuarioLogado');
        const areaPublicacao = document.getElementById('area-publicacao');

        if (usuarioLogado === 'vitor202') {
            const botaoPublicar = document.createElement('a');
            botaoPublicar.href = 'publicar.html';
            botaoPublicar.className = 'download-btn';
            botaoPublicar.textContent = '📤 Publicar Novo Arquivo';
            botaoPublicar.style.background = '#28a745';
            botaoPublicar.style.marginTop = '20px';
            botaoPublicar.style.display = 'inline-block';
            areaPublicacao.appendChild(botaoPublicar);
        }

        document.getElementById('logoutBtn').addEventListener('click', function() {
            sessionStorage.removeItem('isLoggedIn');
            sessionStorage.removeItem('usuarioLogado');
            window.location.href = 'index.html';
        });

        function atualizarContagem() {
            const contagem = document.getElementById('contagemArquivos');
            let arquivos = JSON.parse(localStorage.getItem('arquivosPublicados') || '[]');
            contagem.textContent = arquivos.length;
        }
        atualizarContagem();

        document.addEventListener('contextmenu', event => event.preventDefault());
        document.addEventListener('keydown', function(e) {
            if (e.key === "F12" || (e.ctrlKey && e.shiftKey && e.key === "I") || (e.ctrlKey && e.shiftKey && e.key === "J") || (e.ctrlKey && e.key === "U")) {
                alert("⚠️ Acesso restrito. Esta ação não é permitida.");
                e.preventDefault();
            }
        });
    </script>
</body>
</html><!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>📤 Publicar Novo Arquivo</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="dashboard-container">
        <h2>📤 Registrar Novo Arquivo</h2>
        <p>Registre arquivos para lançamento imediato ou agendado.</p>

        <form id="formPublicar" style="text-align: left; max-width: 400px; margin: 30px auto; font-size: 14px;">
            <label>Nome do Arquivo (ex: jogo.zip)</label><br>
            <input type="text" id="nomeArquivo" placeholder="ex: manual.pdf" required style="width:100%; padding:10px; margin:5px 0;"><br>

            <label>Tamanho (ex: 15MB, 2.5GB)</label><br>
            <input type="text" id="tamanhoArquivo" placeholder="ex: 50MB" required style="width:100%; padding:10px; margin:5px 0;"><br>

            <label>Data de Lançamento</label><br>
            <input type="datetime-local" id="dataLancamento" required style="width:100%; padding:10px; margin:5px 0;"><br>

            <label>Descrição (opcional)</label><br>
            <input type="text" id="descricaoArquivo" placeholder="Versão final, atualizado hoje" style="width:100%; padding:10px; margin:5px 0;"><br>

            <button type="submit" style="width:100%; padding:12px; background:#28a745; color:white; border:none; border-radius:6px; cursor:pointer; margin-top:10px;">
                ✅ Registrar Arquivo
            </button>
        </form>

        <a href="gerenciar.html" class="download-btn" style="background:#17a2b8; margin:10px 0;">📋 Gerenciar Arquivos</a>
        <a href="lista-publica.html" class="download-btn" style="background:#ffc107; margin:10px 0;">🌍 Ver Lista Pública</a>
        <a href="dashboard.html" class="download-btn" style="background:#6c757d;">⬅️ Voltar</a>

        <div id="mensagem" style="margin-top:20px; color: green; font-weight:bold;"></div>
    </div>

    <script>
        if (!sessionStorage.getItem('isLoggedIn') || sessionStorage.getItem('usuarioLogado') !== 'vitor202') {
            alert("⛔ Acesso negado! Apenas vitor202 pode acessar esta área.");
            window.location.href = 'dashboard.html';
        }

        document.getElementById('dataLancamento').valueAsDate = new Date();

        document.getElementById('formPublicar').addEventListener('submit', function(e) {
            e.preventDefault();

            const nome = document.getElementById('nomeArquivo').value.trim();
            const tamanho = document.getElementById('tamanhoArquivo').value.trim();
            const dataLancamento = document.getElementById('dataLancamento').value;
            const descricao = document.getElementById('descricaoArquivo').value.trim() || "Sem descrição";

            if (!nome || !tamanho || !dataLancamento) {
                alert("Preencha todos os campos obrigatórios.");
                return;
            }

            let arquivos = JSON.parse(localStorage.getItem('arquivosPublicados') || '[]');

            arquivos.push({
                id: Date.now(),
                nome: nome,
                tamanho: tamanho,
                dataLancamento: dataLancamento,
                descricao: descricao,
                status: "registrado",
                preregistros: []
            });

            localStorage.setItem('arquivosPublicados', JSON.stringify(arquivos));

            document.getElementById('mensagem').textContent = "✅ Arquivo registrado com sucesso!";
            document.getElementById('nomeArquivo').value = '';
            document.getElementById('tamanhoArquivo').value = '';
            document.getElementById('descricaoArquivo').value = '';

            setTimeout(() => { document.getElementById('mensagem').textContent = ""; }, 3000);
        });

        document.addEventListener('contextmenu', event => event.preventDefault());
        document.addEventListener('keydown', function(e) {
            if (e.key === "F12" || (e.ctrlKey && e.shiftKey && e.key === "I") || (e.ctrlKey && e.shiftKey && e.key === "J") || (e.ctrlKey && e.key === "U")) {
                e.preventDefault();
            }
        });
    </script>
</body>
</html><!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>📋 Gerenciar Arquivos Publicados</title>
    <link rel="stylesheet" href="style.css">
    <style>
        .arquivo-item {
            background: #f8f9fa;
            padding: 15px;
            margin: 10px 0;
            border-radius: 8px;
            text-align: left;
            position: relative;
            box-shadow: 0 2px 4px rgba(0,0,0,0.05);
        }
        .btn-remover {
            background: #dc3545;
            color: white;
            border: none;
            padding: 5px 10px;
            border-radius: 4px;
            cursor: pointer;
            position: absolute;
            top: 10px;
            right: 10px;
            font-size: 12px;
        }
        .btn-remover:hover {
            background: #c82333;
        }
        .meta {
            font-size: 12px;
            color: #6c757d;
        }
        .status-lancado { color: green; font-weight: bold; }
        .status-pre { color: orange; font-weight: bold; }
        .btn-teste-preregistro {
            background: #007bff;
            color: white;
            border: none;
            padding: 3px 8px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 12px;
            margin-left: 10px;
        }
    </style>
</head>
<body>
    <div class="dashboard-container">
        <h2>📋 Gerenciar Arquivos Registrados</h2>
        <p>Apenas vitor202 pode gerenciar.</p>

        <div id="listaArquivos">
        </div>

        <a href="publicar.html" class="download-btn" style="background:#28a745; margin:10px 0;">📤 Registrar Novo Arquivo</a>
        <a href="lista-publica.html" class="download-btn" style="background:#ffc107; margin:10px 0;">🌍 Ver Lista Pública</a>
        <a href="dashboard.html" class="download-btn" style="background:#6c757d;">⬅️ Voltar</a>
    </div>

    <script>
        if (!sessionStorage.getItem('isLoggedIn') || sessionStorage.getItem('usuarioLogado') !== 'vitor202') {
            alert("⛔ Acesso negado!");
            window.location.href = 'dashboard.html';
        }

        function carregarArquivos() {
            const lista = document.getElementById('listaArquivos');
            let arquivos = JSON.parse(localStorage.getItem('arquivosPublicados') || '[]');

            if (arquivos.length === 0) {
                lista.innerHTML = '<p style="color:#6c757d;">Nenhum arquivo registrado.</p>';
                return;
            }

            lista.innerHTML = '';
            const agora = new Date();

            arquivos.forEach(arquivo => {
                const dataLanc = new Date(arquivo.dataLancamento);
                const lancado = agora >= dataLanc;
                const status = lancado ? "LANÇADO" : "PRÉ-LANÇAMENTO";

                const div = document.createElement('div');
                div.className = 'arquivo-item';
                div.innerHTML = `
                    <strong>${arquivo.nome}</strong> <span style="font-size:12px;">(${arquivo.tamanho})</span><br>
                    <span class="meta">Lançamento: ${formatarData(arquivo.dataLancamento)}</span><br>
                    <span class="${lancado ? 'status-lancado' : 'status-pre'}">${status}</span> |
                    <span class="meta">Pré-registros: ${arquivo.preregistros.length}</span><br>
                    <em>${arquivo.descricao}</em><br>
                    <button class="btn-teste-preregistro" onclick="simularPreregistro(${arquivo.id})">✅ Simular Pré-registro</button>
                    <button class="btn-remover" onclick="removerArquivo(${arquivo.id})">🗑️</button>
                `;
                lista.appendChild(div);
            });
        }

        function formatarData(dataISO) {
            const data = new Date(dataISO);
            return data.toLocaleString('pt-BR');
        }

        function simularPreregistro(id) {
            let arquivos = JSON.parse(localStorage.getItem('arquivosPublicados') || '[]');
            const arquivo = arquivos.find(a => a.id === id);
            if (arquivo) {
                arquivo.preregistros.push("usuario-teste-" + Date.now());
                localStorage.setItem('arquivosPublicados', JSON.stringify(arquivos));
                alert("✅ Pré-registro simulado com sucesso!");
                carregarArquivos();
            }
        }

        function removerArquivo(id) {
            if (!confirm("Remover este arquivo?")) return;
            let arquivos = JSON.parse(localStorage.getItem('arquivosPublicados') || '[]');
            arquivos = arquivos.filter(arq => arq.id !== id);
            localStorage.setItem('arquivosPublicados', JSON.stringify(arquivos));
            carregarArquivos();
        }

        carregarArquivos();

        document.addEventListener('contextmenu', event => event.preventDefault());
        document.addEventListener('keydown', function(e) {
            if (e.key === "F12" || (e.ctrlKey && e.shiftKey && e.key === "I") || (e.ctrlKey && e.shiftKey && e.key === "J") || (e.ctrlKey && e.key === "U")) {
                e.preventDefault();
            }
        });
    </script>
</body>
</html><!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>🎮 Lista de Arquivos - Lançamentos e Pré-lançamentos</title>
    <link rel="stylesheet" href="style.css">
    <style>
        .arquivo-item {
            background: white;
            padding: 20px;
            margin: 15px auto;
            border-radius: 10px;
            box-shadow: 0 3px 10px rgba(0,0,0,0.1);
            max-width: 600px;
            text-align: left;
            position: relative;
        }
        .status-lancado { 
            color: green; 
            font-weight: bold;
            display: inline-block;
            background: #d4edda;
            padding: 3px 8px;
            border-radius: 4px;
            font-size: 14px;
        }
        .status-pre { 
            color: #856404; 
            font-weight: bold;
            display: inline-block;
            background: #fff3cd;
            padding: 3px 8px;
            border-radius: 4px;
            font-size: 14px;
        }
        .btn-download {
            background: #007bff;
            color: white;
            text-decoration: none;
            padding: 10px 20px;
            border-radius: 6px;
            display: inline-block;
            margin-top: 10px;
        }
        .btn-preregistro {
            background: #ffc107;
            color: #212529;
            border: none;
            padding: 8px 15px;
            border-radius: 6px;
            cursor: pointer;
            margin-top: 10px;
            font-weight: bold;
        }
        .notificacao {
            background: #d1ecf1;
            padding: 10px;
            border-radius: 6px;
            margin: 10px 0;
            color: #0c5460;
            font-weight: bold;
        }
    </style>
</head>
<body style="background: #f0f2f5; padding: 20px;">
    <div style="max-width: 700px; margin: 0 auto;">
        <h2 style="text-align: center; color: #333;">🎮 Lista de Arquivos Disponíveis</h2>
        <p style="text-align: center; color: #555;">Lançamentos, pré-lançamentos e notificações exclusivas.</p>

        <div id="notificacoes"></div>
        <div id="listaArquivos"></div>

        <div style="text-align: center; margin-top: 30px;">
            <a href="index.html" class="download-btn" style="background:#6c757d;">🔐 Área Restrita (Admin)</a>
        </div>
    </div>

    <script>
        function carregarArquivos() {
            const lista = document.getElementById('listaArquivos');
            const notificacoes = document.getElementById('notificacoes');
            let arquivos = JSON.parse(localStorage.getItem('arquivosPublicados') || '[]');
            const agora = new Date();

            let houveNotificacao = false;
            arquivos = arquivos.map(arquivo => {
                const dataLanc = new Date(arquivo.dataLancamento);
                const eraPre = !arquivo.notificado && agora >= dataLanc;

                if (eraPre && arquivo.preregistros.length > 0) {
                    arquivo.notificado = true;
                    houveNotificacao = true;
                }
                return arquivo;
            });

            localStorage.setItem('arquivosPublicados', JSON.stringify(arquivos));

            notificacoes.innerHTML = '';
            if (houveNotificacao) {
                const divNotif = document.createElement('div');
                divNotif.className = 'notificacao';
                divNotif.innerHTML = '🎉 <strong>NOVO LANÇAMENTO!</strong> Um ou mais arquivos que você acompanhou foram liberados!';
                notificacoes.appendChild(divNotif);
            }

            lista.innerHTML = '';
            if (arquivos.length === 0) {
                lista.innerHTML = '<p style="text-align:center; color:#6c757d;">Nenhum arquivo disponível no momento.</p>';
                return;
            }

            arquivos.forEach(arquivo => {
                const dataLanc = new Date(arquivo.dataLancamento);
                const lancado = agora >= dataLanc;
                const status = lancado ? "LANÇADO" : "PRÉ-LANÇAMENTO";

                const div = document.createElement('div');
                div.className = 'arquivo-item';

                let conteudo = `
                    <h3>${arquivo.nome} <small>(${arquivo.tamanho})</small></h3>
                    <p><strong>Data de Lançamento:</strong> ${formatarData(arquivo.dataLancamento)}</p>
                    <p><span class="${lancado ? 'status-lancado' : 'status-pre'}">${status}</span></p>
                    <p>${arquivo.descricao}</p>
                `;

                if (lancado) {
                    conteudo += `<a href="arquivos/${arquivo.nome}" class="btn-download" download>⬇️ BAIXAR AGORA</a>`;
                } else {
                    conteudo += `<button class="btn-preregistro" onclick="fazerPreregistro(${arquivo.id})">🔔 PRÉ-REGISTRO (AVISAR QUANDO LANÇAR)</button>`;
                }

                div.innerHTML = conteudo;
                lista.appendChild(div);
            });
        }

        function formatarData(dataISO) {
            const data = new Date(dataISO);
            return data.toLocaleString('pt-BR');
        }

        function fazerPreregistro(id) {
            let arquivos = JSON.parse(localStorage.getItem('arquivosPublicados') || '[]');
            const arquivo = arquivos.find(a => a.id === id);
            if (arquivo) {
                const usuarioId = "visitante-" + Date.now();
                arquivo.preregistros.push(usuarioId);
                localStorage.setItem('arquivosPublicados', JSON.stringify(arquivos));
                alert("✅ Pronto! Você será notificado quando este arquivo for lançado!");
            }
        }

        carregarArquivos();
        setInterval(carregarArquivos, 30000);

        document.addEventListener('contextmenu', event => event.preventDefault());
    </script>
</body>
</html>* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea, #764ba2);
    height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    -khtml-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
}

.login-container, .dashboard-container {
    background: white;
    padding: 40px;
    border-radius: 12px;
    box-shadow: 0 10px 30px rgba(0,0,0,0.2);
    text-align: center;
    width: 90%;
    max-width: 400px;
}

h2 {
    margin-bottom: 20px;
    color: #333;
}

input {
    width: 100%;
    padding: 12px;
    margin: 10px 0;
    border: 1px solid #ddd;
    border-radius: 6px;
    font-size: 16px;
}

button {
    width: 100%;
    padding: 12px;
    background: #667eea;
    color: white;
    border: none;
    border-radius: 6px;
    font-size: 16px;
    cursor: pointer;
    margin-top: 10px;
}

button:hover {
    background: #5a67d8;
}

.error {
    color: red;
    margin-top: 15px;
    font-size: 14px;
}

.download-btn {
    display: inline-block;
    padding: 15px 30px;
    background: #007bff;
    color: white;
    text-decoration: none;
    border-radius: 6px;
    font-weight: bold;
    margin: 20px 0;
}

.download-btn:hover {
    background: #0056b3;
}

.nome-usuario {
    user-select: none;
    -webkit-user-select: none;
    cursor: default;
    font-weight: bold;
    color: #d9534f;
}

/* Estilos para gerenciar.html */
.arquivo-item {
    background: #f8f9fa;
    padding: 15px;
    margin: 10px 0;
    border-radius: 8px;
    text-align: left;
    position: relative;
    box-shadow: 0 2px 4px rgba(0,0,0,0.05);
}

.btn-remover {
    background: #dc3545;
    color: white;
    border: none;
    padding: 5px 10px;
    border-radius: 4px;
    cursor: pointer;
    position: absolute;
    top: 10px;
    right: 10px;
    font-size: 12px;
}

.btn-remover:hover {
    background: #c82333;
}

.meta {
    font-size: 12px;
    color: #6c757d;
}

.status-lancado { color: green; font-weight: bold; }
.status-pre { color: orange; font-weight: bold; }

.btn-teste-preregistro {
    background: #007bff;
    color: white;
    border: none;
    padding: 3px 8px;
    border-radius: 4px;
    cursor: pointer;
    font-size: 12px;
    margin-left: 10px;
}

/* Estilos para lista pública */
.btn-download {
    background: #007bff;
    color: white;
    text-decoration: none;
    padding: 10px 20px;
    border-radius: 6px;
    display: inline-block;
    margin-top: 10px;
}

.btn-preregistro {
    background: #ffc107;
    color: #212529;
    border: none;
    padding: 8px 15px;
    border-radius: 6px;
    cursor: pointer;
    margin-top: 10px;
    font-weight: bold;
}

.notificacao {
    background: #d1ecf1;
    padding: 10px;
    border-radius: 6px;
    margin: 10px 0;
    color: #0c5460;
    font-weight: bold;
}<a href="arquivos/SEU_ARQUIVO_AQUI.zip" class="download-btn" download>⬇️ Baixar Arquivo Principal</a>
