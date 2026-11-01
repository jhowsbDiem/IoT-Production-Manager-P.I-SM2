# Contribuindo com o IoT Production Manager

Este guia mostra como levar uma mudança da issue até o pull request: as
branches, os comandos git de cada etapa, o padrão dos commits e dos
comentários. A visão geral do projeto está no [README](README.md).

---

## 🔀 Fluxo de Trabalho

### As regras

1. Toda mudança começa por uma issue.
2. Cada funcionalidade maior tem a sua branch, criada a partir da `main`:
   `feature/<nome>`.
3. Cada issue da funcionalidade tem a sua branch, criada a partir da branch da
   funcionalidade: `feature/<N>-<assunto>` (ou `fix/`, `docs/`), onde `<N>`
   é o número da issue.
4. O pull request da issue vai para a **branch da funcionalidade, nunca para
   a `main`**.
5. O responsável pelo projeto revisa o pull request e junta com
   **"Rebase and merge"**, que preserva os seus commits.
6. Quando a funcionalidade fica pronta, a branch dela entra na `main` por um
   pull request com **merge commit**.

```
main ─────────────────────────────────────●──  merge commit
  └─ feature/<nome> ──●──●──●──●──●───────┘
                         └─ feature/<N>-<assunto> ──●──●
                              (pull request → "Rebase and merge")
```

### Passo a passo de uma issue

**1. Atualize a branch da funcionalidade.** O nome dela está na issue.

```
git fetch
git switch feature/<nome>
git pull
```

**2. Crie a branch da issue** a partir dela:

```
git switch -c feature/<N>-<assunto>
```

**3. Faça a mudança e confira à mão** que ela faz o que a issue pede.

**4. Commite.** Veja os arquivos alterados, adicione-os pelo nome e commite
no [padrão](#-padrão-de-commit):

```
git status
git add <arquivo>
git commit -m "feat: adicionar cadastro de clientes" -m "Closes #12"
```

Cada `-m` vira um parágrafo da mensagem: o primeiro é o título, o segundo o
rodapé. Uma issue pode ter vários commits; só o último leva `Closes #N`.

**5. Envie a branch.** Na primeira vez:

```
git push -u origin feature/<N>-<assunto>
```

Nas próximas, basta `git push`.

**6. Abra o pull request.** No GitHub, clique em **Compare & pull request**.
No campo **base**, troque `main` pela branch da funcionalidade
(`feature/<nome>`). Preencha a descrição que já vem pronta e clique em
**Create pull request**.

**7. Ajustes da revisão.** Se o responsável pedir mudanças, faça-as na mesma
branch, commite e rode `git push`: o pull request se atualiza sozinho.

**8. Se o responsável pedir para atualizar a sua branch** (porque a branch da
funcionalidade recebeu commits novos):

```
git fetch
git rebase origin/feature/<nome>
git push --force-with-lease
```

O `rebase` reaplica os seus commits em cima dos novos. Por isso o push
precisa do `--force-with-lease`, que só sobrescreve a sua própria branch no
GitHub. Use-o apenas nesse passo.

**9. Depois do merge,** volte para a branch da funcionalidade, atualize e
apague a branch da issue:

```
git switch feature/<nome>
git pull
git branch -D feature/<N>-<assunto>
```

O "Rebase and merge" recria os seus commits com identificadores novos. O git
não reconhece a branch antiga como juntada, por isso o `-D` maiúsculo. O
trabalho não se perde: ele já está na branch da funcionalidade. No GitHub,
apague a branch pelo botão **Delete branch** do pull request.

### Na sua primeira contribuição

Acrescente o seu nome na linha **Colaboradores** da seção
"✒️ Autores e Licença" do [README](README.md), num commit
`docs: adicionar <Seu Nome> aos colaboradores`, dentro do seu primeiro pull
request. Se a linha ainda não existir, crie-a logo abaixo da linha do
desenvolvedor:

```markdown
* **Colaboradores:** Seu Nome.
```

---

## 📝 Padrão de Commit

Conventional Commits, em português:

```
<tipo>: <descrição no imperativo, sem ponto final>

Closes #<issue>
```

- Tipos: `feat`, `fix`, `refactor`, `docs`, `perf`, `test`, `style`,
  `chore`, sem escopo entre parênteses.
- Corpo só quando o motivo não é óbvio pela issue: uma frase, entre o título
  e o rodapé.
- Uma issue pode ter vários commits. Só o último leva `Closes #N`.

Exemplo:

```
feat: adicionar cadastro de clientes

Closes #12
```

A issue fecha quando o commit com `Closes #N` chega na `main`, não quando o
seu pull request entra na branch da funcionalidade. Não feche a issue à mão.

---

## 💬 Padrão de Comentários

- O comentário explica **o porquê**, nunca o quê: o código já diz o quê.
- Sem referência a issues, tarefas ou conversas: isso fica no commit.
- Sem código comentado: o histórico fica no git.

```python
# Ruim: repete o código
# Pega a data e a hora atuais
agora = timezone.now()

# Bom: diz o que o código não diz
# timezone.now() e não datetime.now(): o Django guarda as datas em UTC, e
# datetime.now() devolveria a hora local sem fuso.
agora = timezone.now()
```

Funções e classes levam docstring (`"""..."""`), com a mesma regra.

---

## 📚 Onde Fica Cada Documento

- [`README.md`](README.md): visão geral e estado atual do projeto.
- `CONTRIBUTING.md`: este guia.
- [`arquitetura.md`](arquitetura.md): o domínio, a hierarquia de dados e as
  decisões em aberto.
- [`P.I.drawio`](P.I.drawio): o diagrama de fluxo; abre em
  [app.diagrams.net](https://app.diagrams.net).
- [`.github/pull_request_template.md`](.github/pull_request_template.md): o
  modelo que todo pull request novo traz preenchido.
- [`.github/ISSUE_TEMPLATE/`](.github/ISSUE_TEMPLATE/): os modelos de issue
  (bug, documentação, melhoria, funcionalidade, dívida técnica), oferecidos
  ao abrir uma issue nova.
- [`LICENSE`](LICENSE): a licença MIT.
