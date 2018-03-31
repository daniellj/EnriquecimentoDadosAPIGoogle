## Problema

Este mini-projeto foi proposto pelo colaborador [Tiago Prates Carneiro](https://github.com/tpcarneiro), ao qual eu me propus a desenvolver. O link original do projeto é este: [etl-python](https://github.com/tpcarneiro/dev-etl-python).

Há um arquivo de texto ("data_points.txt") em anexo à este repositório, contendo uma lista de coordenadas geográficas obtidas a partir do GPS de um dispositivo móvel. Necessita-se que esse arquivo seja processado e enriquecido com informações disponíveis na internet. O objetivo do enriquecimento é descobrir os endereços correspondentes às coordenadas. Para essa finalidade deve ser utilizada alguma API aberta, como por exemplo: _Google Maps_.

## Requisitos

1. A solução deve ser implementada em Python;
2. A solução como um todo deve poder ser executada em ambiente Linux;
3. Todas as bibliotecas, produtos ou serviços utilizados devem ser open source;
4. As configurações de conexão com banco de dados devem ser parametrizadas;
5. Caso necessário, um script DDL deve ser fornecido e devidamente documentado;

## Insumos

Os dados necessários para o desenvolvimento da solução estão disponíveis no arquivo "data_points.txt"

O arquivo consiste de uma série de coordenadas geográficas compostas por:

- Latitude
- Longitude
- Distância

**OBS: Em todas as linhas, os três atributos são apresentados em dois formatos:**

- Em graus, minutos e segundos
- Em número decimal

Exemplo:

> Latitude: 30°02′59″S   -30.04982864  
> Longitude: 51°12′05″W   -51.20150245  
> Distance: 2.2959 km  Bearing: 137.352°  

## Resultados

O dataframe final deve ser salvo em um banco de dados relacional e precisa conter as seguintes informações:

latitude|longitude|rua|numero|bairro|cidade|cep|estado|pais|endereço completo
--------|---------|---|------|------|------|---|------|----|-----------------

# Lógica da solução desenvolvida

O desenvolvimento da rotina visa buscar o conteúdo de um arquivo de entrada, com o nome padrão esperado de "data_points.txt", encontrando suas coordenada geográficas (latitude e longitude). Para cada coordenada de entrada, busca enriquecer uma base de dados de endereços através de consultas na API do Google Maps. Na rotina, foram tratadoa eventos de falhas na aquisição e decodificação do arquivo JSON que é retornado pela API, assim como há uma checagem se os registros do arquivo já não existem no banco; se existem, não os insere para não gerar duplicidade. Se não encontrar um correspondente no banco de dados, insere o novo registro na tabela. As chaves para essa verificação são: latitude e longitude.

## Requerimentos de ambiente

- S.O.: Linux
- Interpretador Python 3.6 ou versão mais recente.
- SGBD relacional da sua preferência (os testes de execuções foram efetuados em um banco de dados MySQL).
- Ter uma conta no Google, onde deverá ser ativado o aplicativo Google Maps para utilização da API. A API do Google servirá como fonte para requisições de consulta a geolocalizções. Link: https://console.developers.google.com

## Instalação

1. - Abrir o terminal de linha de comando do Linux
2. - Acessar seu "home": cd /home/your_username/
3. - Criar a pasta "Projetos" ("/home/your_username/Projetos/")
4. - Criar a pasta "ETL_Python" ("/home/your_username/Projetos/ETL_Python/")
5. - Criar uma pasta com o nome "data" dentro de "/home/your_username/Projetos/ETL_Python/", que servirá para alocar o arquivo de entrada ("/home/your_username/Projetos/ETL_Python/data/data_points.txt")
6. - Fazer o download do arquivo "EnriquecimentoDados-APIGoogle.py" e alocar na pasta "/home/your_username/Projetos/ETL_Python/", assim como o download do arquivo "data_points.txt", alocando-o na pasta "/home/your_username/Projetos/ETL_Python/data/"
7. - Fazer download do script "CreateObjectsGeolocalizacao.sql". Executar o script DDL no banco de dados para criação da estrutura da tabela
8. - Instalar as dependência do projeto (Python Libraries): baixar o arquivo "requirements.txt", alocando-o na pasta "/home/your_username/Projetos/ETL_Python/". Após, via via terminal do Linux, acessar a pasta onde se encontra o arquivo "requirements.txt", e digite/execute o comando a seguir: pip install -r requirements.txt
9. - Abrir o script "EnriquecimentoDados-APIGoogle.py" e editar os valores das seguintes variáveis (LINHA - indica a linha que está a posição da variável para edição!):

#### Default Values - não modificar esses valores!
LINHA 16 - filename = "data_points"
LINHA 17 - extension = "txt"
LINHA 18 - TABLENAME = 'geolocalizacao'
LINHA 19 - DB_USER = 'geolocalizacao'
LINHA 20 - DB_PASS = 'Geolx!177'
LINHA 21 - DB_NAME = 'geolocalizacao'
LINHA 22 - compare=['latitude', 'longitude']

#### Environment Config. - ajustar esses valores!
LINHA 24 - filepath = "/home/your_username/Projetos/ETL_Python/data/"
LINHA 25 - APIKEYGOOGLEMAPS = '' #inserir sua chave para conexão à API do Google Maps
LINHA 26 - DB_HOST = 'MySQL Server HostName or IP_Address'
LINHA 27 - DB_PORT = 3306 # MySQL default Port

10. - Salve as alterações do script ("EnriquecimentoDados-APIGoogle.py").

Projeto implantado com sucesso.

## Execução

1. - Acessar a pasta onde se encontra o script: cd /home/your_username/Projetos/ETL_Python/
2. - Dar permissão de execução ao script "EnriquecimentoDados-APIGoogle.py": chmod +x EnriquecimentoDados-APIGoogle.py
3. - Executar o script "EnriquecimentoDados-APIGoogle.py" com o interpretador Python via linha de comando no terminal do Linux: python EnriquecimentoDados-APIGoogle.py

Após a execução e finalização do processo de integração, aparacerá uma mensagem em tela: "Processo de carga finalizado com sucesso!". Após isso, acesse o banco de dados e execute a query "SELECT * FROM geolocalizacao.geolocalizacao order by latitude, longitude". Espera-se que ocorram cargas incrementais e nunca tenha registros repetidos, por mais que se repita "N" vezes o processo com o mesmo arquivo da carga inicial ("data_points.txt").

# Agradecimentos
Agradço ao [Tiago Prates Carneiro](https://github.com/tpcarneiro) pelo desafio. Me fez rever muitos conceitos para atender os requisitos do projeto, o que foi enriquecedor (assim como o nome do projeto: Enriquecimento de Dados).