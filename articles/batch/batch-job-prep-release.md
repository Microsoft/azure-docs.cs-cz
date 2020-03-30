---
title: Vytváření úkolů pro přípravu & dokončení úloh na výpočetních uzlech – Azure Batch
description: Pomocí úloh přípravy na úrovni úloh minimalizujte přenos dat do výpočetních uzlů Azure Batch a uvolněte úlohy pro vyčištění uzlu při dokončení úlohy.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: d9f6f015c210592d5d8053b1b34d5357bb357629
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586780"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Spuštění úloh přípravy úloh a úloh y uvolnění úloh y dávkových výpočetních uzlů

 Dávková úloha Azure často vyžaduje určitou formu nastavení před provedením jejích úkolů a údržbu po dokončení úloh. Možná budete muset stáhnout společná vstupní data úlohy do výpočetních uzlů nebo nahrát výstupní data úlohy do Azure Storage po dokončení úlohy. K provedení těchto operací můžete použít **úkoly přípravy úloh** y a **uvolnění úlohy.**

## <a name="what-are-job-preparation-and-release-tasks"></a>Co jsou úkoly přípravy a uvolnění úlohy?
Před spuštěním úlohy se úloha přípravy úloh spustí na všech výpočetních uzlech, které mají spustit alespoň jednu úlohu. Po dokončení úlohy se úloha uvolnění úlohy spustí na každém uzlu ve fondu, který provedl alespoň jednu úlohu. Stejně jako u běžných dávkových úloh můžete určit příkazový řádek, který má být vyvolán při spuštění úlohy přípravy úlohy nebo vydání.

Úlohy přípravy a vydání nabízejí známé funkce dávkových úloh, jako je stahování souborů[(soubory prostředků),][net_job_prep_resourcefiles]zvýšené provádění, proměnné vlastního prostředí, maximální doba provádění, počet opakování a doba uchovávání souborů.

V následujících částech se dozvíte, jak používat třídy [JobPreparationTask][net_job_prep] a [JobReleaseTask][net_job_release] nalezené v knihovně [Batch .NET.][api_net]

> [!TIP]
> Úlohy přípravy a vydání úlohy jsou užitečné zejména v prostředích "sdíleného fondu", ve kterém fond výpočetních uzlů přetrvává mezi spuštění úloh a používá mnoho úloh.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Kdy použít úlohy přípravy a uvolnění úlohy
Úkoly přípravy na práci a uvolnění úlohy jsou vhodné pro následující situace:

**Stažení běžných dat úkolů**

Dávkové úlohy často vyžadují společnou sadu dat jako vstup pro úkoly úlohy. Například při výpočtech denní analýzy rizik jsou údaje o trhu specifické pro danou úlohu, ale jsou společné pro všechny úkoly v úloze. Tato data o trhu, často několik gigabajtů ve velikosti, by měly být staženy do každého výpočetního uzlu pouze jednou tak, aby všechny úlohy, které běží na uzlu můžete použít. Pomocí **úlohy přípravy úlohy** můžete tato data stáhnout do každého uzlu před provedením dalších úloh úlohy.

**Odstranit úlohu a výstup úlohy**

V prostředí "sdíleného fondu", kde výpočetní uzly fondu nejsou vyřazeny z provozu mezi úlohami, může být nutné odstranit data úloh mezi spuštěními. Možná budete muset ušetřit místo na disku v uzlech nebo uspokojit zásady zabezpečení vaší organizace. Pomocí **úlohy uvolnění úlohy** odstraňte data stažená úlohou přípravy úlohy nebo generovaná během provádění úloh.

**Uchovávání protokolu**

Můžete chtít zachovat kopii souborů protokolu, které vaše úkoly generovat nebo případně soubory s výpisem stavu systému, které mohou být generovány neúspěšných aplikací. V takových případech použijte **úlohu uvolnění úlohy** ke kompresi a nahrání těchto dat do účtu [Azure Storage.][azure_storage]

> [!TIP]
> Dalším způsobem, jak zachovat protokoly a další výstupní data úloh a úloh, je použití [knihovny Azure Batch File Conventions.](batch-task-output.md)
>
>

## <a name="job-preparation-task"></a>Úkol přípravy úlohy


Před provedením úlohy úlohy Batch provede úlohu přípravy úlohy na každém výpočetním uzlu naplánovaném ke spuštění úlohy. Ve výchozím nastavení batch čeká na dokončení úlohy přípravy úlohy před spuštěním úloh y naplánované ke spuštění v uzlu. Můžete však nakonfigurovat službu nečekat. Pokud se uzel restartuje, úloha přípravy úlohy se spustí znovu. Toto chování můžete také zakázat. Pokud máte úlohu s nakonfigurovanou úlohou přípravy úlohy a úlohou správce úloh, úloha přípravy úlohy se spustí před úlohou správce úloh, stejně jako u všech ostatních úkolů. Úloha přípravy úlohy je vždy spuštěna jako první.

Úloha přípravy úlohy je spouštěna pouze na uzlech, které jsou naplánovány ke spuštění úlohy. Tím se zabrání zbytečnému spuštění přípravné úlohy v případě, že uzlu není přiřazen úkol. K tomu může dojít, pokud je počet úkolů pro úlohu menší než počet uzlů ve fondu. Platí také v případě, že je povoleno [souběžné provádění úloh,](batch-parallel-node-tasks.md) což ponechává některé uzly nečinné, pokud je počet úloh nižší než celkový počet možných souběžných úloh. Tím, že nespouštějte úlohu přípravy úlohy na nečinných uzlech, můžete utratit méně peněz za poplatky za přenos dat.

