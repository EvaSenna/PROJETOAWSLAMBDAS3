# PROJETOAWSLAMBDAS3
# AWS Lambda + S3 — Automação de Tarefas na Nuvem

Este repositório contém anotações e exemplos práticos sobre **automação de tarefas na AWS utilizando Lambda Functions e Amazon S3** criado com auxílio de inteligência artificial.  
O objetivo é demonstrar como **executar funções automaticamente em resposta a eventos**, sem necessidade de servidores (serverless architecture).

---

## Conceito Geral

A combinação **Amazon S3 + AWS Lambda** permite criar fluxos totalmente automatizados, por exemplo:

- Processar arquivos assim que são enviados para um bucket;
- Converter, redimensionar ou analisar dados automaticamente;
- Enviar notificações ou logs para outros serviços.

Exemplo de fluxo:
> Um arquivo `.csv` é carregado no bucket S3 → o evento dispara uma função Lambda → a função processa o arquivo e grava os resultados em outro bucket.

---

## Arquitetura do Projeto


**Serviços utilizados:**
- **Amazon S3** – armazenamento de arquivos e gatilho de evento.  
- **AWS Lambda** – execução automática de código (Python).  
- **IAM Role** – permissões para Lambda acessar o S3 e logs.  
- **CloudWatch Logs** – monitoramento e registros da execução.

---

## Estrutura Básica do Código Lambda

Arquivo: `lambda/process_file.py`

```python
import json
import boto3

def lambda_handler(event, context):
    s3 = boto3.client('s3')

    # Obtém informações do evento
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = event['Records'][0]['s3']['object']['key']

    print(f"Novo arquivo detectado: {key} em {bucket}")

    # Lê o conteúdo do arquivo (exemplo: CSV, texto etc.)
    obj = s3.get_object(Bucket=bucket, Key=key)
    data = obj['Body'].read().decode('utf-8')

    # Exemplo de processamento simples:
    linhas = len(data.splitlines())
    print(f"O arquivo contém {linhas} linhas.")

    # Grava o resultado em outro bucket (opcional)
    result_bucket = 'meu-bucket-resultados'
    s3.put_object(
        Bucket=result_bucket,
        Key=f"resultado-{key}.txt",
        Body=f"O arquivo {key} possui {linhas} linhas."
    )

    return {
        'statusCode': 200,
        'body': json.dumps(f"Processamento concluído para {key}")
    }
