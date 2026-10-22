# Guia 07: duas pessoas publicando no mesmo projeto

Este guia só interessa se **mais de uma pessoa** vai usar o `/deploy` no mesmo repositório, publicando
no mesmo projeto da Vercel. Se é só você, pode ignorar.

---

## O problema

O modo normal deste material (veja [`04-seguranca-token.md`](04-seguranca-token.md)) usa um token da
Vercel guardado no `.env` de cada máquina. Isso funciona bem para uma pessoa só.

Com duas pessoas, aparece um problema real: o token pertence a **uma conta** da Vercel. Não dá para
duas pessoas, cada uma com a própria conta, publicarem no mesmo projeto usando o mesmo token, a não
ser que o projeto esteja num time com plano Pro da Vercel. Sem isso, a saída errada costuma ser
"compartilhar o token por mensagem", o que é exatamente o que [`04-seguranca-token.md`](04-seguranca-token.md)
pede para nunca fazer.

## A saída: modo Git

A Vercel consegue publicar sozinha, sem token nenhum, quando o **projeto** está ligado direto ao
**repositório do GitHub**. Nesse modelo, cada push no GitHub já dispara a publicação. Cada pessoa usa
o próprio acesso ao GitHub (que ela já tem, como colaboradora do repositório), e ninguém precisa do
token da Vercel para publicar no dia a dia.

**Você mesmo configura isso, uma vez, quando decidir dividir o projeto com mais alguém.** Não precisa
pedir para a MIDDE: é o seu repositório e o seu projeto, e são só três passos.

### 1. Conecte o projeto da Vercel ao repositório do GitHub

No painel da Vercel: abra o projeto → **Settings** → **Git** → **Connect Git Repository** → escolha
o repositório que você criou a partir do template. A partir daqui, todo `git push` nesse repositório
avisa a Vercel.

### 2. Ative o modo Git neste repositório

**Prompt para colar no Claude Code:**

> Quero ativar o modo Git do `/deploy`, porque vou trabalhar com mais alguém neste projeto. Crie o
> arquivo `.deploy-via-git` vazio na raiz e commite.

Por trás, isso roda:

```bash
touch .deploy-via-git
git add .deploy-via-git
git commit -m "Ativa o modo Git do deploy"
git push
```

### 3. Convide a pessoa no GitHub

No repositório, no GitHub: **Settings** → **Collaborators** → **Add people** → digite o usuário ou
e-mail da pessoa, e confirme com permissão de escrita. Ela recebe um convite por e-mail e, ao
aceitar, já pode clonar o repositório e rodar `/deploy` normalmente, sem nunca precisar do seu token.

## O que muda no dia a dia

Nada na forma de usar: continua sendo só `/deploy`. A skill detecta o arquivo `.deploy-via-git` e
troca o fluxo sozinha. A diferença é só por trás:

| | Modo Token | Modo Git |
|---|---|---|
| Quem publica de fato | A CLI da Vercel, usando o token do `.env` | A própria Vercel, ao receber um `git push` |
| O que cada pessoa precisa | O mesmo token (problema com duas pessoas) | Só o próprio acesso ao GitHub |
| Onde fica a prévia | Numa branch temporária, publicada via CLI | Numa branch temporária, publicada pela Vercel ao ver o push |
| Como volta atrás | Promover deploy anterior no painel, ou `/deploy` de novo | `git revert` do commit em `main`, seguido de `git push` |

## Se der errado

- **O `/deploy` não acha o link da prévia depois de alguns minutos:** confira se o passo 1 (conectar
  o projeto ao GitHub, em Settings → Git no painel da Vercel) foi feito mesmo. Sem essa conexão, o
  `.deploy-via-git` sozinho não publica nada.
- **Quer voltar para o modo Token:** apague o `.deploy-via-git`, commite a remoção, e volte a usar o
  `.env`. Os dois modos não precisam ser definitivos.
