# Aula 2 — Pipeline de CI/CD do classificador

**Onde roda:** GitHub Actions + AWS Academy (Lambda + ECR). **Tempo:** ~22 min de prática, logo depois do intervalo e de uma **demo de 5 min** do professor (template, Codespace, `git push`, CI e o gate reprovando). Em **salas do Teams**, com o tempo na tela; o professor entra nas salas sob demanda.

O classificador rotula avaliações de produto (positiva, negativa, neutra). Você vai fazer o pipeline
de sete etapas da aula rodar de verdade. Não usa Git no dia a dia? Forme dupla com quem usa: quem usa digita, quem não usa decide os
critérios (qual regra bloqueia, qual só alerta).

**Antes de começar:** use **Use this template** logado no GitHub (sem login o botão não aparece), crie a cópia **pública** e abra
um Codespace; ele aparece como "disponível" antes de terminar, então espere o terminal acabar de configurar (~8 min).

## 1. Ver o pipeline passar

```bash
git push                                # o CI dispara sozinho
gh run watch                            # ou aba Actions → "Aula 2 · CI do classificador"
```

Cada etapa é um job; `needs` desenha a ordem. O **model gate** (`classificador/model_gate.py`)
compara o F1 do candidato com `classificador/producao.json` e com pisos por classe.

## 2. Ver o gate barrar um lote ruim

O arquivo `classificador/dados/lote-novo.csv` "parece normal" (passa no schema), mas 30% dos textos
são só emojis e a classe *neutra* caiu para ~9%. Teste na ordem do pipeline:

```bash
DADOS=classificador/dados/lote-novo.csv pytest classificador/tests/test_dados.py   # etapa 3: já barra
python classificador/treino.py --dados classificador/dados/lote-novo.csv           # etapa 4
python classificador/model_gate.py artefatos/metricas.json                          # etapa 5: reprova
```

**Perguntas:** o que barrou o lote primeiro, o teste de dados ou o gate? Qual regra é de bloqueio e
qual é só alerta (`warnings.warn` em `test_dados.py`)? Mude os limites e justifique.

## 3. Deploy no AWS Academy

Pré-requisito: `roteiros/setup-aws-academy.md` (passos 1 a 3).

```bash
gh workflow run "Aula 2 · Deploy do classificador (AWS Lambda)"
```

O workflow refaz treino e gate, publica a imagem no ECR, cria uma **versão** do Lambda, aponta o
alias `producao` e testa a URL pública. A URL aparece no resumo da execução.

## Desafio

Adicione ao `test_dados.py` uma checagem de **tamanho do texto** (média de palavras por avaliação
dentro de ±30% da base de treino) e faça o lote-novo falhar também por ela.

## Se você usa a cópia da Aula 1 (a `main` está protegida)

A cópia da Aula 1 **já traz tudo de hoje**: não crie outra nem refaça o laboratório de CODEOWNERS. Como a `main` está protegida, o
`git push` direto é recusado: trabalhe numa branch e abra um Pull Request (o CI roda no PR do mesmo jeito):

```bash
git switch -c aula-2
git push -u origin aula-2
gh pr create --fill
```

O deploy (etapa 7) é manual, com `gh workflow run`, então não depende de `push` na `main`.

## Quando travar

- Sem o botão *Use this template*: você não está logado no GitHub.
- Terminal vazio ou com erro logo após abrir o Codespace: espere terminar de configurar (~8 min).
- Pediram senha ou token: não digite nem diga em voz alta; a aula é gravada. Chame o professor.
- Credenciais da AWS expiradas ou serviço bloqueado: use o **plano B** (rodar o mesmo contêiner localmente); o aprendizado do CI e do gate não depende da nuvem.
