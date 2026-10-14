# Como atualizar a sua cópia do repositório de labs

Você criou o seu repositório com **Use this template**. Isso faz uma **cópia independente**: ela **não** é um *fork* e não
recebe as correções e os arquivos novos que o professor publica depois (por isso o botão "Sync fork" não aparece). Este
roteiro mostra como trazer essas mudanças para a sua cópia, ou quando é mais simples começar uma nova.

> **Aula 2:** não precisa atualizar. O que a Aula 2 usa (classificador, testes e workflows) já estava no template quando você criou a cópia
> na Aula 1. Mantenha a sua cópia (e o seu laboratório de CODEOWNERS) e só trabalhe numa branch se a sua `main` estiver protegida.
>
> **Antes da Aula 4: atualize.** O workflow do canary (`.github/workflows/canary-classificador.yml`) foi corrigido em 12/10/2026: a versão
> sem a correção **promovia a versão ruim** mesmo quando a análise pedia rollback.

## O que mudou no template

| Data | Arquivos | Para quê |
|---|---|---|
| 10/10/2026 | `roteiros/aula-02.md`, `roteiros/atualizar-sua-copia.md`, `README.md` | roteiros novos (leia direto no GitHub) |
| 12/10/2026 | `.github/workflows/canary-classificador.yml` | **correção do canary** (Aula 4) |
| 12/10/2026 | `.github/workflows/deploy-agente-aws.yml`, `roteiros/aula-06.md`, `roteiros/setup-aws-academy.md`, `README.md` | só textos: com o lab encerrado a URL da Lambda pode dar 502 |

**Atalho para o caso comum** (você só fez o laboratório da Aula 1 e não editou nada além do `CODEOWNERS` e do teste dele):

```bash
git remote add template https://github.com/guilherme-argentino/aie-devops-labs.git   # só na primeira vez
git fetch template
git checkout template/main -- .github/workflows/canary-classificador.yml roteiros
git add -A && git commit -m "Atualiza do template" && git push
```

Testado numa cópia simulada (histórico independente, estado da Aula 1): o canary da cópia fica com a correção e os roteiros ficam iguais aos do template.
Não traga `.github` inteiro: sobrescreve o seu `CODEOWNERS`.

## Qual caminho escolher

| Situação | Caminho |
|---|---|
| Você não configurou nada na cópia (sem CODEOWNERS nem proteção da `main`) | **A · Criar uma nova cópia** (mais simples) |
| Você tem trabalho seu na cópia e quer só **trazer arquivos novos ou corrigidos** | **B · Trazer arquivos do template** (recomendado) |
| Você é experiente em Git e quer o histórico mesclado | C · `merge` (avançado, dá conflito em quase tudo) |

## A · Criar uma nova cópia

1. Abra `github.com/guilherme-argentino/aie-devops-labs`, **logado no GitHub**, e clique em **Use this template → Create a new repository**
   (público).
2. Recoloque os *secrets* da AWS (`roteiros/setup-aws-academy.md`) e, se precisar, adicione de novo a sua dupla como
   colaboradora.

A cópia antiga continua existindo; você só deixa de usá-la.

## B · Trazer arquivos do template (sem apagar o seu trabalho)

No terminal do Codespace, dentro do seu repositório:

```bash
# 1) Só na primeira vez: aponte para o repositório do professor (apelido "template")
git remote add template https://github.com/guilherme-argentino/aie-devops-labs.git

# 2) Baixe o que o professor publicou
git fetch template

# 3) Veja o que mudou (esquerda = a sua cópia, direita = o template)
git diff --stat HEAD template/main
git diff --name-only HEAD template/main
```

Para cada arquivo ou pasta da lista que **você não editou**, traga a versão do template:

```bash
git checkout template/main -- classificador/deploy/publicar.sh roteiros
git status
```

Cuidado com os arquivos que **você** editou (por exemplo `.github/CODEOWNERS` e o teste do laboratório da Aula 1): **não** os
traga com o comando acima, porque ele sobrescreve a sua versão. Compare antes, e copie à mão só o que quiser:

```bash
git diff HEAD template/main -- .github/CODEOWNERS
```

Se um arquivo foi **apagado** no template, ele aparece como `deleted` no `git diff` e continua na sua cópia: apague-o à mão se não precisar.

Depois, registre e envie:

```bash
git add -A
git commit -m "Atualiza do template"
git push
```

### Se a sua `main` está protegida (laboratório da Aula 1)

O `git push` direto na `main` será recusado. Faça numa branch e abra um Pull Request:

```bash
git switch -c atualiza-template
# ... os comandos "git checkout template/main -- ..." e o commit acima ...
git push -u origin atualiza-template
gh pr create --fill
```

Peça a aprovação à sua dupla (ninguém aprova o próprio PR) e faça o merge.

### Se o `push` for recusado por causa de `.github/workflows`

Mudanças em workflows podem exigir uma permissão extra do token. Se o `git push` falhar com uma mensagem sobre `workflow`,
edite aquele arquivo pelo navegador, no GitHub, colando o conteúdo da versão do template.

## C · `merge` (avançado)

```bash
git fetch template
git merge template/main --allow-unrelated-histories
```

Como as duas cópias não têm histórico em comum, o Git marca **conflito em todo arquivo que mudou** (`add/add`), mesmo nos que
você nunca tocou. Resolva um a um (ou use o caminho B).

## Quando atualizar

Quando o professor avisar que o template mudou, ou quando uma aula nova usar arquivos que a sua cópia não tem. Se a sua cópia tem o
laboratório de CODEOWNERS e a proteção da `main` (que a Aula 3 reaproveita), prefira o caminho **B**: o **A** obriga a refazer essa
configuração.
