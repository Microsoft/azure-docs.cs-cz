---
title: Vytvořte úkoly přípravy úlohy a dokončení úlohy na výpočetních uzlech - Azure Batch | Microsoft Docs
description: Minimalizovat přenos dat do výpočetních uzlech Azure Batch pomocí úkolů úrovní úlohy přípravy a uvolnění úlohy pro vyčištění uzlu na dokončení úlohy.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 543c03c22b31389c3d6e048cc9f13c24add5aae7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Spuštění úlohy přípravy a uvolnění úloh na Batch výpočetních uzlů

 Úlohu služby Azure Batch často vyžaduje určitý způsob instalace předtím, než její úkoly spustí a po úlohy údržby, po dokončení jeho úkolů. Možná budete muset stáhnout běžných úkolů vstupní data na výpočetní uzly, nebo odeslání úloh výstupní data do úložiště Azure. Po dokončení úlohy. Můžete použít **úlohy přípravy** a **úlohy verze** úlohy musíte provést tyto operace.

## <a name="what-are-job-preparation-and-release-tasks"></a>Co jsou úlohy přípravy a uvolnění úlohy?
Před spuštěním úlohy, spustí úkol přípravy úlohy na všech výpočetních uzlech naplánované spuštění alespoň jeden úkol. Po dokončení úlohy se spustí úkol uvolnění úlohy na každém uzlu ve fondu, která spouští alespoň jeden úkol. Stejně jako u normálních úkoly služby Batch, můžete zadat příkazový řádek, který má být vyvolána při spuštění úlohy přípravné nebo verze úlohy.

Přípravy a uvolnění úlohy nabízejí známé dávkové úlohy funkce jako stažení souboru ([soubory prostředků][net_job_prep_resourcefiles]), provedení, vlastní proměnné prostředí, maximální dobu provádění, počet opakování a dobu uchovávání souboru se zvýšenými oprávněními.

V následujících částech se naučíte používat [JobPreparationTask] [ net_job_prep] a [JobReleaseTask] [ net_job_release] v nalezeny třídy [Batch .NET] [ api_net] knihovny.

> [!TIP]
> Úkoly přípravy a uvolnění úlohy jsou užitečné zejména v prostředích "sdílené fondu", ve které fond výpočetních uzlů potrvají mezi spuštění úloh a používá řadu úloh.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Kdy použít úlohy přípravy a uvolnění úlohy
Úloha přípravy a uvolnění úloh jsou vhodné pro následující případy:

**Stahování dat běžných úloh**

Úlohy batch často vyžadují společnou sadu dat jako vstup pro úkoly úlohy. Například v denní výpočtů analýza rizik a trhu data jsou specifické pro úlohy, ještě společné pro všechny úlohy pro úlohu. Tato data trhu, často několika gigabajtů velikost, by měl být stažen na každém výpočetním uzlu pouze jednou, aby ho můžete používat všechny úlohy, které běží na uzlu. Použití **úkol přípravy úlohy** ke stažení těchto dat do každého uzlu před provedení úlohy je další úlohy.

**Odstranit výstup úlohy a úlohy**

V prostředí "sdílené fondu", pokud nejsou fondu výpočetních uzlů vyřazení mezi úlohami, můžete odstranit data úlohy mezi spustí. Možná budete muset šetří místo na disku na uzly, nebo splňovat zásady zabezpečení vaší organizace. Použití **úkol uvolnění úlohy** k odstranění dat, který byl stáhne úkol přípravy úlohy nebo vygenerován při provádění úlohy.

**Uchování protokolu**

Můžete chtít zachovat kopii soubory protokolů, které vaše úlohy generují nebo možná havárií souborů výpisu paměti, které může být generována aplikací se nezdařilo. Použití **úkol uvolnění úlohy** v takových případech zkomprimovat a odeslat tato data [Azure Storage] [ azure_storage] účtu.

> [!TIP]
> Dalším způsobem zachování protokoly a další úlohy a úkolů výstupní data, je použít [Azure Batch souboru konvence](batch-task-output.md) knihovny.
> 
> 

## <a name="job-preparation-task"></a>Příprava na úlohu
Před spuštěním úlohy služba Batch provádí úkol přípravy úlohy na každém výpočetním uzlu, který je naplánováno spuštění úlohy. Ve výchozím nastavení čeká služba Batch úkol přípravy úlohy provést před spuštěním úlohy naplánován ke spuštění na uzlu. Můžete ale nakonfigurovat službu nechcete čekat. Pokud se uzel restartuje, spustí úkol přípravy úlohy znovu, ale můžete také zakázat toto chování.

Úkol přípravy úlohy je provést pouze u uzlů, které jsou naplánovány pro spuštění úlohy. To zabraňuje zbytečným provádění přípravy úlohy v případě, že uzel není přiřazen úlohu. Tato situace může nastat, pokud je počet úloh pro úlohu menší než počet uzlů ve fondu. Také platí, když [provedení souběžné úlohy](batch-parallel-node-tasks.md) je povoleno, což ponechá nečinnosti Pokud některé uzly počet úloh je nižší než celkový počet možných souběžných úkolů. Spuštěním není úkol přípravy úlohy na nečinných uzlů, strávíte nižší cenu na poplatky za přenos dat.

> [!NOTE]
> [JobPreparationTask] [ net_job_prep_cloudjob] se liší od [CloudPool.StartTask] [ pool_starttask] v tom, že JobPreparationTask provede na začátku každé úlohy, zatímco StartTask spustí, pouze když výpočetního uzlu nejprve spojí fond nebo restartování.
> 
> 

