# Projeto Filipe — Comparação de modelos de IA para programação

## Visão geral

Este projeto utiliza modelos de linguagem locais para gerar soluções de desafios de programação e comparar suas respostas. O fluxo foi desenvolvido em Python, executado em um notebook Jupyter e integrado ao Ollama.

O notebook permite trabalhar com três modelos locais, registrar as respostas em CSV, medir o tempo de geração e acompanhar o status técnico de cada consulta. Também contém uma etapa de coleta e conferência dos desafios por navegador, respeitando a verificação de acesso definida no próprio projeto.

> **Importante:** o campo `status` dos CSVs registra o resultado da consulta ao modelo (por exemplo, `ok`, `erro`, `timeout` ou `vazio`). Ele não representa o veredito de aceitação do site que usamos (LeetCode). A submissão e a conferência do resultado oficial no site são feitas manualmente.

## Funcionalidades

- Consulta a três modelos locais pelo Ollama.
- Configuração de temperatura, seed e timeout.
- Medição do tempo de resposta de cada modelo.
- Extração do código gerado, removendo cercas Markdown quando presentes.
- Registro incremental das respostas em arquivos CSV.
- Retomada do processamento, ignorando combinações já concluídas com status `ok`.
- Testes de erro e timeout.
- Coleta de páginas por navegador conectado ao Chrome, com verificação de `robots.txt`, validação do conteúdo e confirmação manual.
- Geração de um índice de desafios e de cópias públicas dos resultados sem republicar o texto dos enunciados.

## Tecnologias

- Python
- Jupyter Notebook / VS Code
- Ollama
- pandas
- requests
- Selenium
- Google Chrome

## Estrutura do repositório

```text
Projeto-Filipe/
├── benchmark.ipynb
├── desafios.csv
├── requirements.txt
├── .gitignore
└── resultados/
    ├── resultados_antigo_21do10.csv
    ├── resultados_final.csv
    ├── resultados_leetcode.csv
    ├── teste_dos_resultados.csv
    └── teste_leetcode.csv
```

A pasta `cache/` é criada localmente durante a coleta, mas não faz parte do repositório público. Os CSVs locais gerados durante a execução também permanecem fora do Git; a pasta `resultados/` contém cópias preparadas para publicação.

## Requisitos

- Windows 10 ou 11.
- Python instalado.
- Visual Studio Code com suporte a Jupyter.
- Google Chrome.
- Ollama instalado e em execução.
- Os modelos configurados no notebook baixados localmente.

## Instalação no Windows

Abra o PowerShell na pasta do projeto.

### 1. Criar e ativar o ambiente virtual

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

Se o PowerShell bloquear a ativação, consulte a política de execução configurada no Windows ou ative o ambiente pelo terminal integrado do VS Code.

### 2. Instalar as dependências

```powershell
python -m pip install --upgrade pip
pip install -r requirements.txt
```

As bibliotecas padrão utilizadas pelo Python — como `json`, `os`, `re`, `time`, `hashlib`, `datetime` e `urllib` — não precisam ser instaladas pelo `pip`.

### 3. Preparar o Ollama

Com o Ollama instalado, baixe os modelos utilizados pelo notebook:

```powershell
ollama pull qwen2.5-coder:1.5b
ollama pull llama3.2:1b
ollama pull gemma3:1b
```

O notebook utiliza, por padrão, a API local em `http://localhost:11434`.

### 4. Abrir o notebook

Abra `benchmark.ipynb` no VS Code e selecione o interpretador Python do ambiente `.venv`.

A etapa de coleta por Selenium conecta-se a uma instância do Chrome já iniciada com depuração remota na porta `9222`, conforme a configuração `DEBUG_ADDR` do notebook. O navegador deve estar disponível antes de executar essa etapa.

## Como utilizar

1. Confira `OLLAMA_URL`, `MODELOS`, `TIMEOUT_S` e os nomes dos arquivos na primeira célula.
2. Revise a lista `DESAFIOS` e os enunciados de exemplo.
3. Execute as células de validação e montagem do prompt.
4. Verifique se o Ollama está ativo e se os três modelos estão instalados.
5. Execute os testes rápidos antes do ciclo completo.
6. Execute o ciclo de geração para consultar os modelos e registrar as respostas.
7. Confira a tabela de resultados e os status técnicos.
8. Se utilizar a coleta de desafios do LeetCode, execute as células correspondentes. O notebook verifica o `robots.txt`, valida a página e solicita confirmação antes de aceitar cada desafio.
9. Para conferir as soluções no site, extraia o código com `extrair_codigo`, faça a submissão manual e consulte o resultado oficial na plataforma.
10. Ao final, execute a célula **Preparação dos arquivos públicos** para gerar `desafios.csv` e as cópias sanitizadas em `resultados/`.

## Organização das células do notebook

