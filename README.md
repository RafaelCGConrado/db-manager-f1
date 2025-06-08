# Sistema de Gerenciamento da FIA - Projeto Final 🏎️

Este é um sistema de gerenciamento de dados da Fórmula 1 desenvolvido em Python com PostgreSQL, criado como projeto final da disciplina de Laboratório de Bases de Dados.

## 📋 Visão Geral

O sistema permite diferentes tipos de usuários (Administradores, Escuderias e Pilotos) acessarem e gerenciarem dados relacionados à Fórmula 1, incluindo informações sobre corridas, circuitos, pilotos, escuderias, resultados e muito mais.

## 🚀 Início Rápido

### Pré-requisitos
- **Python 3.12** (obrigatório)
- **uv** (gerenciador de pacotes Python moderno)
  ```bash
  # Instalar uv
  curl -LsSf https://astral.sh/uv/install.sh | sh
  # ou no macOS com Homebrew
  brew install uv
  ```
- **Docker & Docker Compose**
- **Git**

### Instalação e Execução

1. **Clone o repositório:**
```bash
git clone <url-do-repositorio>
cd db-manager-f1
```

2. **Execute o sistema completo:**
```bash
make run
```

Este comando irá:
- Instalar dependências com uv
- Iniciar container PostgreSQL
- Configurar base de dados
- Executar a aplicação

## 🗂️ Estrutura do Projeto

```
db-manager-f1/
├── main.py                 # Arquivo principal da aplicação
├── Makefile               # Comandos de automação
├── pyproject.toml         # Configuração do projeto e dependências (uv)
├── uv.lock               # Lock file das dependências
├── requirements.txt       # Dependências Python (compatibilidade)
├── docker-compose.yml     # Configuração Docker
├── db/                    # Scripts SQL
│   ├── dump.sql          # Dump completo da base de dados
│   ├── create_insert.sql # Criação de tabelas adicionais
│   ├── views.sql         # Views da base de dados
│   ├── dashboards.sql    # Funções para dashboards
│   ├── triggers.sql      # Triggers da base de dados
│   └── index.sql         # Índices para otimização
└── scripts/              # Módulos Python
    ├── admin.py          # Funcionalidades do administrador
    ├── constructor.py    # Funcionalidades das escuderias
    ├── driver.py         # Funcionalidades dos pilotos
    ├── conection.py      # Conexão com a base de dados
    └── tela.py           # Interface do utilizador
```

## 🗄️ Scripts SQL (pasta `/db`)

### 📊 `dump.sql`
Contém o dump completo da base de dados original da Fórmula 1, incluindo:
- Todas as tabelas principais (circuits, constructors, drivers, races, results, etc.)
- Extensões PostgreSQL necessárias (cube, earthdistance, pgcrypto)
- Funções e procedimentos base
- Dados históricos da Fórmula 1

### 👥 `create_insert.sql`
**Propósito:** Criação do sistema de utilizadores e autenticação

**Funcionalidades detalhadas:**
- **Tabela USERS:** Sistema de login com três tipos de utilizadores
  - `Administrador`: Acesso total ao sistema
  - `Escuderia`: Acesso a dados específicos da escuderia
  - `Piloto`: Acesso a dados específicos do piloto
- **Encriptação de senhas:** Utiliza SHA-256 para segurança
- **Tabela USERS_LOG:** Registo de acessos ao sistema
- **Inserção automática:** Cria utilizadores baseados nos dados existentes de pilotos e escuderias
- **Prevenção de duplicatas:** Verificações para evitar inserções duplicadas

### 📈 `views.sql`
**Propósito:** Criação de views para consultas complexas e relatórios

**Views incluídas:**
- **vw_relatorio_status:** Agregação de ocorrências por tipo de status nas corridas
- **relatorio_escuderias_pilotos:** Quantidade de pilotos por escuderia
- **relatorio_qtd_corridas:** Total de corridas cadastradas
- **relatorio_corridas_circuito:** Estatísticas de corridas por circuito
- **relatorio_corrida_circuito_tempo:** Detalhes de tempo e voltas por corrida

### 🎯 `dashboards.sql`
**Propósito:** Funções específicas para geração de dashboards interativos

**Funções para Administradores:**
- `dashboard_admin_totais()`: Contadores gerais (pilotos, escuderias, temporadas)
- `dashboard_admin_corridas(ano)`: Resumo de corridas por ano
- `dashboard_admin_escuderias(ano)`: Pontuação das escuderias por ano
- `dashboard_admin_pilotos(ano)`: Pontuação dos pilotos por ano

**Funções para Escuderias:**
- `dashboard_escuderia(constructor_id)`: Estatísticas específicas da escuderia
- `info_escuderia(constructor_id)`: Informações básicas da escuderia

**Funções para Pilotos:**
- `dashboard_piloto_ano(driver_id)`: Período de atividade do piloto
- `dashboard_piloto_vitorias(driver_id)`: Histórico de vitórias e performances
- `info_piloto(driver_id)`: Informações básicas do piloto

