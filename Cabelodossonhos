import React, { useState, useEffect, useCallback, useRef } from "react";
import {
  Home, CalendarDays, Droplets, Droplet, Camera, User, Plus, Check, ChevronRight,
  ChevronLeft, X, Loader2, Sparkles, Sun, Moon, Package, BookOpen,
  ImagePlus, Edit3, RefreshCw, LogOut, AlertCircle, CheckCircle2, Search,
  Trash2, Lock, ShieldCheck, Leaf, Heart, Info, Wand2, ArrowRight,
} from "lucide-react";

/* ============================================================
   TOKENS DE DESIGN
   Paleta de alto contraste: fundo quase branco, texto escuro sólido,
   rosa forte reservado para botões, progresso, ícones e destaques.
   Assinatura visual: o "fio guia" — uma linha ondulada contínua que
   representa uma mecha de cabelo, usada como barra de progresso.
============================================================ */
const C = {
  bg: "#FAF8F8",
  bgDeep: "#F5EDEF",
  surface: "#FFFFFF",
  surfaceSoft: "#F5F1F1",
  primary: "#C2185B",
  primaryDark: "#8E1245",
  primarySoft: "#FBDCE7",
  cream: "#F3E4D3",
  ink: "#1F1F1F",
  inkSoft: "#5A5A5A",
  inkFaint: "#8A8A8A",
  sage: "#3F7A46",
  sageSoft: "#E1EFE0",
  border: "#E7E1E1",
  gold: "#9C6B14",
  danger: "#C0392B",
  dangerSoft: "#FBE4E1",
};

const FONT_DISPLAY = "'Poppins', sans-serif";
const FONT_BODY = "'Inter', sans-serif";

function GlobalStyle() {
  return (
    <style>{`
      @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&family=Poppins:wght@500;600;700;800&display=swap');
      * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
      html, body { overscroll-behavior-y: none; }
      .cds-root { font-family: ${FONT_BODY}; color: ${C.ink}; font-size: 16px; line-height: 1.4; }
      .cds-display { font-family: ${FONT_DISPLAY}; }
      .cds-accordion-body { animation: cdsFadeIn 0.25s ease both; }
      .cds-scroll::-webkit-scrollbar { display: none; }
      .cds-scroll { -ms-overflow-style: none; scrollbar-width: none; }
      @keyframes cdsFadeUp { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
      @keyframes cdsFadeIn { from { opacity: 0; } to { opacity: 1; } }
      @keyframes cdsPop { 0% { transform: scale(0.9); opacity: 0; } 60% { transform: scale(1.04); opacity: 1; } 100% { transform: scale(1); } }
      @keyframes cdsSpin { to { transform: rotate(360deg); } }
      @keyframes cdsDrift { 0% { transform: translateY(0); } 50% { transform: translateY(-6px); } 100% { transform: translateY(0); } }
      @keyframes cdsSparkle { 0%, 100% { opacity: 0.35; transform: scale(0.9) rotate(0deg); } 50% { opacity: 1; transform: scale(1.1) rotate(15deg); } }
      .cds-anim-fadeup { animation: cdsFadeUp 0.45s ease both; }
      .cds-anim-fadein { animation: cdsFadeIn 0.35s ease both; }
      .cds-anim-pop { animation: cdsPop 0.5s cubic-bezier(.2,.9,.3,1.2) both; }
      .cds-anim-drift { animation: cdsDrift 3.2s ease-in-out infinite; }
      .cds-anim-sparkle { animation: cdsSparkle 2.4s ease-in-out infinite; }
      .cds-chip { transition: all 0.18s ease; }
      .cds-btn { transition: transform 0.12s ease, box-shadow 0.12s ease, opacity 0.12s ease; }
      .cds-btn:active { transform: scale(0.97); }
      .cds-card { transition: box-shadow 0.2s ease, transform 0.2s ease; }
      input, textarea, select, button { font-family: ${FONT_BODY}; }
      input:focus, textarea:focus, select:focus { outline: 2px solid ${C.primary}; outline-offset: 1px; }
      button:focus-visible, a:focus-visible { outline: 2px solid ${C.primary}; outline-offset: 2px; }
    `}</style>
  );
}

/* ============================================================
   UTILITÁRIOS
============================================================ */
function uid() {
  return Date.now().toString(36) + Math.random().toString(36).slice(2, 8);
}
function todayISO() {
  return new Date().toISOString().slice(0, 10);
}
function formatDateBR(iso) {
  if (!iso) return "";
  const [y, m, d] = iso.split("-");
  return `${d}/${m}/${y}`;
}
function firstName(name) {
  if (!name) return "";
  return name.trim().split(" ")[0];
}
function fileToCompressedDataUrl(file, maxWidth = 640, quality = 0.72) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = () => {
      const img = new window.Image();
      img.onload = () => {
        const scale = Math.min(1, maxWidth / img.width);
        const w = Math.round(img.width * scale);
        const h = Math.round(img.height * scale);
        const canvas = document.createElement("canvas");
        canvas.width = w;
        canvas.height = h;
        const ctx = canvas.getContext("2d");
        ctx.drawImage(img, 0, 0, w, h);
        resolve(canvas.toDataURL("image/jpeg", quality));
      };
      img.onerror = reject;
      img.src = reader.result;
    };
    reader.onerror = reject;
    reader.readAsDataURL(file);
  });
}

/* ============================================================
   PERSISTÊNCIA (window.storage)
============================================================ */
async function loadMain() {
  try {
    const res = await window.storage.get("main", false);
    return res ? JSON.parse(res.value) : null;
  } catch (e) {
    return null;
  }
}
async function saveMain(data) {
  try {
    const res = await window.storage.set("main", JSON.stringify(data), false);
    return !!res;
  } catch (e) {
    return false;
  }
}
async function loadPhoto(key) {
  try {
    const res = await window.storage.get(key, false);
    return res ? res.value : null;
  } catch (e) {
    return null;
  }
}
async function savePhoto(key, dataUrl) {
  try {
    const res = await window.storage.set(key, dataUrl, false);
    return !!res;
  } catch (e) {
    return false;
  }
}

/* ============================================================
   DADOS DE DOMÍNIO
============================================================ */
const OBJETIVOS = [
  "Crescimento saudável", "Reduzir quebra", "Mais hidratação", "Mais força",
  "Recuperar fios danificados", "Controlar frizz", "Melhorar definição", "Organizar minha rotina",
];
const TIPOS_CABELO = ["Liso", "Ondulado", "Cacheado", "Crespo"];
const ESTADOS_CABELO = ["Ressecado", "Oleoso", "Normal", "Misto", "Muito danificado", "Quebradiço"];
const QUIMICA_OPTS = ["Não", "Sim, coloração", "Sim, descoloração", "Sim, alisamento", "Sim, relaxamento", "Outra"];
const FREQ_LAVAGEM_OPTS = ["Todos os dias", "4–5 vezes por semana", "2–3 vezes por semana", "1 vez por semana", "Menos de 1 vez por semana"];
const CALOR_OPTS = ["Nunca", "Raramente", "Algumas vezes por semana", "Quase todos os dias"];
const PRODUTO_CATEGORIAS = ["Shampoo", "Condicionador", "Máscara", "Leave-in", "Óleo", "Finalizador", "Protetor térmico", "Outros"];

