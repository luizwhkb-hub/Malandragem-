<!doctype html>
<html lang="pt-BR">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Baralho da Malandragem — Protótipo</title>
<style>
  :root{
    --bg:#1f1410; /* marrom escuro */
    --card:#7a2b1e; /* vermelho vinho */
    --accent:#d4a94a; /* dourado */
    --muted:#e8d9c2;
    --glass: rgba(255,255,255,0.04);
    font-family: "Segoe UI", "Helvetica Neue", Arial, sans-serif;
  }
  html,body{height:100%;margin:0;background:linear-gradient(180deg,var(--bg) 0%, #231714 100%);color:var(--muted);}
  .wrap{max-width:980px;margin:18px auto;padding:18px;}
  header{display:flex;gap:12px;align-items:center;margin-bottom:18px}
  .logo{
    width:86px;height:86px;border-radius:10px;
    background:linear-gradient(180deg,#3b2b1f,#241713);display:flex;flex-direction:column;align-items:center;justify-content:center;
    border:3px solid rgba(0,0,0,0.25);box-shadow:0 6px 18px rgba(0,0,0,0.6) inset;
  }
  .logo h1{margin:0;color:var(--accent);font-size:16px;letter-spacing:1px}
  header h2{margin:0;font-size:20px}
  .intro{background:var(--glass);padding:12px;border-radius:10px;margin-bottom:12px;display:flex;gap:12px;flex-direction:column}
  .question-row{display:flex;gap:10px;align-items:center}
  input#pergunta{flex:1;padding:12px;border-radius:8px;border:2px solid rgba(255,255,255,0.06);background:transparent;color:var(--muted);font-size:16px}
  button#jogar{background:var(--card);color:var(--accent);border:none;padding:12px 18px;border-radius:8px;font-weight:700;font-size:18px;cursor:pointer}
  .board{margin-top:16px}
  .cards-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:12px}
  .card{
    perspective:1000px;
    height:120px;
  }
  .card-inner{
    position:relative;width:100%;height:100%;transform-style:preserve-3d;transition:transform .6s;
    cursor:pointer;
  }
  .card.flipped .card-inner{transform:rotateY(180deg);}
  .face{
    position:absolute;inset:0;border-radius:8px;display:flex;align-items:center;justify-content:center;
    backface-visibility:hidden;box-shadow:0 6px 20px rgba(0,0,0,0.6);
  }
  .back{
    background:linear-gradient(180deg,#5a2a1f,#6d2f22);border:3px solid rgba(0,0,0,0.3);
    color:var(--accent);font-weight:700;font-size:14px;
    display:flex;flex-direction:column;gap:6px;padding:10px;text-align:center;
  }
  .front{
    transform:rotateY(180deg);background:linear-gradient(180deg,#f5e9d4,#fff9ea);color:#2b1a12;padding:10px;border:3px solid rgba(0,0,0,0.08);
    display:flex;flex-direction:column;align-items:flex-start;justify-content:center;
  }
  .front .title{font-weight:800;margin-bottom:6px}
  .front .desc{font-size:13px;opacity:0.95}
  .status{margin-top:14px;min-height:130px;background:rgba(0,0,0,0.25);padding:12px;border-radius:10px;border:1px solid rgba(255,255,255,0.02)}
  .status h3{margin:0 0 8px 0;color:var(--accent)}
  .controls{display:flex;gap:8px;margin-top:12px;flex-wrap:wrap}
  .btn{padding:10px 14px;border-radius:8px;border:none;cursor:pointer;font-weight:700}
  .btn.whatsapp{background:#25D366;color:#fff}
  .btn.tiktok{background:#000;color:#fff}
  .small{font-size:13px;opacity:0.9}
  footer{margin-top:18px;color:rgba(255,255,255,0.5);font-size:13px}
  /* Responsive */
  @media (max-width:640px){
    .cards-grid{grid-template-columns:repeat(3,1fr)}
    .card{height:110px}
    header h2{font-size:18px}
  }
</style>
</head>
<body>
  <div class="wrap" role="main">
    <header>
      <div class="logo" aria-hidden="true"><h1>Baralho<br>da Malandragem</h1></div>
      <div>
        <h2>Faz tua pergunta... mas se prepara, que a malandragem não mente!</h2>
        <div class="small">Protótipo funcional — escolha 3 cartas e receba a mensagem automática.</div>
      </div>
    </header>

    <section class="intro" aria-label="Entrada">
      <div class="question-row">
        <input id="pergunta" placeholder="Digite sua pergunta (ex.: Será que encontro trabalho?)" aria-label="Pergunta do usuário">
        <button id="jogar" aria-label="Jogar">JOGAR</button>
      </div>
      <div class="small">Dica: seja direto na pergunta para uma resposta mais certeira.</div>
    </section>

    <section class="board" aria-label="Tabuleiro">
      <div class="cards-grid" id="cardsGrid" aria-live="polite"></div>

      <div class="status" id="status">
        <h3 id="statusTitle">Escolhe 3 cartas e deixa a malandragem trabalhar...</h3>
        <div id="statusBody" class="small">Quando você virar cada carta, a malandragem vai explicar. Depois teremos um resumo que junta tudo com a tua pergunta.</div>

        <div class="controls" id="controls" style="display:none">
          <button class="btn whatsapp" id="shareWhatsapp">Compartilhar no WhatsApp</button>
          <a class="btn tiktok" id="linkTikTok" href="https://www.tiktok.com" target="_blank" rel="noopener">Seguir no TikTok</a>
          <button class="btn" id="jogarNovamente">Jogar Novamente</button>
        </div>
      </div>
    </section>

    <footer>Protótipo criado para validação — personalizo arte, texto e integração quando quiser.</footer>
  </div>

<script>
/*
  Protótipo: Baralho da Malandragem
  - 12 cartas de exemplo
  - Seleção de 3 cartas
  - Revelação individual e resumo automático
  - Compartilhamento via WhatsApp com texto pré-formatado
*/

const cardsData = [
  {id:1, name:"O Zé do Bar", short:"Alegria & Festa", text:"O Zé do Bar traz alegria, celebração e boas relações. Atenção pra não gastar demais e perder o jogo."},
  {id:2, name:"A Dama do Jogo", short:"Cautela & Charme", text:"A Dama do Jogo indica charme e habilidade social. Use a conversa certa, mas fica esperto com promessas vazias."},
  {id:3, name:"O Malandro do Morro", short:"Jogo de cintura", text:"Pede jogo de cintura: improvisa, contorna e negocia. Não adianta brigar quando dá pra contornar."},
  {id:4, name:"O Pé de Chinelo", short:"Humildade & Raiz", text:"Humildade fará as portas abrirem. Cuidar das raízes te dá segurança pra dar o próximo passo."},
  {id:5, name:"A Carta do Troco", short:"Causa & Efeito", text:"O que tu plantou volta — seja esperto: compensa medir pra não se enroscar na hora do troco."},
  {id:6, name:"O Olhar da Rua", short:"Percepção", text:"Fica de olho: informações e atenção vão te mostrar a oportunidade. Observa mais, fala menos."},
  {id:7, name:"O Corote", short:"Tentação", text:"Tem tentação no caminho—pra alguns é festa, pra outros enrascada. Mede os riscos antes de se entregar."},
  {id:8, name:"A Sombra", short:"Segredo", text:"Algo escondido pode influenciar a situação. Nem tudo é o que parece; vai com calma."},
  {id:9, name:"O Vigia", short:"Proteção", text:"Proteção chegando: aliados verdadeiros ou um aviso pra cuidar do que é seu."},
  {id:10, name:"A Rima", short:"Sorte & Ritmo", text:"O ritmo favorece quem aprende a dançar com a vida: pequenas atitudes geram sorte em cadeia."},
  {id:11, name:"O Prego", short:"Obstáculo", text:"Obstáculo pontual: não desanima, ajustar a rota resolve mais fácil do que forçar."},
  {id:12, name:"A Cigana do Beco", short:"Intuição", text:"Confia na intuição; ela costuma saber antes de a cabeça entender. Segue o feeling."}
];

// estado
let deck = [];
let chosen = [];
let perguntaUsuario = "";

function shuffleArray(arr){
  // Fisher-Yates
  for(let i=arr.length-1;i>0;i--){
    const j=Math.floor(Math.random()*(i+1));
    [arr[i],arr[j]]=[arr[j],arr[i]];
  }
}

function renderCards(){
  const grid = document.getElementById('cardsGrid');
  grid.innerHTML='';
  deck.forEach((c, idx)=>{
    const card = document.createElement('div');
    card.className='card';
    card.setAttribute('data-index', idx);
    card.innerHTML = `
      <div class="card-inner" role="button" tabindex="0" aria-pressed="false">
        <div class="face back">
          <div>BARALHO<br><small style="opacity:0.85">${c.name}</small></div>
        </div>
        <div class="face front">
          <div class="title">${c.name}</div>
          <div class="desc">${c.short}<br><br><em style="font-size:13px;color:#5a4030">${c.text}</em></div>
        </div>
      </div>
    `;
    card.addEventListener('click', onCardClick);
    card.addEventListener('keydown', (e)=>{ if(e.key==='Enter' || e.key===' ') onCardClick.call(card,e) });
    grid.appendChild(card);
  });
}

function onCardClick(e){
  const idx = parseInt(this.getAttribute('data-index'));
  // se já selecionou 3 e não é uma das escolhidas, não permite (até permitir "ver" selecionadas)
  if(chosen.length>=3 && !chosen.includes(idx)) return;
  // se já virou, não permite revirar
  const inner = this.querySelector('.card-inner');
  const isFlipped = this.classList.contains('flipped');
  if(isFlipped) return;
  this.classList.add('flipped');
  chosen.push(idx);
  showCardMeaning(idx);
  if(chosen.length===3){
    // depois de um pequeno delay, gerar resumo
    setTimeout(()=> renderSummary(), 900);
  }
}

function showCardMeaning(idx){
  const cardData = deck[idx];
  const statusTitle = document.getElementById('statusTitle');
  const statusBody = document.getElementById('statusBody');
  statusTitle.textContent = `${cardData.name} — ${cardData.short}`;
  statusBody.innerHTML = `<strong>Explica o baralho:</strong> ${cardData.text}`;
}

function renderSummary(){
  const statusTitle = document.getElementById('statusTitle');
  const statusBody = document.getElementById('statusBody');
  const controls = document.getElementById('controls');
  // combinar textos
  const chosenData = chosen.map(i=>deck[i]);
  // sintetiza: pega as partes-chave (curtas) e une com a pergunta
  const frases = chosenData.map(c=>c.short.toLowerCase());
  // montar resumo com tom de malandragem e a pergunta
  let resumo = `Na visão da malandragem, sobre "${perguntaUsuario || 'sua pergunta'}": `;
  resumo += `O baralho aponta ${frases.join(', ')}. `;
  resumo += `Resumo prático: ${sintetizaResumo(chosenData, perguntaUsuario)}`;
  statusTitle.textContent = "Resumo final da malandragem";
  statusBody.innerHTML = `<em style="color:var(--accent)">${resumo}</em>`;
  controls.style.display='flex';
  // preparar botão whatsapp
  const shareBtn = document.getElementById('shareWhatsapp');
  shareBtn.onclick = ()=> {
    const text = encodeURIComponent(`Baralho da Malandragem — sobre: "${perguntaUsuario || ''}"\n\n${resumo}\n\nVem ver: (link do site)`);
    window.open(`https://api.whatsapp.com/send?text=${text}`, '_blank');
  };
}

// função simples que "sintetiza" as 3 cartas em tom coloquial
function sintetizaResumo(chosenData, pergunta){
  // regras básicas: reconhecer palavras-chave e montar frase natural
  // este algoritmo é simples e pode ser substituído por algo mais "AI" posteriormente
  const parts = chosenData.map(c=>{
    // pega a primeira sentença da descrição curta ou do texto
    return c.text.split('.')[0].trim();
  });
  // cria uma frase combinada curta
  const base = parts.join(' — ');
  // acrescenta conselho específico dependendo de cartas
  const keywords = chosenData.map(c=>c.name.toLowerCase()).join(' ');
  let conselho = "Segue com jogo de cintura e não se enrosca nas tentações.";
  if(keywords.includes('pé de chinelo')) conselho = "Vai devagar e honra as tuas raízes; a estabilidade vem primeiro.";
  if(keywords.includes('cara do troco') || keywords.includes('troco')) conselho = "Cuidado com reações: pensa antes de responder.";
  if(keywords.includes('vigia')) conselho = "Confia nos aliados e protege o que é seu.";
  if(keywords.includes('corote')) conselho = "Controla as tentações, irmão; festa hoje, problema amanhã.";
  // final
  let final = `${base}. ${conselho}`;
  // se existir pergunta, personaliza
  if(pergunta){
    final = final + ` (resposta para: "${pergunta}")`;
  }
  return final;
}

// iniciar jogo
function startGame(){
  perguntaUsuario = document.getElementById('pergunta').value.trim();
  // prepara deck (copiar e embaralhar)
  deck = JSON.parse(JSON.stringify(cardsData)); // clone
  shuffleArray(deck);
  chosen = [];
  renderCards();
  // reset status
  document.getElementById('statusTitle').textContent = 'Escolhe 3 cartas e deixa a malandragem trabalhar...';
  document.getElementById('statusBody').innerHTML = 'Quando você virar cada carta, a malandragem vai explicar. Depois teremos um resumo que junta tudo com a tua pergunta.';
  document.getElementById('controls').style.display='none';
  // scroll to board on mobile
  document.getElementById('cardsGrid').scrollIntoView({behavior:'smooth'});
}

// jogar novamente
document.getElementById('jogar').addEventListener('click',startGame);
document.getElementById('jogarNovamente').addEventListener('click',startGame);

// inicializa com deck pronto
startGame();
</script>
</body>
</html>
