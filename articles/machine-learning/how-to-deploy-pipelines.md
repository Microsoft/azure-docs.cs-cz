---
title: Publikování kanálů na ML
titleSuffix: Azure Machine Learning
description: Spouštění pracovních postupů strojového učení s kanály strojového učení a Azure Machine Learning SDK pro Python
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: efedb21a1ec1ed53a8c6bfadf337d23a89c04383
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520172"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Publikování a sledování kanálů strojového učení



V tomto článku se dozvíte, jak sdílet kanál strojového učení se svými kolegy nebo zákazníky.

Kanály strojového učení jsou opakovaně použitelnými pracovními postupy pro úlohy strojového učení. Jedna výhoda kanálů zvyšuje spolupráci. Můžete také vytvářet verze kanálů a umožnit tak zákazníkům používat aktuální model při práci na nové verzi. 

## <a name="prerequisites"></a>Předpoklady

* Vytvoření [pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md) pro uložení všech prostředků kanálu

* [Nakonfigurujte vývojové prostředí](how-to-configure-environment.md) pro instalaci Azure Machine Learning sady SDK nebo použijte [výpočetní instanci Azure Machine Learning](concept-compute-instance.md) s již nainstalovanou sadou SDK.

* Vytvořte a spusťte kanál strojového učení, například pomocí následujícího [kurzu: sestavení kanálu Azure Machine Learning pro vyhodnocování dávky](tutorial-pipeline-batch-scoring-classification.md). Další možnosti najdete v tématu [Vytvoření a spuštění kanálů strojového učení s Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md) .

## <a name="publish-a-pipeline"></a>Publikování kanálu

Jakmile budete mít kanál v provozu, můžete publikovat kanál, aby běžel s různými vstupy. Pro koncový bod REST již publikovaného kanálu pro příjem parametrů je nutné nakonfigurovat kanál tak, aby používal `PipelineParameter` objekty pro argumenty, které se budou lišit.

1. Chcete-li vytvořit parametr kanálu, použijte objekt [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter) s výchozí hodnotou.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Tento `PipelineParameter` objekt přidejte jako parametr do některého z kroků v kanálu následujícím způsobem:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publikování tohoto kanálu, který při vyvolání přijme parametr.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>Spuštění publikovaného kanálu

Všechny publikované kanály mají koncový bod REST. Pomocí koncového bodu kanálu můžete aktivovat spuštění kanálu z libovolného externího systému, včetně klientů mimo Python. Tento koncový bod povoluje ve scénářích dávkového vyhodnocování a přeškolení možnost spravovaná opakovatelnost.