const DIAS_PROGRAMA = [
  { dia: 1, titulo: "Conheça seu cabelo", objetivo: "Entender a textura e o comportamento dos seus fios antes de começar a rotina.",
    explicacao: "Antes de qualquer produto ou técnica, conhecer o seu cabelo é o primeiro passo. Hoje você vai observar como seus fios se comportam molhados e secos.",
    passos: ["Lave o cabelo normalmente e observe como ele se comporta ainda molhado.", "Depois de seco, repare no volume, no brilho e na textura ao toque.", "Anote no Diário Capilar três palavras que descrevem seu cabelo hoje."],
    checklist: ["Observei meu cabelo molhado", "Observei meu cabelo seco", "Registrei no Diário Capilar"],
    dica: "Cabelos com mais porosidade costumam secar mais rápido e absorver produtos com facilidade — mas também perdem hidratação mais rápido." },
  { dia: 2, titulo: "Organize seus produtos", objetivo: "Reunir e organizar todos os produtos capilares que você já possui.",
    explicacao: "Ter clareza sobre o que você já tem evita compras desnecessárias e ajuda a montar uma rotina com o que já está ao seu alcance.",
    passos: ["Separe todos os produtos de cabelo que você tem em casa.", "Cadastre cada um na aba Meus Produtos, informando categoria e função.", "Descarte produtos vencidos ou com cheiro e textura estranhos."],
    checklist: ["Separei meus produtos", "Cadastrei pelo menos 3 produtos", "Verifiquei a validade"],
    dica: "Produtos abertos há mais de 12 meses costumam perder eficácia, mesmo sem estar vencidos." },
  { dia: 3, titulo: "Entenda sua lavagem", objetivo: "Ajustar a forma como você lava o cabelo para preservar fios e couro cabeludo.",
    explicacao: "A lavagem é a base da rotina capilar. A forma de aplicar o shampoo e enxaguar interfere diretamente na saúde dos fios.",
    passos: ["Concentre o shampoo no couro cabeludo, massageando com a ponta dos dedos.", "Deixe a espuma escorrer pelo comprimento em vez de esfregar os fios.", "Enxágue bem — resíduo de produto deixa o cabelo pesado e opaco."],
    checklist: ["Apliquei shampoo no couro cabeludo", "Não esfreguei o comprimento", "Enxaguei completamente"],
    dica: "Água muito quente pode ressecar o couro cabeludo. Prefira água morna." },
  { dia: 4, titulo: "Proteja os fios", objetivo: "Reduzir o atrito e a fricção que causam quebra no dia a dia.",
    explicacao: "Pequenos hábitos, como o tipo de toalha ou fronha, fazem diferença acumulada na saúde dos fios.",
    passos: ["Troque a toalha comum por uma de microfibra ou camiseta de algodão para secar.", "Evite pentear o cabelo molhado com força — use os dedos ou pente de dentes largos.", "Se for prender o cabelo, use elásticos sem metal."],
    checklist: ["Sequei com toalha de microfibra ou camiseta", "Penteei com cuidado", "Usei acessórios sem metal"],
    dica: "O cabelo molhado está mais fragilizado — trate-o com o dobro de cuidado nesse momento." },
  { dia: 5, titulo: "Cuide do couro cabeludo", objetivo: "Dar atenção à raiz, já que um couro cabeludo saudável favorece fios mais fortes.",
    explicacao: "Muita gente cuida só do comprimento e esquece o couro cabeludo, que é onde o fio nasce.",
    passos: ["Faça uma massagem circular no couro cabeludo por 2 a 3 minutos.", "Observe sinais de oleosidade excessiva, ressecamento ou coceira.", "Evite prender o cabelo com muita força perto da raiz."],
    checklist: ["Fiz a massagem no couro cabeludo", "Observei sinais no couro cabeludo", "Evitei presilhas apertadas"],
    dica: "Queda intensa, falhas, coceira persistente ou feridas merecem avaliação de um dermatologista." },
  { dia: 6, titulo: "Desembarace sem quebrar", objetivo: "Aprender uma técnica de desembaraço que reduz a quebra.",
    explicacao: "A forma de desembaraçar é uma das maiores causas de quebra do dia a dia, especialmente em cabelos cacheados e crespos.",
    passos: ["Aplique um creme ou leave-in para facilitar o desembaraço.", "Comece pelas pontas e vá subindo em direção à raiz, em pequenas seções.", "Use um pente de dentes largos ou os dedos, nunca escova em cabelo seco sem produto."],
    checklist: ["Apliquei produto antes de desembaraçar", "Comecei pelas pontas", "Usei pente ou dedos com cuidado"],
    dica: "Dividir o cabelo em seções torna o desembaraço mais rápido e menos doloroso." },
  { dia: 7, titulo: "Primeira revisão", objetivo: "Fazer um balanço da primeira semana e ajustar o que for necessário.",
    explicacao: "Uma semana é tempo suficiente para perceber pequenas mudanças. Hoje é dia de olhar para trás antes de seguir em frente.",
    passos: ["Releia seus registros no Diário Capilar desta semana.", "Tire ou revise sua foto de evolução do Dia 1.", "Anote o que você percebeu de diferente até agora."],
    checklist: ["Revisei meus registros da semana", "Atualizei minha foto de evolução", "Anotei minhas percepções"],
    dica: "Mudanças reais de cabelo costumam aparecer em semanas, não em dias — paciência faz parte do processo." },
  { dia: 8, titulo: "Hidratação", objetivo: "Introduzir a hidratação como parte fixa da rotina.",
    explicacao: "A hidratação repõe água nos fios e é a base de qualquer rotina capilar, especialmente para fios ressecados.",
    passos: ["Aplique uma máscara ou creme hidratante do comprimento às pontas.", "Deixe agir pelo tempo indicado na embalagem do produto.", "Enxágue bem e observe a maciez dos fios."],
    checklist: ["Apliquei hidratação no comprimento e pontas", "Respeitei o tempo de ação", "Enxaguei completamente"],
    dica: "Fios pouco porosos podem precisar de calor (touca térmica) para a hidratação penetrar melhor." },
  { dia: 9, titulo: "Proteção contra calor", objetivo: "Reduzir os danos causados por ferramentas de calor.",
    explicacao: "Secador, chapinha e babyliss em alta temperatura, sem proteção, ressecam e enfraquecem os fios ao longo do tempo.",
    passos: ["Sempre aplique protetor térmico antes de qualquer ferramenta de calor.", "Use a temperatura mais baixa possível que ainda funcione para seu objetivo.", "Quando possível, prefira secar no ar livre ou no modo frio do secador."],
    checklist: ["Usei protetor térmico", "Reduzi a temperatura da ferramenta", "Dei um intervalo de calor quando possível"],
    dica: "O uso frequente de calor sem proteção é uma das principais causas de pontas duplas." },
  { dia: 10, titulo: "Finalização", objetivo: "Aprender a finalizar o cabelo preservando o resultado da lavagem.",
    explicacao: "A finalização define como o cabelo vai se comportar até a próxima lavagem — errar aqui pode anular o cuidado anterior.",
    passos: ["Aplique o finalizador adequado ao seu tipo de cabelo (creme, gel, óleo ou sérum).", "Evite excesso de produto, que deixa o cabelo pesado ou oleoso.", "Deixe secar naturalmente sempre que possível."],
    checklist: ["Escolhi o finalizador certo para meu cabelo", "Apliquei a quantidade adequada", "Evitei mexer demais no cabelo enquanto seca"],
    dica: "Cabelos cacheados e crespos costumam se beneficiar da técnica de 'plopping' com camiseta para reduzir o frizz." },
  { dia: 11, titulo: "Nutrição", objetivo: "Entender a diferença entre hidratação e nutrição, e quando usar cada uma.",
    explicacao: "Enquanto a hidratação repõe água, a nutrição repõe lipídios — óleos e manteigas — essenciais para fios ressecados ou porosos.",
    passos: ["Use um produto nutritivo, à base de óleos vegetais ou manteigas, uma vez esta semana.", "Aplique preferencialmente do meio para as pontas.", "Observe se o cabelo ficou mais maleável e com mais brilho."],
    checklist: ["Apliquei um produto nutritivo", "Concentrei nas pontas", "Observei o resultado"],
    dica: "Cabelos oleosos na raiz geralmente pedem nutrição só no comprimento e pontas, evitando a raiz." },
  { dia: 12, titulo: "Hábitos que causam quebra", objetivo: "Identificar hábitos do dia a dia que enfraquecem os fios sem você perceber.",
    explicacao: "Muita quebra não vem de um único erro, mas do acúmulo de pequenos hábitos repetidos todos os dias.",
    passos: ["Revise como você tem prendido o cabelo — presilhas e elásticos apertados demais machucam os fios.", "Observe se está escovando o cabelo seco com força.", "Note se está usando fronhas ou toalhas ásperas."],
    checklist: ["Revisei como prendo o cabelo", "Escovei com mais suavidade", "Avaliei minha fronha e toalha"],
    dica: "Trocar a fronha de algodão por uma de cetim ou seda reduz o atrito durante a noite." },
  { dia: 13, titulo: "Cuidados durante o sono", objetivo: "Proteger o cabelo enquanto você dorme.",
    explicacao: "Boa parte do atrito que causa frizz e quebra acontece durante a noite, sem que você perceba.",
    passos: ["Prenda o cabelo em uma trança frouxa, coque baixo ou penteado protetor antes de dormir.", "Use fronha de cetim ou seda, se tiver disponível.", "Evite dormir com o cabelo molhado sempre que possível."],
    checklist: ["Protegi o cabelo antes de dormir", "Usei fronha adequada ou touca", "Evitei dormir com cabelo molhado"],
    dica: "Um lenço ou touca de cetim é uma alternativa prática se você não tiver fronha de seda." },
  { dia: 14, titulo: "Revisão da segunda semana", objetivo: "Avaliar os avanços da segunda semana e reforçar o que está funcionando.",
    explicacao: "Você já testou hidratação, nutrição e proteção térmica. Hoje é dia de olhar para os resultados.",
    passos: ["Releia seus registros das últimas duas semanas no Diário Capilar.", "Tire sua foto de evolução do Dia 14.", "Marque quais cuidados fizeram mais diferença para você."],
    checklist: ["Revisei os registros das 2 semanas", "Atualizei minha foto de evolução", "Identifiquei o que funcionou melhor"],
    dica: "Se algo não estiver funcionando, está tudo bem ajustar — cada cabelo responde de um jeito." },
  { dia: 15, titulo: "Identifique o que seu cabelo precisa", objetivo: "Reconhecer sinais que indicam se o cabelo precisa mais de hidratação, nutrição ou reconstrução.",
    explicacao: "Cabelo opaco e sem elasticidade pede hidratação. Cabelo áspero pede nutrição. Cabelo muito elástico quando molhado pode pedir reconstrução.",
    passos: ["Faça o teste da elasticidade: puxe suavemente um fio molhado e observe se ele estica muito antes de voltar.", "Observe o brilho e a maciez do cabelo seco.", "Atualize seu perfil com a necessidade que parece mais evidente agora."],
    checklist: ["Fiz o teste de elasticidade", "Observei brilho e maciez", "Atualizei minhas anotações"],
    dica: "Excesso de reconstrução deixa o cabelo rígido e quebradiço — use com moderação, sempre alternando com hidratação." },
  { dia: 16, titulo: "Ajuste sua rotina", objetivo: "Personalizar ainda mais sua rotina com base no que você aprendeu até aqui.",
    explicacao: "Agora que você já testou diferentes cuidados, é hora de ajustar frequência e produtos ao que realmente funciona para o seu cabelo.",
    passos: ["Revise a frequência de hidratação e nutrição na aba Minha Rotina.", "Ajuste conforme o que percebeu no Dia 15.", "Atualize seus produtos cadastrados se necessário."],
    checklist: ["Revisei a frequência da minha rotina", "Fiz os ajustes necessários", "Atualizei meus produtos"],
    dica: "Rotina capilar não é fixa para sempre — ela acompanha as mudanças do seu cabelo ao longo do tempo." },
  { dia: 17, titulo: "Proteção diária", objetivo: "Incorporar hábitos de proteção contra sol, poluição e vento no dia a dia.",
    explicacao: "Assim como a pele, o cabelo também sofre com a exposição diária ao sol e à poluição.",
    passos: ["Use um leave-in ou produto com proteção UV em dias de exposição ao sol.", "Prenda o cabelo em dias de muito vento para reduzir o atrito.", "Molhe o cabelo com água doce antes de piscina ou mar, se for o caso."],
    checklist: ["Usei proteção para o sol", "Protegi o cabelo do vento quando necessário", "Cuidei do cabelo em contato com cloro ou sal"],
    dica: "Cabelos com química precisam de atenção redobrada ao sol, que pode acelerar o desbotamento da cor." },
  { dia: 18, titulo: "Cuidados com comprimento e pontas", objetivo: "Dar atenção especial à parte mais antiga e frágil do cabelo: as pontas.",
    explicacao: "As pontas são a parte do fio que existe há mais tempo e sofreu mais desgaste — por isso pedem cuidado extra.",
    passos: ["Aplique óleo ou sérum nas pontas após a finalização.", "Avalie se há pontas duplas ou ressecadas.", "Considere um corte de manutenção se notar muitas pontas danificadas."],
    checklist: ["Apliquei produto nas pontas", "Avaliei o estado das pontas", "Considerei a necessidade de um corte"],
    dica: "Aparar as pontas regularmente não acelera nem atrasa o crescimento, mas evita que o dano suba pelo fio." },
  { dia: 19, titulo: "Revise seus produtos", objetivo: "Avaliar se os produtos que você usa realmente atendem às necessidades atuais do seu cabelo.",
    explicacao: "Depois de quase três semanas de rotina, você já sabe muito mais sobre o que seu cabelo precisa — é hora de revisar o que está usando.",
    passos: ["Revise a lista de produtos cadastrados na aba Meus Produtos.", "Use o Analisador de Produtos para entender melhor algum item específico.", "Identifique se falta algum produto essencial na sua rotina."],
    checklist: ["Revisei meus produtos cadastrados", "Usei o Analisador de Produtos", "Identifiquei o que falta, se houver"],
    dica: "Nem sempre é preciso comprar produtos novos — às vezes o ajuste está na frequência de uso." },
  { dia: 20, titulo: "Prepare sua rotina definitiva", objetivo: "Consolidar tudo o que foi aprendido em uma rotina que vai continuar depois do Dia 21.",
    explicacao: "Amanhã você conclui o programa. Hoje é o dia de organizar tudo em uma rotina que faça sentido para continuar na prática.",
    passos: ["Reveja todas as seções da aba Minha Rotina.", "Defina os dias da semana para lavagem, hidratação e nutrição.", "Escreva no Diário Capilar um resumo do que mais funcionou para você."],
    checklist: ["Revisei toda a Minha Rotina", "Defini os dias da semana para cada cuidado", "Escrevi meu resumo no Diário"],
    dica: "Uma rotina simples e sustentável é sempre melhor do que uma rotina perfeita que você não vai manter." },
  { dia: 21, titulo: "Sua nova rotina capilar", objetivo: "Celebrar a conclusão do programa e olhar para o que vem depois.",
    explicacao: "Você chegou ao fim dos 21 dias. Isso não é um ponto final — é o início da sua nova forma de cuidar do cabelo.",
    passos: ["Tire sua foto de evolução final do Dia 21.", "Compare com a foto do Dia 1 na aba Evolução.", "Leia sua rotina de manutenção e continue com ela."],
    checklist: ["Atualizei minha foto de evolução final", "Comparei o Dia 1 com o Dia 21", "Revisei minha rotina de manutenção"],
    dica: "Os melhores resultados capilares vêm da consistência ao longo do tempo, não de mudanças rápidas." },
];

const MANUTENCAO_TIPS = [
  "Mantenha a frequência de lavagem que funcionou melhor para o seu couro cabeludo.",
  "Continue alternando hidratação e nutrição conforme a necessidade do momento.",
  "Use protetor térmico sempre que usar calor, sem exceção.",
  "Revise sua rotina a cada poucos meses — o cabelo muda com estações, clima e cuidados.",
  "Volte ao Diário Capilar sempre que quiser lembrar o que funcionou.",
];

function calcularPerfil(o) {
  const necessidades = new Set();
  const pontosAtencao = new Set();
  if (o.estadoCabelo.includes("Ressecado")) { necessidades.add("Hidratação frequente"); pontosAtencao.add("ressecamento"); }
  if (o.estadoCabelo.includes("Quebradiço")) { necessidades.add("Reconstrução leve"); pontosAtencao.add("quebra"); }
  if (o.estadoCabelo.includes("Muito danificado")) { necessidades.add("Nutrição e reconstrução"); pontosAtencao.add("dano estrutural"); }
  if (o.estadoCabelo.includes("Oleoso")) { pontosAtencao.add("oleosidade na raiz"); }
  if (o.estadoCabelo.includes("Misto")) { necessidades.add("Cuidado equilibrado entre raiz e pontas"); }
  if (o.quimica && o.quimica !== "Não") { necessidades.add("Proteção para fios com química"); pontosAtencao.add("química recente"); }
  if (o.calor === "Quase todos os dias" || o.calor === "Algumas vezes por semana") { necessidades.add("Proteção térmica"); pontosAtencao.add("uso frequente de calor"); }
  if (necessidades.size === 0) necessidades.add("Manutenção e fortalecimento");
  const freqMap = {
    "Todos os dias": "lavagens diárias, com produtos suaves para o couro cabeludo",
    "4–5 vezes por semana": "lavagens a cada 1–2 dias",
    "2–3 vezes por semana": "lavagens a cada 2–3 dias",
    "1 vez por semana": "lavagem semanal, com boa hidratação no intervalo",
    "Menos de 1 vez por semana": "lavagens espaçadas, com atenção redobrada ao couro cabeludo",
  };
  return {
    necessidades: [...necessidades],
    pontosAtencao: [...pontosAtencao],
    objetivoPrincipal: o.objetivos[0] || "Organizar minha rotina",
    frequenciaRecomendada: freqMap[o.frequenciaLavagem] || "lavagens conforme a necessidade do seu couro cabeludo",
  };
}

function rotinaSecoes(data) {
  const freqTxt = data?.perfil?.frequenciaRecomendada || "lavagens conforme a necessidade do seu couro cabeludo";
  return [
    { key: "lavagem", icon: Droplets, titulo: "Lavagem", texto: `Seu perfil indica ${freqTxt}. Concentre o shampoo no couro cabeludo e deixe a espuma escorrer pelo comprimento — sem esfregar os fios.` },
    { key: "hidratacao", icon: Droplet, titulo: "Hidratação", texto: "Use uma máscara ou creme hidratante de 1 a 2 vezes por semana, especialmente se seu cabelo tende a ressecar. Fios pouco porosos podem se beneficiar de calor (touca térmica) durante o tempo de ação." },
    { key: "nutricao", icon: Leaf, titulo: "Nutrição", texto: "Reserve espaço para um produto nutritivo, à base de óleos ou manteigas, uma vez por semana. Concentre do meio às pontas, evitando a raiz se o couro cabeludo for oleoso." },
    { key: "reconstrucao", icon: ShieldCheck, titulo: "Reconstrução", texto: "Nem todo cabelo precisa de reconstrução com a mesma frequência. Fios com química ou muito danificados podem se beneficiar a cada 15–20 dias — mas o excesso deixa os fios rígidos. Observe como seu cabelo reage." },
    { key: "noturno", icon: Moon, titulo: "Cuidados noturnos", texto: "Prenda o cabelo em uma trança frouxa ou coque baixo antes de dormir. Se possível, use fronha de cetim ou seda para reduzir o atrito." },
    { key: "protecao", icon: Sun, titulo: "Proteção", texto: "Use protetor térmico antes de ferramentas de calor e produtos com proteção solar em dias de exposição prolongada ao sol." },
  ];
}

