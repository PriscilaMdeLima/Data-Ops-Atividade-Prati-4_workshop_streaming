# Workshop Streaming: OpenFlights Airports Pipeline

Este projeto implementa um pipeline de dados ponta a ponta, desde a ingestão via Kafka até a criação de camadas Bronze, Silver e Gold utilizando Parquet e Apache Iceberg.

## Arquitetura do Projeto

1.  **Producer (`producer.py`)**: Lê o dataset de aeroportos do OpenFlights e envia para o tópico Kafka `airports_raw` em chunks.
2.  **Consumer Bronze (`consumer_bronze.py`)**: Consome dados do Kafka em micro-batches de 30 segundos e persiste na camada Bronze em formato Parquet.
3.  **Silver Iceberg (`silver_iceberg.py`)**: Processa a camada Bronze, aplica tipagem, limpa dados e persiste em uma tabela Iceberg na camada Silver.
4.  **Gold Brazil (`gold_brazil.py`)**: Filtra aeroportos localizados no Brasil da camada Silver e persiste na camada Gold em formato Iceberg.

## Pré-requisitos

- Python 3.10+
- Docker e Docker Compose
- Java 17+ (para PySpark e Iceberg)

## Como Executar

### 1. Subir o Kafka
```bash
docker compose up -d
```

### 2. Instalar Dependências Python
```bash
pip install -r requirements.txt
```

### 3. Executar o Pipeline

Siga a ordem abaixo para processar os dados:

1.  **Iniciar o Consumidor Bronze** (deixe rodando em um terminal):
    ```bash
    python consumer_bronze.py
    ```

2.  **Iniciar o Producer** (em outro terminal):
    ```bash
    python producer.py
    ```

3.  **Processar Camada Silver** (após o consumidor Bronze ter gravado arquivos):
    ```bash
    python silver_iceberg.py
    ```

4.  **Processar Camada Gold**:
    ```bash
    python gold_brazil.py
    ```

## Estrutura de Pastas

- `data/bronze/`: Arquivos Parquet brutos particionados por data e hora de ingestão.
- `data/warehouse/`: Repositório das tabelas Iceberg (Silver e Gold).