| Bloco | Finalidade |
|---|---|
| 1–3 | Configurações iniciais, lista de desafios e validação |
| 4 | Template do prompt e extração do código |
| 5 | Verificação dos modelos disponíveis no Ollama |
| 6 | Consulta aos modelos, timeout e tratamento de falhas |
| 7 | Registro das respostas em CSV |
| 8A e 8B | Testes rápidos de geração, erro e timeout |
| 9–10 | Ciclo de geração e visualização dos resultados |
| 11–15 | Configuração, coleta, validação e confirmação dos desafios |
| 16–19 | Geração, registro e resumo das respostas dos desafios coletados |
| 20 | Preparação do índice e das cópias públicas dos CSVs |

## Arquivos de apoio e resultados

Os arquivos abaixo são mantidos na pasta `resultados/` como registros e exemplos de execução. Nessa versão pública, a coluna com o texto integral do prompt foi substituída por `hash_enunciado`.

- **`resultados_antigo_21do10.csv`** — registro histórico da primeira versão dos testes, apresentado anteriormente ao professor. Permite preservar a comparação com a etapa inicial do projeto.
- **`resultados_final.csv`** — resultados consolidados da versão revisada do fluxo inicial, com as respostas dos três modelos para os exercícios utilizados nessa etapa.
- **`teste_dos_resultados.csv`** — arquivo de testes usado durante o desenvolvimento para verificar a gravação das respostas, o comportamento dos modelos e cenários de validação.
- **`resultados_leetcode.csv`** — conjunto de resultados da etapa com desafios identificados por URL, contendo as respostas dos três modelos para os dez desafios registrados.
- **`teste_leetcode.csv`** — registro de um teste pontual da etapa de desafios do LeetCode, utilizado para validar a geração e a gravação antes do ciclo completo.

Os arquivos históricos e de teste são mantidos para transparência e rastreabilidade. Eles não devem ser interpretados como uma nova execução do projeto.

## Sobre `desafios.csv` e `hash_enunciado`

O arquivo `desafios.csv` funciona como um índice dos desafios. Ele registra identificador, título, URL, quantidade de caracteres, data de coleta, origem e `hash_enunciado`.

O `hash_enunciado` é uma impressão digital SHA-256 calculada sobre o texto normalizado do enunciado. Ele permite comparar e identificar o conteúdo de referência sem armazenar ou publicar o enunciado integral. O mesmo identificador é utilizado nos CSVs públicos para relacionar cada resultado ao desafio correspondente.

A normalização remove diferenças de espaçamento antes do cálculo. Por isso, alterações apenas em espaços ou quebras de linha não criam hashes diferentes.

## Decisões de publicação e cuidado com conteúdo de terceiros

A pasta local `cache/` contém arquivos JSON com textos coletados das páginas dos desafios. Os CSVs originais também possuem uma coluna `prompt` que inclui o enunciado completo enviado aos modelos.

Esses conteúdos podem incluir material protegido por direitos autorais e sujeito aos termos de uso das respectivas plataformas. Como o repositório será público, adotamos uma medida preventiva:

- A pasta `cache/` não é publicada.
- A coluna `prompt` é substituída por `hash_enunciado` nas cópias públicas dos CSVs.
- O notebook público não mantém saídas de execução que poderiam reproduzir enunciados.
- O exemplo de exercício que fazia referência a um enunciado de terceiro foi substituído por um exemplo próprio.
- Os arquivos originais locais não são alterados; a preparação cria cópias destinadas à publicação.

Essas decisões não constituem uma análise jurídica dos termos das plataformas. São medidas conservadoras para evitar redistribuir enunciados de terceiros e manter o foco público no código, no método e nos resultados experimentais do projeto.

## Problemas comuns

| Problema | Verificação |
|---|---|
| Ollama não responde | Confirme se o Ollama está em execução e se `OLLAMA_URL` aponta para `http://localhost:11434`. |
| Modelo não encontrado | Execute `ollama list` e baixe o modelo ausente com `ollama pull nome-do-modelo`. |
| Timeout | Confira se o modelo está carregado e, se necessário, ajuste `TIMEOUT_S`. |
| Chrome não conecta | Verifique se o Chrome foi iniciado com depuração remota na porta `9222` e se `DEBUG_ADDR` corresponde a essa porta. |
| Desafio não é coletado | Confira a URL, o acesso à página e a permissão retornada pelo `robots.txt`. O notebook não deve tentar contornar bloqueios. |
| CSV não aparece | Confirme a pasta de trabalho atual do notebook e se a célula responsável pelo registro foi executada. |
| Hash diferente | Verifique se o texto do desafio mudou; o hash é calculado sobre o texto normalizado. |

## Git e publicação

O projeto está versionado com Git e publicado no GitHub.

**Repositório:** https://github.com/lipes-repository/PROTOTIPO_INPUT-LLM-OUTPUT

A branch principal utilizada é `main`.

Para obter uma cópia do projeto:

```bash
git clone https://github.com/lipes-repository/PROTOTIPO_INPUT-LLM-OUTPUT.git

## Limitações conhecidas

- A geração é realizada localmente pelo Ollama e depende dos modelos instalados e dos recursos do computador.
- O campo `status` mede o resultado técnico da consulta ao modelo, não a correção funcional do código.
- A submissão das respostas e a consulta ao veredito oficial do site são manuais.
- O projeto não contorna CAPTCHA, autenticação, limites ou bloqueios definidos pelas plataformas.
