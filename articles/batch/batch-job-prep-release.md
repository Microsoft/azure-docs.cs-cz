---
title: Vytváření úloh pro přípravu & dokončených úloh na výpočetních uzlech
description: K minimalizaci přenosu dat do Azure Batch výpočetních uzlů a vyplňování úloh pro vyčištění uzlů při dokončování úloh můžete použít úkoly přípravy na úrovni úlohy.
ms.topic: how-to
ms.date: 02/17/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 5b1084cfdd5995b7983badcdce71460f7bdec3d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88919450"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Spuštění úkolů přípravy úloh a uvolnění úloh na výpočetních uzlech Batch

 Úloha Azure Batch často vyžaduje určitou formu nastavení před spuštěním úkolů a údržbou po dokončení úloh. Je možné, že budete muset stáhnout běžná vstupní data úkolů do výpočetních uzlů nebo odeslat výstupní data úkolu do Azure Storage po dokončení úlohy. K provedení těchto operací můžete použít úkoly **přípravy úlohy** a **vydání úlohy** .

## <a name="what-are-job-preparation-and-release-tasks"></a>Co jsou úkoly přípravy a uvolnění úloh?
Než se spustí úlohy úlohy, úloha přípravy úlohy se spustí na všech výpočetních uzlech, na kterých je naplánované spuštění aspoň jedné úlohy. Po dokončení úlohy se úloha uvolnění úlohy spustí na každém uzlu ve fondu, který provedl alespoň jeden úkol. Stejně jako u běžných úloh Batch můžete zadat příkazový řádek, který se má vyvolat, když se spustí Příprava úlohy nebo úloha vydání.

Úkoly přípravy a uvolnění úloh nabízejí známé funkce dávkové úlohy, jako je například stahování souborů ([soubory prostředků][net_job_prep_resourcefiles]), spuštění se zvýšenými oprávněními, vlastní proměnné prostředí, maximální doba provádění, počet opakování a doba uchovávání souborů.

V následujících částech se dozvíte, jak používat třídy [JobPreparationTask][net_job_prep] a [JobReleaseTask][net_job_release] , které se nacházejí v knihovně [Batch .NET][api_net] .

> [!TIP]
> Úkoly přípravy a uvolnění úloh jsou zvláště užitečné v prostředích sdíleného fondu, ve kterém se mezi spouštěním úloh přetrvají fondy výpočetních uzlů a používají se v mnoha úlohách.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Kdy použít úkoly přípravy a uvolnění úloh
Úkoly přípravy úloh a uvolnění úlohy jsou vhodné v následujících situacích:

**Stáhnout běžná data úlohy**

Dávkové úlohy často vyžadují pro úkoly úlohy společnou sadu dat jako vstup. Například při denních výpočtech analýzy rizik jsou tržní data specifická pro jednotlivé úlohy, ale společná pro všechny úlohy v rámci úlohy. Tato data z trhu, často i několik gigabajtů, by se měla do každého výpočetního uzlu stáhnout jenom jednou, aby se mohl použít libovolný úkol, který na uzlu běží. Použijte **úkol přípravy úlohy** ke stažení těchto dat do každého uzlu před provedením dalších úloh úlohy.

**Odstranit výstup úlohy a úlohy**

V prostředí "Shared Pool", kde výpočetní uzly fondu nejsou vyřazeny z provozu mezi úlohami, může být nutné odstranit data úlohy mezi spuštěním. Možná budete muset na uzlech ušetřit místo na disku nebo vyhovět zásadám zabezpečení vaší organizace. **Úkol uvolnění úlohy** slouží k odstranění dat, která byla stažena úlohou přípravy úlohy nebo vygenerována během provádění úkolu.

**Uchovávání protokolů**

Možná budete chtít uchovat kopii souborů protokolu, které vaše úkoly generují, nebo možná soubory s výpisem stavu systému, které se dají vygenerovat v neúspěšných aplikacích. V takových případech použijte **úkol uvolnění úlohy** pro komprimaci a nahrání těchto dat do účtu [Azure Storage][azure_storage] .

> [!TIP]
> Dalším způsobem, jak uchovat protokoly a další úlohy a výstupní data úloh, je použít knihovnu [Azure Batchch konvencí souborů](batch-task-output.md) .
>
>

