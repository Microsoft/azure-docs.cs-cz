---
title: Odeslání velkého počtu úloh – Azure Batch | Dokumentace Microsoftu
description: Jak efektivně odeslání velkého počtu úkolů v rámci jedné úlohy služby Azure Batch
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: a3327c2c6f9684efe0d86b0cf923886f7310e1b0
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199780"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Odeslání velkého počtu úkolů pro úlohu služby Batch

Když spustíte velkých úloh služby Azure Batch, můžete chtít odeslat desítky, stovky tisíc nebo ještě další úkoly na jednu úlohu. 

Tento článek obsahuje pokyny a příklady kódu pro odeslání velkého počtu úkolů s výrazně vyšší propustnost pro jednu úlohu služby Batch. Po odeslání úlohy se vstupem do fronty služby Batch pro zpracování ve fondu, který zadáte pro úlohu.

## <a name="use-task-collections"></a>Použít kolekce úloh

Rozhraní API služby Batch poskytují metody pro efektivní přidání úkolů do úlohy jako *kolekce*, kromě postupně po jednom. Při přidávání velký počet úkolů, byste měli použít odpovídající metody nebo přetížení pro přidání úlohy jako kolekce. Obecně platí je vytvořit kolekci úloh pomocí definice úlohy, protože sada vstupních souborů nebo parametry neprovedete iteraci pro vaši úlohu.

Maximální velikost kolekci úloh, které můžete přidat v jednom volání závisí na rozhraní API služby Batch můžete použít:

