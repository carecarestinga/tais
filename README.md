# Tais - Assistente Virtual da Cultura

A Taís é uma assistente virtual desenvolvida pelo LAPPIS - Laboratório Avançado
de Produção, Pesquisa e Inovação em Software (FGA/UnB), em parceria com o
Ministério da Cultura.

O nome é uma sigla para Tecnologia de Aprendizado Interativo do Salic.
Ela tem como objetivo ajudar cidadãs e cidadãos a tirar dúvidas sobre a lei
Rouanet e sobre o incentivo a projetos culturais.

## Ambiente

### RocketChat

```sh
sudo docker-compose up -d rocketchat
# aguarde 3 minutos para o rocketchat terminar de levantar
sudo docker-compose up rouana
```

Para que a assistente virtual inicie a conversa você deve criar um `trigger`.
Para isso, entre no rocketchat como `admin`, e vá no painel do Livechat na
seção de Triggers, clique em `New Trigger`. Preencha o Trigger da seguinte forma:

```yaml
Enabled: Yes
Name: Start Talk
Description: Start Talk
Condition: Visitor time on site
    Value: 3
Action: Send Message
 Value: Impersonate next agent from queue
 Value: Olá, meu nome é Taís, sou assistente virtual do MinC! Você quer conversar sobre incentivo à cultura? 
```

O valor `http://localhost:8080/` deve ser a URL de acesso da Taís.

#### Instalação

Para colocar a Taís em um site você precisa inserir o seguinte Javascript na sua página

```js
<!-- Start of Rocket.Chat Livechat Script -->
<script type="text/javascript">
(function(w, d, s, u) {

    // !!! Mudar para o seu host AQUI !!!
    host = 'http://localhost:3000';
    // !!! ^^^^^^^^^^^^^^^^^^^^^^^^^^ !!!

    w.RocketChat = function(c) { w.RocketChat._.push(c) }; w.RocketChat._ = []; w.RocketChat.url = u;
    var h = d.getElementsByTagName(s)[0], j = d.createElement(s);
    j.async = true; j.src = host + '/packages/rocketchat_livechat/assets/rocketchat-livechat.min.js?_=201702160944';
    h.parentNode.insertBefore(j, h);
})(window, document, 'script', host + '/livechat');
</script>
<!-- End of Rocket.Chat Livechat Script -->
```

**Atenção**: Você precisa alterar a variavel `host` dentro do código acima para a url do site onde estará
o seu Rocket.Chat.

### Console

```sh
sudo docker build -t rouana -f docker/tais/Dockerfile .
sudo docker run --rm --name rouana -it -v $PWD/rouana:/rouana rouana python train.py all
```

## Site do Beta

### Ambiente de Desenvolvimento

#### Setup

```
sudo docker-compose run --rm web python manage.py migrate
sudo docker-compose run --rm web python manage.py createsuperuser
```

#### Execução

```
sudo docker-compose up -d web
```

Você pode acessar o site por padrão na url `localhost:8000`

## Analytics

### Setup

```
sudo docker-compose run --rm rouana python /analytics/setup_elastic.py setup
```

Lembre-se de setar as seguintes variaveis de ambiente no `docker-compose`

```
ENVIRONMENT_NAME=localhost
TAIS_VERSION=last-commit-hash
```

#### Execução

```
sudo docker-compose up -d kibana
```

Você pode acessar o kibana no `locahost:5601`

## Análise do bot

### Uso

* Levante o container do `bot notebook`

```sh
docker-compose up
```

* Acesse o notebook em `localhost:8888`

* Utilize o `token` mostrado no log do `jupyter-notebook` para acessar o notebook.
