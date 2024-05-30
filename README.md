# CP 3 :: Web XPL :: SSTI
Resolução de laboratórios do Portswigger da vulnerabilidade Server Side Template Injection. A seguinte imagem demonstra os dados da conta usada no Portswigger.

![Detalhes da conta usada](/imgs/0.png)

## Lab 01
A vulnerabilidade estava presente no parâmetro da *query* **message**, que era requerido quando um determinado produto não era encontrado. De acordo com a própria descrição do laboratório, a linguagem de *template* usada era o Embedded Ruby (ERB). Portanto, uma dentre tantas *tags* que poderiam ser usadas para a renderização de comandos enviados pelo usuário é a `<%=%>`, que se refere a uma *tag* que retorna os resultados de saída pelo comando. 

### Mensagem de erro produzida pela aplicação
![Imagem demonstrativa da mensagem de erro](/imgs/1.png)

### PoC da vulnerabilidade
![PoC da vulnerabilidade](/imgs/2.png)

### MD5 do arquivo `morale.txt`
![MD5 do arquivo](/imgs/3.png)

### Tipo do arquivo `morale.txt`
O binário `file` foi usado.
![Tipo do arquivo](/imgs/4.png)

### Cópia do arquivo `morale.txt` do servidor
Diversos métodos foram tentados, como abertura de um servidor HTTP com `http.server` em Python por parte do servidor (não foi possível acessar esse servidor HTTP criado), tentativa de requisição por parte do servidor ao computador local por meio do `ngrok` (ao que parece, o servidor falha em resolver nomes, e o `ngrok`, assim como outros sites que tentavam externalizar ou receber requisições, só usava `ipv6`, e não fui capaz de fazer sequer um `ping` em ipv6), uso do `scp`, que, no entanto, pede pela senha da máquina alvo e até mesmo renderização da função `link_to` do ERB que criaria um *link* para o arquivo (que infelizmente não estava presente). Por fim, a estratégia usada, depois de um pouco de pesquisa, foi converter o arquivo em base64 (utilizando o binário `base64`), copiar esse conteúdo, remover os espaços em branco (que aparentemente eram gerados no conteúdo da página), e finalmente, decodificar o base64 novamente. As hashes MD5 eram iguais e portanto, o conteúdo era o mesmo. As seguintes imagens demonstram a cópia do arquivo utilizando a estratégia citada.

*Duas estratégias para a remoção dos espaços em branco foram usadas, por meio do editor de texto `vim` e por um pequeno *script* feito em Python. Ambas estratégias estão demonstradas na segunda imagem.

![Convertendo o conteúdo do arquivo em base64](/imgs/5_1.png)
![Decodificando arquivo e teste das hashes](/imgs/5_2.png)

### Payload para finalização do lab
O *payload* utilizado foi o ``<%=`rm morale.txt`%>``. A tag utilizada renderiza a saída das expressões introduzidas nas tags, enquanto o uso das crases se refere a execução de comandos do sistema. Apesar disso, a função `system()` também poderia ser usada.

![Payload utilizado para finalização do lab](/imgs/6.png)

### Linguagem utilizada
A linguagem utilizada pelo ERB é o **Ruby**, linguagem dinâmica e de script orientada à objetos.

![Linguagem utilizada pelo ERB](/imgs/7.png)
