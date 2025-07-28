Documentação do Fluxo n8n: Controle de Seguidores
Visão Geral
Este fluxo do n8n foi projetado para coletar e atualizar a contagem de seguidores de perfis em diversas redes sociais (YouTube, Instagram e Twitch) e registrar esses dados em uma planilha do Google Sheets. O objetivo principal é automatizar o scraping e facilitar o monitoramento e a atualização das informações de seguidores.

<img width="1122" height="606" alt="image" src="https://github.com/user-attachments/assets/8468a7f5-874a-4937-84ea-47bc0370af38" />


Como Funciona
O fluxo opera da seguinte forma:

Início do Fluxo (When clicking ‘Execute workflow’): O fluxo é iniciado manualmente.

Base de Dados (Google Sheets): O primeiro passo é ler os dados de uma planilha do Google Sheets que contém as informações dos canais/perfis a serem monitorados, incluindo a URL e um ID.

Planilha: Clientes (ID: 1u1SL5P-DWc613ojFdmynywCjzVoQgWWdPPECXrFb_JE)

Aba: Folha1 (gid=0)

Contador de Itens e Separador (Split In Batches): Cada linha da planilha (que representa um perfil de rede social) é processada individualmente. Isso garante que cada URL seja tratada de forma independente.

Espera (Wait): Há uma pausa (em minutos, não especificado no JSON) entre o processamento de cada item. Essa espera é crucial para evitar bloqueios, já que se trata de web scraping e muitas chamadas simultâneas podem levar ao bloqueio por parte dos sites.

Distribuição (Switch): Este nó direciona o fluxo com base na URL do perfil. Ele verifica se a URL é do YouTube, Instagram ou Twitch.

Saída 1 (YouTube): Se a URL começar com "https://www.youtube.com/" (indicando YouTube).

Saída 2 (Instagram): Se a URL começar com "https://www.instagram.com/" (indicando Instagram).

Saída 3 (Twitch): Se a URL começar com "https://twitchtracker.com/" (indicando Twitch).

Coleta de Dados por Plataforma:

Coleta YouTube (YouTube): Para perfis do YouTube, utiliza o nó nativo do YouTube para obter as estatísticas do canal, especificamente a contagem de inscritos (subscriberCount).

Coleta Instagram (HTTP Request): Para perfis do Instagram, faz uma requisição HTTP para a URL https://fanhub.pro/tucktools_user, passando o ID do perfil como parâmetro de consulta (username). Coleta os dados de seguidores da resposta JSON (user_followers).

Coleta Twitch (HTTP Request): Para perfis da Twitch, faz uma requisição HTTP para a URL fornecida na planilha.

Extração HTML (HTML) - Apenas para Twitch: Após a coleta da Twitch, este nó extrai a contagem de seguidores do conteúdo HTML da página, utilizando um seletor CSS específico (#content-wrapper > div:nth-child(5) > div > div.g-x-l.g-x-l-4 > div:nth-child(3) > div.g-x-s-value > span:nth-child(2)). O valor extraído é mapeado para a chave Twitch.

Edição de Dados (Set):

Edição YouTube: Mapeia a contagem de inscritos do YouTube ($json.statistics.subscriberCount) para uma nova propriedade chamada Youtube.

Edição Instagram: Mapeia a contagem de seguidores do Instagram ($json.user_followers) para uma nova propriedade chamada Instagram.

Unificar Dados (Merge): Este nó coleta os dados processados de todas as ramificações (YouTube, Instagram, Twitch) antes de enviá-los de volta para a planilha.

Atualização da Planilha (Google Sheets):

Adicionar Youtube (Google Sheets): Atualiza a coluna Seguidores na planilha do Google Sheets com o valor coletado do YouTube, usando a coluna Canal como chave para correspondência.

Adicionar Instagram (Google Sheets): Atualiza a coluna Seguidores na planilha do Google Sheets com o valor coletado do Instagram, usando a coluna Canal como chave para correspondência.

Adicionar Twitch (Google Sheets): Atualiza a coluna Seguidores na planilha do Google Sheets com o valor coletado da Twitch, usando a coluna Canal como chave para correspondência.

Credenciais Utilizadas
Google Sheets: Google Sheets account 2 (ID: xlL5EG2LIcuEHD1s)

YouTube: YouTube account (ID: DUQaNucnNJyaeV8V)

Configurações Importantes
Documento do Google Sheets: O fluxo está configurado para interagir com a planilha:

Nome: Clientes

URL: https://docs.google.com/spreadsheets/d/1u1SL5P-DWc613ojFdmynywCjzVoQgWWdPPUCXrFb_JE/edit?usp=drivesdk

Aba: Folha1 (gid=0)

Colunas Mapeadas para Atualização: Seguidores e Canal são as colunas primárias para interação no Google Sheets.

Requisições HTTP (Instagram e Twitch): Utilizam cabeçalhos específicos (User-Agent, Referer, Origin, etc.) para simular requisições de navegador e evitar bloqueios. O timeout para a coleta da Twitch é de 100.000 ms (100 segundos).

Observações
O fluxo assume que a planilha do Google Sheets contém as colunas Canal, Url e ID para cada perfil.

A extração de dados da Twitch é feita via scraping HTML, o que pode ser frágil e quebrar se a estrutura do site (twitchtracker.com) for alterada.

A parte de "Espera" (Wait) não tem um valor de tempo definido no JSON que você forneceu, o que significa que ele usará o tempo padrão configurado no nó "Wait" dentro do n8n.
