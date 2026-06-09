<div align="center">
  <img width="158" height="40" alt="logo" src="https://github.com/user-attachments/assets/2cb1382e-5191-4189-9cb5-980a11f9e891" />

  <h3>Timer Pomodoro com troca de contexto, trilha sonora e gerenciamento de tarefas</h3>

  ![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=flat&logo=html5&logoColor=white)
  ![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=flat&logo=css3&logoColor=white)
  ![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat&logo=javascript&logoColor=black)
</div>

---

## ✨ Sobre o Projeto

**Fokus** é um timer de produtividade baseado na técnica **Pomodoro**, com três modos de uso, trilha sonora ambiente, visual imersivo que muda de acordo com o contexto escolhido e um sistema completo de gerenciamento de tarefas com persistência via `localStorage`.

---

## 🎯 Modos de Uso

<div align="center">

| 🟣 Foco | 🟢 Descanso Curto | 🔵 Descanso Longo |
|:---:|:---:|:---:|
| <img width="422" height="422" alt="foco" src="https://github.com/user-attachments/assets/a2b03184-315d-4cb6-be73-2db56301c536" /> | <img width="422" height="422" alt="descanso-curto" src="https://github.com/user-attachments/assets/e55ae92f-6910-4f43-8c0d-b6b8d8e96af3" />| <img width="422" height="422" alt="descanso-longo" src="https://github.com/user-attachments/assets/27b1a302-74dd-4a00-9536-d107f0debab0"  />|
| **25 minutos** | **5 minutos** | **15 minutos** |
| Fundo roxo escuro | Fundo verde escuro | Fundo azul escuro |

</div>

---

## ⚙️ Funcionalidades

**Timer**
- ✅ Timer com contagem regressiva precisa (via `setInterval`)
- ✅ Três contextos: **Foco**, **Descanso Curto** e **Descanso Longo**
- ✅ Visual e fundo dinâmicos — mudam conforme o contexto ativo
- ✅ Música ambiente com toggle on/off (`loop` automático)
- ✅ Efeitos sonoros: play, pause e alarme ao finalizar
- ✅ Botão **Começar / Pausar** com ícone dinâmico
- ✅ Alerta ao término do tempo
- ✅ Design responsivo (desktop, tablet e mobile)

**Gerenciamento de Tarefas**
- ✅ Criar tarefas com descrição livre
- ✅ Editar tarefas existentes via `prompt`
- ✅ Selecionar a tarefa ativa em andamento
- ✅ Marcar tarefa como concluída automaticamente ao fim de um ciclo de foco
- ✅ Remover tarefas concluídas ou todas as tarefas
- ✅ Persistência completa via `localStorage` — tarefas salvas entre sessões

---

## 🗂️ Estrutura de Arquivos

```
fokus/
├── index.html          # Estrutura da aplicação
├── styles.css          # Estilos, temas e responsividade
├── script.js           # Lógica do timer, contextos e áudio
├── script-crud.js      # CRUD de tarefas e integração com localStorage
├── imagens/
│   ├── logo.png
│   ├── foco.png
│   ├── descanso-curto.png
│   ├── descanso-longo.png
│   ├── pattern.png
│   ├── play_arrow.png
│   ├── pause.png
│   ├── edit.png
│   ├── add_circle.png
│   ├── check.svg
│   ├── trash.svg
│   ├── more.svg
│   ├── music_note.png
│   └── favicon.ico
└── sons/
    ├── luna-rise-part-one.mp3   # Trilha ambiente
    ├── play.wav
    ├── pause.mp3
    └── beep.mp3
```

---

## 🚀 Como Executar

1. **Clone o repositório:**
   ```bash
   git clone https://github.com/seu-usuario/fokus.git
   ```

2. **Acesse a pasta:**
   ```bash
   cd fokus
   ```

3. **Abra no navegador:**
   ```bash
   open index.html
   # ou arraste o arquivo para o navegador
   ```

> ⚠️ Para o áudio funcionar corretamente, recomenda-se abrir via servidor local (ex: extensão **Live Server** no VS Code), pois alguns navegadores bloqueiam áudio em `file://`.

---

## 🧠 Como Funciona

### Troca de Contexto

