# Crachá de Emergência com QR Code (PHP + Laravel)

Cadastra funcionários, gera um QR Code para cada um e, ao escanear, abre uma página de emergência com nome, cargo, tipo sanguíneo e botão de ligar.

## Como instalar

Requisitos: PHP 8.2+, Composer.

```bash
# 1. Criar um projeto Laravel novo
composer create-project laravel/laravel cracha-qr
cd cracha-qr

# 2. Instalar o gerador de QR Code
composer require simplesoftwareio/simple-qrcode

# 3. Copiar os arquivos desta pasta por cima do projeto (sobrescreva routes/web.php):
#    app/Models/Funcionario.php
#    app/Http/Controllers/FuncionarioController.php
#    routes/web.php
#    resources/views/layout.blade.php, index.blade.php, qr.blade.php, perfil.blade.php
#    database/migrations/2024_01_01_000000_create_funcionarios_table.php

# 4. Banco de dados (SQLite já vem configurado por padrão no Laravel 11+)
php artisan migrate

# 5. Liberar a pasta pública dos QR Codes
php artisan storage:link

# 6. Rodar
php artisan serve
```

Abra http://127.0.0.1:8000

## Como funciona

1. **Cadastro**: o formulário em `/` salva o funcionário na tabela `funcionarios` (cada um recebe um ID único).
2. **QR Code**: logo após salvar, o Laravel gera o QR com o link `/perfil/{id}` e salva em `storage/app/public/qrs/qr_{id}.svg`. A página seguinte mostra o QR com botões de baixar e imprimir.
3. **Leitura**: ao escanear, o celular abre `/perfil/{id}` e o Laravel busca o funcionário no banco e monta a página de emergência.

## Importante: para funcionar no celular

`127.0.0.1` só funciona no seu próprio computador. Para o celular de outra pessoa acessar o perfil:

- **Teste em casa/escola**: rode `php artisan serve --host=0.0.0.0` e defina no `.env` `APP_URL=http://IP-DO-SEU-PC:8000` (ex: `http://192.168.0.10:8000`), com o celular na mesma rede Wi-Fi. Depois limpe o cache: `php artisan config:clear`.
- **Uso real**: hospede o projeto em um servidor com domínio (ex: Hostinger, Railway) e coloque esse domínio em `APP_URL` **antes** de gerar os QR Codes, senão eles apontarão para o endereço antigo.

## Observações de segurança

Os dados de saúde ficam públicos para quem tiver o link do QR (é o objetivo do crachá), então cadastre apenas o necessário e evite expor a lista de funcionários (`/`) em produção sem login. O Laravel Breeze resolve isso rapidamente.