## <a name="job-preparation-task"></a>Úkol přípravy úlohy


Před provedením úkolů úlohy Batch spustí úkol přípravy úlohy na každém výpočetním uzlu naplánovaném pro spuštění úkolu. Ve výchozím nastavení bude služba Batch čekat na dokončení úlohy přípravy úlohy před spuštěním úloh, které jsou naplánovány k provedení na uzlu. Službu však můžete nakonfigurovat tak, aby nečekala. Pokud se uzel restartuje, úloha přípravy úlohy se znovu spustí. Toto chování můžete také zakázat. Pokud máte úlohu s nakonfigurovanou úlohou přípravy úlohy a úlohou Správce úloh, úloha přípravy úlohy se spustí před úkolem správce úloh stejně jako u všech ostatních úloh. Úkol přípravy úlohy se vždycky spustí nejdřív.

Úkol přípravy úlohy je proveden pouze v uzlech, které mají naplánované spuštění úlohy. Tím se zabrání zbytečnému provedení přípravné úlohy pro případ, že uzel není přiřazený úkol. Tato situace může nastat, pokud je počet úloh úlohy menší než počet uzlů ve fondu. Platí také v případě, že je povoleno [Souběžné provádění úloh](batch-parallel-node-tasks.md) , což ponechá některé uzly nečinné, pokud je počet úloh nižší než celkové možné souběžné úlohy. Nespuštěním úlohy přípravy úlohy na nečinných uzlech můžete strávit poplatky za přenos dat bez dalších poplatků.

> [!NOTE]
> [JobPreparationTask][net_job_prep_cloudjob] se liší od [CloudPool. StartTask][pool_starttask] v tom, že JobPreparationTask spouští na začátku každé úlohy, zatímco StartTask se spustí jenom v případě, že se výpočetní uzel nejdřív připojí k fondu nebo restartuje.
>


>## <a name="job-release-task"></a>Úkol uvolnění úlohy

Jakmile je úloha označena jako dokončená, úkol uvolnění úlohy se spustí na každém uzlu ve fondu, který provedl alespoň jeden úkol. Úlohu označíte jako dokončenou vyvoláním žádosti o ukončení. Služba Batch pak nastaví stav úlohy na *ukončení*, ukončí všechny aktivní nebo spuštěné úlohy přidružené k úloze a spustí úlohu uvolnění úlohy. Úloha se pak přesune do *dokončeného* stavu.

> [!NOTE]
> Úkol uvolnění úlohy se taky provede odstraněním úlohy. Pokud se ale úloha už ukončila, úloha vydání se nespustí podruhé, pokud se úloha později odstraní.

