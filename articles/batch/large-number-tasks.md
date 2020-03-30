---
title: Odeslat velký počet úkolů – Azure Batch | Dokumenty společnosti Microsoft
description: Jak efektivně odeslat velmi velký počet úloh v jedné úloze Azure Batch
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: labrenne
ms.custom: ''
ms.openlocfilehash: c3857e512da5fe4fceefa5f735ddc65f73e11623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77026043"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Odeslání velkého počtu úkolů do dávkové úlohy

Když spustíte rozsáhlé úlohy Azure Batch, můžete odeslat desítky tisíc, stovky tisíc nebo dokonce více úloh do jedné úlohy. 

Tento článek poskytuje pokyny a některé příklady kódu pro odeslání velkého počtu úkolů s podstatně zvýšenou propustností pro jednu dávkovou úlohu. Po odeslání úkolů zadají frontu dávka pro zpracování ve fondu, který zadáte pro úlohu.

## <a name="use-task-collections"></a>Použití kolekcí úkolů

Dávková api poskytují metody pro efektivní přidání úkolů do úlohy jako *kolekce*, kromě jednoho po druhém. Při přidávání velkého počtu úkolů byste měli použít příslušné metody nebo přetížení k přidání úkolů jako kolekce. Obecně lze vytvořit kolekci úloh definováním úkolů při itetování přes sadu vstupních souborů nebo parametrů pro vaši úlohu.

Maximální velikost kolekce úloh, kterou můžete přidat do jednoho volání, závisí na rozhraní API dávky, které používáte:

* Následující dávková api omezují kolekci na **100 úkolů**. Omezení může být menší v závislosti na velikosti úkolů – například pokud úkoly mají velký počet souborů prostředků nebo proměnné prostředí.

    * [ROZHRANÍ API PRO ODPOČINEK](/rest/api/batchservice/task/addcollection)
    * [Rozhraní API pro Python](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Node.js API](/javascript/api/@azure/batch/task?view=azure-node-latest)

  Při použití těchto api, je třeba poskytnout logiku rozdělit počet úkolů ke splnění limitu kolekce a zpracování chyb a opakování v případě, že přidání úkolů selže. Pokud kolekce úloh je příliš velký přidat, požadavek generuje chybu a by měl být opakován znovu s menším počtem úkolů.

