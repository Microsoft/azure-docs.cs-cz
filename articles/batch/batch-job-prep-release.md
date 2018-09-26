---
title: Vytvoření úkolů přípravy úloh a dokončení úlohy na výpočetních uzlech – Azure Batch | Dokumentace Microsoftu
description: Můžete omezit přenosy dat do výpočetních uzlů služby Azure Batch, úrovní úlohy přípravy úlohy a uvolnění úlohy pro uzel vyčištění po dokončení úlohy.
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
ms.openlocfilehash: da69cc22fbb071ce3fa4b2c53aaf0b1ec4ba5e46
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096306"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Spuštění úlohy přípravy a uvolnění úloh v Batch výpočetních uzlů

 Úlohu služby Azure Batch často vyžaduje určitou formu instalace předtím, než její úkoly jsou spouštěny a následné úlohy údržby dokončení svých úkolů. Může být potřeba stáhnout vstupní data pro běžné úlohy na výpočetních uzlech, nebo nahrát úloh výstupní data do služby Azure Storage po dokončení úlohy. Můžete použít **přípravy** a **úlohy vydání** úkoly k provedení těchto operací.

## <a name="what-are-job-preparation-and-release-tasks"></a>Jaké jsou úlohy přípravy a uvolnění úlohy?
Před spuštěním úkolů úlohy přípravy úlohy běží na všech výpočetních uzlech naplánované spuštění alespoň jeden úkol. Po dokončení úlohy na každém uzlu ve fondu, ve kterém se spustil alespoň jeden úkol spustí úkol uvolnění úlohy. Stejně jako u běžných úkolů služby Batch můžete zadat příkazový řádek, který má být vyvolána při spuštění úlohy přípravy a uvolnění úlohy.

Úkoly přípravy a uvolnění úloh nabízejí známé dávkové úlohy funkce jako stahování souborů ([soubory prostředků][net_job_prep_resourcefiles]), se zvýšenými oprávněními provádění, vlastní proměnné prostředí, maximální dobu provádění, zkuste to znovu počet a dobu uchovávání souboru.

V následujících částech se dozvíte víc o použití [JobPreparationTask] [ net_job_prep] a [JobReleaseTask] [ net_job_release] třídy součástí [ Batch .NET] [ api_net] knihovny.

> [!TIP]
> Úkoly přípravy a uvolnění úloh jsou zvláště užitečné v prostředích "sdíleného fondu", ve které fond výpočetních uzlů přetrvá mezi spuštění úlohy a používá mnoho úloh.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Kdy použít úlohy přípravy a uvolnění úlohy
Úlohy přípravy a uvolnění úloh jsou vhodné v těchto situacích:

**Stáhnout data běžných úkolů**

Úlohy batch často vyžadují společnou sadu dat jako vstup pro úkoly úlohy. Například v každodenní výpočty rizik analýzy, trhu je specifické pro úlohy, ještě společné pro všechny úkoly v úloze. Tato data na trhu, často několik gigabajtů velikost, mají být stažené do každého výpočetního uzlu pouze jednou tak, aby ho může použít jakékoli úlohy, která běží na uzlu. Použití **úkol přípravy úlohy** ke stažení těchto dat do každého uzlu před provádění úlohy je další úkoly.

**Odstranit výstup úloh a úkolů**

V prostředí "sdíleného fondu", kde nejsou Vyřazená z provozu mezi úlohami fond výpočetních uzlů, budete muset odstranit data úlohy mezi spuštění. Můžete potřebovat a uchovejte prostor na disku na uzly, splňují zásady zabezpečení vaší organizace. Použití **úkol uvolnění úlohy** odstranit data, která se stahují úkol přípravy úlohy nebo vygenerována během provádění úkolu.

**Uchovávání protokolů**

Můžete chtít zachovat kopii soubory protokolů, které vaše úlohy generují, chybě nebo možná soubory s výpisem paměti, které mohou být generovány aplikace, které selhaly. Použití **úkol uvolnění úlohy** v takových případech zkomprimovat a nahrát tato data do [služby Azure Storage] [ azure_storage] účtu.

> [!TIP]
> Dalším způsobem, jak zachovat protokolů a dalších úloh a výstupní data, je použít [Azure Batch File Conventions](batch-task-output.md) knihovny.
> 
> 

## <a name="job-preparation-task"></a>Příprava na úlohu
Před spuštěním úkolů úlohy služby Batch spustí úkol přípravy úlohy na každém výpočetním uzlu, který je naplánován ke spuštění úlohy. Ve výchozím nastavení čeká služba Batch úkol přípravy úlohy dokončení před spuštěním úlohy naplánované na uzlu. Můžete ale nakonfigurovat službu nečekat. Pokud se uzel restartuje, úkol přípravy úlohy znovu spustí, ale toto chování můžete také zakázat.

Úkol přípravy úlohy je provedena pouze na uzlech, které jsou naplánovány ke spuštění úlohy. To zabraňuje zbytečné spouštění přípravný úkol v případě, že uzlu se nepřiřazují úkolu. Tato situace může nastat, pokud počet úkolů pro úlohu je menší než počet uzlů ve fondu. Také platí, kdy [provedení souběžné úlohy](batch-parallel-node-tasks.md) je povoleno, který ponechá některé if nečinných uzlů počet úkolů je nižší než celkový počet možných souběžných úkolů. Nespouštíte úkol přípravy úlohy na nečinné uzly, můžete strávit méně peněz na poplatky za přenos dat.