* Kolekce, kterou chcete omezit následující rozhraní API služby Batch **100 úloh**. Limit může být nižší v závislosti na velikosti úlohy – například pokud úlohy mají velký počet souborů prostředků nebo proměnné prostředí.

    * [REST API](/rest/api/batchservice/task/addcollection)
    * [Rozhraní API pro Python](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python#azure_batch_operations_TaskOperations_add_collection)
    * [Rozhraní API Node.js](/javascript/api/azure-batch/task?view=azure-node-latest#addcollection)

  Při použití těchto rozhraní API, budete muset poskytnout logiku k rozdělení počet úloh podle limitu kolekcí a zpracování chyb a opakování pokusů v případě selhání přidání úlohy. Pokud kolekce úloh je příliš velký pro přidání, požadavku dojde k chybě a je třeba opakovat znovu s menším počtem úkoly.

* Následující rozhraní API podporují mnohem větší kolekce úloh – omezené jenom podle dostupnosti paměti RAM na straně odesílání klienta. Tato rozhraní API zpracovat transparentně dělení kolekci úloh na "bloky" pro nižší úrovně rozhraní API a opakované pokusy neúspěšné přidání úlohy.

    * [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Rozhraní API Javy](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [Rozšíření Azure Batch CLI](batch-cli-templates.md) s využitím šablon rozhraní příkazového řádku služby Batch
    * [Rozšíření SDK pro Python](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Zvýšení prostupnosti odeslání úlohy

Můžete přidat velké kolekce úkolů do úlohy – například nějakou dobu trvat, až na 1 minutu, chcete-li přidat 20 000 úloh prostřednictvím rozhraní API pro .NET. V závislosti na rozhraní API služby Batch a úlohy můžete zlepšit propustnost úloh úpravou jednoho nebo více z následujících akcí:

* **Úloha velikost** – přidání velkých úloh trvá déle, než přidáte menších. Ke snížení velikosti každý úkol v kolekci, můžete zjednodušit příkazového řádku úkolu, snížení počtu proměnných prostředí nebo efektivněji zpracovávat požadavky na spuštění úlohy. Například namísto použití velký počet souborů prostředků, nainstalujte pomocí závislosti úkolů [spouštěcí úkol](batch-api-basics.md#start-task) fondu nebo použijete [balíčku aplikace](batch-application-packages.md) nebo [kontejneru Dockeru](batch-docker-container-workloads.md).

* **Počet paralelních operací** – v závislosti na rozhraní API služby Batch, zvýšení propustnosti zvýšením maximální počet souběžných operací pomocí klienta služby Batch. Nastavit tuto konfiguraci nastavení použití [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) vlastnosti v rozhraní .NET API nebo `threads` parametr metody jako [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python#add-collection)v rozšíření sady SDK služby Batch Python. (Tato vlastnost není k dispozici v nativní sadou SDK služby Batch Python.) Ve výchozím nastavení je tato vlastnost nastavena na hodnotu 1, ale je nastavená na vyšší hodnotu zvýšit propustnost operací. Můžete zvolit kompromis vyšší propustnost podle používání šířky pásma sítě a výkonu procesoru. Úloha propustnosti zvýší o až 100krát `MaxDegreeOfParallelism` nebo `threads`. V praxi byste měli nastavit počet souběžných operací nižší než 100. 
 
  Rozšíření rozhraní příkazového řádku Azure Batch pomocí šablon služby Batch se zvyšuje počet souběžných operací automaticky podle počtu dostupných jader, ale tato vlastnost se nedá konfigurovat v rozhraní příkazového řádku. 

* **Omezení počtu připojení HTTP** – počet souběžných připojení prostřednictvím protokolu HTTP můžete omezit výkon klienta Batch při jeho je přidávání velkého počtu úloh. Počet připojení pomocí protokolu HTTP je omezen pomocí určitých rozhraní API. Při vývoji s .NET API, například [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) je nastavena na 2 ve výchozím nastavení. Doporučujeme, abyste zvýšili hodnotu na číslo zavřít nebo větší než počet paralelních operací.

## <a name="example-batch-net"></a>Příklad: Batch .NET

Následující fragmenty C# zobrazit nastavení pro konfiguraci při přidávání velký počet úkolů pomocí rozhraní Batch .NET API.

Pokud chcete zvýšit propustnost úkolu, zvyšte hodnotu [z MaxDegreeofParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) vlastnost [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Příklad:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Přidat kolekci úkolů do úlohy pomocí odpovídající přetížení [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) nebo [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) metody. Příklad:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Příklad: Rozšíření rozhraní příkazového řádku služby batch

Rozšíření rozhraní příkazového řádku Azure Batch s využitím [šablon rozhraní příkazového řádku služby Batch](batch-cli-templates.md), vytvořte soubor JSON šablony projektu, který zahrnuje [továrny úloh](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). Továrny úloh konfiguruje kolekci souvisejících úkolů pro úlohu z definice jeden úkol.  

Následuje ukázka úlohy šablony jednorozměrném čištění parametrů úlohy s velkým počtem úkoly – v takovém případě 250 000. Příkazový řádek úkolu je jednoduchý `echo` příkazu.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```
Ke spuštění úlohy se šablonou, naleznete v tématu [šablon rozhraní příkazového řádku pro použití Azure Batch a přenos souborů](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Příklad: Rozšíření služby batch Python SDK

Pokud chcete používat Azure Batch Python SDK rozšíření, je nutné nejprve nainstalujte sadu Python SDK a rozšíření:

```
pip install azure-batch
pip install azure-batch-extensions
```

Nastavení `BatchExtensionsClient` , který používá rozšíření sady SDK:

```python

client = batch.BatchExtensionsClient(base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Vytvořte kolekci úloh určených k přidání do úlohy. Příklad:


```python
tasks=list()
# Populate the list with your tasks
...

```

Přidání pomocí kolekcí úloh [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python#add-collection). Nastavte `threads` parametr zvýšit počet souběžných operací:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Rozšíření služby Batch Python SDK podporuje taky přidávání parametrů úkolů do úlohy pomocí specifikace formátu JSON pro objekt pro vytváření úloh. Například nakonfigurovat parametry úlohy pro čištění parametrů podobně jako v předchozím [šablony rozhraní příkazového řádku služby Batch](#example-batch-cli-template) příkladu:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Do úlohy přidáte parametry úlohy. Nastavte `threads` parametr zvýšit počet souběžných operací:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Další postup

* Další informace o používání rozšíření rozhraní příkazového řádku Azure Batch s [šablon rozhraní příkazového řádku služby Batch](batch-cli-templates.md).
* Další informace o [Batch Python SDK rozšíření](https://pypi.org/project/azure-batch-extensions/).