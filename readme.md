# Vaccmap

## COVID-19 Mapa de Vacinação Brasileiro

## Descrição da arquitetura adotada e seus componentes.

##### Pipe-and-Filter Pattern

O Pipe and Filter é um padrão de projeto arquitetônico que permite fluxo / assíncrono em processamento. Nesse padrão, decompõe-se uma tarefa que executa processamento complexo em uma série de elementos separados que podem ser reutilizados pode melhorar o desempenho, a escalabilidade e a capacidade de reutilização, permitindo que os elementos da tarefa que realizam o processamento sejam implantados e escalonados independentemente.

<p align="center">
  <img src="https://github.com/vidalmatheus/fsquare/blob/master/img/filteronproject.png?raw=true" />
</p>

#  Descrição dos serviços disponibilizados com suas interfaces e forma de uso.

O Vaccmap possibilita ao usuário o consumo de dados por meio de uma *API*, onde pode-se obter dados pré-filtrados sobre a vacinação em diferentes locais do Brasil. O cliente é capaz de selecionar uma cidade, estado e um tipo de vacina, assim, requisitar dados através de uma *jsonAPI*.  Sendo assim, a aplicação atua como um intermediário entre o banco de informações de vacinação nacional, o Portal Brasileiro de Dados Abertos, de forma a disponibilizar ao usuário dados previamente filtrados.

Desse modo, primeiramente a aplicação utiliza da *API* fornecida pelo *Dados.gov.br*, para fazer uma chamada de serviço:

```python
class VaccinationAPI():
    data_set = set()
    base_url = 'https://imunizacao-es.saude.gov.br'
    username = 'imunizacao_public'
    password = '********'
    auth_params = (username, password)

    ...

    def _make_request(self, method, endpoint, json, counter=0) -> dict:
    url = f'{self.base_url}/{endpoint}'
    try:
        resp = requests.request(method, url, json=json, auth=self.auth_params)
    except Exception:
        return self._handle_error(method, endpoint, json, counter)
    resp = self._check_response(method, endpoint, json, resp, counter)
    return resp
```

Após isso, os dados obtidos na resposta da requisição serão filtrados, de acordo com o que o cliente deseja consumir.
 


<p align="center">
  <img src="https://github.com/vidalmatheus/fsquare/blob/master/img/system_design.png?raw=true" />
</p>

# Manual de Instalação

## A) Se você deseja apenas executar o front-end simulado (mocked), execute as seguintes etapas:

+ ### 1) Instale o  nvm (https://github.com/creationix/nvm)

+ ### 2) Instale as dependencias:
```bash
> cd frontend
> nvm use 9
> npm i
```

+ ### 3) Execute o mocked frontend:
```bash
> npm run dev
```

## B) Para o uso do backend com o frontend, faça as seguintes etapas:

+ ### 1) Crie um python3.6 virtual environment
```bash
> python3.6 -m venv vaccmap
```

+ ### 2) Instale o nvm (https://github.com/creationix/nvm)

+ ### 3) Adicione this alias to your .bashrc
```bash
alias vaccmap='cd /home/matheusvidal/Documents/vaccmap;
       . ../.virtualenv/vaccmap/bin/activate;
       nvm use 9;
       . dev.sh'
```

+ ### 4) Na pasta do projeto, instale as dependências:
```bash
> vaccmap
> pip install -r requirements.txt
```

+ ### 5) Inicie o servidor e o container com o banco de dados:
```bash
> dkpgnginx
```

+ ### 6) Em outro terminal, execute as migrações:
```bash
> vaccmap
> ./manage.py migrate
```

+ ### 7) Crie uma user application no banco de dados:
```bash
> ./manage.py createsuperuser
```

+ ### 8) Para rodar o backend:
```bash
> ./manage runserver
```

+ ### 9) Para todar o front:
```bash
> vaccmap
> cd frontend
> npm i
> API_MOCK=0 npm run dev
```

# Comandos Dev

Ao executar o script de shell `dev.sh`, você terá os seguintes comandos para ajudar no seu desenvolvimento:

- **devhelp**: Imprime comandos de ajuda

- **dkdev**: Executa um *dockerized* _postgresql_, _nginx_ e _metabase_

- **dkdb**: Executa um *dockerized* _postgresql_ database na porta 5432

- **dknginx**: Executa um *dockerized* _nginx_ na porta 80 para 8000 (back), 3000 (front), 3001 (mocked front) e 3002 (metabase)

- **pytest**: Executa os testes

- **pycoverage**: Executa os testes em python com flake8 e relatório de cobertura

- **dkbuild**: Constrói uma imagem docker para o projeto

- **dkfulldev**: Executa um *dockerized* _postgresql_, _nginx_, _metabase_ and the project image