Ao clicar em um dos três botões, o atributo `data-contexto` do `<html>` é atualizado. O CSS usa esse atributo como seletor para alterar o gradiente de fundo, e o JS troca a imagem do banner e o texto do título:

```js
html.setAttribute("data-contexto", contexto);
banner.setAttribute("src", `./imagens/${contexto}.png`);
```

```css
[data-contexto="foco"] {
  --main-bg-color: linear-gradient(180deg, #8B1FF8 0%, #041832 48.44%, #01080E 100%);
}
```

### Timer

O timer usa `setInterval` com intervalo de 1 segundo. Ao zerar, dispara um beep, exibe um alerta e — se o contexto for **foco** — despacha um evento customizado que marca a tarefa ativa como concluída:

```js
const contagemRegressiva = () => {
  if (tempoDecorridoEmSegundos <= 0) {
    audioTempoFinalizado.play();
    alert("Tempo finalizado!");
    const evento = new CustomEvent("FocoFinalizado");
    document.dispatchEvent(evento);
    zerar();
    return;
  }
  tempoDecorridoEmSegundos--;
  mostrarTempo();
};
```

### Gerenciamento de Tarefas e localStorage

As tarefas são armazenadas como um array de objetos no `localStorage` e reconstruídas no DOM a cada carregamento da página. Cada tarefa possui `descricao` e `completa`:

```js
let tarefas = JSON.parse(localStorage.getItem("tarefas")) || [];

function atualizarTarefas() {
  localStorage.setItem("tarefas", JSON.stringify(tarefas));
}
```

### Comunicação entre Scripts via Eventos Customizados

A conclusão automática de tarefas é feita através de um `CustomEvent`, desacoplando a lógica do timer da lógica de tarefas:

```js
// script.js — dispara ao fim do ciclo de foco
const evento = new CustomEvent("FocoFinalizado");
document.dispatchEvent(evento);

// script-crud.js — escuta e atualiza a tarefa selecionada
document.addEventListener("FocoFinalizado", () => {
  tarefaSelecionada.completa = true;
  atualizarTarefas();
});
```

---

## 🛠️ Tecnologias

| Tecnologia | Uso |
|---|---|
| HTML5 | Estrutura semântica da aplicação |
| CSS3 | Variáveis CSS, gradientes dinâmicos, toggle personalizado, responsividade |
| JavaScript ES6+ | Timer, troca de contexto, controle de áudio, CRUD de tarefas |
| localStorage | Persistência de tarefas entre sessões |
| CustomEvent API | Comunicação desacoplada entre módulos JS |
| Google Fonts | Fontes `Unbounded` (timer) e `Montserrat` (interface) |
| Web Audio API | Reprodução de sons via `new Audio()` |

---

## 🔮 Melhorias Futuras

- [ ] Contador de ciclos Pomodoro completados
- [ ] Notificação do sistema (Web Notifications API)
- [ ] Seleção de músicas / playlists
- [ ] Configuração personalizada dos tempos
- [ ] Salvar preferências no `localStorage`
- [ ] Modo claro

---

## 📚 Projeto de Curso

Este projeto foi desenvolvido como parte de dois cursos da **[Alura](https://www.alura.com.br)**:

**Curso 1 — Timer e manipulação do DOM**
> 📖 [JavaScript: manipulando elementos no DOM](https://cursos.alura.com.br/course/javascript-manipulando-elementos-dom)
> Foco em seletores, eventos, atributos e atualização dinâmica da interface. Resultado: o timer Pomodoro com troca de contexto e trilha sonora.

**Curso 2 — CRUD, eventos e localStorage**
> 📖 [JavaScript: explorando a manipulação de elementos e da localStorage](https://cursos.alura.com.br/course/javascript-manipulacao-elementos-localstorage)
> Foco em criação e manipulação dinâmica de elementos, persistência de dados com `localStorage`, eventos customizados e comunicação entre módulos. Resultado: o sistema completo de gerenciamento de tarefas integrado ao timer.

> 🎓 **Plataforma:** Alura

---

## 📄 Licença

Este projeto está sob a licença MIT. Imagens geradas por IA no Adobe Firefly.

---

<div align="center">
  Desenvolvido com 🎧 e foco total.
</div>
