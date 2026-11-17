# Sprint 1 — Diagrama de Classes

Sistema Web de Aluguel de Automóveis

## 1. Diagrama

```mermaid
classDiagram
    direction TB

    class Usuario {
        <<abstract>>
        -String id
        -String email
        -String senhaHash
        -Perfil perfil
        +autenticar(email, senha) boolean
    }

    class Cliente {
        -String nome
        -String cpf
        -String rg
        -String endereco
        -String profissao
        +adicionarVinculo(v VinculoEmpregaticio) void
        +removerVinculo(v VinculoEmpregaticio) void
    }

    class VinculoEmpregaticio {
        -String entidadeEmpregadora
        -BigDecimal rendimento
    }

    class Agente {
        -String nome
        -String cnpj
        -String endereco
        -TipoAgente tipo
        +ehBanco() boolean
    }

    class Proprietario {
        <<interface>>
        +getId() String
        +getNome() String
    }

    class Automovel {
        -String id
        -String marca
        -String modelo
        -int ano
        -String placa
        -String matricula
        -TipoProprietario tipoProprietario
        -String proprietarioId
    }

    class PedidoAluguel {
        -String id
        -LocalDate dataInicio
        -LocalDate dataFim
        -LocalDateTime dataCriacao
        -StatusPedido status
        +cancelar() void
        +aprovar(a AvaliacaoFinanceira) void
        +reprovar(a AvaliacaoFinanceira) void
        +iniciarExecucao() void
    }

    class AvaliacaoFinanceira {
        -String id
        -LocalDateTime dataAnalise
        -Parecer parecer
        -String observacao
    }

    class ContratoCredito {
        -String id
        -BigDecimal valorFinanciado
        -int prazoMeses
        -SituacaoCredito situacao
    }

    class HistoricoStatus {
        -StatusPedido statusAnterior
        -StatusPedido statusNovo
        -LocalDateTime data
    }

    class Perfil {
        <<enumeration>>
        CLIENTE
        AGENTE
    }
    class TipoAgente {
        <<enumeration>>
        EMPRESA
        BANCO
    }
    class TipoProprietario {
        <<enumeration>>
        CLIENTE
        EMPRESA
        BANCO
    }
    class StatusPedido {
        <<enumeration>>
        EM_ANALISE
        APROVADO
        REPROVADO
        CANCELADO
        CONTRATO_EM_EXECUCAO
    }
    class Parecer {
        <<enumeration>>
        APROVADO
        REPROVADO
    }
    class SituacaoCredito {
        <<enumeration>>
        SOLICITADO
        CONCEDIDO
        QUITADO
        CANCELADO
    }

    Usuario <|-- Cliente
    Usuario <|-- Agente
    Proprietario <|.. Cliente
    Proprietario <|.. Agente

    Cliente "1" *-- "0..3" VinculoEmpregaticio : possui
    Cliente "1" --> "0..*" PedidoAluguel : solicita
    Agente "0..1" <-- "0..*" PedidoAluguel : responsavel
    PedidoAluguel "0..*" --> "1" Automovel : refere-se a
    Proprietario "1" <-- "0..*" Automovel : pertence a

    PedidoAluguel "1" *-- "0..1" AvaliacaoFinanceira : possui
    AvaliacaoFinanceira "0..*" --> "1" Agente : realizada por
    PedidoAluguel "1" *-- "0..1" ContratoCredito : financiado por
    ContratoCredito "0..*" --> "1" Agente : concedido por banco
    PedidoAluguel "1" *-- "0..*" HistoricoStatus : registra

    Usuario ..> Perfil
    Agente ..> TipoAgente
    Automovel ..> TipoProprietario
    PedidoAluguel ..> StatusPedido
    AvaliacaoFinanceira ..> Parecer
    ContratoCredito ..> SituacaoCredito
```

## 2. Dicionário de classes