**Funções de Relatórios:**
- `relatorio_pontos_por_ano(driver_id)`: Pontuação anual do piloto
- `relatorio_vitorias_pilotos(constructor_id)`: Vitórias dos pilotos da escuderia
- `relatorio_resultado_status(constructor_id)`: Status dos resultados da escuderia
- `relatorio_aeroportos(cidade)`: Aeroportos próximos a cidades

### ⚡ `triggers.sql`
**Propósito:** Automatização e validação de dados

**Triggers implementados:**
- **verificastatus():** Validação de IDs de status (devem ser positivos)
- **verificaaeroporto():** Validação de dados de aeroportos
- **atualizacontagem():** Atualização automática de contadores
- **atualiza_users_pilotos():** Sincronização de dados de pilotos
- **atualiza_users_escuderias():** Sincronização de dados de escuderias

### 🚀 `index.sql`
**Propósito:** Otimização de performance da base de dados

**Índices criados:**
- Índices em colunas frequentemente consultadas
- Índices compostos para consultas complexas
- Índices em chaves estrangeiras
- Otimizações específicas para relatórios e dashboards

## 🛠️ Comandos do Makefile

### Comandos Principais

#### `make run`
**Comando mais importante** - Executa todo o setup e inicia a aplicação:
1. Instala dependências com uv
2. Inicia PostgreSQL
3. Configura base de dados
4. Executa a aplicação

#### `make help`
Mostra todos os comandos disponíveis com descrições.

### Gestão do Ambiente

#### `make install`
Instala as dependências Python usando o **uv** (gerenciador de pacotes moderno e rápido).
- Verifica se Python 3.12 está instalado
- Utiliza `uv sync` para sincronizar dependências
- Mais rápido que pip tradicional
- Gerencia automaticamente o ambiente virtual (.venv)

### Gestão da Base de Dados

#### `make docker-up`
Inicia apenas o container PostgreSQL (sem configurar a base de dados).

#### `make setup-db`
Configura a base de dados executando todos os scripts SQL na ordem correta:
1. `dump.sql` (base de dados completa)
2. `create_insert.sql` (sistema de utilizadores)
3. `views.sql` (views)
4. `dashboards.sql` (funções)
5. `triggers.sql` (triggers)
6. `index.sql` (índices)

#### `make setup-db-force`
Força a re-execução de todos os scripts SQL, mesmo se a base já estiver configurada.

#### `make reset-db`
**Reset completo** - Remove containers e volumes, criando uma base de dados completamente nova.

### Comandos de Limpeza

#### `make clean`
Para e remove containers Docker (mantém volumes).

#### `make clean-db-volumes`
Remove apenas os volumes da base de dados (dados persistentes).

#### `make clean-docker`
Limpeza completa de recursos Docker (containers, volumes, órfãos).

#### `make clean-all`
Limpeza total: remove containers, volumes E ambiente virtual Python (.venv).

## 👤 Sistema de Utilizadores

### Tipos de Utilizador

1. **Administrador**
   - Login: `admin` / Password: `admin`
   - Acesso total: dashboards, relatórios, cadastro de pilotos/escuderias

2. **Escuderia**
   - Login: `{constructorref} c` (ex: `ferrari c`)
   - Password: `{constructorref}` (ex: `ferrari`)
   - Acesso: dashboard da escuderia, pesquisa de pilotos, relatórios específicos

3. **Piloto**
   - Login: `{driverref} d` (ex: `hamilton d`)
   - Password: `{driverref}` (ex: `hamilton`)
   - Acesso: dashboard pessoal, relatórios de performance

## 🔧 Resolução de Problemas

### "Database tables already exist"
A base de dados já está configurada. Use `make reset-db` para reset completo.

### "Python 3.12 is required but not found"
Instale Python 3.12:
```bash
# macOS com Homebrew
brew install python@3.12

# Ou baixe de python.org
```

### "psycopg2 installation failed"
Instale dependências do sistema:
```bash
# macOS
brew install postgresql

# Ubuntu/Debian
sudo apt-get install libpq-dev python3-dev
```

### Container PostgreSQL não inicia
Verifique se a porta 5432 está livre:
```bash
lsof -i :5432
```

## 📝 Desenvolvimento

Para desenvolver no projeto:

1. **Verifique os pré-requisitos:**
   - Python 3.12 (obrigatório)
   - uv package manager
   - Docker & Docker Compose

2. **Ambiente de desenvolvimento:**
```bash
make install
make docker-up
```

2. **Testar mudanças na base de dados:**
```bash
make setup-db-force
```

3. **Reset completo para testes:**
```bash
make reset-db
make setup-db
```

## 🎯 Funcionalidades Principais

- **Dashboards interativos** para cada tipo de utilizador
- **Sistema de relatórios** com dados históricos da F1
- **Pesquisa avançada** de pilotos, escuderias e corridas
- **Cadastro de novos pilotos/escuderias** (apenas administradores)
- **Análise de performance** com estatísticas detalhadas
- **Sistema de logs** para auditoria de acessos

---

**Desenvolvido para a disciplina de Laboratório de Bases de Dados**  
*Sistema de Gerenciamento da FIA - Fórmula 1* 🏁
