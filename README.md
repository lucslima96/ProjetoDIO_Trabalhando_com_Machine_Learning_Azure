
# Azure Machine Learning Workspace e Treinamento de Modelo Automático

- Referências

- Criação do Azure Machine Learning Workspace

- Implantação e Teste do Modelo
 


## Referências

 - [Explore o Aprendizado Automatizado de Máquina no Azure Machine Learning](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/01-machine-learning.html)
 - [Portal Azure](https://portal.azure.com)
 - [Fonte de Dados](https://aka.ms/bike-rentals)
## Criação do Azure Machine Learning Workspace

Para utilizar o Azure Machine Learning, é necessário criar um espaço de trabalho (workspace) na sua assinatura do Azure. Este espaço de trabalho permitirá o uso do Azure Machine Learning Studio para interagir com os recursos associados.

**Passos a passo:**  

1. Acesse o portal do Azure utilizando suas credenciais da Microsoft.

2. Selecione "+ Criar um recurso", busque por "Machine Learning" e crie um novo recurso do Azure Machine Learning com as seguintes configurações:  

- **Assinatura:** Sua assinatura do Azure.
- **Grupo de recursos:** Crie ou selecione um grupo de recursos existente.
- **Nome:** Insira um nome único para o seu espaço de trabalho.
- **Região:** Selecione a região geográfica mais próxima.
- **Conta de armazenamento:** Anote a conta de armazenamento padrão que será criada para o workspace.
- **Vault de chaves:** Anote o cofre de chaves padrão que será criado para o workspace.
- **Insights de aplicativo:** Anote o recurso de Insights de Aplicativo que será criado automaticamente para o workspace.
- **Registro de contêiner:** Nenhum (será criado automaticamente na primeira implantação de um modelo em contêiner).
3. Selecione "Revisar + criar" e, em seguida, "Criar". Aguarde a criação do seu espaço de trabalho (isso pode levar alguns minutos) e vá para o recurso implantado.

4. Selecione "Iniciar Studio" (ou abra uma nova guia do navegador e acesse https://ml.azure.com). Faça login no Azure Machine Learning Studio usando sua conta Microsoft. Feche quaisquer mensagens que apareçam.

5. No Azure Machine Learning Studio, você deverá ver o espaço de trabalho que acabou de criar. Caso contrário, selecione "Todos os espaços de trabalho" no menu à esquerda e escolha o espaço de trabalho recém-criado.


## Treinamento de Modelo Automático

Agora, vamos utilizar o Treinamento Automático de Machine Learning para treinar um modelo com base em um conjunto de dados históricos de aluguel de bicicletas.

**Passo a passo:**  

1. No Azure Machine Learning Studio, acesse a página de "Automated ML" (em Autoria)

2. Crie um novo trabalho Automated ML com as seguintes configurações:  

- **Configurações básicas:**  

    - Nome do trabalho: mslearn-bike-automl
    - Nome do experimento: mslearn-bike-rental
    - Descrição: Treinamento automático de machine learning para previsão de aluguel de bicicletas

- **Tipo de tarefa e dados:**  
    - Tipo de tarefa: Regressão
    - Conjunto de dados: Crie um novo conjunto de dados com as configurações fornecidas.

- **Configurações da tarefa:**
    - Nome: bike-rentals
    - Descrição: Historico de dados do aluguel de bicicletas
    - Tipo: Tabular

- **Fonte de dados:**  

    - "Select From web files"
    - **Web URL:** 
        - [https://aka.ms/bike-rentals](https://aka.ms/bike-rentals)
        - **Ignorar validação de dados:** não selecionar

- **Configurações:**  
    - Delimitador: Vírgula
    - Codificação: UTF-8
    - Cabeçalhos de coluna: Somente o primeiro arquivo contém cabeçalhos
    - Pular linhas: Nenhuma
    - Conjunto de dados contém dados em várias linhas: Não selecionar
- **Esquema:**
    - Inclua todas as colunas, exceto Caminho
    - Revise os tipos detectados automaticamente
    - Selecionar Criar. Após a criação do conjunto de dados, selecione o aluguéis de bicicletas conjunto de dados para continuar enviando o trabalho de ML automatizado.

- **Configurações da tarefa:**

    - Tipo de tarefa: Regressão
    - Conjunto de dados: aluguel de bicicletas
    - Coluna de destino: Aluguel (inteiro)
    - Configurações adicionais:
    - Métrica primária: Erro quadrático médio da raiz normalizada
    - Explique o melhor modelo: Não selecionado
    - Use todos os modelos suportados: Unselecionado. Você restrinja o trabalho para tentar apenas alguns algoritmos específicos.
    - Modelos permitidos: Selecionar apenas RandomForest e LightGBM — normalmente você quer tentar o maior número possível, mas cada modelo adicionado aumenta o tempo que leva para executar o trabalho.

- **Limites:** Expanda esta seção
    - Testes máximos: 3
    - Testes simultâneos máximos: 3
    - Nós máximos: 3
    - Limiar de pontuação métrica: 0,085 (de modo que, se um modelo atingir uma pontuação média quadrática de erro métrico de raiz normalizada de 0,085 ou menos, o trabalho terminará.)
    - Tempo limite: 15
    - Tempo limite de iteração: 15
    - Ativar a rescisão antecipada: Selecionado

- **Validação e Teste:** 
    - Tipo de validação: Divisão de validação de trem
    - Percentagem de dados de validação: 10
    - Conjunto de dados de teste: Nenhum

- **Computação:**
    - Selecione o tipo de computação: Sem servidor
    - Tipo de máquina virtual: CPU
    - Nível de máquina virtual: Dedicado
    - Tamanho da máquina virtual: Standard_DS3_V2*
    - Número de instâncias: 1

3. Envie o trabalho de treinamento e aguarde a conclusão. Isso pode levar algum tempo.

4. Após a conclusão, revise o melhor modelo treinado na guia "Overview". Analise as métricas de desempenho na guia "Metrics".

## Implantação e Teste do Modelo

Após treinar o modelo, você pode implantá-lo e testá-lo.

**Passo a Passo:**  

1. Na guia "Model" do melhor modelo treinado, selecione "Deploy" e escolha a opção "Web service".

- Nome: predict-rentals
- Descrição: Prever aluguéis de bicicletas
- Tipo de computação: Azure Container Instance
- Habilitar autenticação: Selecionado
- Aguarde o início da implantação - isso pode levar alguns segundos. O status da implantação será indicado como "Running".

- Espere até que o status de implantação mude para "Succeeded". Isso pode levar de 5 a 10 minutos.

4. Agora, você pode testar o serviço implantado.

5. Acesse "Endpoints" e abra o endpoint em tempo real "predict-rentals".
- Na guia de teste, insira os dados de entrada fornecidos no exemplo JSON e clique em "Test".
~~~JSON
{
   "Inputs": { 
     "data": [
       {
         "day": 1,
         "mnth": 1,   
         "year": 2022,
         "season": 2,
         "holiday": 0,
         "weekday": 1,
         "workingday": 1,
         "weathersit": 2, 
         "temp": 0.3, 
         "atemp": 0.3,
         "hum": 0.3,
         "windspeed": 0.3 
       }
     ]    
   },   
   "GlobalParameters": 1.0
 }
~~~
6. Revise os resultados do teste, que incluirão o número previsto de aluguéis com base nos recursos de entrada.

7. **_Parabéns! Você treinou, implantou e testou com sucesso um modelo de machine learning para prever aluguéis de bicicletas usando o Azure Machine Learning!_**