/* ============================================================
   DADOS PADRÃO
============================================================ */
function defaultData() {
  return {
    auth: { loggedIn: false, name: "", email: "" },
    onboardingCompleted: false,
    onboarding: { objetivos: [], tipoCabelo: "", estadoCabelo: [], quimica: "", frequenciaLavagem: "", calor: "" },
    perfil: null,
    program: { currentDay: 1, diasConcluidos: [], checklists: {} },
    products: [],
    diary: [],
    checklistDaily: {},
  };
}

/* ============================================================
   UI ATOMS
============================================================ */
function Btn({ children, onClick, variant = "primary", disabled, full, icon: Icon, type = "button", size = "md" }) {
  const base = {
    fontWeight: 700,
    borderRadius: 14,
    border: "none",
    cursor: disabled ? "not-allowed" : "pointer",
    display: "inline-flex",
    alignItems: "center",
    justifyContent: "center",
    gap: 8,
    width: full ? "100%" : undefined,
    opacity: disabled ? 0.5 : 1,
    fontSize: size === "sm" ? 14.5 : 16,
    minHeight: size === "sm" ? 42 : 50,
    padding: size === "sm" ? "9px 16px" : "12px 20px",
    letterSpacing: 0.2,
  };
  const variants = {
    primary: { background: C.primary, color: "#fff", boxShadow: `0 6px 16px -6px ${C.primary}99` },
    secondary: { background: C.surface, color: C.primaryDark, border: `1.5px solid ${C.border}` },
    ghost: { background: "transparent", color: C.primaryDark },
    sage: { background: C.sage, color: "#fff" },
    danger: { background: C.danger, color: "#fff" },
  };
  return (
    <button type={type} className="cds-btn" onClick={disabled ? undefined : onClick} disabled={disabled} style={{ ...base, ...variants[variant] }}>
      {Icon && <Icon size={18} strokeWidth={2.3} />}
      {children}
    </button>
  );
}

function Card({ children, style, onClick, pad = 16 }) {
  return (
    <div
      className="cds-card"
      onClick={onClick}
      style={{
        background: C.surface,
        borderRadius: 16,
        padding: pad,
        boxShadow: "0 2px 10px -4px rgba(31,31,31,0.08)",
        border: `1px solid ${C.border}`,
        cursor: onClick ? "pointer" : "default",
        ...style,
      }}
    >
      {children}
    </div>
  );
}

function Accordion({ icon: Icon, title, teaser, children, defaultOpen = false }) {
  const [open, setOpen] = useState(defaultOpen);
  return (
    <Card pad={0} style={{ overflow: "hidden" }}>
      <button
        onClick={() => setOpen((o) => !o)}
        style={{ width: "100%", background: "none", border: "none", cursor: "pointer", display: "flex", alignItems: "center", gap: 12, padding: "14px 16px", textAlign: "left" }}
      >
        {Icon && (
          <div style={{ width: 40, height: 40, borderRadius: 12, background: C.cream, display: "flex", alignItems: "center", justifyContent: "center", flexShrink: 0 }}>
            <Icon size={19} color={C.primaryDark} />
          </div>
        )}
        <div style={{ flex: 1, minWidth: 0 }}>
          <div style={{ fontSize: 15.5, fontWeight: 700, color: C.ink }}>{title}</div>
          {!open && teaser && <div style={{ fontSize: 13.5, color: C.inkSoft, marginTop: 2, overflow: "hidden", textOverflow: "ellipsis", whiteSpace: "nowrap" }}>{teaser}</div>}
        </div>
        <ChevronRight size={18} color={C.inkFaint} style={{ flexShrink: 0, transform: open ? "rotate(90deg)" : "none", transition: "transform 0.2s ease" }} />
      </button>
      {open && (
        <div className="cds-accordion-body" style={{ padding: "0 16px 16px 68px", fontSize: 14, color: C.inkSoft, lineHeight: 1.55 }}>
          {children}
        </div>
      )}
    </Card>
  );
}

function SectionTitle({ eyebrow, title, right }) {
  return (
    <div style={{ display: "flex", alignItems: "flex-end", justifyContent: "space-between", marginBottom: 14 }}>
      <div>
        {eyebrow && <div style={{ fontSize: 12, fontWeight: 700, color: C.primary, letterSpacing: 0.6, textTransform: "uppercase", marginBottom: 3 }}>{eyebrow}</div>}
        <div className="cds-display" style={{ fontSize: 21, fontWeight: 600, color: C.ink }}>{title}</div>
      </div>
      {right}
    </div>
  );
}

function TextInput({ label, value, onChange, placeholder, type = "text", multiline, rows = 3 }) {
  const shared = {
    width: "100%",
    padding: "13px 15px",
    borderRadius: 14,
    border: `1.5px solid ${C.border}`,
    background: C.surfaceSoft,
    fontSize: 15.5,
    color: C.ink,
  };
  return (
    <div style={{ marginBottom: 14 }}>
      {label && <div style={{ fontSize: 13.5, fontWeight: 700, color: C.inkSoft, marginBottom: 6 }}>{label}</div>}
      {multiline ? (
        <textarea rows={rows} value={value} placeholder={placeholder} onChange={(e) => onChange(e.target.value)} style={{ ...shared, resize: "vertical" }} />
      ) : (
        <input type={type} value={value} placeholder={placeholder} onChange={(e) => onChange(e.target.value)} style={shared} />
      )}
    </div>
  );
}

function SelectField({ label, value, onChange, options }) {
  return (
    <div style={{ marginBottom: 14 }}>
      {label && <div style={{ fontSize: 13.5, fontWeight: 700, color: C.inkSoft, marginBottom: 6 }}>{label}</div>}
      <select value={value} onChange={(e) => onChange(e.target.value)} style={{ width: "100%", padding: "13px 15px", borderRadius: 14, border: `1.5px solid ${C.border}`, background: C.surfaceSoft, fontSize: 15.5, color: C.ink }}>
        <option value="">Selecione</option>
        {options.map((o) => <option key={o} value={o}>{o}</option>)}
      </select>
    </div>
  );
}

function ChipGroup({ options, selected, onToggle, multi = true }) {
  const isSel = (o) => (multi ? selected.includes(o) : selected === o);
  return (
    <div style={{ display: "flex", flexWrap: "wrap", gap: 10 }}>
      {options.map((o) => {
        const active = isSel(o);
        return (
          <button
            key={o}
            type="button"
            className="cds-chip"
            onClick={() => onToggle(o)}
            style={{
              padding: "11px 17px",
              borderRadius: 999,
              border: `1.5px solid ${active ? C.primary : C.border}`,
              background: active ? C.primary : C.surface,
              color: active ? "#fff" : C.ink,
              fontWeight: 600,
              fontSize: 14.5,
              cursor: "pointer",
              display: "inline-flex",
              alignItems: "center",
              gap: 6,
            }}
          >
            {active && <Check size={15} strokeWidth={3} />}
            {o}
          </button>
        );
      })}
    </div>
  );
}

function WaveProgress({ percent = 0, height = 38, trackColor, fillColor }) {
  const pathRef = useRef(null);
  const [len, setLen] = useState(0);
  const pct = Math.max(0, Math.min(100, percent));
  const d = "M2,19 C 38,2 76,36 114,19 C 152,2 190,36 228,19 C 266,2 304,36 340,19 C 352,15 356,17 358,19";
  useEffect(() => { if (pathRef.current) setLen(pathRef.current.getTotalLength()); }, []);
  return (
    <svg viewBox="0 0 360 38" width="100%" height={height} style={{ display: "block" }}>
      <path d={d} fill="none" stroke={trackColor || C.primarySoft} strokeWidth="7" strokeLinecap="round" />
      <path
        ref={pathRef}
        d={d}
        fill="none"
        stroke={fillColor || C.primary}
        strokeWidth="7"
        strokeLinecap="round"
        strokeDasharray={len || 1}
        strokeDashoffset={len ? len - (len * pct) / 100 : 0}
        style={{ transition: "stroke-dashoffset 0.9s cubic-bezier(.3,.8,.3,1)" }}
      />
    </svg>
  );
}

function Toast({ toast }) {
  if (!toast) return null;
  const isErr = toast.type === "error";
  return (
    <div
      className="cds-anim-pop"
      style={{
        position: "fixed", bottom: 92, left: "50%", transform: "translateX(-50%)", zIndex: 60,
        background: isErr ? C.danger : C.ink, color: "#fff", padding: "12px 20px", borderRadius: 999,
        fontSize: 14, fontWeight: 600, boxShadow: "0 10px 30px -10px rgba(0,0,0,0.35)",
        display: "flex", alignItems: "center", gap: 8, maxWidth: "88%", textAlign: "center",
      }}
    >
      {isErr ? <AlertCircle size={16} /> : <Sparkles size={16} />}
      {toast.message}
    </div>
  );
}

function Modal({ open, onClose, title, children }) {
  if (!open) return null;
  return (
    <div className="cds-anim-fadein" style={{ position: "fixed", inset: 0, background: "rgba(31,31,31,0.5)", zIndex: 70, display: "flex", alignItems: "flex-end", justifyContent: "center" }} onClick={onClose}>
      <div
        className="cds-anim-fadeup cds-scroll"
        onClick={(e) => e.stopPropagation()}
        style={{ background: C.bg, width: "100%", maxWidth: 480, maxHeight: "88vh", overflowY: "auto", borderRadius: "26px 26px 0 0", padding: "20px 20px 28px" }}
      >
        <div style={{ display: "flex", alignItems: "center", justifyContent: "space-between", marginBottom: 16 }}>
          <div className="cds-display" style={{ fontSize: 19, fontWeight: 600 }}>{title}</div>
          <button onClick={onClose} style={{ background: C.surface, border: `1px solid ${C.border}`, borderRadius: 999, padding: 8, cursor: "pointer" }}>
            <X size={18} />
          </button>
        </div>
        {children}
      </div>
    </div>
  );
}

function EmptyState({ icon: Icon, title, text, action }) {
  return (
    <div className="cds-anim-fadeup" style={{ textAlign: "center", padding: "40px 18px" }}>
      <div style={{ width: 68, height: 68, borderRadius: "50%", background: C.primarySoft, display: "flex", alignItems: "center", justifyContent: "center", margin: "0 auto 16px" }}>
        <Icon size={30} color={C.primaryDark} strokeWidth={1.7} />
      </div>
      <div className="cds-display" style={{ fontSize: 18, fontWeight: 600, marginBottom: 6 }}>{title}</div>
      <div style={{ fontSize: 14, color: C.inkSoft, marginBottom: 18, lineHeight: 1.5 }}>{text}</div>
      {action}
    </div>
  );
}

function LoadingState({ label = "Carregando..." }) {
  return (
    <div style={{ display: "flex", flexDirection: "column", alignItems: "center", justifyContent: "center", minHeight: "60vh", gap: 14 }}>
      <div style={{ width: 44, height: 44, borderRadius: "50%", border: `3.5px solid ${C.primarySoft}`, borderTopColor: C.primary, animation: "cdsSpin 0.8s linear infinite" }} />
      <div style={{ fontSize: 14, color: C.inkSoft, fontWeight: 600 }}>{label}</div>
    </div>
  );
}

function Avatar({ name, size = 44 }) {
  const initials = (name || "?").trim().split(" ").slice(0, 2).map((s) => s[0]?.toUpperCase()).join("");
  return (
    <div style={{ width: size, height: size, borderRadius: "50%", background: `linear-gradient(135deg, ${C.primary}, ${C.gold})`, color: "#fff", display: "flex", alignItems: "center", justifyContent: "center", fontWeight: 700, fontSize: size * 0.38, flexShrink: 0 }}>
      {initials || <User size={size * 0.5} />}
    </div>
  );
}

function TopHeader({ title, subtitle, onBack, right }) {
  return (
    <div style={{ padding: "18px 20px 6px", display: "flex", alignItems: "center", gap: 10 }}>
      {onBack && (
        <button onClick={onBack} style={{ background: C.surface, border: `1px solid ${C.border}`, borderRadius: 999, width: 38, height: 38, display: "flex", alignItems: "center", justifyContent: "center", cursor: "pointer", flexShrink: 0 }}>
          <ChevronLeft size={20} color={C.ink} />
        </button>
      )}
      <div style={{ flex: 1, minWidth: 0 }}>
        <div className="cds-display" style={{ fontSize: 22, fontWeight: 600, color: C.ink, lineHeight: 1.2 }}>{title}</div>
        {subtitle && <div style={{ fontSize: 14, color: C.inkSoft, marginTop: 2 }}>{subtitle}</div>}
      </div>
      {right}
    </div>
  );
}

function BottomNav({ screen, navigate }) {
  const items = [
    { key: "home", label: "Início", icon: Home },
    { key: "program", label: "21 Dias", icon: CalendarDays },
    { key: "routine", label: "Rotina", icon: Droplets },
    { key: "evolution", label: "Evolução", icon: Camera },
    { key: "profile", label: "Perfil", icon: User },
  ];
  return (
    <div style={{ position: "fixed", bottom: 0, left: 0, right: 0, zIndex: 50, display: "flex", justifyContent: "center" }}>
      <div style={{ width: "100%", maxWidth: 480, background: C.surface, boxShadow: "0 -2px 16px rgba(31,31,31,0.08)", borderTop: `1px solid ${C.border}`, display: "flex", padding: "8px 4px 10px" }}>
        {items.map((it) => {
          const active = screen === it.key;
          const Icon = it.icon;
          return (
            <button
              key={it.key}
              onClick={() => navigate(it.key)}
              style={{ flex: 1, background: "none", border: "none", cursor: "pointer", display: "flex", flexDirection: "column", alignItems: "center", gap: 3, padding: "6px 2px", color: active ? C.primary : C.inkSoft }}
            >
              <Icon size={23} strokeWidth={active ? 2.5 : 2} fill={active ? C.primarySoft : "none"} />
              <span style={{ fontSize: 11, fontWeight: active ? 800 : 600 }}>{it.label}</span>
            </button>
          );
        })}
      </div>
    </div>
  );
}