> [!IMPORTANT]
> Pokud ke správě přístupu k vašemu kanálu používáte řízení přístupu na základě role Azure (Azure RBAC), [nastavte oprávnění pro váš scénář kanálu (školení nebo bodování)](how-to-assign-roles.md#common-scenarios).

K vyvolání běhu předchozího kanálu potřebujete Azure Active Directory token hlavičky ověřování. Získání takového tokenu je popsané v referenčních informacích [třídy AzureCliAuthentication](/python/api/azureml-core/azureml.core.authentication.azurecliauthentication) a v tématu [ověřování v](https://aka.ms/pl-restep-auth) poznámkovém bloku Azure Machine Learning.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

`json`Argument žádosti post musí pro `ParameterAssignments` klíč obsahovat slovník obsahující parametry kanálu a jejich hodnoty. Kromě toho `json` argument může obsahovat následující klíče:

| Klíč | Popis |
| --- | --- | 
| `ExperimentName` | Název experimentu přidruženého k tomuto koncovému bodu |
| `Description` | Text volného tvaru popisující koncový bod | 
| `Tags` | Nezadatelné páry klíč-hodnota, které se dají použít k označení a komentování žádostí  |
| `DataSetDefinitionValueAssignments` | Slovník používaný pro změnu datových sad bez přeškolení (viz diskuze níže) | 
| `DataPathAssignments` | Slovník používaný pro změnu datapaths bez přeškolení (viz diskuze níže) | 

### <a name="run-a-published-pipeline-using-c"></a>Spuštění publikovaného kanálu pomocí jazyka C # 

Následující kód ukazuje, jak se má vytvořit asynchronní volání kanálu z jazyka C#. Částečný fragment kódu právě ukazuje strukturu volání a není součástí ukázky společnosti Microsoft. Nezobrazuje kompletní třídy nebo zpracování chyb. 

```csharp
[DataContract]
public class SubmitPipelineRunRequest
{
    [DataMember]
    public string ExperimentName { get; set; }

    [DataMember]
    public string Description { get; set; }

    [DataMember(IsRequired = false)]
    public IDictionary<string, string> ParameterAssignments { get; set; }
}

// ... in its own class and method ... 
const string RestEndpoint = "your-pipeline-endpoint";

using (HttpClient client = new HttpClient())
{
    var submitPipelineRunRequest = new SubmitPipelineRunRequest()
    {
        ExperimentName = "YourExperimentName", 
        Description = "Asynchronous C# REST api call", 
        ParameterAssignments = new Dictionary<string, string>
        {
            {
                // Replace with your pipeline parameter keys and values
                "your-pipeline-parameter", "default-value"
            }
        }
    };

    string auth_key = "your-auth-key"; 
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", auth_key);

    // submit the job
    var requestPayload = JsonConvert.SerializeObject(submitPipelineRunRequest);
    var httpContent = new StringContent(requestPayload, Encoding.UTF8, "application/json");
    var submitResponse = await client.PostAsync(RestEndpoint, httpContent).ConfigureAwait(false);
    if (!submitResponse.IsSuccessStatusCode)
    {
        await WriteFailedResponse(submitResponse); // ... method not shown ...
        return;
    }

    var result = await submitResponse.Content.ReadAsStringAsync().ConfigureAwait(false);
    var obj = JObject.Parse(result);
    // ... use `obj` dictionary to access results
}
```

### <a name="run-a-published-pipeline-using-java"></a>Spuštění publikovaného kanálu pomocí Java

Následující kód ukazuje volání kanálu, který vyžaduje ověření (viz [nastavení ověřování pro Azure Machine Learning prostředky a pracovní postupy](how-to-setup-authentication.md)). Pokud je váš kanál nasazený veřejně, nepotřebujete volání, která vytváří `authKey` . Částečný fragment kódu nezobrazuje kódování Java Class a Exception-zpracovává. Kód používá `Optional.flatMap` pro zřetězení funkcí, které mohou vracet prázdné `Optional` . Použití `flatMap` zkratek a objasnění kódu, ale Upozorňujeme, že se `getRequestBody()` výjimky požití.

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.Optional;
// JSON library
import com.google.gson.Gson;

String scoringUri = "scoring-endpoint";
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";
String dataToBeScored = "{ \"ExperimentName\" : \"My_Pipeline\", \"ParameterAssignments\" : { \"pipeline_arg\" : \"20\" }}";

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);
Optional<String> authBody = getRequestBody(client, tokenAuthenticationRequest);
Optional<String> authKey = authBody.flatMap(body -> Optional.of(gson.fromJson(body, AuthenticationBody.class).access_token);;
Optional<HttpRequest> scoringRequest = authKey.flatMap(key -> Optional.of(scoringRequest(key, scoringUri, dataToBeScored)));
Optional<String> scoringResult = scoringRequest.flatMap(req -> getRequestBody(client, req));
// ... etc (`scoringResult.orElse()`) ... 

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl)
{
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(authUrl))
        .POST(HttpRequest.BodyPublishers.ofString(bodyString))
        .build();
    return request;
}

static HttpRequest scoringRequest(String authKey, String scoringUri, String dataToBeScored)
{
    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(scoringUri))
        .header("Authorization", String.format("Token %s", authKey))
        .POST(HttpRequest.BodyPublishers.ofString(dataToBeScored))
        .build();
    return request;

}