| Classe | Responsabilidade |
|--------|------------------|
| **Usuario** | Classe abstrata com credenciais e perfil. Base do controle de acesso. |
| **Cliente** | Pessoa física. Armazena nome, CPF, RG, endereço, profissão e seus vínculos de renda. |
| **VinculoEmpregaticio** | Entidade empregadora e rendimento associado. Composição de Cliente, máximo de 3. |
| **Agente** | Empresa ou Banco que avalia pedidos. O tipo é definido por `TipoAgente`. |
| **Proprietario** | Interface que permite a Cliente e Agente (empresa ou banco) serem donos de automóveis. |
| **Automovel** | Veículo com marca, modelo, ano, placa, matrícula e proprietário obrigatório. |
| **PedidoAluguel** | Solicitação do cliente. Concentra status, período e a transição de estados. |
| **AvaliacaoFinanceira** | Parecer do agente com data e justificativa. |
| **ContratoCredito** | Financiamento opcional do pedido, concedido por agente do tipo banco. |
| **HistoricoStatus** | Registro de cada mudança de status, usado na consulta de histórico (UC07). |

## 3. Restrições (OCL em linguagem natural)

| ID | Restrição |
|----|-----------|
| R1 | `Cliente.vinculos.size() <= 3` |
| R2 | `ContratoCredito.banco.tipo = BANCO` |
| R3 | `Automovel.proprietarioId` é obrigatório e deve existir conforme `tipoProprietario`. |
| R4 | `AvaliacaoFinanceira.agente` é obrigatório, e só `Perfil.AGENTE` avalia. |
| R5 | Pedido com status `CANCELADO` não pode ser aprovado nem ir para `CONTRATO_EM_EXECUCAO`. |
| R6 | Um cliente só acessa pedidos em que `pedido.cliente = clienteLogado`. |
| R7 | `PedidoAluguel.dataFim > dataInicio`. |
| R8 | `ContratoCredito` só existe para pedido `APROVADO` ou `CONTRATO_EM_EXECUCAO`. |
| R9 | `Agente.tipo = BANCO` ou `EMPRESA`, e o CNPJ é único. |

## 4. Máquina de estados do pedido

```mermaid
stateDiagram-v2
    [*] --> EM_ANALISE : cliente cria pedido
    EM_ANALISE --> APROVADO : agente aprova
    EM_ANALISE --> REPROVADO : agente reprova
    EM_ANALISE --> CANCELADO : cliente cancela
    APROVADO --> CONTRATO_EM_EXECUCAO : agente confirma
    APROVADO --> CANCELADO : cliente cancela
    REPROVADO --> [*]
    CANCELADO --> [*]
    CONTRATO_EM_EXECUCAO --> [*]
```

| De | Para | Quem | Condição |
|----|------|------|----------|
| (novo) | Em análise | Cliente | Dados válidos |
| Em análise | Aprovado | Agente | Avaliação com parecer aprovado |
| Em análise | Reprovado | Agente | Avaliação com parecer reprovado e justificativa |
| Em análise / Aprovado | Cancelado | Cliente | Dono do pedido |
| Aprovado | Contrato em execução | Agente | Pedido não cancelado |

## 5. Mapeamento para MongoDB


| Coleção | Conteúdo | Observação |
|---------|----------|------------|
| `usuarios` | Cliente e Agente (campo `perfil`) | Os vínculos de renda ficam **embutidos** no documento do cliente (máx. 3). |
| `automoveis` | Automóvel com `tipoProprietario` e `proprietarioId` | Referência ao `usuarios._id`. |
| `pedidos` | Pedido com `clienteId`, `automovelId`, `agenteId` | Avaliação, contrato de crédito e histórico de status **embutidos**. |

## 6. Decisões de modelagem

1. **Herança Usuario → Cliente / Agente:** os dois perfis compartilham autenticação e acesso.
2. **Agente com enum `TipoAgente`** em vez de subclasses Empresa e Banco: o comportamento é o mesmo, e a diferença (apenas banco concede crédito) é uma regra validada em R2.
3. **Interface Proprietario:** permite que o automóvel pertença a cliente, empresa ou banco sem duplicar classes.
4. **AvaliacaoFinanceira e ContratoCredito como composição** do pedido: não existem sem ele.
5. **HistoricoStatus** atende ao requisito de consulta de histórico dos pedidos.