* Následující api podporují mnohem větší kolekce úloh – omezené pouze dostupností paměti RAM v odesílajícím klientovi. Tato řešení API transparentně zpracovávají rozdělení kolekce úloh na "bloky" pro řešení API nižší úrovně a opakování, pokud se sčítání úloh nezdaří.

    * [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Java API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [Rozšíření nastavení uživatelského příkazu azure batch](batch-cli-templates.md) se šablonami dávkového příkazového příkazového příkazu
    * [Rozšíření sady Python SDK](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Zvýšení propustnosti odesílání úkolů

Přidání velké kolekce úkolů do úlohy může nějakou dobu trvat – například až 1 minuta přidání 20 000 úloh prostřednictvím rozhraní .NET API. V závislosti na dávkovém rozhraní API a úloze můžete propustnost úloh zlepšit úpravou jedné nebo více z následujících možností:

* **Velikost úkolu** – Přidání velkých úloh trvá déle než přidání menších úkolů. Chcete-li zmenšit velikost jednotlivých úloh v kolekci, můžete zjednodušit příkazový řádek úkolu, snížit počet proměnných prostředí nebo efektivněji zpracovat požadavky na provádění úloh. Například namísto použití velkého počtu souborů prostředků nainstalujte závislosti úloh pomocí [úlohy spuštění](batch-api-basics.md#start-task) ve fondu nebo použijte [balíček aplikace](batch-application-packages.md) nebo [kontejner Dockeru](batch-docker-container-workloads.md).

* **Počet paralelních operací** – v závislosti na rozhraní API dávky zvyšte propustnost zvýšením maximálního počtu souběžných operací klientem Batch. Nakonfigurujte toto nastavení pomocí vlastnosti [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) v rozhraní .NET API nebo `threads` parametru metod, jako je [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) v rozšíření Batch Python SDK. (Tato vlastnost není k dispozici v nativním dávkovém pythonu SDK.) Ve výchozím nastavení je tato vlastnost nastavena na hodnotu 1, ale nastavte ji vyšší, aby se zlepšila propustnost operací. Můžete vyměnit zvýšenou propustnost tím, že spotřebovává šířku pásma sítě a některé výkon procesoru. Propustnost úloh se zvýší až `MaxDegreeOfParallelism` `threads`o 100násobek nebo . V praxi byste měli nastavit počet souběžných operací pod 100. 
 
  Rozšíření rozhraní příkazového od rozhraní API azure batch se šablonami batch zvyšuje počet souběžných operací automaticky na základě počtu dostupných jader, ale tato vlastnost není konfigurovatelná v rozhraní příkazového příkazu. 

* **Omezení připojení HTTP** – Počet souběžných připojení HTTP může omezit výkon klienta Batch při přidávání velkého počtu úloh. Počet připojení HTTP je omezen s určitými api. Při vývoji s rozhraním .NET API je například vlastnost [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) ve výchozím nastavení nastavena na hodnotu 2. Doporučujeme zvýšit hodnotu na číslo blízké nebo větší než počet paralelních operací.

## <a name="example-batch-net"></a>Příklad: Dávka .NET

Následující úryvky jazyka C# zobrazují nastavení, která se mají konfigurovat při přidávání velkého počtu úloh pomocí rozhraní API batch .NET.

Chcete-li zvýšit propustnost úloh, zvyšte hodnotu vlastnosti [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) [služby BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Například:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Přidejte kolekci úloh do úlohy pomocí příslušnépřetížení [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) nebo [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) metody. Například:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Příklad: Rozšíření licenčovacího lisu dávky

Pomocí rozšíření Azure Batch CLI se [šablonami Batch CLI vytvořte](batch-cli-templates.md)soubor JSON šablony úloh, který obsahuje [továrnu úloh](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). Továrna úloh konfiguruje kolekci souvisejících úloh pro úlohu z jedné definice úlohy.  

Následuje ukázková šablona úlohy pro jednorozměrnou parametrickou úlohu tažení s velkým počtem úkolů - v tomto případě 250 000. Příkazový řádek úkolu `echo` je jednoduchý příkaz.

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
Pokud chcete spustit úlohu se šablonou, přečtěte [si informace o použití šablon Azure Batch CLI a přenosu souborů](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Příklad: Dávkové rozšíření sady Python SDK

Chcete-li použít rozšíření Azure Batch Python SDK, nejprve nainstalujte pythonsdka a rozšíření:

```
pip install azure-batch
pip install azure-batch-extensions
```

Nastavte sadu, `BatchExtensionsClient` která používá rozšíření sady SDK:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Vytvořte kolekci úkolů, které chcete přidat do úlohy. Například:


```python
tasks = list()
# Populate the list with your tasks
...
```

Přidejte kolekci úkolů pomocí [souboru task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python). Nastavte `threads` parametr pro zvýšení počtu souběžných operací:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Rozšíření Batch Python SDK také podporuje přidávání parametrů úlohy do úlohy pomocí specifikace JSON pro továrnu úloh. Například nakonfigurujte parametry úlohy pro parametrické tažení podobné tomu v předchozím příkladu šablony rozhraní příkazového řádku dávky:

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
                        "retentionTime": "PT1H"
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

Přidejte parametry úlohy do úlohy. Nastavte `threads` parametr pro zvýšení počtu souběžných operací:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Další kroky

* Další informace o použití rozšíření Azure Batch CLI se [šablonami Batch CLI](batch-cli-templates.md).
* Další informace o [rozšíření Batch Python SDK](https://pypi.org/project/azure-batch-extensions/).
