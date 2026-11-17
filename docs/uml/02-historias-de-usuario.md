# Sprint 1 — Histórias de Usuário

Sistema Web de Aluguel de Automóveis

**Legenda de prioridade:** Alta (obrigatória e essencial), Média (obrigatória), Baixa (complementar).

## Resumo

| ID | História | Perfil | Prioridade | Caso de uso |
|----|----------|--------|-----------|-------------|
| HU01 | Cadastro de cliente | Visitante | Alta | UC01 |
| HU02 | Cadastro de agente | Visitante | Alta | UC01, UC14 |
| HU03 | Login e controle de acesso | Usuário | Alta | UC02 |
| HU04 | Gerenciar dados do cliente | Cliente | Alta | UC03 |
| HU05 | Gerenciar vínculos de renda | Cliente | Alta | UC04 |
| HU06 | Cadastrar automóvel | Cliente / Agente | Alta | UC05 |
| HU07 | Consultar, editar e excluir automóvel | Cliente / Agente | Média | UC05 |
| HU08 | Criar pedido de aluguel | Cliente | Alta | UC06 |
| HU09 | Consultar pedidos e histórico | Cliente | Alta | UC07 |
| HU10 | Editar pedido | Cliente | Média | UC08 |
| HU11 | Cancelar pedido | Cliente | Alta | UC09 |
| HU12 | Consultar pedidos pendentes | Agente | Alta | UC10 |
| HU13 | Registrar avaliação financeira | Agente | Alta | UC11 |
| HU14 | Confirmar execução do contrato | Agente | Média | UC12 |
| HU15 | Registrar contrato de crédito | Agente Banco | Média | UC13 |
| HU16 | Editar dados do agente | Agente | Baixa | UC14 |

---

## HU01 — Cadastro de cliente

**Como** visitante, **quero** me cadastrar como cliente informando nome, CPF, RG, endereço e profissão, **para** poder acessar o sistema e solicitar aluguéis.

**Critérios de aceitação**
- [ ] Dado que preencho todos os campos obrigatórios com dados válidos, quando envio o formulário, então meu cadastro é criado com perfil **Cliente**.
- [ ] Dado que informo um CPF já cadastrado, então o sistema recusa e informa o motivo.
- [ ] Dado que deixo campo obrigatório vazio, então o sistema indica quais campos corrigir.
- [ ] A senha é armazenada com hash, nunca em texto puro.

## HU02 — Cadastro de agente

**Como** visitante representando uma empresa ou banco, **quero** me cadastrar como agente informando nome, CNPJ, endereço e tipo (Empresa ou Banco), **para** poder analisar pedidos de aluguel.

**Critérios de aceitação**
- [ ] O tipo do agente é obrigatório e restrito a *Empresa* ou *Banco*.
- [ ] O CNPJ é único e validado.
- [ ] Após o cadastro, o usuário tem perfil **Agente**.

## HU03 — Login e controle de acesso

**Como** usuário cadastrado, **quero** fazer login, **para** acessar apenas as funcionalidades do meu perfil.

**Critérios de aceitação**
- [ ] Sem autenticação, qualquer rota protegida redireciona para o login.
- [ ] Cliente não acessa telas de avaliação financeira.
- [ ] Agente não acessa telas de criação de pedido.
- [ ] Credenciais inválidas geram mensagem genérica, sem revelar qual campo falhou.
- [ ] É possível encerrar a sessão (logout).

## HU04 — Gerenciar dados do cliente

**Como** cliente, **quero** consultar, editar e excluir meu cadastro, **para** manter meus dados atualizados ou encerrar minha conta.

**Critérios de aceitação**
- [ ] Vejo e edito apenas os meus dados.
- [ ] Ao excluir, o sistema pede confirmação.
- [ ] Cliente com pedido em *Contrato em execução* não pode ser excluído.

## HU05 — Gerenciar vínculos de renda

**Como** cliente, **quero** informar até três entidades empregadoras e o rendimento de cada uma, **para** que o agente avalie minha capacidade financeira.

**Critérios de aceitação**
- [ ] Posso adicionar, editar e remover vínculos.
- [ ] Ao tentar adicionar um quarto vínculo, o sistema bloqueia e explica o limite de 3.
- [ ] Rendimento deve ser um valor numérico maior ou igual a zero.

## HU06 — Cadastrar automóvel

**Como** cliente ou agente, **quero** cadastrar um automóvel com marca, modelo, ano, placa, matrícula/identificador e proprietário, **para** que ele possa ser alugado.

**Critérios de aceitação**
- [ ] Todos os campos são obrigatórios.
- [ ] O proprietário é do tipo cliente, empresa ou banco.
- [ ] A placa e a matrícula são únicas.
- [ ] Cliente só cadastra automóvel em seu próprio nome.

