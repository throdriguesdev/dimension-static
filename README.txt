
# Deploy de Site com S3 e CloudFront

Este projeto configura um pipeline automatizado para fazer o deploy de um site estático em um bucket S3 e atualizar o cache do CloudFront automaticamente.

## Como Funciona
1. O código é enviado para o repositório GitHub na branch `main`.
2. O GitHub Actions é acionado automaticamente e executa as seguintes etapas:
   - Faz o checkout do código.
   - Configura as credenciais AWS.
   - Sincroniza os arquivos com o S3.
   - Invalida o cache do CloudFront para garantir que as alterações entrem em vigor.

## Pipeline (GitHub Actions)
```yaml
name: Deploy to S3 and CloudFront

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: sa-east-1

      - name: Deploy to S3
        run: |
          aws s3 sync ./ s3://dimension-static-website-th/static-dimension/             --delete             --exclude ".git/*"             --exclude ".github/*"             --exclude "node_modules/*"             --exclude ".gitignore"             --exclude "README.md"

      - name: Invalidate CloudFront Cache
        run: |
          aws cloudfront create-invalidation             --distribution-id ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }}             --paths "/*"
```

## Configuração
Configure os seguintes segredos no GitHub:
- **AWS_ACCESS_KEY_ID** – Chave de acesso da AWS.  
- **AWS_SECRET_ACCESS_KEY** – Chave secreta da AWS.  
- **CLOUDFRONT_DISTRIBUTION_ID** – ID da distribuição CloudFront.  

## Como Rodar Localmente
1. Clone o repositório:  
```bash
git clone ---
```  
2. Configure a AWS CLI:  
```bash
aws configure
```  
3. Envie os arquivos para o S3:  
```bash
aws s3 sync ./ s3://NOME-DO-BUCKET/ --delete
```  
4. Invalide o cache do CloudFront:  
```bash
aws cloudfront create-invalidation --distribution-id CLOUDFRONT_DISTRIBUTION_ID --paths "/*"
```  

## Tecnologias Utilizadas
- GitHub Actions  
- AWS S3  
- AWS CloudFront  
- AWS CLI  
