# 🚗 Sistema de Gestão de Estacionamento Inteligente em Python 💻

Um sistema de terminal robusto e modular desenvolvido em Python para o gerenciamento de pátios de estacionamento. O projeto simula operações reais de controle de fluxo de veículos, aplicando persistência de dados em arquivos, validações rigorosas por expressões regulares e uma rotina probabilística de simulação de incidentes.

---

## 🚀 Funcionalidades Principais

* **Persistência de Dados Automatizada:** Utiliza arquivos no formato JSON como banco de dados secundário, garantindo a retenção das informações mesmo após o encerramento do script.
* **Mapa de Vagas em Tempo Real:** Interface textual dinamizada que renderiza o estado de ocupação do pátio, com tratamento visual para vagas exclusivas (PCD) e suporte a compatibilidade retroativa de registros.
* **Sanitização Inteligente de Inputs (UX):** Filtros defensivos baseados em manipulação de strings que limpam entradas acidentais do usuário (ex: removem textos de campos numéricos), evitando falhas catastróficas por exceções.
* **Validação por Expressões Regulares (Regex):** Validador estrito de placas automotivas compatível tanto com o formato tradicional brasileiro (ABC-1234) quanto com o padrão Mercosul (ABC1D23).
* **Algoritmo Probabilístico de Colisão:** Rotina que calcula dinamicamente o adensamento do estacionamento e simula a chance de pequenos incidentes (batidas) nas manobras, dependendo da ocupação das vagas adjacentes.
* **Auditoria Oculta (Logging):** Registro assíncrono de eventos e alertas críticos em arquivo local de log (`.log`), simulando práticas de governança de sistemas corporativos.

---

## 🛠️ Lógica de Operação

### 1. Estrutura do Mapa de Vagas
O estacionamento é composto por 15 vagas gerenciadas em memória por meio de dicionários chave-valor. Ao solicitar a exibição das vagas (Opção 3), o sistema renderiza uma interface limpa:

```text
==================================================
             MAPA DO ESTACIONAMENTO
==================================================
Vaga 01: [ Livre (PCD) ]
Vaga 02: [ Livre ]
Vaga 03: [ Ocupada: ABC1D23 | Carro | Marca: Toyota ]
Vaga 04: [ Livre ]
...
--------------------------------------------------
1 ocupadas | 14 livres
==================================================
```

2. Fluxo Probabilístico de Colisão
Ao estacionar um veículo, o algoritmo analisa as vagas diretamente vizinhas (adjacência inferior e superior):
* 0 vizinhos ocupados: 0% de chance de colisão
* 1 vizinho ocupado: 15% de chance de colisão.
* 2 vizinhos ocupados: 35% de chance de colisão.

Caso a colisão seja detectada de forma aleatória dentro destas faixas de probabilidade, o usuário recebe um aviso em tela e o evento é silenciosamente registrado no arquivo de auditoria externa.

## 🖥️ Como Funciona a Interface (Menu de Comandos)
O sistema roda inteiramente pelo terminal através de um menu numérico interativo e contínuo. Veja o que acontece ao acionar cada opção:

## 📥 Se você digitar 1 (Entrada de Veículo)
O sistema inicia o fluxo para colocar um carro em uma vaga:

Verificação de Lotação: O programa checa se ainda há vagas. Se o pátio estiver com as 15 vagas cheias, ele avisa Estacionamento cheio. e cancela a operação.

Coleta da Placa: Pede para você digitar a placa. O sistema transforma tudo em maiúsculas automaticamente e usa a lógica de Regex para validar. Se você digitar uma placa fora do padrão (antigo ou Mercosul), ou se o carro já estiver estacionado, ele bloqueia.

Coleta da Marca: Pede a marca do veículo (ex: fiat virará Fiat automaticamente). O tipo do veículo é definido por padrão como "Carro".

Escolha da Vaga: Pede o número da vaga (1 a 15). Graças à sanitização, se você digitar algo como "Vaga 05", o sistema limpa o texto e entende que você quer a vaga 5.

Regra de PCD: Se você escolher a Vaga 01 (que é exclusiva para PCD), o sistema perguntará se o veículo possui essa credencial (s/n). Se você responder n, a entrada é recusada.

Finalização: Salva os dados no arquivo Dados.json, registra a atividade no arquivo de log e roda o cálculo probabilístico para ver se o carro bateu ou não ao estacionar.

## 📤 Se você digitar 2 (Saída de Veículo)
O sistema faz a liberação e desocupação da vaga:

Identificação: Pede para você digitar o número da vaga que está saindo.

Validação: O sistema limpa o input e confere se a vaga é válida. Se você tentar dar saída em uma vaga que já estava vazia (Livre), o sistema gera um alerta de inconsistência.

Remoção: Se estiver tudo certo, o programa lê a placa do veículo que estava ali, mostra a mensagem de sucesso, limpa os dados da vaga voltando o status para None, atualiza o banco de dados Dados.json e grava o horário e a ação no arquivo de logs.

## 🔍 Se você digitar 3 (Ver Vagas)
O sistema renderiza na tela o Mapa do Estacionamento atualizado em tempo real. Ele lê o dicionário na memória e exibe linha por linha quem está estacionado (mostrando placa, tipo e marca) e quais vagas estão livres, exibindo o totalizador de vagas ocupadas e disponíveis no rodapé do painel.

## ❌ Se você digitar # (Sair do Sistema)
O programa interrompe o laço de repetição principal (while True), exibe uma mensagem de encerramento seguro e fecha o terminal de forma limpa, garantindo que nenhum dado em trânsito seja corrompido no arquivo JSON.