Úlohy uvolnění úloh se můžou spouštět po dobu maximálně 15 minut, než se služba Batch ukončí. Další informace najdete v [dokumentaci REST API Reference](/rest/api/batchservice/job/add#jobreleasetask).
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Úkoly přípravy a uvolnění úloh pomocí batch .NET
Pokud chcete použít úkol přípravy úlohy, přiřaďte objekt [JobPreparationTask][net_job_prep] k vlastnosti [vlastnosti cloudjob. JobPreparationTask][net_job_prep_cloudjob] vaší úlohy. Podobně inicializujte [JobReleaseTask][net_job_release] a přiřaďte ji k vlastnosti [vlastnosti cloudjob. JobReleaseTask][net_job_prep_cloudjob] vaší úlohy, abyste mohli nastavit úlohu verze úlohy.

V tomto fragmentu kódu `myBatchClient` je instance [BatchClient][net_batch_client]a `myPool` je existující fond v rámci účtu Batch.

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

Jak už bylo zmíněno dříve, úloha vydání se spustí, když se úloha ukončí nebo odstraní. Ukončení úlohy pomocí [JobOperations. TerminateJobAsync][net_job_terminate]. Odstraňte úlohu pomocí [JobOperations. DeleteJobAsync][net_job_delete]. Při dokončení jeho úkolů nebo při dosažení časového limitu, který jste nadefinovali, se obvykle úloha ukončí nebo odstraní.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Ukázka kódu na GitHubu
Pokud chcete zobrazit úkoly přípravy úlohy a vydání v akci, podívejte se na vzorový projekt [JobPrepRelease][job_prep_release_sample] na GitHubu. Tato Konzolová aplikace provede následující akce:

1. Vytvoří fond se dvěma uzly.
2. Vytvoří úlohu s přípravou, vydanou verzí a standardní úlohou úlohy.
3. Spustí úkol přípravy úlohy, který nejdřív zapíše ID uzlu do textového souboru v adresáři Shared (sdílený) uzlu.
4. Spustí úlohu na každém uzlu, který zapíše své ID úlohy do stejného textového souboru.
5. Po dokončení všech úloh (nebo vypršení časového limitu) vytiskne obsah každého textového souboru uzlu do konzoly.
6. Po dokončení úlohy spustí úkol uvolnění úlohy, aby se soubor odstranil z uzlu.
7. Vytiskne kódy ukončení úloh přípravy a uvolnění úlohy pro každý uzel, na kterém byly provedeny.
8. Pozastaví provádění, aby bylo možné potvrzovat úlohu nebo odstranění fondu.

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
> Vzhledem k vytvoření proměnné a času spuštění uzlů v novém fondu (některé uzly jsou připravené na úlohy před ostatními) se může zobrazit jiný výstup. Konkrétně vzhledem k tomu, že úkoly jsou dokončeny rychle, jeden z uzlů fondu může spustit všechny úkoly úlohy. Pokud k tomu dojde, budete si všimnout, že úlohy přípravy a vydání úlohy pro uzel, který neprovede žádné úlohy, neexistují.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Kontrola úloh přípravy a vydání úloh v Azure Portal
Když spustíte ukázkovou aplikaci, můžete použít [Azure Portal][portal] k zobrazení vlastností úlohy a jejích úkolů nebo dokonce stáhnout sdílený textový soubor, který je upraven úlohami úlohy.

Níže uvedený snímek obrazovky ukazuje okno **přípravné úkoly** v Azure Portal po spuštění ukázkové aplikace. Až se vaše úkoly dokončí (ale před odstraněním úlohy a fondu), přejděte do vlastností *JobPrepReleaseSampleJob* a zobrazte jejich vlastnosti kliknutím na **Příprava úkolů** nebo na úlohy vydaných **verzí** .

![Vlastnosti přípravy úlohy v Azure Portal][1]

## <a name="next-steps"></a>Další kroky
### <a name="application-packages"></a>Balíčky aplikací
Kromě úlohy přípravy úlohy můžete také použít funkci [balíčky aplikací](batch-application-packages.md) služby Batch k přípravě výpočetních uzlů pro provádění úloh. Tato funkce je zvláště užitečná pro nasazení aplikací, které nevyžadují spuštění instalačního programu, aplikací, které obsahují mnoho (100) souborů, nebo aplikací, které vyžadují striktní správu verzí.

### <a name="installing-applications-and-staging-data"></a>Instalace aplikací a pracovních dat
Tento příspěvek na blogu MSDN poskytuje přehled několika metod přípravy uzlů na spuštěné úlohy:

[Instalace aplikací a pracovních dat na výpočetních uzlech Batch][forum_post]

Napsaný jedním z Azure Batch členů týmu, popisuje několik postupů, které můžete použít k nasazení aplikací a dat do výpočetních uzlů.

[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_listjobs]: /dotnet/api/microsoft.azure.batch.joboperations
[api_rest]: /rest/api/batchservice/
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: /dotnet/api/microsoft.azure.batch.jobpreparationtask
[net_job_prep_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_job_prep_resourcefiles]: /dotnet/api/microsoft.azure.batch.jobpreparationtask
[net_job_delete]: /previous-versions/azure/mt281411(v=azure.100)
[net_job_terminate]: /previous-versions/azure/mt188985(v=azure.100)
[net_job_release]: /dotnet/api/microsoft.azure.batch.jobreleasetask
[net_job_release_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[pool_starttask]: /dotnet/api/microsoft.azure.batch.cloudpool

[net_list_certs]: /dotnet/api/microsoft.azure.batch.certificateoperations
[net_list_compute_nodes]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_job_schedules]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_jobprep_status]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_jobs]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_nodefiles]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_pools]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_schedule_jobs]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_task_files]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_list_tasks]: /dotnet/api/microsoft.azure.batch.joboperations

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
