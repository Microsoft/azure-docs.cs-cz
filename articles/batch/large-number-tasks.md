---
title: Odeslat velký počet úkolů – Azure Batch | Microsoft Docs
description: Jak efektivně odeslat velmi velký počet úkolů v rámci jedné Azure Batch úlohy
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: fc47b18dd51bbaa48d950515cccfe618d9e58426
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132751"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Odeslání velkého počtu úkolů do dávkové úlohy

Když spouštíte velké Azure Batch úlohy, můžete chtít odeslat desítky tisíců, stovek tisíců nebo ještě více úloh do jedné úlohy. 

Tento článek obsahuje pokyny a některé příklady kódu pro odeslání velkého počtu úkolů s podstatně vyšší propustností do jedné dávkové úlohy. Po odeslání úkolů vstoupí do fronty Batch ke zpracování ve fondu, který zadáte pro úlohu.

## <a name="use-task-collections"></a>Použití kolekcí úloh

Rozhraní API pro Batch poskytují metody pro efektivní přidávání úkolů do úlohy jako *kolekce*, a to i v jednom okamžiku. Pokud přidáváte velký počet úkolů, měli byste použít příslušné metody nebo přetížení k přidání úkolů jako kolekce. Obecně platí, že vytvoříte kolekci úloh definováním úkolů při iteraci přes sadu vstupních souborů nebo parametrů pro vaši úlohu.

Maximální velikost kolekce úloh, kterou můžete přidat do jednoho volání, závisí na rozhraní API pro dávkové zpracování, které používáte:

* Následující rozhraní API pro dávky omezují kolekci na **100 úloh**. Omezení může být menší v závislosti na velikosti úkolů – například pokud mají úlohy velký počet souborů prostředků nebo proměnných prostředí.

    * [REST API](/rest/api/batchservice/task/addcollection)
    * [Rozhraní API pro Python](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Rozhraní API Node.js](/javascript/api/@azure/batch/task?view=azure-node-latest)

  Při použití těchto rozhraní API je potřeba poskytnout logiku pro rozdělení počtu úkolů pro splnění limitu kolekce a zpracování chyb a opakování v případě neúspěchu přidávání úloh. Pokud je kolekce úkolů příliš velká pro přidání, požadavek vygeneruje chybu a měl by být znovu opakován s méně úlohami.

