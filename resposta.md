# 📝 Resposta do Laboratório: A Wiki Perdida dos Arquivos Corporativos

> Preencha este arquivo com a sua proposta de solução.
>
> Sua resposta deve explicar como transformar os documentos brutos da pasta `raw/` em uma Wiki Corporativa Inteligente, pesquisável e segura usando apenas serviços da AWS.

---

## 👤 Identificação

**Nome:**  
Elias Santos

**Data:**  
16/10/2026

**Link do repositório:**  
(https://github.com/elias-santos87/laboratorio-wiki-aws)

---

# ✅ Quest 1: O Mapa dos Arquivos Perdidos

## 1.1 Formatos encontrados na pasta `raw/`

Descreva quais tipos de arquivos existem dentro da pasta `raw/`.

```md
Exemplo de como responder, com o formato e o que ele implica:
- <extensao>: <nasce digital ou precisa de OCR?>, <o que da para extrair>
```

> Abra a pasta e liste o que voce encontrou de fato. Esta quest avalia a sua
> leitura do acervo, entao a resposta certa e a que corresponde aos arquivos.

**Sua resposta:**

```md
pdf—ata_reuniao_vendas_sa.pdf:
documento digital com camada de texto, contendo cinco páginas. A extração pode ser realizada sem OCR, utilizando bibliotecas de processamento de PDF em uma função AWS Lambda.
O documento pode conter datas, participantes, assuntos discutidos, decisões, responsáveis e próximos passos.

png—ata_resultados_vendas_novos_dados.png:imagem de uma página digitalizada, OCR utilizando o Amazon Textract para reconhecer e extrair o texto. A qualidade da imagem pode influenciar a precisão da extração.

csv—vendas_sa_dados_ficticios_laboratorio.csv: arquivo estruturado com 240 oportunidades comerciais e 19 colunas,representando dados exportados de um CRM.Deve ser processado como uma tabela, preservando os nomes das colunas, os tipos de dados e os relacionamentos entre os registros.
```

---

## 1.2 Principais desafios encontrados

Explique quais dificuldades esses documentos podem apresentar.

```md
Exemplo:
- Arquivos sem padrão de nomenclatura
- Documentos escaneados com baixa qualidade
- Textos manuscritos ou parcialmente ilegíveis
- Atas com estruturas diferentes
- Informações importantes espalhadas em vários formatos
```

**Sua resposta:**

```md
Heterogeneidade dos formatos: os arquivos contêm documentos textuais, imagens digitalizadas e dados tabulares.
Reconhecimento óptico de caracteres: a imagem digitalizada pode apresentar erros de reconhecimento devido à resolução, ao contraste ou à qualidade da digitalização.
Ausência de padronização: datas, nomes, abreviações e termos comerciais podem aparecer de maneiras diferentes.
Preservação da estrutura: tabelas e registros do CRM precisam manter suas colunas e relações, evitando a perda de contexto durante a conversão para texto.
Informações distribuídas: decisões e responsabilidades podem estar em documentos diferentes.
Duplicidade de informações: um mesmo assunto pode aparecer em mais de um documento ou versão.
Ambiguidade textual: uma decisão proposta não significa necessariamente que ela foi aprovada.
Confidencialidade: documentos corporativos podem conter dados comerciais sensíveis, exigindo controle de acesso.
Rastreabilidade: cada informação extraída deve permanecer vinculada ao arquivo de origem.
Qualidade dos dados: campos vazios, formatos inconsistentes ou informações incompletas podem comprometer as consultas.
```

---

## 1.3 Informações importantes a serem extraídas

Liste quais informações precisam ser identificadas para transformar os documentos em conhecimento pesquisável.

**Sua resposta:**

```md
Identificador único do documento.
Nome, formato, tamanho e localização do arquivo original.
Data de criação, data de modificação e data identificada no conteúdo, quando disponíveis.
Tipo documental: ata, imagem digitalizada ou exportação de CRM.
Área, departamento e projeto relacionados.
Tema principal e palavras-chave.
Participantes e pessoas mencionadas.
Decisões tomadas e seu status, quando identificável.
Responsáveis pelas atividades.
Próximos passos, prazos e pendências
Riscos, problemas e oportunidades identificados.
Valores comerciais, estágios das oportunidades e demais campos existentes no CRM.
Nível de confidencialidade.
Texto extraído e sua origem, incluindo página ou linha quando aplicável.
Data e versão do processamento.
Indicadores de qualidade da extração e necessidade de revisão.
```

---

## 1.4 Estratégia de classificação inicial

Como você classificaria os documentos sem depender de subpastas dentro de `raw/`?

**Sua resposta:**

```md
Uma função AWS Lambda será acionada quando um arquivo for enviado ao Amazon S3. Ela identificará o tipo do arquivo pela extensão, pelo tipo de conteúdo e, quando necessário, pela validação de sua estrutura.

A classificação inicial será:

Categoria  
Documento textual
Documentodigitalizado
Dados estruturados
Dados estruturados

Regra de identificação
PDF com camada de texto
Imagem PNG ou PDF sem textoutilizável
Arquivo CSV com cabeçalho eregistros
Arquivo não suportado ou inválido

Estratégia
Extração direta
OCR com Amazon Textract
Validação e processamentotabular
Registro de erro e revisão

Após a classificação, os documentos receberão metadados padronizados,como
document_id,document_type,department,confidentiality e processing_status.

Os arquivos processados poderão ser organizados em prefixos específicos no S3, como processed/,metadata/e errors/, sem alterar a estrutura original dos arquivos recebidos.
```

---

# ✅ Quest 2: O Portal de Entrada na AWS

## 2.1 Armazenamento dos arquivos brutos

Explique como os arquivos da pasta `raw/` seriam enviados e armazenados na AWS.

Serviços que você pode considerar:

- Amazon S3
- AWS IAM
- AWS KMS
- Amazon S3 Versioning
- Amazon S3 Lifecycle

**Sua resposta:**

```md
s3://wiki-corporativa-<ambiente>/raw/: arquivos originais.
s3://wiki-corporativa-<ambiente>/processed/:textos extraídos e normalizados.
s3://wiki-corporativa-<ambiente>/metadata/: metadados estruturados.
s3://wiki-corporativa-<ambiente>/errors/: relatórios de falhas.
s3://wiki-corporativa-<ambiente>/knowledge-base/: documentos preparados para indexação, quando necessário.

Os nomes dos buckets serão definidos de acordo com as regras de nomenclatura da AWS.

Para proteger os dados, serão utilizados:

AWS IAM:permissões de menor privilégio para usuários, funções Lambda e demais serviços.
AWS KMS:criptografia dos objetos em repouso com chaves gerenciadas pelo cliente, quando necessária.
Amazon S3 Versioning:histórico das versões dos objetos.
Amazon S3 Block Public Access:bloqueio de acesso público.
Amazon S3 Lifecycle:regras de retenção e transição para classes de armazenamento mais econômicas.
Amazon CloudTrail:auditoria das operações relevantes de acesso e gerenciamento.

A comunicação entre os componentes utilizará HTTPS e, quando aplicável, endpoints privados da VPC para reduzir a exposição à internet.
```

---

## 2.2 Preservação dos arquivos originais

Explique como garantir que os arquivos originais sejam mantidos intactos e rastreáveis.

**Sua resposta:**

```md
Os arquivos originais serão preservados em uma área de entrada separada dos resultados processados.

A solução adotará as seguintes medidas:

1.
Armazenar os arquivos originais sem modificações.
2.
Ativar o versionamento do S3 para recuperar versões anteriores.
3.
Calcular um hash SHA-256 durante a ingestão para verificar a integridade dos arquivos.
4.
Registrar o nome original, a chave S3, a versão do objeto, o hash, a data de ingestão e o identificador do processamento.
5.
Aplicar políticas IAM que impeçam as funções de processamento de sobrescrever ou excluir os objetos originais.
6.
Utilizar S3 Object Lock, se houver requisitos de retenção imutável, com o modo de retenção apropriado.
7.
Manter os resultados de OCR, os textos normalizados e os metadados em objetos separados.
```

---

## 2.3 Extração de texto dos documentos

Explique como cada tipo de arquivo seria processado.

Considere:

- PDFs escaneados;
- Imagens;
- PDFs digitais;
- Arquivos `.txt`;
- Arquivos `.docx`;
- Arquivos `.md`.

Serviços que você pode considerar:

- Amazon Textract
- AWS Lambda
- AWS Step Functions
- Amazon S3
- Amazon CloudWatch

**Sua resposta:**

```md
O processamento será orquestrado pelo AWS Step Functions, que coordenará as etapas de classificação, extração, normalização, enriquecimento e indexação.

PDF digital:ata_reuniao_vendas_sa.pdf

Uma função AWS Lambda utilizará uma biblioteca compatível com o ambiente de execução para extrair o texto diretamente da camada textual do PDF.
O processo preservará, sempre que possível, a separação por página, os títulos e a ordem dos parágrafos.

O OCR não será utilizado inicialmente, pois o arquivo já possui texto digital. Caso uma página não contenha texto extraível ou apresente conteúdo em imagem, ela poderá ser encaminhada para uma etapa complementar com o Amazon Textract.

Imagem digitalizada:ata_resultados_vendas_novos_dados.png

A imagem será encaminhada ao Amazon Textract, que reconhecerá os caracteres e retornará blocos de texto, linhas e palavras, além de informações de confiança do reconhecimento.

Uma função Lambda processará o resultado, organizará o texto na ordem de leitura e preservará as referências à página e aos blocos extraídos.

Se a confiança do reconhecimento estiver abaixo de um limite definido pela empresa, o documento será marcado para revisão humana.

Arquivo CSV:vendas_sa_dados_ficticios_laboratorio.csv

O CSV será processado como dados estruturados, e não como um documento corrido.

Uma função Lambda ou um processo de dados compatível com o volume realizará:

Leitura do cabeçalho e validação das 19 colunas esperadas.
Verificação da quantidade de registros.
Padronização de datas, números e valores monetários conforme os tipos reais das colunas.
Identificação de campos vazios, duplicidades e registros inválidos.
Preservação de um identificador por oportunidade, utilizando a chave existente ou criando uma chave técnica.
Conversão de cada oportunidade em uma representação textual organizada, quando for útil para a busca semântica.

Os dados tabulares também serão mantidos em formato estruturado, preferencialmente Parquet após a validação, para análises futuras com Amazon Athena e AWS Glue Data Catalog.
```

---

## 2.4 Tratamento de falhas

Explique como sua solução identificaria e registraria erros de processamento.

**Sua resposta:**

```md
O AWS Step Functions controlará o fluxo de processamento e definirá políticas de repetição para falhas temporárias, como limites de requisição ou indisponibilidade momentânea de serviços.
As principais medidas serão:

Configurar tentativas automáticas com intervalos progressivos para erros transitórios.
Separar falhas recuperáveis de erros permanentes, como arquivos corrompidos ou formatos inválidos.
Registrar o identificador do documento, a etapa que falhou, o horário e a mensagem de erro.
Utilizar Amazon CloudWatch Logs e métricas para acompanhar a execução.
Criar alarmes no CloudWatch para falhas recorrentes e aumento de documentos pendentes.
Manter registros de erro no S3 e disponibilizar um fluxo de reprocessamento.
Utilizar AWS CloudTrail para auditar operações de acesso e alterações de configuração.

Cada documento terá um estado de processamento, como RECEIVED,PROCESSING,PROCESSED,REVIEW_REQUIRED ou FAILED.

Isso permitirá identificar documentos que não foram processados e reexecutar apenas as etapas necessárias, sem repetir todo o pipeline.
```

---

# ✅ Quest 3: A Relíquia dos Metadados

## 3.1 Padronização dos textos processados

Explique como os textos extraídos seriam limpos, normalizados e preparados para consulta.

**Sua resposta:**

```md
Após a extração, os textos serão submetidos a uma etapa de normalização implementada em AWS Lambda.

Essa etapa incluirá:

1.
Normalização de caracteres e codificação para UTF-8.
2.
Remoção de caracteres inválidos e ruídos introduzidos pela extração.
3.
Ajuste de quebras de linha, espaços duplicados e parágrafos.
4.
Preservação de títulos, listas, tabelas e separações por página sempre que relevantes.
5.
Padronização de datas para um formato comum, preferencialmente ISO 8601.
6.
Padronização de nomes de campos, categorias e identificadores.
7.
Identificação de duplicidades por hash e comparação de conteúdo.
8.
Preservação de valores originais quando uma transformação puder alterar o significado.
9.
Validação dos campos obrigatórios e registro de informações ausentes.
10.
Divisão dos documentos em seções para facilitar a busca.

No CSV, a normalização será realizada por coluna, sem eliminar registros nem modificar silenciosamente os valores originais.

O resultado será armazenado em um formato estruturado, como JSON, acompanhado do texto normalizado e dos dados necessários para rastrear a fonte.
```

---

## 3.2 Metadados propostos

Defina quais metadados você extrairia de cada documento.

| Metadado | Por que ele é importante? |
|---|---|
| Nome do documento | Identifica o arquivo original. |
| Tipo do documento | Determina como o conteúdo deve ser interpretado. |
| Data identificada | Permite pesquisas por período e contextualização temporal. |
| Tema principal | Organiza documentos por assunto. |
| Participantes | Permite encontrar reuniões e decisões relacionadas a pessoas |
| Decisões tomadas | Recupera deliberações registradas nos documentos |
| Responsáveis | Identifica quem ficou encarregado de cada ação. |
| Próximos passos | Permite localizar tarefas e pendências. |
| Nível de confidencialidade | Controla a exposição de informações sensíveis |
| Caminho do arquivo original | Mantém a ligação entre o conhecimento e sua fonte. |

Adicione outros metadados, se necessário.

---

## 3.3 Uso de IA para enriquecimento dos documentos

Explique como o Amazon Bedrock poderia ajudar a identificar temas, decisões, responsáveis, pendências e resumos dos documentos.

**Sua resposta:**

```md
O Amazon Bedrock será utilizado para analisar os textos normalizados e gerar informações estruturadas que facilitem a consulta.
Um modelo de linguagem receberá o conteúdo e instruções para identificar:

Tema principal e temas secundários.
Resumo executivo.
Decisões explicitamente registradas.
Participantes e responsáveis.
Próximas ações, prazos e pendências.
Riscos, problemas e oportunidades.
Relações entre projetos e documentos.
Palavras-chave para classificação.

A saída será solicitada em um formato estruturado, como JSON, para facilitar a validação e o armazenamento.

A IA deverá diferenciar decisões aprovadas de propostas, hipóteses ou assuntos apenas discutidos. Cada item extraído deverá incluir uma referência ao trecho de origem sempre que possível.

A solução também utilizará validações para verificar se os campos obrigatórios estão presentes e se as referências de origem são válidas.

Informações ambíguas ou extraídas de trechos com baixa confiança serão encaminhadas para revisão humana. Os resultados gerados por IA serão tratados como dados derivados, sem substituir o documento original.
```

---

## 3.4 Armazenamento dos metadados

Explique onde os metadados seriam armazenados e como seriam conectados aos documentos originais.

Serviços que você pode considerar:

- Amazon S3
- Amazon DynamoDB
- AWS Glue Data Catalog
- Amazon Bedrock Knowledge Bases

**Sua resposta:**

```md
Proponho separar os metadados operacionais, os documentos processados e os índices de busca.

Amazon S3:
armazenará os arquivos originais, os textos normalizados, os JSONs de metadados e as representações textuais das oportunidades do CRM.
Amazon DynamoDB:
armazenará registros operacionais por documento, incluindo
document_id, status, versão de origem, hash, datas de processamento, metadados principais e referências aos objetos S3.
AWS Glue Data Catalog:
registrará os esquemas das tabelas estruturadas, permitindo que o Amazon Athena consulte os dados do CRM.
Amazon Bedrock Knowledge Bases:
utilizará os documentos preparados no S3 como fonte de conhecimento e gerenciará o processo de recuperação e indexação semântica.

O identificador document_id será a chave de ligação entre os registros do DynamoDB e os objetos armazenados no S3. Os documentos indexados também terão metadados de origem para que a Wiki consiga apresentar as fontes utilizadas em cada resposta.

O Amazon DynamoDB não será utilizado como substituto da base vetorial. Cada serviço terá uma responsabilidade específica.
```

---

# ✅ Quest 4: O Oráculo da Wiki Inteligente

## 4.1 Estratégia de indexação

Explique como os documentos seriam divididos em trechos menores e preparados para busca semântica.

**Sua resposta:**

```md
Os documentos processados serão preparados para indexação no Amazon Bedrock Knowledge Bases.
Os textos serão divididos em trechos menores, chamados
chunks, preservando o contexto semântico.
Para atas, a divisão priorizará títulos, seções, tópicos e parágrafos relacionados. O objetivo é evitar que uma decisão fique separada do seu responsável ou prazo.
Para a imagem digitalizada, os trechos serão criados a partir do texto extraído pelo Textract, preservando a referência à página original.
Para o CSV, cada oportunidade poderá ser transformada em um registro textual independente, contendo os nomes dos campos e seus respectivos valores. Por exemplo, o registro poderá ser representado por linhas como Etapa: ...,Valor: ... e Responsável: ..., conforme as colunas realmente existentes.

Essa representação textual será utilizada para busca semântica, enquanto os dados tabulares originais permanecerão disponíveis para consultas analíticas.

Cada trecho terá metadados como:

document_id;
source_key;
document_type;
department;
confidentiality;
source_version_id;
page_number, quando aplicável;
chunk_id.

A estratégia de divisão e o tamanho dos trechos serão ajustados por testes de recuperação, considerando o tamanho dos documentos e a preservação do contexto. Quando necessário, haverá sobreposição entre trechos consecutivos para evitar perda de informações nas fronteiras.
```

---

## 4.2 Busca semântica e base vetorial

Explique como embeddings seriam gerados e onde seriam armazenados.

Serviços que você pode considerar:

- Amazon Bedrock Knowledge Bases
- Amazon OpenSearch Serverless
- Amazon Aurora PostgreSQL com pgvector
- Amazon S3 Vectors
- Modelos de embeddings no Amazon Bedrock

**Sua resposta:**

```md
A busca semântica será implementada com Amazon Bedrock Knowledge Bases e um modelo de embeddings disponível no Amazon Bedrock.

O processo funcionará da seguinte maneira:

1.
O conteúdo preparado será disponibilizado no S3.
2.
A Knowledge Base processará os documentos e seus metadados.
3.
Um modelo de embeddings converterá cada trecho textual em uma representação vetorial numérica.
4.
Os vetores serão armazenados em um mecanismo de recuperação compatível, como o Amazon OpenSearch Serverless configurado como armazenamento vetorial.
5.
Quando o usuário fizer uma pergunta, o sistema gerará o embedding da consulta.
6.
A busca recuperará os trechos semanticamente mais relevantes, respeitando os filtros de metadados aplicáveis.
7.
Os trechos recuperados serão enviados ao modelo de linguagem para fundamentar a resposta.

Essa abordagem permite recuperar informações mesmo quando a pergunta utiliza palavras diferentes das existentes no documento original.

Por exemplo, uma consulta sobre “ações comerciais pendentes” poderá encontrar um trecho que utiliza a expressão “próximas atividades de vendas”.

Os filtros por data, departamento, projeto e tipo documental complementarão a busca semântica.
```

---

## 4.3 Geração de respostas com IA

Explique como a Wiki responderia perguntas em linguagem natural com base nos documentos originais.

Considere explicar:

- Como a pergunta do usuário seria recebida;
- Como os trechos relevantes seriam recuperados;
- Como o Amazon Bedrock geraria a resposta;
- Como a resposta indicaria as fontes utilizadas.

**Sua resposta:**

```md
A Wiki utilizará a abordagem RAG ( Retrieval-Augmented Generation), combinando recuperação de documentos com geração de linguagem natural.

O fluxo será:
1.
O conteúdo preparado será disponibilizado no S3.
2.
A Knowledge Base processará os documentos e seus metadados.
3.
Um modelo de embeddings converterá cada trecho textual em uma representação vetorial numérica.
4.
Os vetores serão armazenados em um mecanismo de recuperação compatível, como o Amazon OpenSearch Serverless configurado como armazenamento vetorial.
5.
Quando o usuário fizer uma pergunta, o sistema gerará o embedding da consulta.
6.
A busca recuperará os trechos semanticamente mais relevantes, respeitando os filtros de metadados aplicáveis.
7.
Os trechos recuperados serão enviados ao modelo de linguagem para fundamentar a resposta.
1.
O usuário envia uma pergunta em linguagem natural pela interface.
2.
O sistema autentica o usuário e verifica suas permissões.
3.
A consulta é encaminhada para a camada de aplicação.
4.
O Amazon Bedrock Knowledge Bases recupera os trechos relevantes da base de conhecimento.
5.
O Amazon Bedrock utiliza esses trechos como contexto para gerar uma resposta.
6.
A resposta é validada e apresentada com referências às fontes utilizadas.

O modelo será instruído a responder somente com base nas evidências recuperadas, indicar quando não houver informação suficiente e não inventar datas, decisões ou responsáveis.

A resposta poderá apresentar:

Resumo objetivo.
Decisões encontradas.
Pessoas envolvidas.
Datas e prazos identificados.
Próximos passos e pendências.
Nome do documento de origem.
Referência à página ou ao trecho correspondente, quando disponível.
Link autorizado para consultar o documento original.

Por exemplo, diante da pergunta “Quais decisões foram tomadas sobre a expansão comercial?”, a Wiki pesquisará os documentos indexados, reunirá os trechos relevantes e elaborará uma síntese com as respectivas fontes.
```

---

## 4.4 Interface de consulta

Proponha como os usuários acessariam essa Wiki Inteligente.

Serviços que você pode considerar:

- Amazon Q Business
- AWS Amplify
- Amazon API Gateway
- AWS Lambda
- Amazon Cognito

**Sua resposta:**

```md
Para disponibilizar a Wiki aos funcionários, proponho uma aplicação web interna.
A arquitetura de interface utilizará:

AWS Amplify:
hospedagem e publicação da aplicação web.
Amazon Cognito:
autenticação dos usuários e gerenciamento de identidade.
Amazon API Gateway:
exposição controlada dos endpoints de consulta.
AWS Lambda:
validação das solicitações, aplicação de regras de negócio e integração com o Bedrock Knowledge Bases.
Amazon Bedrock:
geração das respostas em linguagem natural.

A interface oferecerá:

Campo de pesquisa em linguagem natural.
Filtros por período, departamento, projeto e tipo de documento.
Resumos de documentos.
Lista de fontes e links para os arquivos originais.
Histórico de consultas, conforme a política de retenção da empresa.
Opção para sinalizar respostas incorretas ou incompletas.

O acesso será protegido por autenticação e autorização. As permissões do usuário serão aplicadas antes da recuperação dos documentos, por meio de filtros confiáveis e verificações no backend.
```

---

## 4.5 Segurança, auditoria e monitoramento

Explique como controlar acesso, proteger dados, auditar consultas e monitorar custos, erros e qualidade das respostas.

Serviços que você pode considerar:

- AWS IAM
- AWS KMS
- Amazon Cognito
- AWS CloudTrail
- Amazon CloudWatch
- Amazon Macie
- AWS Cost Explorer

**Sua resposta:**

```md
A segurança será tratada como requisito fundamental desde a ingestão até a consulta.

Controle de acesso

AWS IAM: permissões de menor privilégio para usuários, serviços e funções.
Amazon Cognito: autenticação dos usuários da Wiki.
AWS KMS: criptografia de dados em repouso, conforme a configuração de cada serviço.
Amazon S3 Block Public Access: bloqueio de exposição pública dos arquivos.
Amazon Macie: descoberta e identificação de possíveis dados sensíveis nos objetos S3, conforme os recursos e formatos suportados.
Filtros de autorização: restrição de documentos por departamento, classificação e permissões do usuário.

A autorização será validada no backend. Os usuários não terão acesso direto aos buckets, aos índices vetoriais ou aos recursos internos de processamento, salvo quando explicitamente autorizado.

Auditoria
O AWS CloudTrail registrará eventos de API e operações relevantes para auditoria. Os logs de aplicação registrarão informações como identificador da consulta, usuário autorizado, horário, fontes recuperadas e resultado do processamento, respeitando as políticas de privacidade.

O conteúdo integral das perguntas e respostas não será registrado indiscriminadamente. Quando houver necessidade de armazená-lo para auditoria, serão aplicadas regras de retenção, controle de acesso e proteção de dados.

Monitoramento

O Amazon CloudWatch será utilizado para acompanhar:

Falhas de execução no Step Functions e no Lambda.
Quantidade de documentos processados.
Documentos pendentes ou encaminhados para revisão.
Latência das consultas.
Erros na recuperação e na geração das respostas.
Consumo de recursos e métricas operacionais disponíveis.
Alarmes para anomalias e falhas recorrentes.

Custos e qualidade

O AWS Cost Explorer ajudará a analisar os gastos por serviço e período. Quando apropriado, serão configurados orçamentos e alertas de custo.

A qualidade da Wiki será avaliada por meio de testes com perguntas conhecidas, conferência das fontes, precisão das respostas e avaliação de usuários.

Também serão definidos limites de tamanho de documentos, políticas de reprocessamento e monitoramento de consumo de tokens para controlar custos.
```

---

# 🧩 Arquitetura Final da Solução

Agora reúna tudo em uma visão única.

## 1. Visão geral

Explique em poucas linhas a ideia central da sua arquitetura.

**Sua resposta:**

```md
A proposta consiste em construir uma Wiki Corporativa Inteligente utilizando exclusivamente serviços da AWS.
O Amazon S3 será o repositório central dos arquivos originais e processados. O AWS Step Functions coordenará o pipeline, enquanto o AWS Lambda realizará a classificação, a extração e a normalização dos dados. O Amazon Textract tratará documentos digitalizados, e o Amazon Bedrock ajudará a enriquecer o conteúdo com metadados e resumos.
O Amazon Bedrock Knowledge Bases será responsável pela recuperação semântica dos documentos, utilizando embeddings e uma base vetorial compatível. A interface web permitirá que funcionários autenticados façam perguntas em linguagem natural e recebam respostas fundamentadas nos arquivos originais.
A arquitetura prioriza segurança, rastreabilidade, escalabilidade, tratamento de falhas e controle de custos.
Falhas de execução no Step Functions e no Lambda.
Quantidade de documentos processados.
Documentos pendentes ou encaminhados para revisão.
Latência das consultas.
Erros na recuperação e na geração das respostas.
Consumo de recursos e métricas operacionais disponíveis.
Alarmes para anomalias e falhas recorrentes
```

---

## 2. Serviços AWS utilizados

| Serviço AWS | Papel na solução |
|---|---|
| Amazon S3 | Armazenar arquivos originais, textos processados,metadados e documentos preparados para indexação |
| Amazon Textract | Extrair texto de imagens e documentos digitalizados porOCR. |
| Amazon Bedrock | Gerar resumos, identificar temas e metadados e formularrespostas com base nas fontes recuperadas. |
| Amazon Bedrock Knowledge Bases | Gerenciar a ingestão e a recuperação de conhecimento parabusca semântica e RAG. |
| AWS Lambda | Executar classificação, extração, limpeza, validação,enriquecimento e integração entre serviços. |
| AWS Step Functions | Orquestrar as etapas, controlar estados, tratar falhas epermitir reprocessamento. |
| Amazon CloudWatch | Centralizar logs, métricas, alarmes e acompanhamentooperacional. |
| AWS IAM | Controlar permissões entre usuários e serviços |
| AWS KMS | Gerenciar chaves de criptografia. |

A seleção final dos serviços vetoriais e de armazenamento poderá ser ajustada conforme o volume de documentos, os requisitos de latência, a segurança e o orçamento.

---

## 3. Fluxo de dados de ponta a ponta

Descreva o caminho dos dados desde a pasta `raw/` até a Wiki Inteligente.

```md
Exemplo de estrutura:

1. Arquivos estão inicialmente na pasta raw/
2. Arquivos são enviados para o Amazon S3
3. Documentos escaneados passam pelo Amazon Textract
4. Arquivos digitais têm seus textos extraídos
5. Textos são limpos e padronizados
6. Metadados são extraídos
7. Conteúdos são indexados em uma base pesquisável
8. Usuário pesquisa na Wiki
9. IA responde com base nos documentos originais
```

**Sua resposta:**

```md
1.
Os arquivos são disponibilizados na pasta local
raw/
.
2.
Os três arquivos são enviados para um bucket privado do Amazon S3.
3.
O sistema registra o identificador, o hash e a versão de cada arquivo.
4.
A chegada dos arquivos inicia o processamento, por meio de eventos do S3 e AWS Lambda, ou de uma execução controlada do AWS Step Functions.
5.
O Step Functions coordena a classificação de cada arquivo.
6.
O PDF digital passa pela extração direta de texto.
7.
A imagem digitalizada é processada pelo Amazon Textract.
8.
O CSV é validado e transformado em dados estruturados, preservando as 19 colunas e os registros.
9.
Os resultados são limpos, normalizados e validados por funções Lambda.
10.
O Amazon Bedrock enriquece o conteúdo com resumos, temas, decisões e outros metadados, conforme as evidências disponíveis.
11.
Os metadados operacionais são armazenados no DynamoDB, e os resultados são salvos no S3.
12.
Os dados tabulares são catalogados no AWS Glue Data Catalog e podem ser consultados pelo Amazon Athena.
13.
Os documentos textuais preparados são disponibilizados para o Amazon Bedrock Knowledge Bases.
14.
A Knowledge Base gera embeddings e os armazena no mecanismo vetorial configurado.
15.
O usuário acessa a aplicação web hospedada no AWS Amplify e autentica-se pelo Amazon Cognito.
16.
A aplicação envia a pergunta para o Amazon API Gateway e o AWS Lambda, que verificam a autorização.
17.
O backend consulta a Knowledge Base e recupera os trechos relevantes.
18.
O Amazon Bedrock gera uma resposta fundamentada nos trechos recuperados.
19.
A interface apresenta a resposta com fontes, datas e links autorizados para os documentos originais.
20.
O Amazon CloudWatch, o AWS CloudTrail e as ferramentas de custos acompanham a operação e permitem auditoria.
```

---

## 4. Diagrama textual da arquitetura

Crie um diagrama simples usando texto.

```md
Exemplo:

raw/ → Amazon S3 → Lambda/Step Functions → Textract → S3 Processado → Bedrock Knowledge Bases → Interface de Consulta → Usuário Final
```

**Sua resposta:**

```md
USUÁRIOS CORPORATIVOS | v Amazon Cognito (autenticação) | v AWS Amplify (interface) | v Amazon API Gateway | v AWS Lambda (API) | Validação de acesso | v Amazon Bedrock Knowledge Bases | Recuperação semântica | v Amazon OpenSearch Serverless | Trechos relevantes | v Amazon Bedrock | v Resposta com fontes e citações
```

---

## 5. Riscos e limitações

Liste possíveis desafios da sua solução.

```md
Exemplo:
- Documentos ilegíveis podem prejudicar a extração de texto.
- OCR pode gerar erros em documentos com baixa qualidade.
- Custos podem aumentar conforme o volume de documentos.
- Metadados inferidos por IA podem precisar de validação humana.
- Respostas geradas por IA devem sempre referenciar documentos de origem.
```

**Sua resposta:**

```md
Os principais riscos e limitações da solução são:

OCR imperfeito:
imagens de baixa qualidade podem gerar textos incorretos.
Erros de extração:
PDFs complexos podem apresentar tabelas, colunas e elementos fora da ordem de leitura.
Alucinações da IA:
o modelo pode produzir informações incorretas se as instruções ou as evidências forem insuficientes.
Metadados incorretos:
responsáveis, decisões e prazos inferidos precisam de validação.
Busca incompleta:
uma estratégia inadequada de divisão dos textos pode deixar de recuperar trechos relevantes.
Dados tabulares:
a conversão do CSV em texto não substitui consultas analíticas estruturadas.
Segurança:
filtros inadequados podem permitir a exposição de documentos a usuários sem autorização.
Custo:
o consumo do Textract, do Bedrock, da base vetorial e das funções de processamento pode aumentar com o volume.
Limites de serviço:
tamanhos máximos de documentos, cotas de requisição e limites de execução devem ser considerados.
Sincronização:
alterações nos documentos precisam refletir-se na indexação e nos metadados.
Retenção e exclusão:
remover um documento do S3 não garante, por si só, a remoção imediata de todas as suas cópias, versões e representações indexadas.
Qualidade das fontes:
a IA não consegue recuperar informações que não estejam presentes ou legíveis nos documentos processados.

Para reduzir esses riscos, serão implementados testes de qualidade, revisão humana, controle de acesso, rastreabilidade, monitoramento, políticas de retenção e procedimentos de atualização e exclusão dos índices.
```

---

## 6. Melhorias futuras

Descreva como a solução poderia evoluir.

```md
Exemplo:
- Criar uma interface web para consulta.
- Criar um chat interno para perguntas sobre atas.
- Adicionar controle de acesso por departamento.
- Criar dashboard de decisões e pendências.
- Gerar alertas automáticos sobre ações em aberto.
- Integrar com ferramentas corporativas.
```

**Sua resposta:**

```md
A solução poderá evoluir com as seguintes funcionalidades:

Criar uma interface de chat corporativo para consultas sobre reuniões, projetos e oportunidades comerciais.

Implementar controle de acesso por departamento, projeto e nível de confidencialidade.
Criar dashboards com decisões, pendências, responsáveis e prazos.
Gerar alertas automáticos para tarefas próximas do vencimento.
Integrar os documentos a outros repositórios corporativos por meio de conectores autorizados.
Criar fluxos de aprovação humana para metadados e decisões críticas.
Implementar avaliações automatizadas da qualidade das respostas e da recuperação semântica.
Criar relatórios de uso, custos, latência e satisfação dos usuários.
Adicionar suporte a novos formatos, como DOCX, TXT e outros tipos de PDF.
Implementar versionamento dos metadados e atualização incremental dos índices.
Desenvolver consultas analíticas estruturadas sobre os dados comerciais por meio do Amazon Athena.
Adicionar políticas de retenção, exclusão e anonimização conforme as necessidades da empresa.
```

---

# 🧠 Checklist Final

Antes de entregar, confirme se sua solução responde:

Como transformar documentos escaneados em texto? Utilizando o Amazon Textract para OCR.
- [ ] Como lidar com diferentes formatos dentro da mesma pasta `raw/`?  Classificando cada arquivo e aplicando um pipeline específico.
- [ ] Como armazenar os documentos originais? Utilizando Amazon S3, versionamento, criptografia e permissões restritas.
- [ ] Como preservar a rastreabilidade entre resposta e documento fonte? Mantendo identificadores, hashes, versões, páginas de origem e referências aos objetos S3.
- [ ] Como organizar metadados? Utilizando registros padronizados no DynamoDB e esquemas no AWS Glue Data Catalog.
- [ ] Como criar busca semântica? Gerando embeddings com modelos do Amazon Bedrock e recuperando os vetores por meio da Knowledge Base.
- [ ] Como usar Amazon Bedrock na solução? Para enriquecer documentos, gerar resumos e formular respostas fundamentadas nas fontes
- [ ] Como proteger documentos sensíveis? Aplicando IAM, Cognito, KMS, bloqueio de acesso público e autorização no backend.
- [ ] Como monitorar falhas? Utilizando Step Functions, CloudWatch e registros de auditoria.
- [ ] Como a empresa usaria essa Wiki no dia a dia? Por uma interface web autenticada, com perguntas em linguagem natural, filtros e referências aos documentos originais.

---

# 🏁 Conclusão

Escreva uma breve conclusão defendendo sua solução como se estivesse apresentando para uma liderança técnica ou de negócio.

**Sua resposta:**

```md
A proposta transforma um conjunto de arquivos corporativos heterogêneos em uma fonte de conhecimento organizada, segura e pesquisável. A combinação de Amazon S3, AWS Lambda, AWS Step Functions, Amazon Textract e Amazon Bedrock permite automatizar a ingestão, a extração, a normalização e o enriquecimento dos documentos. O Amazon Bedrock Knowledge Bases viabiliza a busca semântica e a geração de respostas contextualizadas, enquanto os mecanismos de autenticação, autorização, criptografia, auditoria e monitoramento protegem a informação corporativa.
A solução também reconhece que dados tabulares, como as oportunidades do CRM, exigem tratamento próprio e podem ser consultados analiticamente com Amazon Athena. Com rastreabilidade até os documentos originais, revisão de informações incertas e monitoramento contínuo, a Wiki poderá reduzir o tempo de busca, facilitar a recuperação de decisões e apoiar a tomada de decisões baseada em evidências.
Assim, a empresa deixa de depender exclusivamente da busca manual por arquivos e passa a contar com uma plataforma inteligente de conhecimento, preparada para crescer de forma controlada e segura dentro do ecossistema AWS.
```