/* ============================================================
   BLOCO DE CUIDADO / CHECKLIST DE UM DIA (reutilizado)
============================================================ */
function DayCareBlock({ dayInfo, checkedItems, onToggleItem, onComplete, isCurrent, isDone }) {
  const allChecked = checkedItems.every(Boolean);
  return (
    <Card pad={16}>
      <div style={{ display: "flex", alignItems: "center", gap: 8, marginBottom: 6 }}>
        <div style={{ fontSize: 12, fontWeight: 800, color: C.primary, letterSpacing: 0.4 }}>DIA {dayInfo.dia} DE 21</div>
        {isDone && <CheckCircle2 size={16} color={C.sage} />}
      </div>
      <div className="cds-display" style={{ fontSize: 20, fontWeight: 700, marginBottom: 10, color: C.ink }}>{dayInfo.titulo}</div>

      <div style={{ marginBottom: 14 }}>
        <div style={{ fontSize: 12, fontWeight: 800, color: C.inkSoft, letterSpacing: 0.3, marginBottom: 4 }}>OBJETIVO</div>
        <div style={{ fontSize: 14.5, color: C.ink, lineHeight: 1.5 }}>{dayInfo.objetivo}</div>
      </div>

      <div style={{ fontSize: 12, fontWeight: 800, color: C.inkSoft, letterSpacing: 0.3, marginBottom: 8 }}>O QUE FAZER</div>
      <div style={{ display: "flex", flexDirection: "column", gap: 8, marginBottom: 14 }}>
        {dayInfo.passos.map((p, i) => (
          <div key={i} style={{ display: "flex", gap: 10, fontSize: 14.5, lineHeight: 1.45 }}>
            <div style={{ width: 20, height: 20, borderRadius: "50%", background: C.cream, color: C.primaryDark, fontSize: 11, fontWeight: 800, display: "flex", alignItems: "center", justifyContent: "center", flexShrink: 0, marginTop: 1 }}>{i + 1}</div>
            <div style={{ color: C.ink }}>{p}</div>
          </div>
        ))}
      </div>

      <div style={{ background: C.cream, borderRadius: 12, padding: "10px 12px", display: "flex", gap: 8, marginBottom: 14 }}>
        <Sparkles size={16} color={C.gold} style={{ flexShrink: 0, marginTop: 1 }} />
        <div style={{ fontSize: 13.5, color: C.ink, lineHeight: 1.45 }}><b>Dica do dia: </b>{dayInfo.dica}</div>
      </div>

      <div style={{ fontSize: 12, fontWeight: 800, color: C.inkSoft, letterSpacing: 0.3, marginBottom: 8 }}>CONCLUIR CUIDADO</div>
      <div style={{ display: "flex", flexDirection: "column", gap: 8, marginBottom: 16 }}>
        {dayInfo.checklist.map((c, i) => (
          <label key={i} style={{ display: "flex", alignItems: "center", gap: 10, cursor: "pointer", padding: "9px 12px", background: checkedItems[i] ? C.sageSoft : C.surfaceSoft, borderRadius: 12, border: `1px solid ${checkedItems[i] ? C.sage : C.border}` }}>
            <div
              onClick={() => onToggleItem(i)}
              style={{ width: 20, height: 20, borderRadius: 6, border: `2px solid ${checkedItems[i] ? C.sage : C.inkFaint}`, background: checkedItems[i] ? C.sage : "transparent", display: "flex", alignItems: "center", justifyContent: "center", flexShrink: 0 }}
            >
              {checkedItems[i] && <Check size={13} color="#fff" strokeWidth={3.5} />}
            </div>
            <span style={{ fontSize: 14.5, color: C.ink }}>{c}</span>
          </label>
        ))}
      </div>

      {isCurrent && !isDone && (
        <Btn full disabled={!allChecked} onClick={onComplete} icon={Check}>CONCLUIR DIA</Btn>
      )}
      {isDone && (
        <div style={{ textAlign: "center", fontSize: 14, fontWeight: 700, color: C.sage, background: C.sageSoft, borderRadius: 12, padding: "10px" }}>✨ Cuidado concluído!</div>
      )}
    </Card>
  );
}

/* ============================================================
   TELA: SPLASH
============================================================ */
function SplashScreen() {
  return (
    <div style={{ minHeight: "100vh", display: "flex", flexDirection: "column", alignItems: "center", justifyContent: "center", gap: 16, background: `linear-gradient(160deg, ${C.bg}, ${C.bgDeep})` }}>
      <div className="cds-anim-drift" style={{ fontSize: 46 }}>💇‍♀️</div>
      <div className="cds-display" style={{ fontSize: 20, fontWeight: 600, color: C.primaryDark }}>Cabelo dos Sonhos</div>
    </div>
  );
}

/* ============================================================
   TELA: AUTENTICAÇÃO
============================================================ */
function AuthScreen({ onAuth, showToast }) {
  const [tab, setTab] = useState("criar");
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");
  const [recoverSent, setRecoverSent] = useState(false);

  function submitCriar() {
    if (!name.trim() || !email.trim()) { showToast("Preencha nome e e-mail para continuar.", "error"); return; }
    onAuth({ name: name.trim(), email: email.trim() });
  }
  function submitEntrar() {
    if (!email.trim()) { showToast("Informe seu e-mail para entrar.", "error"); return; }
    onAuth({ name: name.trim() || "Bem-vinda de volta", email: email.trim() });
  }

  return (
    <div className="cds-anim-fadeup" style={{ minHeight: "100vh", background: `linear-gradient(180deg, ${C.bgDeep}, ${C.bg} 40%)`, display: "flex", flexDirection: "column", justifyContent: "center", padding: "32px 24px" }}>
      <div style={{ textAlign: "center", marginBottom: 28 }}>
        <div style={{ fontSize: 40, marginBottom: 8 }}>💇‍♀️</div>
        <div className="cds-display" style={{ fontSize: 25, fontWeight: 700, color: C.primaryDark }}>Cabelo dos Sonhos</div>
        <div style={{ fontSize: 14, color: C.inkSoft, marginTop: 4 }}>Acesso completo à sua rotina capilar</div>
      </div>

      <Card pad={22}>
        <div style={{ display: "flex", background: C.cream, borderRadius: 12, padding: 4, marginBottom: 18 }}>
          {[{ k: "criar", l: "Criar conta" }, { k: "entrar", l: "Entrar" }, { k: "recuperar", l: "Recuperar" }].map((t) => (
            <button key={t.k} onClick={() => { setTab(t.k); setRecoverSent(false); }} style={{ flex: 1, padding: "9px 4px", borderRadius: 9, border: "none", cursor: "pointer", background: tab === t.k ? C.surface : "transparent", fontWeight: 700, fontSize: 12.5, color: tab === t.k ? C.primaryDark : C.inkSoft }}>{t.l}</button>
          ))}
        </div>

        {tab === "criar" && (
          <div>
            <TextInput label="Seu nome" value={name} onChange={setName} placeholder="Como podemos te chamar?" />
            <TextInput label="E-mail" value={email} onChange={setEmail} placeholder="voce@email.com" type="email" />
            <Btn full onClick={submitCriar} icon={ArrowRight}>CRIAR CONTA E ENTRAR</Btn>
          </div>
        )}
        {tab === "entrar" && (
          <div>
            <TextInput label="E-mail" value={email} onChange={setEmail} placeholder="voce@email.com" type="email" />
            <Btn full onClick={submitEntrar} icon={ArrowRight}>ENTRAR</Btn>
          </div>
        )}
        {tab === "recuperar" && (
          <div>
            {!recoverSent ? (
              <>
                <TextInput label="E-mail cadastrado" value={email} onChange={setEmail} placeholder="voce@email.com" type="email" />
                <Btn full onClick={() => { if (!email.trim()) { showToast("Informe um e-mail.", "error"); return; } setRecoverSent(true); }}>ENVIAR INSTRUÇÕES</Btn>
              </>
            ) : (
              <div style={{ textAlign: "center", padding: "10px 4px" }}>
                <CheckCircle2 size={30} color={C.sage} style={{ marginBottom: 8 }} />
                <div style={{ fontSize: 14, color: C.ink, lineHeight: 1.5 }}>Se esse e-mail estiver cadastrado, você receberá as instruções de acesso em breve.</div>
              </div>
            )}
          </div>
        )}
      </Card>

      <div style={{ textAlign: "center", fontSize: 13.5, color: C.inkSoft, marginTop: 20, lineHeight: 1.5 }}>Acesso único e completo — sem planos, sem comparações, sem mensalidade dentro do app.</div>
    </div>
  );
}

/* ============================================================
   ONBOARDING
============================================================ */
function OnboardingScreen({ initialAnswers, name, onFinish }) {
  const [step, setStep] = useState(0);
  const [ans, setAns] = useState(initialAnswers);
  const [calculating, setCalculating] = useState(false);
  const totalSteps = 7; // 1..7 são perguntas, 0 é intro, 7 é resultado

  function toggleMulti(field, value) {
    setAns((a) => {
      const arr = a[field].includes(value) ? a[field].filter((v) => v !== value) : [...a[field], value];
      return { ...a, [field]: arr };
    });
  }
  function setSingle(field, value) { setAns((a) => ({ ...a, [field]: value })); }

  function canAdvance() {
    if (step === 1) return ans.objetivos.length > 0;
    if (step === 2) return !!ans.tipoCabelo;
    if (step === 3) return ans.estadoCabelo.length > 0;
    if (step === 4) return !!ans.quimica;
    if (step === 5) return !!ans.frequenciaLavagem;
    if (step === 6) return !!ans.calor;
    return true;
  }

  function next() {
    if (step === 6) {
      setCalculating(true);
      setStep(7);
      setTimeout(() => setCalculating(false), 1400);
      return;
    }
    setStep((s) => s + 1);
  }

  const progressPct = step === 0 ? 0 : step === 7 ? 100 : Math.round(((step - 1) / 6) * 100);

  return (
    <div className="cds-anim-fadeup" style={{ minHeight: "100vh", background: C.bg, display: "flex", flexDirection: "column" }}>
      {step > 0 && (
        <div style={{ padding: "18px 22px 0" }}>
          <div style={{ display: "flex", alignItems: "center", gap: 10, marginBottom: 8 }}>
            {step < 7 && (
              <button onClick={() => setStep((s) => Math.max(0, s - 1))} style={{ background: "none", border: "none", cursor: "pointer", padding: 4 }}>
                <ChevronLeft size={20} color={C.inkSoft} />
              </button>
            )}
            <div style={{ flex: 1, height: 6, background: C.primarySoft, borderRadius: 999, overflow: "hidden" }}>
              <div style={{ width: `${progressPct}%`, height: "100%", background: C.primary, borderRadius: 999, transition: "width 0.4s ease" }} />
            </div>
          </div>
          {step < 7 && <div style={{ fontSize: 13, fontWeight: 700, color: C.inkSoft, paddingLeft: 2 }}>Pergunta {step} de 7</div>}
        </div>
      )}

      <div style={{ flex: 1, padding: "24px 24px 40px", display: "flex", flexDirection: "column" }}>
        {step === 0 && (
          <div style={{ flex: 1, display: "flex", flexDirection: "column", justifyContent: "center", textAlign: "center" }}>
            <div className="cds-anim-drift" style={{ fontSize: 52, marginBottom: 18 }}>💗</div>
            <div className="cds-display" style={{ fontSize: 26, fontWeight: 700, lineHeight: 1.25, marginBottom: 14 }}>Seu cabelo merece uma rotina feita para você.</div>
            <div style={{ fontSize: 15, color: C.inkSoft, lineHeight: 1.6, marginBottom: 32 }}>Vamos conhecer melhor seu cabelo e criar um plano de cuidados personalizado{name ? `, ${firstName(name)}` : ""}.</div>
            <Btn full onClick={next} icon={ArrowRight}>COMEÇAR</Btn>
          </div>
        )}

        {step === 1 && (
          <div className="cds-anim-fadeup" key={1}>
            <div className="cds-display" style={{ fontSize: 23, fontWeight: 600, marginBottom: 6 }}>Qual é o seu principal objetivo?</div>
            <div style={{ fontSize: 13.5, color: C.inkSoft, marginBottom: 20 }}>Pode escolher mais de uma opção.</div>
            <ChipGroup options={OBJETIVOS} selected={ans.objetivos} onToggle={(v) => toggleMulti("objetivos", v)} />
            <div style={{ height: 26 }} />
            <Btn full disabled={!canAdvance()} onClick={next} icon={ChevronRight}>CONTINUAR</Btn>
          </div>
        )}

        {step === 2 && (
          <div className="cds-anim-fadeup" key={2}>
            <div className="cds-display" style={{ fontSize: 23, fontWeight: 600, marginBottom: 20 }}>Como você descreveria seu cabelo?</div>
            <ChipGroup options={TIPOS_CABELO} selected={ans.tipoCabelo} onToggle={(v) => setSingle("tipoCabelo", v)} multi={false} />
            <div style={{ height: 26 }} />
            <Btn full disabled={!canAdvance()} onClick={next} icon={ChevronRight}>CONTINUAR</Btn>
          </div>
        )}

        {step === 3 && (
          <div className="cds-anim-fadeup" key={3}>
            <div className="cds-display" style={{ fontSize: 23, fontWeight: 600, marginBottom: 6 }}>Como está o seu cabelo atualmente?</div>
            <div style={{ fontSize: 13.5, color: C.inkSoft, marginBottom: 20 }}>Selecione quantas opções fizerem sentido.</div>
            <ChipGroup options={ESTADOS_CABELO} selected={ans.estadoCabelo} onToggle={(v) => toggleMulti("estadoCabelo", v)} />
            <div style={{ height: 26 }} />
            <Btn full disabled={!canAdvance()} onClick={next} icon={ChevronRight}>CONTINUAR</Btn>
          </div>
        )}

        {step === 4 && (
          <div className="cds-anim-fadeup" key={4}>
            <div className="cds-display" style={{ fontSize: 23, fontWeight: 600, marginBottom: 20 }}>Você usa química?</div>
            <ChipGroup options={QUIMICA_OPTS} selected={ans.quimica} onToggle={(v) => setSingle("quimica", v)} multi={false} />
            <div style={{ height: 26 }} />
            <Btn full disabled={!canAdvance()} onClick={next} icon={ChevronRight}>CONTINUAR</Btn>
          </div>
        )}

        {step === 5 && (
          <div className="cds-anim-fadeup" key={5}>
            <div className="cds-display" style={{ fontSize: 23, fontWeight: 600, marginBottom: 20 }}>Quantas vezes você lava o cabelo?</div>
            <ChipGroup options={FREQ_LAVAGEM_OPTS} selected={ans.frequenciaLavagem} onToggle={(v) => setSingle("frequenciaLavagem", v)} multi={false} />
            <div style={{ height: 26 }} />
            <Btn full disabled={!canAdvance()} onClick={next} icon={ChevronRight}>CONTINUAR</Btn>
          </div>
        )}

        {step === 6 && (
          <div className="cds-anim-fadeup" key={6}>
            <div className="cds-display" style={{ fontSize: 23, fontWeight: 600, marginBottom: 20 }}>Você utiliza ferramentas de calor?</div>
            <ChipGroup options={CALOR_OPTS} selected={ans.calor} onToggle={(v) => setSingle("calor", v)} multi={false} />
            <div style={{ height: 26 }} />
            <Btn full disabled={!canAdvance()} onClick={next} icon={ChevronRight}>CONTINUAR</Btn>
          </div>
        )}

        {step === 7 && (
          <div className="cds-anim-fadeup" style={{ flex: 1, display: "flex", flexDirection: "column" }}>
            {calculating ? (
              <div style={{ flex: 1, display: "flex", flexDirection: "column", alignItems: "center", justifyContent: "center", textAlign: "center", gap: 16 }}>
                <div className="cds-anim-sparkle" style={{ fontSize: 44 }}>✨</div>
                <div className="cds-display" style={{ fontSize: 20, fontWeight: 600 }}>Pronto! Vamos montar sua rotina.</div>
                <div style={{ fontSize: 13.5, color: C.inkSoft }}>Analisando as respostas do seu perfil...</div>
              </div>
            ) : (
              <ResultadoPerfil ans={ans} onConfirm={() => onFinish(ans)} />
            )}
          </div>
        )}
      </div>
    </div>
  );
}

