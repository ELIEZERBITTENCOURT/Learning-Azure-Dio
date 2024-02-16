# Projeto de Previsão com Azure ML

Este é um projeto que visa criar um modelo de previsão usando o Azure Machine Learning (Azure ML) e configurar os pontos de extremidade para disponibilizar o modelo como um serviço web.

## Passo 1: Configuração do Ambiente Azure ML

1 - Criei uma conta Azure e acesse o portal Azure.
2 - Criei um novo recurso Azure Machine Learning.
3 - Criei um workspace do Azure ML e registre-o.
4 - Instalei as bibliotecas necessárias, incluindo o SDK Azure ML.

```bash
pip install azureml-sdk
```

## Passo 2: Desenvolvimento do Modelo

1 - Escolhi um conjunto de dados apropriado para o problema de previsão.
2 - Realizei a preparação dos dados, incluindo limpeza, transformação e divisão em conjuntos de treinamento e teste.
3 - Escolhi um algoritmo de machine learning adequado e treinei o modelo.

```bash
from azureml.core import Workspace, Experiment, RunConfiguration, ScriptRunConfig

# Carrega o workspace
ws = Workspace.from_config()

# Cria um experimento
experiment = Experiment(workspace=ws, name='nome_do_experimento')

# Configura o ambiente de execução
run_config = RunConfiguration()

# Defini o script de treinamento
script_config = ScriptRunConfig(source_directory='.',
                                script='script_de_treinamento.py',
                                run_config=run_config)

# Submete o experimento
run = experiment.submit(config=script_config)
```

## Passo 3: Implantação do Modelo

1 - Após treinar o modelo, registrei-o no Azure ML.
2 - Criei um ponto de extremidade de serviço web para implantar o modelo.

```bash
from azureml.core import Model
from azureml.core.webservice import AciWebservice, Webservice

# Registra o modelo
model = run.register_model(model_name='nome_do_modelo', model_path='outputs/model.pkl')

# Configura o ponto de extremidade
aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

# Implementa o modelo como um ponto de extremidade de serviço web
service = Model.deploy(workspace=ws,
                       name='nome_do_servico',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aciconfig)

# Aguarda a implantação
service.wait_for_deployment(show_output=True)
```

## Passo 4: Testar o Ponto de Extremidade

Após a implantação, testei o ponto de extremidade usando solicitações HTTP (pode usar a SDK Azure ML).

```bash
import json
import requests

# URL do ponto de extremidade
scoring_uri = service.scoring_uri

# Dados de exemplo para teste
data = {"data": [[1, 2, 3, 4]]}

# Envia uma solicitação HTTP para o ponto de extremidade
response = requests.post(scoring_uri, json.dumps(data), headers={'Content-Type':'application/json'})

# Exibe a resposta
print(response.json())
```