> [!NOTE]
> [JobPreparationTask] [ net_job_prep_cloudjob] se liší od [CloudPool.StartTask] [ pool_starttask] v tom, že JobPreparationTask spouští na začátku každé úlohy, zatímco StartTask spustí, pouze když připojení výpočetního uzlu nejprve fondu nebo restartování.
> 
> 

## <a name="job-release-task"></a>Uvolnění úlohy
Jakmile úloha označena jako dokončená, je proveden úkol uvolnění úlohy na každém uzlu ve fondu, ve kterém se spustil alespoň jeden úkol. Úloha se označit jako dokončený vydáním žádost o ukončení. Služba Batch pak nastaví stav úlohy na *ukončení*, ukončí všechny aktivní nebo spuštěné úkoly spojené s úlohou a úkol uvolnění úlohy se spustí. Úloha je následně přesunuto do *Dokončit* stavu.

> [!NOTE]
> Odstranění úlohy, spustí úkol uvolnění úlohy. Ale pokud úloha již byla ukončena, uvolnění úlohy není spuštěn podruhé Pokud úloha je později odstranit.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Úlohy přípravy a uvolnění úlohy pomocí rozhraní Batch .NET
Pokud chcete použít úkol přípravy úlohy, přiřadit [JobPreparationTask] [ net_job_prep] objekt pro vaši úlohu [CloudJob.JobPreparationTask] [ net_job_prep_cloudjob] vlastnost. Podobně, inicializovat [JobReleaseTask] [ net_job_release] a přiřadíte ho k vaší úlohy [CloudJob.JobReleaseTask] [ net_job_prep_cloudjob] vlastnost pro nastavení úlohy uvolnění úlohy.

V tomto fragmentu kódu `myBatchClient` je instance [BatchClient][net_batch_client], a `myPool` je existující fond v rámci účtu Batch.

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

Jak už bylo zmíněno dříve, uvolnění úlohy se spustí při úlohy je byl ukončen nebo odstraněn. Ukončit úlohu s [JobOperations.TerminateJobAsync][net_job_terminate]. Úlohu můžete odstranit příkazem [JobOperations.DeleteJobAsync][net_job_delete]. Obvykle ukončit nebo odstranit úlohu při dokončení svých úkolů nebo když bylo dosaženo časového limitu, který jste definovali.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Vzorový kód na Githubu
Chcete-li zobrazit přípravy úlohy a uvolnění úlohy v akci, podívejte [JobPrepRelease] [ job_prep_release_sample] ukázkového projektu na Githubu. Tato Konzolová aplikace provede následující akce:

1. Vytvoří fond se dvěma uzly.
2. Vytvoří úlohu s přípravy úlohy, vydání a standardní úlohy.
3. Spustí úkol přípravy úlohy, které nejprve zapíše ID uzlu do textového souboru v adresáři "sdílené" uzlu.
4. Spustí úlohu v každém uzlu, který zapíše jeho ID úkolu do stejné textového souboru.
5. Po dokončení všech úkolů (nebo je dosaženo časového limitu), vytiskne obsah textového souboru každý uzel do konzoly.
6. Když úloha je dokončená, spustí úkol uvolnění úlohy se odstranit soubor z uzlu.
7. Vypíše ukončovací kódy úkolů přípravy a uvolnění úlohy pro každý uzel, na kterém se provedla.
8. Pozastaví spuštění umožňující potvrzení odstranění úlohy a/nebo fondu.

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
> Z důvodu proměnné vytváření a počáteční čas uzlů v novém fondu (některé uzly jsou připraveny pro úlohy před ostatními) se může zobrazit různé výstup. Konkrétně protože dokončení úloh rychle, jeden z uzlů fondu může provést všechny úkoly úlohy. Pokud k tomu dojde, všimnete si, že příprava úlohy a uvolnění úloh neexistují pro uzel, který spuštěné žádné úlohy.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Kontrola přípravy úlohy a uvolnění úloh na webu Azure Portal
Když spustíte ukázkovou aplikaci, můžete použít [webu Azure portal] [ portal] zobrazí vlastnosti úlohy a úkoly, nebo dokonce i stahovat sdílené textový soubor, který je upraven na úkoly úlohy.

Na snímku obrazovky níže ukazuje **okna úkoly přípravy** na webu Azure Portal po spuštění ukázkové aplikace. Přejděte *JobPrepReleaseSampleJob* vlastnosti po dokončení úkolů (ale před odstraněním úlohy a fondu) a klikněte na tlačítko **přípravné úkoly** nebo **uvolnění úlohy**zobrazení jejich vlastností.

![Vlastnosti úlohy přípravy na webu Azure portal][1]

## <a name="next-steps"></a>Další postup
### <a name="application-packages"></a>Balíčky aplikací
Kromě úkol přípravy úlohy lze také použít [balíčky aplikací](batch-application-packages.md) funkcí služby Batch pro přípravu výpočetních uzlů k provádění úkolu. Tato funkce je zvláště užitečná pro nasazení aplikací, které nevyžadují spuštění instalačního programu, aplikace, které obsahují mnoho souborů (100 +) nebo aplikace, které vyžadují striktní verzí.

### <a name="installing-applications-and-staging-data"></a>Instalace aplikací a přípravu dat
Tento příspěvek na fóru MSDN poskytuje přehled o několik metod pro spouštění úkolů přípravy uzly:

[Instalace aplikací a přípravu dat v listu výpočetních uzlů][forum_post]

Zapsat pomocí některého z členů týmu služby Azure Batch, popisuje několik technik, které můžete použít k nasazení aplikací a dat do výpočetních uzlů.

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
