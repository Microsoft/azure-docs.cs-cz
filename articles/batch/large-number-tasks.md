---
title: Odeslání velkého počtu úkolů do dávkové úlohy
description: Naučte se, jak efektivně odeslat velmi velký počet úkolů v rámci jedné Azure Batch úlohy.
ms.topic: how-to
ms.date: 12/30/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 08cf92507a4556afbf56c9cb7e2c9c1b3a6c9479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831512"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Odeslání velkého počtu úkolů do dávkové úlohy

Když spouštíte velké Azure Batch úlohy, můžete chtít odeslat desítky tisíců, stovek tisíců nebo ještě více úloh do jedné úlohy.

V tomto článku se dozvíte, jak odeslat velký počet úkolů s podstatně vyšší propustností do jedné dávkové úlohy. Po odeslání úkolů vstoupí do fronty Batch ke zpracování ve fondu, který zadáte pro úlohu.

## <a name="use-task-collections"></a>Použití kolekcí úloh

Pokud přidáváte velký počet úkolů, použijte příslušné metody nebo přetížení poskytované rozhraním API pro dávku k přidání úkolů jako *kolekce* místo jednoho. Obecně platí, že vytvoříte kolekci úloh definováním úkolů při iteraci přes sadu vstupních souborů nebo parametrů pro vaši úlohu.

Maximální velikost kolekce úloh, kterou můžete přidat do jednoho volání, závisí na rozhraní API pro dávkové zpracování, které používáte.

### <a name="apis-allowing-collections-of-up-to-100-tasks"></a>Rozhraní API umožňující shromažďování až 100 úloh

Tato rozhraní API pro dávky omezují kolekci na 100 úloh. Omezení může být menší v závislosti na velikosti úloh (například v případě, že úlohy mají velký počet souborů prostředků nebo proměnných prostředí).

- [REST API](/rest/api/batchservice/task/addcollection)
- [Rozhraní API pro Python](/python/api/azure-batch/azure.batch.operations.TaskOperations)
- [Node.js API](/javascript/api/@azure/batch/task)

Při použití těchto rozhraní API je potřeba poskytnout logiku pro rozdělení počtu úkolů pro splnění limitu kolekce a zpracování chyb a opakování v případě selhání přidání úlohy. Pokud je kolekce úkolů příliš velká pro přidání, požadavek vygeneruje chybu a měl by být znovu opakován s méně úlohami.

### <a name="apis-allowing-collections-of-larger-numbers-of-tasks"></a>Rozhraní API umožňující shromažďování většího počtu úloh

Další rozhraní API pro Batch podporují mnohem větší kolekce úloh, které jsou omezené jenom na dostupnost paměti RAM odesílajícího klienta. Tato rozhraní API transparentně nadělí kolekci úloh na "bloky" do "bloků" pro rozhraní API nižší úrovně a pokusy o selhání přidání úlohy.

- [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync)
- [Rozhraní API Javy](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync)
- [Azure Batch rozšíření CLI](batch-cli-templates.md) pomocí šablon Batch CLI
- [Rozšíření Python SDK](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Zvýšit propustnost odeslání úkolu

Přidání velké kolekce úloh do úlohy může nějakou dobu trvat. Například přidání 20 000 úloh prostřednictvím rozhraní .NET API může trvat až jednu minutu. V závislosti na rozhraní API služby Batch a vašich úlohách můžete zvýšit propustnost úlohy úpravou jednoho nebo více z následujících možností.

### <a name="task-size"></a>Velikost úlohy

Přidávání velkých úloh trvá déle než přidávání menších. Chcete-li zmenšit velikost každého úkolu v kolekci, můžete zjednodušit příkazový řádek úlohy, snížit počet proměnných prostředí nebo zvládnout požadavky na provádění úloh efektivněji.

Například namísto použití velkého počtu souborů prostředků nainstalujte závislosti úloh pomocí [spouštěcího úkolu](jobs-and-tasks.md#start-task) ve fondu, nebo použijte [balíček aplikace](batch-application-packages.md) nebo [kontejner Docker](batch-docker-container-workloads.md).

### <a name="number-of-parallel-operations"></a>Počet paralelních operací

V závislosti na rozhraní API pro Batch můžete zvýšit propustnost zvýšením maximálního počtu souběžných operací klientem Batch. Nakonfigurujte toto nastavení pomocí vlastnosti [BatchClientParallelOptions. z MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) v rozhraní .NET API nebo parametru metod, jako je například `threads` [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations) v rozšíření sady Batch Python SDK. (Tato vlastnost není v nativní sadě SDK pro sadu Batch k dispozici.)

Ve výchozím nastavení je tato vlastnost nastavená na hodnotu 1, ale můžete ji nastavit na vyšší, aby se zlepšila propustnost operací. Zvýšení propustnosti se zvyšuje využitím šířky pásma sítě a určitého výkonu procesoru. Propustnost úlohy roste až 100 časů `MaxDegreeOfParallelism` nebo `threads` . V praxi byste měli nastavit počet souběžných operací na hodnotu nižší než 100.

 Rozšíření Azure Batch CLI pomocí šablon Batch zvyšuje počet souběžných operací automaticky na základě počtu dostupných jader, ale tato vlastnost se v rozhraní příkazového řádku nedá nakonfigurovat.

### <a name="http-connection-limits"></a>Omezení připojení HTTP

Když máte spoustu souběžných připojení HTTP, může při přidávání velkého počtu úkolů omezit výkon klienta Batch. Některá rozhraní API omezují počet připojení HTTP. Při vývoji s rozhraním .NET API je například vlastnost [Třída ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) ve výchozím nastavení nastavena na hodnotu 2. Doporučujeme zvýšit hodnotu na číslo blížící se nebo větší než počet paralelních operací.

## <a name="example-batch-net"></a>Příklad: Batch .NET

Následující fragmenty kódu jazyka C# ukazují nastavení ke konfiguraci při přidávání velkého počtu úkolů pomocí rozhraní API pro dávku .NET.

Chcete-li zvýšit propustnost úlohy, zvyšte hodnotu vlastnosti [Z MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient). Například:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```

Přidejte do úlohy kolekci úloh pomocí vhodného přetížení metody [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync) nebo [AddTask a ta](/dotnet/api/microsoft.azure.batch.cloudjob.addtask
) . Například:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```

## <a name="example-batch-cli-extension"></a>Příklad: rozšíření Batch CLI

Pomocí rozšíření Azure Batch CLI s [šablonami Batch CLI](batch-cli-templates.md)vytvořte soubor JSON šablony úlohy, který obsahuje objekt pro [vytváření úloh](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). Objekt pro vytváření úloh konfiguruje kolekci souvisejících úloh pro úlohu z definice jedné úlohy.  

Toto je Ukázková šablona úlohy pro jednorozměrné úlohy úklidu parametrů s velkým počtem úkolů (v tomto případě 250 000). Příkazový řádek úlohy je jednoduchý `echo` příkaz.

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

Nastavení `BatchExtensionsClient` , které používá rozšíření sady SDK:

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

Přidejte kolekci úloh pomocí [Task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations). Nastavte `threads` parametr pro zvýšení počtu souběžných operací:

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

Přidejte parametry úlohy do úlohy. Nastavte `threads` parametr pro zvýšení počtu souběžných operací:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o použití rozšíření Azure Batch CLI s [šablonami Batch CLI](batch-cli-templates.md).
- Přečtěte si další informace o [rozšíření sady Batch Python SDK](https://pypi.org/project/azure-batch-extensions/).
- Přečtěte si o [osvědčených postupech pro Azure Batch](best-practices.md).