static Optional<String> getRequestBody(HttpClient client, HttpRequest request) {
    try {
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        if (response.statusCode() != 200) {
            System.out.println(String.format("Unexpected server response %d", response.statusCode()));
            return Optional.empty();
        }
        return Optional.of(response.body());
    }catch(Exception x)
    {
        System.out.println(x.toString());
        return Optional.empty();
    }
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

### <a name="changing-datasets-and-datapaths-without-retraining"></a>Změna datových sad a datových cest bez přeškolení

Je možné, že budete chtít naučit a odvozovat na různých datových sadách a datacestách. Například můžete chtít vytvořit z menší datové sady, ale odvozovat pro kompletní datovou sadu. Datové sady můžete přepínat pomocí `DataSetDefinitionValueAssignments` klíče v `json` argumentu požadavku. V případě, že jste přepnuli datapaths `DataPathAssignments` . Postup pro obojí je podobný:

1. Ve svém skriptu definice kanálu vytvořte `PipelineParameter` pro datovou sadu. Vytvořte `DatasetConsumptionConfig` nebo `DataPath` z `PipelineParameter` :

    ```python
    tabular_dataset = Dataset.Tabular.from_delimited_files('https://dprepdata.blob.core.windows.net/demo/Titanic.csv')
    tabular_pipeline_param = PipelineParameter(name="tabular_ds_param", default_value=tabular_dataset)
    tabular_ds_consumption = DatasetConsumptionConfig("tabular_dataset", tabular_pipeline_param)
    ```

1. Do skriptu ML získáte přístup k dynamicky zadané datové sadě pomocí `Run.get_context().input_datasets` :

    ```python
    from azureml.core import Run
    
    input_tabular_ds = Run.get_context().input_datasets['tabular_dataset']
    dataframe = input_tabular_ds.to_pandas_dataframe()
    # ... etc ...
    ```

    Všimněte si, že skript ML přistupuje k hodnotě zadané pro `DatasetConsumptionConfig` ( `tabular_dataset` ) a nikoli hodnotě `PipelineParameter` ( `tabular_ds_param` ).

1. Ve svém skriptu definice kanálu nastavte `DatasetConsumptionConfig` jako parametr na `PipelineScriptStep` :

    ```python
    train_step = PythonScriptStep(
        name="train_step",
        script_name="train_with_dataset.py",
        arguments=["--param1", tabular_ds_consumption],
        inputs=[tabular_ds_consumption],
        compute_target=compute_target,
        source_directory=source_directory)
    
    pipeline = Pipeline(workspace=ws, steps=[train_step])
    ```

1. K dynamickému přepínání datových sad v volání Inferencing REST použijte `DataSetDefinitionValueAssignments` :
    
    ```python
    tabular_ds1 = Dataset.Tabular.from_delimited_files('path_to_training_dataset')
    tabular_ds2 = Dataset.Tabular.from_delimited_files('path_to_inference_dataset')
    ds1_id = tabular_ds1.id
    d22_id = tabular_ds2.id
    
    response = requests.post(rest_endpoint, 
                             headers=aad_token, 
                             json={
                                "ExperimentName": "MyRestPipeline",
                               "DataSetDefinitionValueAssignments": {
                                    "tabular_ds_param": {
                                        "SavedDataSetReference": {"Id": ds1_id #or ds2_id
                                    }}}})
    ```

Poznámkové bloky [předvádí DataSet a PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb) a [předvádí DataPath a PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) mají kompletní příklady této techniky.

## <a name="create-a-versioned-pipeline-endpoint"></a>Vytvoření koncového bodu kanálu se správou verzí

Koncový bod kanálu můžete vytvořit s několika publikovanými kanály. Tento postup vám poskytne pevný koncový bod REST při iteraci a aktualizuje vaše kanály ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>Odeslání úlohy do koncového bodu kanálu

Úlohu můžete odeslat do výchozí verze koncového bodu kanálu:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

Můžete také odeslat úlohu do konkrétní verze:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Totéž můžete dosáhnout pomocí REST API:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>Použití publikovaných kanálů v studiu

Můžete také spustit publikovaný kanál z studia:

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com).

1. [Zobrazení pracovního prostoru](how-to-manage-workspace.md#view)

1. Na levé straně vyberte **koncové body**.

1. V horní části vyberte **koncové body kanálu**.
 ![Seznam publikovaných kanálů ve strojovém učení](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Vyberte konkrétní kanál, který se má spustit, spotřebovat nebo zkontrolovat výsledky předchozích spuštění koncového bodu kanálu.

## <a name="disable-a-published-pipeline"></a>Zakázání publikovaného kanálu

Pokud chcete kanál ze seznamu publikovaných kanálů skrýt, můžete ho zakázat v sadě Studio nebo v sadě SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Můžete ho znovu povolit pomocí `p.enable()` . Další informace naleznete v tématu [PublishedPipeline Class Reference třídy](/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline) .

## <a name="next-steps"></a>Další kroky

- Pomocí [těchto poznámkových bloků Jupyter na GitHubu](https://aka.ms/aml-pipeline-readme) můžete dále prozkoumat kanály strojového učení.
- Podívejte se na referenční nápovědu sady SDK pro balíček [AzureML-Pipelines-Core](/python/api/azureml-pipeline-core/) a balíček [AzureML-Pipelines-Steps](/python/api/azureml-pipeline-steps/) .
- Tipy k ladění kanálů a řešení potíží najdete v tématu [postupy](how-to-debug-pipelines.md) .