> [!NOTE]
> [JobPreparationTask][net_job_prep_cloudjob] se liší od [CloudPool.StartTask][pool_starttask] v tom, že JobPreparationTask provede na začátku každé úlohy, vzhledem k tomu, StartTask provede pouze v případě, že výpočetní uzel nejprve připojí fondu nebo restartuje.
>


>## <a name="job-release-task"></a>Úloha uvolnění úlohy

Jakmile je úloha označena jako dokončená, úloha uvolnění úlohy je spuštěna na každém uzlu ve fondu, který provedl alespoň jednu úlohu. Úlohu označíte jako dokončenou vydáním žádosti o ukončení. Služba Batch pak nastaví stav úlohy na *ukončování*, ukončí všechny aktivní nebo spuštěné úlohy přidružené k úloze a spustí úlohu uvolnění úlohy. Úloha se pak přesune do *stavu dokončení.*

> [!NOTE]
> Odstranění úlohy také provede úlohu uvolnění úlohy. Pokud však úloha již byla ukončena, úloha vydání není spuštěna podruhé, pokud je úloha později odstraněna.

Úlohy vydání úlohy lze spustit maximálně 15 minut před ukončením služby Batch. Další informace naleznete v [referenční dokumentaci rozhraní REST API](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask).
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Úlohy přípravy a uvolnění úloh y Batch .NET
Chcete-li použít úlohu přípravy úlohy, přiřaďte objekt [JobPreparationTask][net_job_prep] k vlastnosti [CloudJob.JobPreparationTask][net_job_prep_cloudjob] vaší úlohy. Podobně inicializovat [JobReleaseTask][net_job_release] a přiřadit jej k vaší úlohy [CloudJob.JobReleaseTask][net_job_prep_cloudjob] vlastnost nastavit úlohu vydání úlohy.

V tomto fragmentu kódu `myBatchClient` je instancí [BatchClient][net_batch_client]a `myPool` je existujícím fondem v rámci účtu Batch.

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

Jak již bylo zmíněno dříve, úloha vydání je spuštěna při ukončení nebo odstranění úlohy. Ukončete úlohu pomocí [joboperations.TerminateJobAsync][net_job_terminate]. Odstraňte úlohu s [JobOperations.DeleteJobAsync][net_job_delete]. Úlohu obvykle ukončíte nebo odstraníte po dokončení jejích úkolů nebo po dosažení časového času, který jste definovali.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Ukázka kódu na GitHubu
Chcete-li zobrazit úkoly přípravy úloh a uvolnění v akci, podívejte se na ukázkový projekt [JobPrepRelease][job_prep_release_sample] na GitHubu. Tato konzolová aplikace provádí následující akce:

1. Vytvoří fond se dvěma uzly.
2. Vytvoří úlohu s přípravami, uvolněním úloh a standardními úkoly.
3. Spustí úlohu přípravy úlohy, která nejprve zapíše ID uzlu do textového souboru v adresáři "sdílené" uzlu.
4. Spustí úlohu na každém uzlu, který zapíše id úlohy do stejného textového souboru.
5. Po dokončení všech úkolů (nebo je dosaženo časového času) vytiskne obsah textového souboru každého uzlu do konzoly.
6. Po dokončení úlohy spustí úlohu uvolnění úlohy k odstranění souboru z uzlu.
7. Vytiskne ukončovací kódy úloh přípravy úloh a uvolnění úloh pro každý uzel, na kterém byly provedeny.
8. Pozastaví provádění povolit potvrzení úlohy nebo odstranění fondu.

Výstup z ukázkové aplikace je podobný následujícímu:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
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
> Vzhledem k vytvoření proměnné a čas zahájení uzlů v novém fondu (některé uzly jsou připraveny pro úkoly před ostatními), může se zobrazit jiný výstup. Konkrétně vzhledem k tomu, že úkoly dokončit rychle, jeden z uzlů fondu může provést všechny úkoly úlohy. Pokud k tomu dojde, zjistíte, že úlohy přípravy a uvolnění úlohy úlohy úlohy projektu neexistují pro uzel, který provedl žádné úkoly.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Kontrola úloh přípravy a uvolnění na webu Azure Portal
Při spuštění ukázkové aplikace můžete pomocí [portálu Azure][portal] zobrazit vlastnosti úlohy a její úkoly nebo dokonce stáhnout sdílený textový soubor, který je upraven úkoly úlohy úlohy.

Snímek obrazovky níže ukazuje **okno Úlohy přípravy** na webu Azure portal po spuštění ukázkové aplikace. Po dokončení úloh (ale před odstraněním úlohy a fondu) přejděte na vlastnosti *JobPrepReleaseSampleJob* a kliknutím na **úkoly přípravy** nebo **Uvolnění** zobrazte jejich vlastnosti.

![Vlastnosti přípravy úloh na webu Azure Portal][1]

## <a name="next-steps"></a>Další kroky
### <a name="application-packages"></a>Balíčky aplikací
Kromě úlohy přípravy úlohy můžete také použít funkci [balíčky aplikací](batch-application-packages.md) Batch k přípravě výpočetních uzlů pro spuštění úlohy. Tato funkce je užitečná zejména pro nasazení aplikací, které nevyžadují spuštění instalačního programu, aplikací obsahujících mnoho (100+) souborů nebo aplikací, které vyžadují přísné řízení verzí.

### <a name="installing-applications-and-staging-data"></a>Instalace aplikací a přípravná data
Tento příspěvek na fóru MSDN poskytuje přehled několika způsobů přípravy uzlů pro spouštění úloh:

[Instalace aplikací a pracovních dat na výpočetních uzlech Batch][forum_post]

Napsaný jedním z členů týmu Azure Batch, popisuje několik technik, které můžete použít k nasazení aplikací a dat pro výpočet uzlů.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
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