function ResultadoPerfil({ ans, onConfirm }) {
  const perfil = calcularPerfil(ans);
  return (
    <div>
      <div style={{ textAlign: "center", marginBottom: 20 }}>
        <div style={{ fontSize: 38, marginBottom: 8 }}>🌸</div>
        <div className="cds-display" style={{ fontSize: 13, fontWeight: 700, color: C.primary, letterSpacing: 0.5, textTransform: "uppercase" }}>Seu perfil capilar</div>
      </div>
      <Card pad={20} style={{ marginBottom: 16 }}>
        <div className="cds-display" style={{ fontSize: 19, fontWeight: 600, marginBottom: 14, lineHeight: 1.3 }}>Fios que precisam de {perfil.necessidades[0]?.toLowerCase()}</div>
        <div style={{ marginBottom: 12 }}>
          <div style={{ fontSize: 12.5, fontWeight: 700, color: C.inkSoft, marginBottom: 6 }}>PRINCIPAIS NECESSIDADES</div>
          <div style={{ display: "flex", flexWrap: "wrap", gap: 7 }}>
            {perfil.necessidades.map((n) => <span key={n} style={{ background: C.primarySoft, color: C.primaryDark, padding: "6px 12px", borderRadius: 999, fontSize: 13, fontWeight: 700 }}>{n}</span>)}
          </div>
        </div>
        {perfil.pontosAtencao.length > 0 && (
          <div style={{ marginBottom: 12 }}>
            <div style={{ fontSize: 12.5, fontWeight: 700, color: C.inkSoft, marginBottom: 6 }}>PONTOS DE ATENÇÃO</div>
            <div style={{ display: "flex", flexWrap: "wrap", gap: 7 }}>
              {perfil.pontosAtencao.map((n) => <span key={n} style={{ background: C.cream, color: C.ink, padding: "6px 12px", borderRadius: 999, fontSize: 13, fontWeight: 600 }}>{n}</span>)}
            </div>
          </div>
        )}
        <div style={{ marginBottom: 4 }}>
          <div style={{ fontSize: 12.5, fontWeight: 700, color: C.inkSoft, marginBottom: 4 }}>OBJETIVO PRINCIPAL</div>
          <div style={{ fontSize: 14.5 }}>{perfil.objetivoPrincipal}</div>
        </div>
        <div>
          <div style={{ fontSize: 12.5, fontWeight: 700, color: C.inkSoft, marginBottom: 4, marginTop: 10 }}>FREQUÊNCIA RECOMENDADA</div>
          <div style={{ fontSize: 14.5 }}>{perfil.frequenciaRecomendada}</div>
        </div>
      </Card>
      <Btn full onClick={onConfirm} icon={ArrowRight}>VER MINHA ROTINA</Btn>
    </div>
  );
}

/* ============================================================
   TELA: INÍCIO (DASHBOARD)
============================================================ */
function HomeScreen({ data, navigate, onToggleDailyChecklist }) {
  const { program } = data;
  const doneCount = program.diasConcluidos.length;
  const pct = Math.round((doneCount / 21) * 100);
  const currentDay = Math.min(program.currentDay, 21);
  const dayInfo = DIAS_PROGRAMA[currentDay - 1];
  const isDoneToday = program.diasConcluidos.includes(currentDay);
  const finished = program.currentDay > 21;
  const nextDayInfo = !finished && currentDay < 21 ? DIAS_PROGRAMA[currentDay] : null;

  const dailyKey = todayISO();
  const dailyItems = ["Segui minha rotina", "Protegi meus fios", "Evitei excesso de calor", "Cuidei do couro cabeludo", "Registrei minha evolução"];
  const dailyState = data.checklistDaily[dailyKey] || dailyItems.map(() => false);
  const dailyDone = dailyState.filter(Boolean).length;

  const hour = new Date().getHours();
  const saudacao = hour < 12 ? "Bom dia" : hour < 18 ? "Boa tarde" : "Boa noite";

  const quickLinks = [
    { icon: Package, label: "Produtos", screen: "products" },
    { icon: Search, label: "Analisar", screen: "analyzer" },
    { icon: BookOpen, label: "Diário", screen: "diary" },
    { icon: Camera, label: "Evolução", screen: "evolution" },
  ];

  return (
    <div className="cds-anim-fadeup cds-scroll" style={{ paddingBottom: 110 }}>
      <div style={{ padding: "18px 20px 0" }}>
        <div style={{ display: "flex", alignItems: "center", justifyContent: "space-between" }}>
          <div>
            <div className="cds-display" style={{ fontSize: 21, fontWeight: 700 }}>{saudacao}, {firstName(data.auth.name) || "querida"} 🌸</div>
            <div style={{ fontSize: 14, color: C.inkSoft, marginTop: 2 }}>Vamos cuidar do seu cabelo hoje?</div>
          </div>
          <Avatar name={data.auth.name} size={40} />
        </div>
      </div>

      <div style={{ padding: "14px 20px 0" }}>
        <Card pad={16} style={{ background: C.primaryDark, border: "none" }}>
          <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 8 }}>
            <div style={{ color: "#fff", fontSize: 12.5, fontWeight: 700, opacity: 0.92 }}>SEU PROGRESSO</div>
            <div style={{ color: "#fff", fontSize: 14.5, fontWeight: 800 }}>{doneCount}/21 dias</div>
          </div>
          <WaveProgress percent={pct} height={28} trackColor="rgba(255,255,255,0.3)" fillColor="#fff" />
        </Card>
      </div>

      <div style={{ padding: "14px 20px 0" }}>
        {finished ? (
          <Card pad={18} style={{ textAlign: "center" }}>
            <div style={{ fontSize: 28, marginBottom: 6 }}>🎉</div>
            <div className="cds-display" style={{ fontSize: 16.5, fontWeight: 700, marginBottom: 4 }}>Programa concluído!</div>
            <div style={{ fontSize: 14, color: C.inkSoft, marginBottom: 14 }}>Continue com sua rotina de manutenção.</div>
            <Btn onClick={() => navigate("routine")}>VER MINHA ROTINA</Btn>
          </Card>
        ) : (
          <Card pad={18}>
            <div style={{ display: "flex", alignItems: "center", gap: 6, marginBottom: 6 }}>
              <Heart size={14} color={C.primary} fill={C.primary} />
              <div style={{ fontSize: 12, fontWeight: 800, color: C.primary, letterSpacing: 0.3 }}>CUIDADO DE HOJE</div>
              {isDoneToday && <CheckCircle2 size={15} color={C.sage} style={{ marginLeft: "auto" }} />}
            </div>
            <div className="cds-display" style={{ fontSize: 18.5, fontWeight: 700, marginBottom: 6 }}>Dia {dayInfo.dia} — {dayInfo.titulo}</div>
            <div style={{ fontSize: 14.5, color: C.inkSoft, lineHeight: 1.5, marginBottom: 14, display: "-webkit-box", WebkitLineClamp: 2, WebkitBoxOrient: "vertical", overflow: "hidden" }}>{dayInfo.objetivo}</div>
            <Btn full onClick={() => navigate("day-detail", { dia: currentDay })} icon={isDoneToday ? CheckCircle2 : ArrowRight}>
              {isDoneToday ? "VER CUIDADO DE HOJE" : "COMEÇAR"}
            </Btn>
          </Card>
        )}
      </div>

      {nextDayInfo && (
        <div style={{ padding: "12px 20px 0" }}>
          <div style={{ display: "flex", alignItems: "center", gap: 12, background: C.surface, border: `1px solid ${C.border}`, borderRadius: 16, padding: "12px 14px" }}>
            <div style={{ width: 36, height: 36, borderRadius: 11, background: C.cream, display: "flex", alignItems: "center", justifyContent: "center", flexShrink: 0 }}>
              <Droplet size={17} color={C.primaryDark} />
            </div>
            <div style={{ flex: 1, minWidth: 0 }}>
              <div style={{ fontSize: 11.5, fontWeight: 700, color: C.inkSoft }}>SEU PRÓXIMO CUIDADO</div>
              <div style={{ fontSize: 14.5, fontWeight: 700, color: C.ink, overflow: "hidden", textOverflow: "ellipsis", whiteSpace: "nowrap" }}>{nextDayInfo.titulo}</div>
            </div>
            <div style={{ fontSize: 12.5, fontWeight: 700, color: C.primary, flexShrink: 0 }}>{isDoneToday ? "Amanhã" : `Dia ${nextDayInfo.dia}`}</div>
          </div>
        </div>
      )}

      <div style={{ padding: "14px 20px 0" }}>
        <Accordion icon={Check} title="Checklist de hoje" teaser={`${dailyDone}/${dailyItems.length} concluídos`}>
          <div style={{ display: "flex", flexDirection: "column", gap: 8, marginTop: 4 }}>
            {dailyItems.map((it, i) => (
              <label key={i} onClick={() => onToggleDailyChecklist(dailyKey, i, dailyItems.length)} style={{ display: "flex", alignItems: "center", gap: 10, cursor: "pointer" }}>
                <div style={{ width: 19, height: 19, borderRadius: 6, border: `2px solid ${dailyState[i] ? C.sage : C.inkFaint}`, background: dailyState[i] ? C.sage : "transparent", display: "flex", alignItems: "center", justifyContent: "center", flexShrink: 0 }}>
                  {dailyState[i] && <Check size={12} color="#fff" strokeWidth={3.5} />}
                </div>
                <span style={{ fontSize: 14.5, color: dailyState[i] ? C.inkSoft : C.ink, textDecoration: dailyState[i] ? "line-through" : "none" }}>{it}</span>
              </label>
            ))}
          </div>
        </Accordion>
      </div>

      <div style={{ padding: "16px 0 0 20px" }}>
        <div style={{ fontSize: 13, fontWeight: 800, color: C.ink, marginBottom: 10, paddingRight: 20 }}>Acesso rápido</div>
        <div className="cds-scroll" style={{ display: "flex", gap: 10, overflowX: "auto", paddingRight: 20, paddingBottom: 4 }}>
          {quickLinks.map((q) => (
            <button key={q.screen} onClick={() => navigate(q.screen)} style={{ flexShrink: 0, background: C.surface, border: `1px solid ${C.border}`, borderRadius: 14, padding: "12px 16px", display: "flex", alignItems: "center", gap: 8, cursor: "pointer", minWidth: 130 }}>
              <div style={{ width: 32, height: 32, borderRadius: 10, background: C.cream, display: "flex", alignItems: "center", justifyContent: "center", flexShrink: 0 }}>
                <q.icon size={16} color={C.primaryDark} strokeWidth={2} />
              </div>
              <span style={{ fontSize: 13.5, fontWeight: 700, color: C.ink }}>{q.label}</span>
            </button>
          ))}
        </div>
      </div>
    </div>
  );
}

