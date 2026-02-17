# ☁️ Serverless Portfolio & Visitor Tracker

![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)
![DynamoDB](https://img.shields.io/badge/DynamoDB-4053D6?style=for-the-badge&logo=amazon-dynamodb&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/github%20actions-%232671E5.svg?style=for-the-badge&logo=githubactions&logoColor=white)

> **Language / Idioma**: [🇺🇸 English](#-project-overview) | [🇧🇷 Português](#-visão-geral-do-projeto)

---

## 🇺🇸 Project Overview

This project represents the evolution of my personal portfolio into a fully **Serverless Architecture**. 
It goes beyond static hosting by integrating a backend logic using **AWS Lambda** and **API Gateway** to track visitor statistics in real-time without provisioning a single server.

### 🏗️ Architecture

1.  **Frontend:** Hosted on **S3**, distributed via **CloudFront** (CDN) for low latency, with DNS managed by **Route53**.
2.  **Backend (Serverless):**
    * **API Gateway:** Exposes the endpoint securely.
    * **AWS Lambda (Python):** Handles the logic. It captures the user's IP, fetches geolocation data (Country/Region) via an external API, and updates the database.
3.  **Database:** **DynamoDB** (NoSQL).
    * `My-Cv-WebSite-Visitor-Count`: Stores the atomic total view count.
    * `cv-visitors-ip`: Logs individual access details (IP, Time, Location).
4.  **CI/CD:** A **GitHub Actions** workflow automatically syncs changes from the repository to the S3 bucket upon every push to the master branch.

### 🚀 Key Features

* **Atomic Counter:** Ensures accurate visitor count even with concurrent access.
* **Geo-Tracking:** Identifies where visitors are coming from using public IP data.
* **Automated Deployment:** No manual file uploads; `git push` updates the live site.

---

## 🇧🇷 Visão Geral do Projeto

Esse projeto é a evolução do meu portfólio. A ideia aqui foi sair do básico e transformar um site estático em uma aplicação **Cloud Native** e **Serverless**.
Eu queria ter métricas reais de quem acessa meu currículo, mas sem subir servidor (EC2). A solução? **AWS Lambda + DynamoDB**.

### 🏗️ Como a Mágica Acontece

O fluxo é o seguinte:

1.  **Hospedagem Blindada:** O site tá num bucket **S3**, mas ninguém acessa ele direto. O acesso passa pelo **CloudFront** (CDN) para garantir cache e velocidade global, com o domínio gerenciado pelo **Route53**.
2.  **O "Cérebro" (Backend):**
    * Quando você abre o site, um JavaScript chama meu **API Gateway**.
    * O Gateway acorda uma função **Lambda** escrita em Python.
    * Essa Lambda faz duas coisas:
        1.  Incrementa o contador de visitas de forma atômica (pra não dar erro de contagem).
        2.  Pega o seu IP, consulta uma API externa para descobrir seu País/Região e salva tudo isso no banco.
3.  **Banco de Dados:** Tudo vai para o **DynamoDB** (NoSQL). Tenho duas tabelas: uma só pro contador e outra para o log de auditoria dos acessos.
4.  **DevOps & Automação:**
    * Cansei de fazer upload manual. Configurei uma pipeline no **GitHub Actions**.
    * Alterou o código? Deu `git push`? O GitHub loga na minha conta AWS e sincroniza os arquivos novos pro S3 automaticamente.

### 🛠️ Tech Stack & Ferramentas

* **Cloud:** AWS (S3, CloudFront, Route53, Lambda, API Gateway, DynamoDB)
* **IaC & Automação:** GitHub Actions (CI/CD)
* **Linguagem:** Python (Boto3) & JavaScript
* **Database:** NoSQL (DynamoDB)

### 🧩 Snippet da Lógica (Python/Boto3)

Um trecho de como eu garanto a contagem e a geolocalização simultaneamente:

```python
# Atualiza o contador (Atomicamente)
table1.update_item(
    Key={'value_counts': 'view-count'},
    UpdateExpression='ADD Quantity :inc',
    ExpressionAttributeValues={':inc': 1}
)

# Enriquece os dados com Geolocalização
with urllib.request.urlopen(f"[http://ip-api.com/json/](http://ip-api.com/json/){ip}") as response:
    geo = json.loads(response.read().decode())
    
# Salva o Log
table2.put_item(
    Item={
        'IP': ip,
        'country': geo.get("country"),
        'region': geo.get("regionName")
    }
)

<div align="center">
<sub>Developed by Filipe de Abreu using AWS Serverless Architecture.</sub>
</div>