* Následující rozhraní API podporují mnohem větší kolekce úloh – omezené jenom při dostupnosti paměti RAM v odesílajícím klientovi. Tato rozhraní API transparentně nadělí kolekci úloh na "bloky" pro rozhraní API na nižší úrovni a pokusy se opakuje, pokud se nepovede přidání úkolů.

    * [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Rozhraní API Javy](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [Azure Batch rozšíření CLI](batch-cli-templates.md) pomocí šablon Batch CLI
    * [Rozšíření Python SDK](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Zvýšit propustnost odeslání úkolu

Přidání velké kolekce úloh do úlohy může nějakou dobu trvat – například až 1 minutu, aby bylo možné přidat 20 000 úkolů přes rozhraní .NET API. V závislosti na rozhraní API služby Batch a vašich úlohách můžete zvýšit propustnost úlohy úpravou jednoho nebo více z těchto možností:

* **Velikost úlohy** – přidávání velkých úloh trvá déle než přidávání menších. Chcete-li zmenšit velikost každého úkolu v kolekci, můžete zjednodušit příkazový řádek úlohy, snížit počet proměnných prostředí nebo zvládnout požadavky na provádění úloh efektivněji. Například namísto použití velkého počtu souborů prostředků nainstalujte závislosti úloh pomocí [spouštěcího úkolu](batch-api-basics.md#start-task) ve fondu nebo použijte [balíček aplikace](batch-application-packages.md) nebo [kontejner Docker](batch-docker-container-workloads.md).

* **Počet paralelních operací** – v závislosti na rozhraní API dávky zvyšte propustnost zvýšením maximálního počtu souběžných operací klientem Batch. Nakonfigurujte toto nastavení pomocí vlastnosti [BatchClientParallelOptions. z MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) v rozhraní .NET API nebo parametr `threads` metod, jako je například [TaskOperations. Add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) v rozšíření Batch Python SDK. (Tato vlastnost není v nativní sadě SDK pro sadu Batch k dispozici.) Ve výchozím nastavení je tato vlastnost nastavena na hodnotu 1, ale je nastavena vyšší pro zlepšení propustnosti operací. Zvýšení propustnosti se zvyšuje využitím šířky pásma sítě a určitého výkonu procesoru. Propustnost úlohy se zvyšuje o až 100 časů `MaxDegreeOfParallelism` nebo `threads`. V praxi byste měli nastavit počet souběžných operací pod 100. 
 
  Rozšíření Azure Batch CLI pomocí šablon Batch zvyšuje počet souběžných operací automaticky na základě počtu dostupných jader, ale tato vlastnost se v rozhraní příkazového řádku nedá nakonfigurovat. 

* **Omezení připojení HTTP** – počet souběžných připojení HTTP může omezit výkon klienta Batch při přidávání velkého počtu úkolů. Počet připojení HTTP je omezený pomocí určitých rozhraní API. Při vývoji s rozhraním .NET API je například vlastnost [Třída ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) ve výchozím nastavení nastavena na hodnotu 2. Doporučujeme zvýšit hodnotu na číslo blížící se nebo větší než počet paralelních operací.

## <a name="example-batch-net"></a>Příklad: Batch .NET

Následující C# fragmenty kódu ukazují nastavení pro konfiguraci při přidávání velkého počtu úkolů pomocí rozhraní API pro dávku .NET.

Chcete-li zvýšit propustnost úlohy, zvyšte hodnotu vlastnosti [Z MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Příklad:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Přidejte do úlohy kolekci úloh pomocí vhodného přetížení metody [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) nebo [AddTask a ta](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) . Příklad:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Příklad: rozšíření Batch CLI

Pomocí rozšíření Azure Batch CLI s [šablonami Batch CLI](batch-cli-templates.md)vytvořte soubor JSON šablony úlohy, který obsahuje objekt pro [vytváření úloh](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). Objekt pro vytváření úloh konfiguruje kolekci souvisejících úloh pro úlohu z definice jedné úlohy.  

Následuje ukázka šablony úlohy pro jednorozměrné úlohy čištění parametrů s velkým počtem úkolů – v tomto případě 250 000. Příkazový řádek úlohy je jednoduchý příkaz `echo`.

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
Pokud chcete úlohu spustit se šablonou, přečtěte si téma [použití Azure Batch šablon CLI a přenosu souborů](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Příklad: rozšíření sady Batch Python SDK

Chcete-li použít rozšíření Azure Batch Python SDK, nejprve nainstalujte sadu Python SDK a rozšíření:

```
pip install azure-batch
pip install azure-batch-extensions
```

Nastavte `BatchExtensionsClient`, která používá rozšíření sady SDK:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Vytvořte kolekci úkolů, které chcete přidat do úlohy. Příklad:


```python
tasks = list()
# Populate the list with your tasks
...
```

Přidejte kolekci úkolů pomocí [Task. add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python). Nastavte parametr `threads` pro zvýšení počtu souběžných operací:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Rozšíření služby Batch Python SDK podporuje také přidání parametrů úlohy do úlohy pomocí specifikace JSON pro objekt pro vytváření úloh. Například nakonfigurujte parametry úlohy pro čištění parametrů podobně jako v předchozím příkladu v šabloně dávkového rozhraní příkazového řádku Batch:

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

Přidejte parametry úlohy do úlohy. Nastavte parametr `threads` pro zvýšení počtu souběžných operací:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o použití rozšíření Azure Batch CLI s [šablonami Batch CLI](batch-cli-templates.md).
* Přečtěte si další informace o [rozšíření sady Batch Python SDK](https://pypi.org/project/azure-batch-extensions/).