/* ============================================================
   TELA: PROGRAMA 21 DIAS (LISTA)
============================================================ */
function ProgramScreen({ data, navigate }) {
  const { program } = data;
  const semanas = [
    { label: "Semana 1", dias: DIAS_PROGRAMA.slice(0, 7) },
    { label: "Semana 2", dias: DIAS_PROGRAMA.slice(7, 14) },
    { label: "Semana 3", dias: DIAS_PROGRAMA.slice(14, 21) },
  ];
  return (
    <div className="cds-anim-fadeup cds-scroll" style={{ paddingBottom: 110 }}>
      <TopHeader title="Meu desafio de 21 dias" subtitle={`${program.diasConcluidos.length} de 21 dias concluídos`} />
      <div style={{ padding: "6px 20px 0" }}>
        {semanas.map((sem) => (
          <div key={sem.label} style={{ marginBottom: 18 }}>
            <div style={{ fontSize: 13, fontWeight: 800, color: C.primaryDark, marginBottom: 8, letterSpacing: 0.2 }}>{sem.label}</div>
            <div style={{ display: "flex", flexDirection: "column", gap: 8 }}>
              {sem.dias.map((d) => {
                const done = program.diasConcluidos.includes(d.dia);
                const isCurrent = d.dia === program.currentDay && !done;
                const locked = d.dia > program.currentDay;
                return (
                  <button
                    key={d.dia}
                    disabled={locked}
                    onClick={() => navigate("day-detail", { dia: d.dia })}
                    style={{
                      display: "flex", alignItems: "center", gap: 12, textAlign: "left", cursor: locked ? "not-allowed" : "pointer",
                      background: isCurrent ? C.primarySoft : C.surface, border: `1.5px solid ${isCurrent ? C.primary : C.border}`,
                      borderRadius: 14, padding: "11px 14px", opacity: locked ? 0.6 : 1,
                    }}
                  >
                    <div style={{
                      width: 34, height: 34, borderRadius: "50%", flexShrink: 0, display: "flex", alignItems: "center", justifyContent: "center",
                      background: done ? C.sage : isCurrent ? C.primary : C.cream, color: done || isCurrent ? "#fff" : C.inkSoft, fontWeight: 800, fontSize: 13,
                    }}>
                      {locked ? <Lock size={14} /> : done ? <Check size={16} strokeWidth={3} /> : d.dia}
                    </div>
                    <div style={{ flex: 1, minWidth: 0 }}>
                      <div style={{ fontSize: 14.5, fontWeight: 700, color: C.ink, overflow: "hidden", textOverflow: "ellipsis", whiteSpace: "nowrap" }}>{String(d.dia).padStart(2, "0")} {d.titulo}</div>
                    </div>
                    {!locked && <ChevronRight size={17} color={C.inkFaint} style={{ flexShrink: 0 }} />}
                  </button>
                );
              })}
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}

function DayDetailScreen({ data, dia, navigate, onToggleItem, onCompleteDay }) {
  const dayInfo = DIAS_PROGRAMA[dia - 1];
  const { program } = data;
  const done = program.diasConcluidos.includes(dia);
  const isCurrent = dia === program.currentDay;
  const checked = program.checklists[dia] || dayInfo.checklist.map(() => false);
  return (
    <div className="cds-anim-fadeup cds-scroll" style={{ paddingBottom: 110 }}>
      <TopHeader title={`Dia ${dia}`} onBack={() => navigate("program")} />
      <div style={{ padding: "8px 20px 0" }}>
        <DayCareBlock
          dayInfo={dayInfo}
          checkedItems={checked}
          onToggleItem={(i) => onToggleItem(dia, i, dayInfo.checklist.length)}
          onComplete={() => onCompleteDay(dia)}
          isCurrent={isCurrent}
          isDone={done}
        />
      </div>
    </div>
  );
}

/* ============================================================
   TELA: MINHA ROTINA
============================================================ */
function RoutineScreen({ data }) {
  const secoes = rotinaSecoes(data);
  return (
    <div className="cds-anim-fadeup cds-scroll" style={{ paddingBottom: 110 }}>
      <TopHeader title="Minha Rotina" subtitle="Toque em cada cuidado para ver os detalhes" />
      <div style={{ padding: "10px 20px 0", display: "flex", flexDirection: "column", gap: 10 }}>
        {secoes.map((s, i) => (
          <Accordion key={s.key} icon={s.icon} title={s.titulo} teaser={s.texto} defaultOpen={i === 0}>
            {s.texto}
          </Accordion>
        ))}
      </div>
    </div>
  );
}

/* ============================================================
   TELA: MEUS PRODUTOS
============================================================ */
function ProductsScreen({ data, onAddProduct, onDeleteProduct, showToast }) {
  const [modal, setModal] = useState(false);
  return (
    <div className="cds-anim-fadeup cds-scroll" style={{ paddingBottom: 110 }}>
      <TopHeader title="Meus Produtos" subtitle={`${data.products.length} produto(s) cadastrado(s)`} right={
        <button onClick={() => setModal(true)} style={{ background: C.primary, border: "none", borderRadius: 999, width: 38, height: 38, display: "flex", alignItems: "center", justifyContent: "center", cursor: "pointer" }}>
          <Plus size={20} color="#fff" />
        </button>
      } />
      <div style={{ padding: "12px 20px 0" }}>
        {data.products.length === 0 ? (
          <EmptyState icon={Package} title="Nenhum produto ainda" text="Cadastre os produtos que você já tem para organizar sua rotina." action={<Btn onClick={() => setModal(true)} icon={Plus}>ADICIONAR PRODUTO</Btn>} />
        ) : (
          <div style={{ display: "flex", flexDirection: "column", gap: 12 }}>
            {data.products.slice().reverse().map((p) => (
              <Card key={p.id} pad={16}>
                <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start", gap: 10 }}>
                  <div style={{ flex: 1, minWidth: 0 }}>
                    <div style={{ display: "flex", alignItems: "center", gap: 8, marginBottom: 4 }}>
                      <span style={{ background: C.primarySoft, color: C.primaryDark, fontSize: 11, fontWeight: 800, padding: "3px 9px", borderRadius: 999 }}>{p.categoria}</span>
                    </div>
                    <div style={{ fontSize: 15.5, fontWeight: 700 }}>{p.nome}</div>
                    {p.marca && <div style={{ fontSize: 14, color: C.inkSoft, marginTop: 2 }}>{p.marca}</div>}
                    {p.uso && <div style={{ fontSize: 14, color: C.ink, marginTop: 6 }}><b>Uso:</b> {p.uso}</div>}
                    {p.frequencia && <div style={{ fontSize: 14, color: C.ink, marginTop: 2 }}><b>Frequência:</b> {p.frequencia}</div>}
                    {p.obs && <div style={{ fontSize: 14, color: C.inkSoft, marginTop: 4, fontStyle: "italic" }}>{p.obs}</div>}
                  </div>
                  <button onClick={() => onDeleteProduct(p.id)} style={{ background: "none", border: "none", cursor: "pointer", padding: 6, flexShrink: 0 }}>
                    <Trash2 size={17} color={C.inkFaint} />
                  </button>
                </div>
              </Card>
            ))}
          </div>
        )}
      </div>

      <Modal open={modal} onClose={() => setModal(false)} title="Adicionar produto">
        <ProductForm onSave={(p) => { onAddProduct(p); setModal(false); showToast("Produto adicionado! ✨"); }} />
      </Modal>
    </div>
  );
}

function ProductForm({ onSave }) {
  const [nome, setNome] = useState("");
  const [categoria, setCategoria] = useState("");
  const [marca, setMarca] = useState("");
  const [uso, setUso] = useState("");
  const [frequencia, setFrequencia] = useState("");
  const [obs, setObs] = useState("");
  const canSave = nome.trim() && categoria;
  return (
    <div>
      <TextInput label="Nome do produto" value={nome} onChange={setNome} placeholder="Ex: Máscara de hidratação intensa" />
      <SelectField label="Categoria" value={categoria} onChange={setCategoria} options={PRODUTO_CATEGORIAS} />
      <TextInput label="Marca" value={marca} onChange={setMarca} placeholder="Ex: Marca X" />
      <TextInput label="Para que utiliza" value={uso} onChange={setUso} placeholder="Ex: hidratação semanal" />
      <TextInput label="Frequência" value={frequencia} onChange={setFrequencia} placeholder="Ex: 1x por semana" />
      <TextInput label="Observações" value={obs} onChange={setObs} placeholder="Alguma observação sobre o produto?" multiline />
      <Btn full disabled={!canSave} onClick={() => onSave({ id: uid(), nome: nome.trim(), categoria, marca: marca.trim(), uso: uso.trim(), frequencia: frequencia.trim(), obs: obs.trim(), createdAt: todayISO() })} icon={Check}>SALVAR PRODUTO</Btn>
    </div>
  );
}

/* ============================================================
   TELA: ANALISADOR DE PRODUTOS (usa API Anthropic com visão)
============================================================ */
async function analisarProdutoAPI({ nome, imageBase64, mimeType }) {
  const instrucoes = `Você é um assistente educativo de cuidados capilares dentro do app "Cabelo dos Sonhos". Sua tarefa é dar uma orientação breve e cautelosa sobre um produto capilar.
REGRAS IMPORTANTES:
- Nunca invente a composição química exata do produto quando não for possível identificá-la com segurança pela imagem ou nome.
- Nunca faça diagnóstico médico nem prometa resultados.
- Se não for possível identificar o produto com confiança razoável, defina "identificado" como false.
- Responda ESTRITAMENTE em JSON puro, sem markdown, sem texto fora do JSON, no formato:
{"identificado": true|false, "categoria": "string curta", "indicadoPara": "string curta", "comoEncaixar": "string curta e prática", "observacao": "string curta"}
Se identificado for false, deixe categoria, indicadoPara e comoEncaixar como "" e coloque em observacao exatamente: "Não consegui identificar esse produto com segurança. Digite o nome ou envie uma foto mais nítida."
Escreva em português do Brasil, tom feminino, acolhedor e prático.`;

  const content = [];
  if (imageBase64) {
    content.push({ type: "image", source: { type: "base64", media_type: mimeType, data: imageBase64 } });
  }
  content.push({ type: "text", text: `${instrucoes}\n\nNome informado pela usuária (pode estar vazio): "${nome || ""}"` });

  const response = await fetch("https://api.anthropic.com/v1/messages", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      model: "claude-sonnet-4-6",
      max_tokens: 500,
      messages: [{ role: "user", content }],
    }),
  });
  const dataRes = await response.json();
  const text = (dataRes.content || []).filter((b) => b.type === "text").map((b) => b.text).join("");
  const clean = text.replace(/```json|```/g, "").trim();
  return JSON.parse(clean);
}

function AnalyzerScreen({ showToast }) {
  const [nome, setNome] = useState("");
  const [preview, setPreview] = useState(null);
  const [imgData, setImgData] = useState(null);
  const [loading, setLoading] = useState(false);
  const [resultado, setResultado] = useState(null);
  const fileRef = useRef(null);

  async function handleFile(e) {
    const file = e.target.files?.[0];
    if (!file) return;
    try {
      const dataUrl = await fileToCompressedDataUrl(file, 700, 0.75);
      setPreview(dataUrl);
      setImgData({ base64: dataUrl.split(",")[1], mimeType: "image/jpeg" });
    } catch {
      showToast("Não foi possível carregar a imagem.", "error");
    }
  }

  async function handleAnalyze() {
    if (!nome.trim() && !imgData) { showToast("Digite o nome do produto ou envie uma foto.", "error"); return; }
    setLoading(true);
    setResultado(null);
    try {
      const r = await analisarProdutoAPI({ nome: nome.trim(), imageBase64: imgData?.base64, mimeType: imgData?.mimeType });
      setResultado(r);
    } catch (e) {
      showToast("Não foi possível analisar agora. Tente novamente.", "error");
    } finally {
      setLoading(false);
    }
  }

  return (
    <div className="cds-anim-fadeup cds-scroll" style={{ paddingBottom: 110 }}>
      <TopHeader title="Analisar produto" subtitle="Envie uma foto ou digite o nome" />
      <div style={{ padding: "12px 20px 0" }}>
        <Card pad={18} style={{ marginBottom: 16 }}>
          <div
            onClick={() => fileRef.current?.click()}
            style={{ border: `1.5px dashed ${C.primary}`, borderRadius: 16, padding: preview ? 8 : 26, textAlign: "center", cursor: "pointer", background: C.surfaceSoft, marginBottom: 14 }}
          >
            {preview ? (
              <img src={preview} alt="Produto" style={{ width: "100%", maxHeight: 200, objectFit: "cover", borderRadius: 12 }} />
            ) : (
              <>
                <ImagePlus size={28} color={C.primary} style={{ marginBottom: 8 }} />
                <div style={{ fontSize: 13.5, fontWeight: 700, color: C.primaryDark }}>Toque para enviar uma foto</div>
              </>
            )}
          </div>
          <input ref={fileRef} type="file" accept="image/*" capture="environment" onChange={handleFile} style={{ display: "none" }} />
          <TextInput label="Nome do produto (opcional se enviou foto)" value={nome} onChange={setNome} placeholder="Ex: Máscara Nutritiva X" />
          <Btn full onClick={handleAnalyze} disabled={loading} icon={loading ? Loader2 : Wand2}>{loading ? "ANALISANDO..." : "ANALISAR PRODUTO"}</Btn>
        </Card>

        {loading && (
          <Card pad={20} style={{ textAlign: "center" }}>
            <Loader2 size={26} color={C.primary} style={{ animation: "cdsSpin 0.9s linear infinite", marginBottom: 8 }} />
            <div style={{ fontSize: 13.5, color: C.inkSoft }}>Buscando informações do produto...</div>
          </Card>
        )}

        {resultado && !loading && (
          <Card pad={20} className="cds-anim-fadeup">
            {resultado.identificado ? (
              <>
                <div style={{ fontSize: 12, fontWeight: 800, color: C.primary, marginBottom: 4 }}>CATEGORIA PROVÁVEL</div>
                <div style={{ fontSize: 16, fontWeight: 700, marginBottom: 14 }}>{resultado.categoria}</div>
                <div style={{ fontSize: 12, fontWeight: 800, color: C.primary, marginBottom: 4 }}>PODE SER INTERESSANTE PARA</div>
                <div style={{ fontSize: 14.5, marginBottom: 14 }}>{resultado.indicadoPara}</div>
                <div style={{ fontSize: 12, fontWeight: 800, color: C.primary, marginBottom: 4 }}>COMO ENCAIXAR NA ROTINA</div>
                <div style={{ fontSize: 14.5, marginBottom: resultado.observacao ? 14 : 0 }}>{resultado.comoEncaixar}</div>
                {resultado.observacao && <div style={{ fontSize: 13, color: C.inkSoft, fontStyle: "italic" }}>{resultado.observacao}</div>}
              </>
            ) : (
              <div style={{ display: "flex", gap: 10 }}>
                <Info size={20} color={C.inkSoft} style={{ flexShrink: 0 }} />
                <div style={{ fontSize: 14, color: C.ink, lineHeight: 1.5 }}>{resultado.observacao || "Não consegui identificar esse produto com segurança. Digite o nome ou envie uma foto mais nítida."}</div>
              </div>
            )}
          </Card>
        )}

        <div style={{ fontSize: 13.5, color: C.inkSoft, textAlign: "center", marginTop: 16, lineHeight: 1.5, padding: "0 8px" }}>
          Esta análise é orientativa e educativa — não substitui a bula do fabricante nem uma avaliação profissional.
        </div>
      </div>
    </div>
  );
}

/* ============================================================
   TELA: DIÁRIO CAPILAR
============================================================ */
function DiaryEntryPhoto({ entryId }) {
  const [url, setUrl] = useState(null);
  useEffect(() => { loadPhoto(`diary-photo-${entryId}`).then(setUrl); }, [entryId]);
  if (!url) return null;
  return <img src={url} alt="Registro" style={{ width: "100%", maxHeight: 180, objectFit: "cover", borderRadius: 12, marginTop: 10 }} />;
}

function DiaryScreen({ data, onAddDiary, showToast }) {
  const [modal, setModal] = useState(false);
  return (
    <div className="cds-anim-fadeup cds-scroll" style={{ paddingBottom: 110 }}>
      <TopHeader title="Meu Diário" subtitle={`${data.diary.length} registro(s)`} right={
        <button onClick={() => setModal(true)} style={{ background: C.primary, border: "none", borderRadius: 999, width: 38, height: 38, display: "flex", alignItems: "center", justifyContent: "center", cursor: "pointer" }}>
          <Plus size={20} color="#fff" />
        </button>
      } />
      <div style={{ padding: "12px 20px 0" }}>
        {data.diary.length === 0 ? (
          <EmptyState icon={BookOpen} title="Seu diário está vazio" text="Registre como seu cabelo está e como você se sente ao longo dos 21 dias." action={<Btn onClick={() => setModal(true)} icon={Plus}>NOVO REGISTRO</Btn>} />
        ) : (
          <div style={{ display: "flex", flexDirection: "column", gap: 12 }}>
            {data.diary.slice().reverse().map((e) => (
              <Card key={e.id} pad={16}>
                <div style={{ fontSize: 12, fontWeight: 800, color: C.primary, marginBottom: 6 }}>{formatDateBR(e.data)}</div>
                {e.comoEstava && <div style={{ fontSize: 14, marginBottom: 4 }}><b>Como estava:</b> {e.comoEstava}</div>}
                {e.comoSeSentiu && <div style={{ fontSize: 14, color: C.ink, lineHeight: 1.5, marginBottom: 4 }}>{e.comoSeSentiu}</div>}
                {e.produtos && <div style={{ fontSize: 14, color: C.inkSoft }}><b>Produtos:</b> {e.produtos}</div>}
                {e.obs && <div style={{ fontSize: 14, color: C.inkSoft, marginTop: 4, fontStyle: "italic" }}>{e.obs}</div>}
                {e.hasPhoto && <DiaryEntryPhoto entryId={e.id} />}
              </Card>
            ))}
          </div>
        )}
      </div>

      <Modal open={modal} onClose={() => setModal(false)} title="Novo registro">
        <DiaryForm onSave={(entry, photoDataUrl) => { onAddDiary(entry, photoDataUrl); setModal(false); showToast("Registro salvo! 📔"); }} />
      </Modal>
    </div>
  );
}

function DiaryForm({ onSave }) {
  const [comoEstava, setComoEstava] = useState("");
  const [comoSeSentiu, setComoSeSentiu] = useState("");
  const [produtos, setProdutos] = useState("");
  const [obs, setObs] = useState("");
  const [preview, setPreview] = useState(null);
  const fileRef = useRef(null);

  async function handleFile(e) {
    const file = e.target.files?.[0];
    if (!file) return;
    const dataUrl = await fileToCompressedDataUrl(file, 700, 0.75);
    setPreview(dataUrl);
  }

  const canSave = comoEstava.trim() || comoSeSentiu.trim();

  return (
    <div>
      <TextInput label="Como o cabelo estava?" value={comoEstava} onChange={setComoEstava} placeholder="Ex: macio, com brilho, um pouco ressecado nas pontas" />
      <TextInput label="Como você se sentiu?" value={comoSeSentiu} onChange={setComoSeSentiu} placeholder="Ex: hoje senti meu cabelo mais macio." multiline />
      <TextInput label="Produtos utilizados" value={produtos} onChange={setProdutos} placeholder="Ex: máscara hidratante + leave-in" />
      <TextInput label="Observações" value={obs} onChange={setObs} placeholder="Alguma outra observação?" multiline rows={2} />
      <div onClick={() => fileRef.current?.click()} style={{ border: `1.5px dashed ${C.primary}`, borderRadius: 16, padding: preview ? 8 : 20, textAlign: "center", cursor: "pointer", background: C.surfaceSoft, marginBottom: 16 }}>
        {preview ? <img src={preview} alt="" style={{ width: "100%", maxHeight: 160, objectFit: "cover", borderRadius: 12 }} /> : (
          <>
            <ImagePlus size={22} color={C.primary} style={{ marginBottom: 6 }} />
            <div style={{ fontSize: 13, fontWeight: 700, color: C.primaryDark }}>Adicionar foto (opcional)</div>
          </>
        )}
      </div>
      <input ref={fileRef} type="file" accept="image/*" onChange={handleFile} style={{ display: "none" }} />
      <Btn full disabled={!canSave} icon={Check} onClick={() => onSave({ id: uid(), data: todayISO(), comoEstava: comoEstava.trim(), comoSeSentiu: comoSeSentiu.trim(), produtos: produtos.trim(), obs: obs.trim(), hasPhoto: !!preview, createdAt: Date.now() }, preview)}>SALVAR REGISTRO</Btn>
    </div>
  );
}

/* ============================================================
   TELA: EVOLUÇÃO
============================================================ */
function PhotoSlot({ dayLabel, photoUrl, onPick }) {
  const fileRef = useRef(null);
  return (
    <div>
      <input ref={fileRef} type="file" accept="image/*" style={{ display: "none" }} onChange={(e) => { const f = e.target.files?.[0]; if (f) onPick(f); }} />
      <div onClick={() => fileRef.current?.click()} style={{ aspectRatio: "1/1", borderRadius: 14, background: photoUrl ? `url(${photoUrl}) center/cover` : C.cream, border: `1.5px ${photoUrl ? "solid" : "dashed"} ${C.primary}`, display: "flex", alignItems: "center", justifyContent: "center", cursor: "pointer", overflow: "hidden" }}>
        {!photoUrl && <Camera size={22} color={C.primaryDark} />}
      </div>
      <div style={{ textAlign: "center", fontSize: 12, fontWeight: 700, color: C.inkSoft, marginTop: 5 }}>{dayLabel}</div>
    </div>
  );
}

function EvolutionScreen({ data, photos, onPickPhoto }) {
  const { program } = data;
  const doneCount = program.diasConcluidos.length;
  const pct = Math.round((doneCount / 21) * 100);
  let streak = 0;
  for (let d = program.currentDay - 1; d >= 1; d--) { if (program.diasConcluidos.includes(d)) streak++; else break; }

  return (
    <div className="cds-anim-fadeup cds-scroll" style={{ paddingBottom: 110 }}>
      <TopHeader title="Minha Evolução ✨" />
      <div style={{ padding: "10px 20px 0" }}>
        <SectionTitle title="Fotos do progresso" />
        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 12, marginBottom: 22 }}>
          {[1, 7, 14, 21].map((d) => (
            <PhotoSlot key={d} dayLabel={`Dia ${d}`} photoUrl={photos[d]} onPick={(file) => onPickPhoto(d, file)} />
          ))}
        </div>

        {photos[1] && photos[21] && (
          <Card pad={16} style={{ marginBottom: 20 }}>
            <div style={{ fontSize: 13, fontWeight: 700, marginBottom: 10, color: C.primaryDark }}>Antes e depois</div>
            <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 10 }}>
              <div><img src={photos[1]} alt="Dia 1" style={{ width: "100%", borderRadius: 12, aspectRatio: "1/1", objectFit: "cover" }} /><div style={{ textAlign: "center", fontSize: 11.5, color: C.inkSoft, marginTop: 4, fontWeight: 700 }}>DIA 1</div></div>
              <div><img src={photos[21]} alt="Dia 21" style={{ width: "100%", borderRadius: 12, aspectRatio: "1/1", objectFit: "cover" }} /><div style={{ textAlign: "center", fontSize: 11.5, color: C.inkSoft, marginTop: 4, fontWeight: 700 }}>DIA 21</div></div>
            </div>
          </Card>
        )}

        <SectionTitle title="Estatísticas" />
        <Card pad={20} style={{ marginBottom: 16 }}>
          <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 12 }}>
            <div style={{ fontSize: 14, fontWeight: 700 }}>{doneCount}/21 dias concluídos</div>
            <div style={{ fontSize: 14, fontWeight: 800, color: C.primary }}>{pct}%</div>
          </div>
          <WaveProgress percent={pct} />
          <div style={{ display: "flex", gap: 14, marginTop: 18 }}>
            <div style={{ flex: 1, textAlign: "center", background: C.cream, borderRadius: 14, padding: "12px 8px" }}>
              <div style={{ fontSize: 20, fontWeight: 800, color: C.primaryDark }}>{streak}</div>
              <div style={{ fontSize: 11.5, color: C.inkSoft, fontWeight: 700 }}>sequência atual</div>
            </div>
            <div style={{ flex: 1, textAlign: "center", background: C.cream, borderRadius: 14, padding: "12px 8px" }}>
              <div style={{ fontSize: 20, fontWeight: 800, color: C.primaryDark }}>{data.diary.length}</div>
              <div style={{ fontSize: 11.5, color: C.inkSoft, fontWeight: 700 }}>registros no diário</div>
            </div>
          </div>
        </Card>
      </div>
    </div>
  );
}