## HU07 — Consultar, editar e excluir automóvel

**Como** cliente ou agente, **quero** consultar, editar e excluir automóveis sob minha responsabilidade, **para** manter o catálogo correto.

**Critérios de aceitação**
- [ ] A lista pode ser filtrada por marca, modelo ou placa.
- [ ] Automóvel vinculado a pedido em *Em análise*, *Aprovado* ou *Contrato em execução* não pode ser excluído.
- [ ] Todo automóvel continua com proprietário após a edição.

## HU08 — Criar pedido de aluguel

**Como** cliente, **quero** criar um pedido escolhendo automóvel e período, **para** solicitar o aluguel.

**Critérios de aceitação**
- [ ] O pedido registra cliente, automóvel, período e data de criação automaticamente.
- [ ] O status inicial é **Em análise**.
- [ ] A data final deve ser posterior à data inicial, e a inicial não pode estar no passado.
- [ ] O pedido pertence ao cliente autenticado, sem possibilidade de escolher outro cliente.

## HU09 — Consultar pedidos e histórico

**Como** cliente, **quero** ver a lista dos meus pedidos com status atual e histórico de mudanças, **para** acompanhar o andamento.

**Critérios de aceitação**
- [ ] Vejo apenas os meus pedidos.
- [ ] Cada pedido mostra status, automóvel, período e agente responsável (quando houver).
- [ ] O histórico lista cada mudança de status com data.
- [ ] Posso ver o parecer e a justificativa após a avaliação.

## HU10 — Editar pedido

**Como** cliente, **quero** editar o automóvel ou o período do meu pedido, **para** corrigir dados antes da análise.

**Critérios de aceitação**
- [ ] Só é possível editar pedidos com status **Em análise**.
- [ ] Pedidos aprovados, reprovados, cancelados ou em execução ficam somente leitura.
- [ ] Não consigo editar pedido de outro cliente.

## HU11 — Cancelar pedido

**Como** cliente, **quero** cancelar um pedido meu, **para** desistir do aluguel.

**Critérios de aceitação**
- [ ] O status muda para **Cancelado** e o histórico registra a mudança.
- [ ] Pedido cancelado não pode ser aprovado nem colocado em execução.
- [ ] Pedido já em execução não pode ser cancelado por esta funcionalidade.

## HU12 — Consultar pedidos pendentes

**Como** agente, **quero** listar os pedidos com status *Em análise*, **para** escolher quais avaliar.

**Critérios de aceitação**
- [ ] A lista mostra cliente, automóvel, período, data de criação e rendimentos declarados.
- [ ] Pedidos cancelados não aparecem na lista.

## HU13 — Registrar avaliação financeira

**Como** agente, **quero** registrar um parecer (aprovado ou reprovado) com justificativa, **para** decidir sobre o pedido.

**Critérios de aceitação**
- [ ] A avaliação grava agente responsável, data da análise, parecer e observação.
- [ ] Parecer *aprovado* muda o status para **Aprovado**. Parecer *reprovado* muda para **Reprovado**.
- [ ] A justificativa é obrigatória em caso de reprovação.
- [ ] Pedido cancelado não pode ser avaliado.
- [ ] Apenas usuários com perfil Agente executam esta ação.

## HU14 — Confirmar execução do contrato

**Como** agente, **quero** confirmar a execução de um pedido aprovado, **para** iniciar o contrato.

**Critérios de aceitação**
- [ ] Só pedidos com status **Aprovado** podem ir para **Contrato em execução**.
- [ ] A mudança é registrada no histórico.

## HU15 — Registrar contrato de crédito

**Como** agente do tipo banco, **quero** associar um contrato de crédito a um pedido, **para** formalizar o financiamento.

**Critérios de aceitação**
- [ ] O contrato registra pedido, banco concedente, valor financiado, prazo e situação do crédito.
- [ ] Agente do tipo Empresa não consegue criar contrato de crédito.
- [ ] O contrato é opcional: pedidos sem contrato seguem normalmente.
- [ ] Só é possível para pedido aprovado e não cancelado.
- [ ] Cada pedido tem no máximo um contrato de crédito.

## HU16 — Editar dados do agente

**Como** agente, **quero** consultar e editar os dados da minha empresa ou banco, **para** manter o cadastro atualizado.

**Critérios de aceitação**
- [ ] Posso editar nome, endereço e dados de contato.
- [ ] CNPJ e tipo não podem ser alterados depois do cadastro.

---

## Status dos pedidos (referência)

| Status | Significado |
|--------|-------------|
| Em análise | Criado pelo cliente, aguardando avaliação. |
| Aprovado | Aprovado financeiramente pelo agente. |
| Reprovado | Recusado na análise financeira. |
| Cancelado | Cancelado pelo cliente. |
| Contrato em execução | Aprovado e confirmado para execução. |