## <a name="job-release-task"></a>Uvolnění úlohy
Jakmile úloha je označena jako dokončená, úkol uvolnění úlohy se spustí na každém uzlu ve fondu, která spouští alespoň jeden úkol. Úlohu můžete označit jako vydáním žádost o ukončení. Služba Batch potom nastaví stav úlohy na *ukončení*, ukončí všechny aktivní nebo spuštěné úkoly spojené s úlohou a úkol uvolnění úlohy se spustí. Úloha je následně přesunuto do *Dokončit* stavu.

> [!NOTE]
> Odstranění úlohy také spustí úkol uvolnění úlohy. Ale pokud úloha už byla ukončena, uvolnění úlohy není spuštěn podruhé Pokud úloha je později odstranit.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Příprava úlohy a uvolnění úlohy pomocí rozhraní Batch .NET
Pokud chcete použít úkol přípravy úlohy, přiřazení [JobPreparationTask] [ net_job_prep] objekt, který má vaše úloha [CloudJob.JobPreparationTask] [ net_job_prep_cloudjob] vlastnost. Podobně, inicializovat [JobReleaseTask] [ net_job_release] a přiřaďte ho do vaší úlohy [CloudJob.JobReleaseTask] [ net_job_prep_cloudjob] vlastnost nastavit úkol uvolnění úlohy.

V tento fragment kódu `myBatchClient` je instance [BatchClient][net_batch_client], a `myPool` je existující fond v rámci účtu Batch.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Jak už bylo zmíněno dříve, uvolnění úlohy se spustí, až se úloha je ukončeno, nebo odstranit. Ukončit úlohu s [JobOperations.TerminateJobAsync][net_job_terminate]. Odstranit úlohu s [JobOperations.DeleteJobAsync][net_job_delete]. Obvykle ukončit nebo odstranit úlohu po dokončení jeho úkolů, nebo když bylo dosaženo časového limitu, který jste definovali.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Ukázka kódu na Githubu
Najdete v části úlohy přípravy a uvolnění úlohy v akci, podívejte se [JobPrepRelease] [ job_prep_release_sample] ukázkového projektu na Githubu. Tato Konzolová aplikace provede následující akce:

1. Vytvoří fond s dvěma uzly "malá".
2. Vytvoří úlohu s přípravy úlohy, verzi a standardní úlohy.
3. Spustí úkol přípravy úlohy, které nejprve zapíše ID uzlu do textového souboru v adresáři "sdílené" uzlu.
4. Spustí úlohu na každém uzlu, který zapíše jeho ID úkolu do stejné textového souboru.
5. Jakmile jsou všechny úkoly dokončené (nebo je dosaženo časového limitu), vytiskne obsah na konzole pro každý uzel textového souboru.
6. Po dokončení úlohy, spustí úkol uvolnění úlohy odstranění souboru z uzlu.
7. Vytiskne ukončovacích kódů úkolů přípravy a uvolnění úlohy pro každý uzel, na kterém provést.
8. Pozastaví spuštění umožňující potvrzení odstranění úlohy a fondu.

Výstup z ukázkové aplikace je podobný následujícímu:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> Z důvodu proměnné vytváření a počáteční čas uzly v rámci nového fondu (některé uzly jsou připravené pro úlohy než ostatní) může se zobrazit odlišný výstup. Konkrétně protože úlohy dokončit rychle, mezi uzly fondu může provést všechny úkoly úlohy. Pokud k tomu dojde, si všimnete, že příprava úlohy a uvolnění úlohy nejsou k dispozici pro uzel, který spuštěné žádné úlohy.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Zkontrolujte přípravy úlohy a úlohy verzi portálu Azure
Když spustíte ukázkovou aplikaci, můžete použít [portál Azure] [ portal] pro zobrazení vlastností úlohy a její úkoly nebo i stažení sdílené textový soubor, který je upraveném úkoly úlohy.

Na snímku obrazovky níže ukazuje **přípravy úlohy okno** na portálu Azure po spuštění ukázkové aplikace. Přejděte na *JobPrepReleaseSampleJob* vlastnosti po dokončení úkolů (ale před odstraněním úlohy a fondu) a klikněte na tlačítko **přípravných kroků** nebo **uvolnění úlohy** zobrazit jejich vlastnosti.

![Vlastnosti přípravy úlohy na portálu Azure][1]

## <a name="next-steps"></a>Další postup
### <a name="application-packages"></a>Balíčky aplikací
Kromě úkol přípravy úlohy, můžete také použít [balíčky aplikací](batch-application-packages.md) služby Batch pro přípravu výpočetních uzlů pro provedení úlohy. Tato funkce je užitečná zejména při nasazení aplikací, které nevyžadují systémem instalační program, aplikace, které obsahují mnoho (100 +) souborů nebo aplikace, které vyžadují striktní verzí.

### <a name="installing-applications-and-staging-data"></a>Instalace aplikací a pracovní data
Tento příspěvek na fóru MSDN obsahuje přehled několik metod pro spuštěné úkoly přípravy uzlů:

[Instalace aplikací a pracovní data na Batch výpočetních uzlů][forum_post]

Zapsána pomocí některého z členů týmu Azure Batch, popisuje několik technik, které můžete použít k nasazení aplikace a data na výpočetní uzly.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