/* ============================================================
   TELA: PERFIL
============================================================ */
function ProfileScreen({ data, navigate, onLogout, onEditProfile, onRestartOnboarding }) {
  const [editOpen, setEditOpen] = useState(false);
  const o = data.onboarding;
  const rows = [
    { label: "Nome", value: data.auth.name },
    { label: "Tipo de cabelo", value: o.tipoCabelo },
    { label: "Objetivo", value: data.perfil?.objetivoPrincipal },
    { label: "Frequência de lavagem", value: o.frequenciaLavagem },
    { label: "Uso de química", value: o.quimica },
    { label: "Uso de calor", value: o.calor },
  ];
  return (
    <div className="cds-anim-fadeup cds-scroll" style={{ paddingBottom: 110 }}>
      <TopHeader title="Meu Perfil" />
      <div style={{ padding: "6px 20px 0", display: "flex", alignItems: "center", gap: 14, marginBottom: 18 }}>
        <Avatar name={data.auth.name} size={58} />
        <div>
          <div className="cds-display" style={{ fontSize: 18, fontWeight: 700 }}>{data.auth.name}</div>
          <div style={{ fontSize: 13, color: C.inkSoft }}>{data.auth.email}</div>
        </div>
      </div>

      <div style={{ padding: "0 20px" }}>
        <Card pad={18} style={{ marginBottom: 16 }}>
          {rows.map((r, i) => (
            <div key={r.label} style={{ display: "flex", justifyContent: "space-between", padding: "10px 0", borderBottom: i < rows.length - 1 ? `1px solid ${C.border}` : "none" }}>
              <span style={{ fontSize: 14, color: C.inkSoft }}>{r.label}</span>
              <span style={{ fontSize: 14, fontWeight: 700, color: C.ink, textAlign: "right" }}>{r.value || "—"}</span>
            </div>
          ))}
        </Card>

        <div style={{ display: "flex", flexDirection: "column", gap: 10, marginBottom: 20 }}>
          <Btn full variant="secondary" icon={Edit3} onClick={() => setEditOpen(true)}>EDITAR MEU PERFIL</Btn>
          <Btn full variant="secondary" icon={RefreshCw} onClick={onRestartOnboarding}>REFAZER DIAGNÓSTICO</Btn>
          <Btn full variant="secondary" icon={BookOpen} onClick={() => navigate("diary")}>MEUS REGISTROS</Btn>
          <Btn full variant="secondary" icon={Camera} onClick={() => navigate("evolution")}>MINHA EVOLUÇÃO</Btn>
        </div>

        <Card pad={16} style={{ background: C.cream, border: "none", marginBottom: 16 }}>
          <div style={{ display: "flex", gap: 10 }}>
            <Info size={18} color={C.primaryDark} style={{ flexShrink: 0, marginTop: 1 }} />
            <div style={{ fontSize: 14, color: C.ink, lineHeight: 1.5 }}>Este app tem finalidade educativa e organizacional. Ele não diagnostica doenças, não garante crescimento e não promete resultados. Em caso de queda intensa, falhas, coceira persistente ou feridas, procure um dermatologista.</div>
          </div>
        </Card>

        <Btn full variant="danger" icon={LogOut} onClick={onLogout}>SAIR DA CONTA</Btn>
      </div>

      <Modal open={editOpen} onClose={() => setEditOpen(false)} title="Editar meu perfil">
        <ProfileEditForm data={data} onSave={(v) => { onEditProfile(v); setEditOpen(false); }} />
      </Modal>
    </div>
  );
}

