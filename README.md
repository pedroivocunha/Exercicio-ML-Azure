# Projeto de Aprendizado de Máquina

Este projeto foi desenvolvido como parte do exercício prático do curso "Microsoft Certified: Azure AI Fundamentals". Abaixo estão os passos seguidos para chegar à etapa final do exercício.

## Passos Seguidos

1. **Configuração do Ambiente**
   - Crie uma conta no Azure.
   - Configure um novo recurso de Machine Learning no portal do Azure.
   - Instale o SDK do Azure Machine Learning:
     ```bash
     pip install azureml-sdk
     ```

2. **Criação do Workspace**
   - No portal do Azure, crie um novo workspace de Machine Learning.
   - Configure o workspace no seu ambiente local:
     ```python
     from azureml.core import Workspace

     ws = Workspace.create(name='myworkspace',
                           subscription_id='your-subscription-id',
                           resource_group='your-resource-group',
                           create_resource_group=True,
                           location='your-location')
     ```

3. **Preparação dos Dados**
   - Carregue e prepare os dados para o treinamento do modelo.
   - Divida os dados em conjuntos de treinamento e teste.

4. **Criação e Treinamento do Modelo**
   - Defina o modelo de aprendizado de máquina.
   - Treine o modelo usando os dados preparados:
     ```python
     from azureml.train.automl import AutoMLConfig

     automl_config = AutoMLConfig(task='classification',
                                  primary_metric='accuracy',
                                  training_data=train_data,
                                  label_column_name='label',
                                  n_cross_validations=5)

     experiment = Experiment(ws, 'automl-classification')
     run = experiment.submit(automl_config)
     ```

5. **Implantação do Modelo**
   - Implante o modelo treinado como um serviço web no Azure:
     ```python
     from azureml.core.model import Model
     from azureml.core.webservice import AciWebservice, Webservice

     model = run.register_model(model_name='best_model', model_path='outputs/model.pkl')

     aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
     service = Model.deploy(workspace=ws,
                            name='myservice',
                            models=[model],
                            inference_config=inference_config,
                            deployment_config=aci_config)
     service.wait_for_deployment(show_output=True)
     ```

6. **Teste do Serviço Web**
   - Teste o serviço web implantado para garantir que está funcionando corretamente:
     ```python
     import requests

     url = service.scoring_uri
     headers = {'Content-Type': 'application/json'}
     data = { "input_data": {
      "columns": [
      "day",
      "mnth",
      "year",
      "season",
      "holiday",
      "weekday",
      "workingday",
      "weathersit",
      "temp",
      "atemp",
      "hum",
      "windspeed"
      ],
      "index": [0],
      "data": [[1,1,2022,2,0,1,1,2,0.3,0.3,0.3,0.3]]
      }
     }


     response = requests.post(url, json=data, headers=headers)
     print(response.json())
     ```

## Conclusão

Este `README.md` descreve os passos seguidos para configurar, treinar e implantar um modelo de aprendizado de máquina usando o Azure Machine Learning. O arquivo `.json` de pontos de extremidade também foi salvo neste repositório.
