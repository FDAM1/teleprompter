# Prompter

Teleprompter que roda no celular, no tablet e no desktop com o mesmo código.
É um **PWA**: abre no navegador e pode ser instalado como aplicativo, funcionando
offline depois da primeira abertura.

Sem build, sem dependências, sem servidor de aplicação — é HTML, CSS e JavaScript.

---

## Rodando

**Jeito rápido (desktop):** abra `index.html` no navegador.
Nesse modo o app funciona, mas *não* instala nem usa o microfone: navegadores
bloqueiam essas capacidades em `file://`.

**Jeito completo (recomendado):** sirva a pasta por HTTP.

```bash
python -m http.server 8123 --directory prompter
```

Depois abra `http://localhost:8123`.

Para usar no celular, publique a pasta em qualquer hospedagem estática com HTTPS
(GitHub Pages, Netlify, Vercel, Cloudflare Pages). **HTTPS é obrigatório** para
instalar o app e para o microfone funcionar.

### Instalar como aplicativo

- **Android (celular e tablet):** abra o site no Chrome → menu ⋮ → *Instalar app*.
  Ele ganha ícone na tela inicial, abre em tela cheia e roda offline.
- **Desktop (Chrome/Edge):** ícone de instalar na barra de endereço, ou menu → *Instalar*.
- **iPhone/iPad:** Safari → Compartilhar → *Adicionar à Tela de Início*.
  Funciona, mas o Safari não tem reconhecimento de fala — veja a tabela abaixo.

### Gerar um APK para a Play Store (opcional)

O PWA vira um app Android nativo com [Bubblewrap](https://github.com/GoogleChromeLabs/bubblewrap),
sem reescrever nada:

```bash
npx @bubblewrap/cli init --manifest https://SEU-DOMINIO/manifest.webmanifest
npx @bubblewrap/cli build
```

---

## O que dá para fazer

### Biblioteca
Busca, fixar no topo, renomear, duplicar, excluir, baixar como `.txt` e backup
completo em `.json`. Um roteiro se cria digitando, colando ou abrindo um arquivo
`.txt`/`.md`.

**Editar depois de criado:** toque no roteiro na lista para abrir o editor, ou use
o lápis na barra de cima durante a apresentação. O texto salva sozinho enquanto
você digita.

### Apresentação
- Ritmo em **palavras por minuto** (60 a 280). O tempo total é calculado a partir da
  contagem de palavras, então o cronômetro bate com a leitura real.
- Tamanho do texto, entrelinha, margens, alinhamento e tipografia (Inter ou serifada).
- Quatro combinações de cor: **Estúdio**, **Penumbra**, **Papel** e **Alto contraste**.
- **Linha guia** com altura ajustável e **modo foco**, que apaga o texto fora da
  linha de leitura.
- **Espelho horizontal** (para vidro de teleprompter) e **espelho vertical**
  (para o aparelho montado de cabeça para baixo). Os controles nunca espelham.
- Contagem regressiva de 3 segundos, barra de progresso arrastável e tela mantida acesa.
- A frase que está na linha guia fica em destaque; o que já passou escurece.

### Velocidade pela voz
Três modos, em *Ajustes → Ritmo*:

| Modo | O que faz |
|---|---|
| **Desligado** | Rola no ritmo fixo que você definiu. |
| **Detectar fala** | O texto anda enquanto você fala e para quando você silencia. Só mede o volume do microfone — funciona em qualquer navegador que dê acesso ao microfone. |
| **Seguir o texto** | Reconhece as palavras ditas, acha onde você está no roteiro e ajusta a velocidade sozinho: acelera quando você corre, freia quando você se demora, para quando você pausa. |

"Seguir o texto" usa a Web Speech API:

| Navegador | Detectar fala | Seguir o texto |
|---|---|---|
| Chrome / Edge (desktop) | sim | sim |
| Chrome (Android) | sim | sim |
| Firefox | sim | não |
| Safari (macOS/iOS) | sim | não |

Quando o reconhecimento não existe, o app avisa e cai para "detectar fala" sozinho.

### Teclado e pedal
Pedais bluetooth de virar página mandam setas ou espaço, então funcionam direto:

| Tecla | Ação |
|---|---|
| espaço / enter | play / pause |
| ↑ ↓ | ritmo −/+ 5 ppm |
| ← → | recuar / avançar 5 s |
| Home | voltar ao início |
| E | espelho horizontal |
| F | tela cheia |
| Esc | sair |

No celular, tocar na área do texto também dá play/pause.

---

## Onde ficam os dados

Tudo no `localStorage` do próprio aparelho — nada sai dele, não há conta nem servidor.
Por isso: **limpar os dados do site apaga os roteiros**. Use *Exportar backup* antes
de trocar de aparelho ou navegador.

O reconhecimento de fala do Chrome, quando ligado, envia o áudio para os servidores
do Google (é assim que a Web Speech API funciona). O modo "detectar fala" não envia
nada: processa o volume localmente.

## Arquivos

```
index.html              telas e ícones SVG
css/app.css             design inteiro (tokens, temas, componentes)
js/store.js             persistência, roteiros, utilidades de texto
js/voice.js             microfone: medidor de volume e reconhecimento de fala
js/prompter.js          motor de rolagem, medição e sincronia com a fala
js/sheets.js            folhas inferiores (ajustes e menus)
js/app.js               navegação, biblioteca, editor e controles
sw.js                   cache offline (stale-while-revalidate)
manifest.webmanifest    metadados de instalação
tools/make-icons.js     regenera os PNGs dos ícones
dist/prompter.html      versão em arquivo único
```

Ao publicar uma versão nova, troque `prompter-v1` em `sw.js` por `prompter-v2`
para forçar os aparelhos a baixarem tudo de novo.