function ProfileEditForm({ data, onSave }) {
  const [name, setName] = useState(data.auth.name);
  const [tipoCabelo, setTipoCabelo] = useState(data.onboarding.tipoCabelo);
  const [quimica, setQuimica] = useState(data.onboarding.quimica);
  const [frequenciaLavagem, setFrequenciaLavagem] = useState(data.onboarding.frequenciaLavagem);
  const [calor, setCalor] = useState(data.onboarding.calor);
  return (
    <div>
      <TextInput label="Nome" value={name} onChange={setName} />
      <SelectField label="Tipo de cabelo" value={tipoCabelo} onChange={setTipoCabelo} options={TIPOS_CABELO} />
      <SelectField label="Uso de química" value={quimica} onChange={setQuimica} options={QUIMICA_OPTS} />
      <SelectField label="Frequência de lavagem" value={frequenciaLavagem} onChange={setFrequenciaLavagem} options={FREQ_LAVAGEM_OPTS} />
      <SelectField label="Uso de calor" value={calor} onChange={setCalor} options={CALOR_OPTS} />
      <Btn full icon={Check} onClick={() => onSave({ name, tipoCabelo, quimica, frequenciaLavagem, calor })}>SALVAR ALTERAÇÕES</Btn>
    </div>
  );
}

/* ============================================================
   TELA: CONCLUSÃO DOS 21 DIAS
============================================================ */
function ConclusionScreen({ data, navigate }) {
  return (
    <div className="cds-anim-fadeup cds-scroll" style={{ paddingBottom: 110, textAlign: "center", padding: "40px 24px 110px" }}>
      <div className="cds-anim-pop" style={{ fontSize: 54, marginBottom: 12 }}>🌸</div>
      <div className="cds-display" style={{ fontSize: 26, fontWeight: 700, marginBottom: 8 }}>Parabéns!</div>
      <div style={{ fontSize: 15, color: C.inkSoft, marginBottom: 22 }}>Você concluiu seu programa de 21 dias.</div>

      <Card pad={22} style={{ marginBottom: 20 }}>
        <div style={{ fontSize: 15, fontWeight: 700, marginBottom: 10 }}>21/21 dias</div>
        <WaveProgress percent={100} />
        <div style={{ fontSize: 22, fontWeight: 800, color: C.primary, marginTop: 12 }}>100% concluído</div>
      </Card>

      <div style={{ textAlign: "left" }}>
        <SectionTitle title="Sua próxima etapa" />
        <Card pad={18} style={{ marginBottom: 20 }}>
          <div style={{ display: "flex", flexDirection: "column", gap: 10 }}>
            {MANUTENCAO_TIPS.map((t, i) => (
              <div key={i} style={{ display: "flex", gap: 10 }}>
                <Heart size={16} color={C.primary} style={{ flexShrink: 0, marginTop: 2 }} />
                <div style={{ fontSize: 14, color: C.ink, lineHeight: 1.5 }}>{t}</div>
              </div>
            ))}
          </div>
        </Card>
      </div>

      <Btn full icon={Droplets} onClick={() => navigate("routine")}>VER MINHA ROTINA DE MANUTENÇÃO</Btn>
    </div>
  );
}

/* ============================================================
   APP PRINCIPAL
============================================================ */
export default function App() {
  const [data, setData] = useState(null);
  const [photos, setPhotos] = useState({ 1: null, 7: null, 14: null, 21: null });
  const [ready, setReady] = useState(false);
  const [screen, setScreen] = useState("home");
  const [params, setParams] = useState({});
  const [toast, setToast] = useState(null);
  const [celebrate, setCelebrate] = useState(null);

  useEffect(() => {
    (async () => {
      const loaded = await loadMain();
      const finalData = loaded || defaultData();
      setData(finalData);
      const p = {};
      for (const d of [1, 7, 14, 21]) p[d] = await loadPhoto(`photo-day-${d}`);
      setPhotos(p);
      if (!finalData.auth.loggedIn) setScreen("auth");
      else if (!finalData.onboardingCompleted) setScreen("onboarding");
      else setScreen("home");
      setReady(true);
    })();
  }, []);

  useEffect(() => {
    if (toast) { const t = setTimeout(() => setToast(null), 2600); return () => clearTimeout(t); }
  }, [toast]);

  function showToast(message, type = "success") { setToast({ message, type, id: Date.now() }); }

  const persist = useCallback((updater) => {
    setData((prev) => {
      const next = typeof updater === "function" ? updater(prev) : updater;
      saveMain(next).then((ok) => { if (!ok) showToast("Não foi possível salvar. Tente novamente.", "error"); });
      return next;
    });
  }, []);

  function navigate(s, p = {}) { setScreen(s); setParams(p); window.scrollTo?.(0, 0); }

  function handleAuth({ name, email }) {
    persist((prev) => ({ ...prev, auth: { loggedIn: true, name, email } }));
    if (data && data.onboardingCompleted) navigate("home");
    else navigate("onboarding");
  }

  function handleOnboardingFinish(ans) {
    const perfil = calcularPerfil(ans);
    persist((prev) => ({ ...prev, onboarding: ans, perfil, onboardingCompleted: true }));
    navigate("home");
  }

  function handleRestartOnboarding() {
    persist((prev) => ({ ...prev, onboardingCompleted: false }));
    navigate("onboarding");
  }

  function handleToggleTodayItem(dia, index, len) {
    persist((prev) => {
      const current = prev.program.checklists[dia] || Array(len).fill(false);
      const next = current.slice();
      next[index] = !next[index];
      return { ...prev, program: { ...prev.program, checklists: { ...prev.program.checklists, [dia]: next } } };
    });
  }

  function handleCompleteDay(dia) {
    let willFinish = false;
    persist((prev) => {
      if (prev.program.diasConcluidos.includes(dia)) return prev;
      const diasConcluidos = [...prev.program.diasConcluidos, dia].sort((a, b) => a - b);
      const currentDay = dia === prev.program.currentDay ? Math.min(dia + 1, 22) : prev.program.currentDay;
      if (diasConcluidos.length === 7) setCelebrate({ text: "Uma semana concluída! 🌸" });
      else if (diasConcluidos.length === 14) setCelebrate({ text: "Você está quase lá! ✨" });
      else if (diasConcluidos.length === 21) willFinish = true;
      return { ...prev, program: { ...prev.program, diasConcluidos, currentDay } };
    });
    if (willFinish) setTimeout(() => navigate("conclusion"), 500);
    else showToast("✨ Cuidado concluído!");
  }

  function handleToggleDailyChecklist(dateKey, index, len) {
    persist((prev) => {
      const current = prev.checklistDaily[dateKey] || Array(len).fill(false);
      const next = current.slice();
      next[index] = !next[index];
      return { ...prev, checklistDaily: { ...prev.checklistDaily, [dateKey]: next } };
    });
  }

  function handleAddProduct(p) {
    persist((prev) => ({ ...prev, products: [...prev.products, p] }));
  }
  function handleDeleteProduct(id) {
    persist((prev) => ({ ...prev, products: prev.products.filter((p) => p.id !== id) }));
  }

  function handleAddDiary(entry, photoDataUrl) {
    persist((prev) => ({ ...prev, diary: [...prev.diary, entry] }));
    if (photoDataUrl) savePhoto(`diary-photo-${entry.id}`, photoDataUrl);
  }

  async function handlePickEvolutionPhoto(day, file) {
    try {
      const dataUrl = await fileToCompressedDataUrl(file, 800, 0.8);
      setPhotos((p) => ({ ...p, [day]: dataUrl }));
      const ok = await savePhoto(`photo-day-${day}`, dataUrl);
      if (ok) showToast("Foto salva! 📸"); else showToast("Não foi possível salvar a foto.", "error");
    } catch {
      showToast("Não foi possível carregar a foto.", "error");
    }
  }

  function handleEditProfile(v) {
    persist((prev) => ({
      ...prev,
      auth: { ...prev.auth, name: v.name },
      onboarding: { ...prev.onboarding, tipoCabelo: v.tipoCabelo, quimica: v.quimica, frequenciaLavagem: v.frequenciaLavagem, calor: v.calor },
      perfil: calcularPerfil({ ...prev.onboarding, tipoCabelo: v.tipoCabelo, quimica: v.quimica, frequenciaLavagem: v.frequenciaLavagem, calor: v.calor }),
    }));
    showToast("Perfil atualizado!");
  }

  function handleLogout() {
    persist((prev) => ({ ...prev, auth: { loggedIn: false, name: "", email: "" } }));
    navigate("auth");
  }

  useEffect(() => {
    if (celebrate) { const t = setTimeout(() => setCelebrate(null), 2200); return () => clearTimeout(t); }
  }, [celebrate]);

  const showNav = ready && !["auth", "onboarding"].includes(screen);

  return (
    <div className="cds-root" style={{ minHeight: "100vh", background: C.bg, maxWidth: 480, margin: "0 auto", position: "relative" }}>
      <GlobalStyle />
      {!ready ? (
        <SplashScreen />
      ) : screen === "auth" ? (
        <AuthScreen onAuth={handleAuth} showToast={showToast} />
      ) : screen === "onboarding" ? (
        <OnboardingScreen initialAnswers={data.onboarding} name={data.auth.name} onFinish={handleOnboardingFinish} />
      ) : (
        <>
          {screen === "home" && <HomeScreen data={data} navigate={navigate} onToggleDailyChecklist={handleToggleDailyChecklist} />}
          {screen === "program" && <ProgramScreen data={data} navigate={navigate} />}
          {screen === "day-detail" && <DayDetailScreen data={data} dia={params.dia} navigate={navigate} onToggleItem={handleToggleTodayItem} onCompleteDay={handleCompleteDay} />}
          {screen === "routine" && <RoutineScreen data={data} />}
          {screen === "products" && <ProductsScreen data={data} onAddProduct={handleAddProduct} onDeleteProduct={handleDeleteProduct} showToast={showToast} />}
          {screen === "analyzer" && <AnalyzerScreen showToast={showToast} />}
          {screen === "diary" && <DiaryScreen data={data} onAddDiary={handleAddDiary} showToast={showToast} />}
          {screen === "evolution" && <EvolutionScreen data={data} photos={photos} onPickPhoto={handlePickEvolutionPhoto} />}
          {screen === "profile" && <ProfileScreen data={data} navigate={navigate} onLogout={handleLogout} onEditProfile={handleEditProfile} onRestartOnboarding={handleRestartOnboarding} />}
          {screen === "conclusion" && <ConclusionScreen data={data} navigate={navigate} />}
        </>
      )}

      {showNav && <BottomNav screen={screen} navigate={navigate} />}
      <Toast toast={toast} />

      {celebrate && (
        <div className="cds-anim-pop" style={{ position: "fixed", inset: 0, zIndex: 80, display: "flex", alignItems: "center", justifyContent: "center", background: "rgba(31,31,31,0.5)" }} onClick={() => setCelebrate(null)}>
          <div style={{ background: C.surface, borderRadius: 26, padding: "34px 30px", textAlign: "center", maxWidth: 300 }}>
            <div style={{ fontSize: 40, marginBottom: 10 }}>🎉</div>
            <div className="cds-display" style={{ fontSize: 19, fontWeight: 700 }}>{celebrate.text}</div>
          </div>
        </div>
      )}
    </div>
  );
}
