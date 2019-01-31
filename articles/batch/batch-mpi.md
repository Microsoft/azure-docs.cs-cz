---
title: Použití úkolů s více instancemi ke spouštění aplikací MPI – Azure Batch | Dokumentace Microsoftu
description: Zjistěte, jak spouštět aplikace rozhraní MPI (Message Passing Interface) ve službě Azure Batch pomocí úloh typem s více instancemi.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 06/12/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2c7d6f75b9f2d6932ae3c8de863ad277fea8ffe
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55460535"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Použití úkolů s více instancemi ke spouštění aplikací rozhraní MPI (Message Passing Interface) ve službě Batch

Úkoly s více instancemi umožňují spustit úlohu služby Azure Batch na několika výpočetních uzlech současně. Povolit tyto úlohy vysokovýkonného výpočetního prostředí scénáře, jako jsou aplikace rozhraní MPI (Message Passing Interface) ve službě Batch. V tomto článku se dozvíte, jak provádět úkoly s více instancemi pomocí [Batch .NET] [ api_net] knihovny.

> [!NOTE]
> Zaměřte se na Batch .NET, MS-MPI, příklady v tomto článku a Windows výpočetních uzlů, platí pro další platformy a technologie (Python a Intel MPI na uzly s Linuxem, například) zde uvedenou koncepci úkolů s více instancemi.
>
>

## <a name="multi-instance-task-overview"></a>Přehled úloh s více instancemi
Ve službě Batch, je obvykle každý úkol spustí na jeden výpočetní uzel – odeslání více úkolů do úlohy a plány služby Batch každý úkol spuštěn na uzlu. Ale nakonfigurováním úkolu **nastavení víc instancí**, dáte Batch místo toho vytvořte jednu primární úlohu a několik dílčích úkolů, které jsou poté provedeny ve více uzlech.

![Přehled úloh s více instancemi][1]

Při odesílání úlohy s více instancemi nastavení do úlohy služby Batch provádí několik kroků jedinečný pro úkoly s více instancemi:

1. Služba Batch vytvoří jeden **primární** a několik **dílčí úkoly** podle nastavení víc instancí. Celkový počet úloh (primární plus všechny dílčí úkoly) odpovídá počtu **instance** (výpočetních uzlů) určíte v nastavení víc instancí.
2. Batch označí jeden výpočetních uzlů, jako **hlavní**a naplánuje primární úkolů ke spuštění na hlavním serveru. Naplánuje dílčí úkoly pro spuštění ve zbývající části výpočetních uzlů přidělených pro úkoly s více instancemi, jednoho dílčího úkolu na uzlu.
3. Primární a všechny dílčí úkoly stahovat **společné soubory prostředků** zadaným v nastaveních s více instancemi.
4. Po běžných prostředků soubory byly staženy, primární a dílčí úkoly spouštět **koordinace příkaz** zadaným v nastaveních s více instancemi. Příkaz koordinaci se obvykle používá k přípravě uzlů pro provádění úlohy. To může zahrnovat spouštění služby na pozadí (například [Microsoft MPI][msmpi_msdn]společnosti `smpd.exe`) a ověření, že uzly jsou připravené ke zpracování zpráv mezi uzly.
5. Primární úkol spustí **příkaz aplikace** na hlavní uzel *po* příkazu koordinace byla úspěšně dokončena, tak, že primárním a všechny dílčí úkoly. Příkaz aplikace je příkazový řádek samotný úkoly s více instancemi a je provedena pouze ve primárního úkolu. V [MS MPI][msmpi_msdn]– řešení, to je, kde spustit vaši aplikaci s povolenými MPI použitím `mpiexec.exe`.

> [!NOTE]
> I když je funkčně distinct, "s více instancemi úloha" není typu jedinečný úkolu jako [StartTask] [ net_starttask] nebo [JobPreparationTask] [ net_jobprep]. Úkoly s více instancemi je jednoduše standardní úlohy služby Batch ([CloudTask] [ net_task] v Batch .NET) nakonfigurovali jejichž nastavení víc instancí. V tomto článku označujeme to jako **úkoly s více instancemi**.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Požadavky pro úkoly s více instancemi
Úkoly s více instancemi vyžadují fond s **komunikace mezi uzly povolena**a s **provedení souběžné úlohy zakázané**. Chcete-li zakázat spuštění souběžných úloh, nastavte [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool#Microsoft_Azure_Batch_CloudPool_MaxTasksPerComputeNode) vlastnost na hodnotu 1.

> [!NOTE]
> Batch [omezení](batch-quota-limit.md#other-limits) velikost fondu se komunikace mezi uzly povolena.


Tento fragment kódu ukazuje, jak vytvořit fond pro úkoly s více instancemi pomocí knihovny Batch .NET.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

> [!NOTE]
> Pokud se pokusíte spustit úkolu víc instancí ve fondu s komunikace mezi uzly zakázána, nebo se *maxTasksPerNode* hodnota větší než 1, úloha není vůbec naplánováno – zůstane po neomezenou dobu v "aktivním" stavu. 


### <a name="use-a-starttask-to-install-mpi"></a>StartTask použít k instalaci MPI
Pro spouštění aplikací MPI s úkoly s více instancemi, musíte nejprve nainstalovat implementace MPI (MS-MPI nebo Intel MPI, například) na výpočetních uzlech ve fondu. Toto je vhodná doba k použití [StartTask][net_starttask], která se spustí pokaždé, když se uzel připojí fondu nebo byl restartován. Tento fragment kódu vytvoří, která určuje instalační balíček MS-MPI jako StartTask [soubor prostředků][net_resourcefile]. Spouštěcí úkol příkazový řádek se spustí po soubor prostředků se stáhne do uzlu. V takovém případě příkazový řádek provede bezobslužnou instalaci MS MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Vzdálený přímý přístup do paměti (RDMA)
Při výběru možnosti [podporující RDMA velikost](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) například A9 výpočetních uzlů ve fondu Batch, vaše aplikace MPI můžete využít Azure vysoce výkonné, s nízkou latencí paměti vzdáleného přímého přístupu (do počítače RDMA) sítě.

Vyhledání velikosti definované jako "Podpora RDMA" v těchto článcích:

* **CloudServiceConfiguration** fondy

  * [Velikosti pro Cloud Services](../cloud-services/cloud-services-sizes-specs.md) (jenom Windows)
* **VirtualMachineConfiguration** fondy

  * [Velikosti virtuálních počítačů v Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Velikosti virtuálních počítačů v Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> Abyste mohli využívat RDMA [Linuxových výpočetních uzlů](batch-linux-nodes.md), je nutné použít **Intel MPI** na uzlech. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Vytvoření úkolu víc instancí pomocí rozhraní Batch .NET
Teď, když jsme si popsali požadavky fondu a instalace balíčku MPI, můžeme vytvořit úkoly s více instancemi. V tomto fragmentu kódu, se nám vytvořit standardní [CloudTask][net_task], nakonfigurujte její [MultiInstanceSettings] [ net_multiinstance_prop] vlastnost. Jak už bylo zmíněno dříve, není úkoly s více instancemi typu různé úlohy, ale standardní úlohy služby Batch nakonfigurované nastavení víc instancí.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Primární úlohy a dílčí úkoly
Když vytvoříte nastavení víc instancí úlohy, zadejte počet výpočetních uzlů, které jsou ke spuštění úlohy. Při odesílání úkolů do úlohy, služba Batch vytvoří jeden **primární** úloh a dostatečně **dílčí úkoly** společně, které odpovídají počtu uzlů, které jste zadali.

Tyto úlohy jsou přiřazeny id celé číslo v rozsahu od 0 do *numberOfInstances* – 1. Úlohu s id 0 je primární úloha a všechny ostatní identifikátory jsou dílčí úkoly. Pokud vytvoříte následující nastavení víc instancí úlohy, primárního úkolu by mít id, 0 a dílčí úkoly by měla ID 1 až 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Hlavní uzel
Při odesílání úkolu víc instancí, služba Batch jeden z výpočetních uzlů označuje jako "hlavní" uzel a naplánuje primární úkolů ke spuštění na hlavní uzel. Dílčí úkoly jsou naplánovány ke spuštění na zbývající uzly přiděleny úkoly s více instancemi.

## <a name="coordination-command"></a>Příkaz koordinace
**Koordinace příkaz** se provedl obě primární a dílčích úkolů.

Vyvolání příkazu koordinace blokuje – Batch příkaz aplikace nespustí, dokud je úspěšně vrácený příkaz koordinace pro všechny dílčí úkoly. Příkaz koordinace by proto spustit žádné služby vyžaduje na pozadí, ověřte, že jsou připravené k použití a ukončete. Tento příkaz koordinace řešení prostřednictvím MS-MPI verze 7 spustí službu SMPD na uzlu, například následně skončí:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Všimněte si použití `start` v tomto příkazu koordinace. Toto je povinné, protože `smpd.exe` aplikace nevrátí ihned po spuštění. Bez použití [start] [ cmd_start] příkazu, tento příkaz koordinace by vrátit a proto by blokovaly příkaz aplikace spouštění.

## <a name="application-command"></a>Příkaz aplikace
Jakmile primárního úkolu a všechny dílčí úkoly dokončení provádění příkazu koordinace, příkazový řádek úkolu víc instancí provádí ve primárního úkolu *pouze*. To označujeme **příkaz aplikace** ho odlišuje od příkazu koordinace.

Pro aplikace MS MPI, použijte příkaz aplikace ke spuštění vaší aplikace s povolenými MPI s `mpiexec.exe`. Například tady je příkaz aplikace pro řešení, které využívá MS-MPI verze 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Protože MS-MPI `mpiexec.exe` používá `CCP_NODES` proměnné ve výchozím nastavení (naleznete v tématu [proměnné prostředí](#environment-variables)) vylučuje příkazový řádek aplikace příkladu výše.
>
>

## <a name="environment-variables"></a>Proměnné prostředí
Služba batch vytvoří několik [proměnné prostředí] [ msdn_env_var] specifické pro víc instancí úlohy na výpočetních uzlů přidělených pro úkoly s více instancemi. Příkazové řádky koordinaci a aplikace může odkazovat tyto proměnné prostředí, jak jde skriptům a programům, které provedou.

Následující proměnné prostředí vytvářejí pomocí služby Batch pro použití úkolů s více instancemi:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Všechny podrobnosti o těchto a dalších Batch proměnné prostředí výpočetních uzlů, včetně jejich obsah a viditelnost, najdete v části [proměnné prostředí výpočetních uzlů][msdn_env_var].

> [!TIP]
> Batch Linux MPI vzorový kód obsahuje příklad jak některé z těchto proměnných prostředí použít. [Koordinace cmd] [ coord_cmd_example] skriptu Bash stáhne běžné aplikace a vstupní soubory ze služby Azure Storage, povolí sdílené složky systému souborů NFS na hlavní uzel a nakonfiguruje do dalších uzlů jako klienti NFS přidělit úkoly s více instancemi.
>
>

## <a name="resource-files"></a>Soubory prostředků
Existují dvě sady souborů prostředků, které je potřeba zvážit úkoly s více instancemi: **společné soubory prostředků** , který *všechny* úkoly stahují (obě primární a dílčí úkoly) a **soubory prostředků** zadaná pro víc instancí úlohy, které *pouze primární* úlohy stahování.

Můžete zadat jednu nebo víc **společné soubory prostředků** v nastavení víc instancí úlohy. Tyto společné soubory prostředků se stahují ze [služby Azure Storage](../storage/common/storage-introduction.md) do každého uzlu **sdíleného adresáře úkolu** primárním a všechny dílčí úkoly. Dostanete sdíleného adresáře úkolu z příkazové řádky koordinaci a aplikace s použitím `AZ_BATCH_TASK_SHARED_DIR` proměnné prostředí. `AZ_BATCH_TASK_SHARED_DIR` Cesta je stejná na každý uzel přidělené úkoly s více instancemi, proto můžete sdílet příkaz jeden koordinaci mezi primárním a všechny dílčí úkoly. Batch "nesdílí" adresáři ve smyslu vzdáleného přístupu, ale můžete ho použít jako připojené nebo sdílení bodu, jak je uvedeno výše v tipu pro proměnné prostředí.

Soubory prostředků, které zadáte pro samotný úkoly s více instancemi se stáhnou do pracovního adresáře úkolu, `AZ_BATCH_TASK_WORKING_DIR`, ve výchozím nastavení. Jak už bylo zmíněno, na rozdíl od obecné soubory prostředků, pouze primární úkol stáhne soubory prostředků zadaná pro samotný úkoly s více instancemi.

> [!IMPORTANT]
> Vždy použít proměnné prostředí `AZ_BATCH_TASK_SHARED_DIR` a `AZ_BATCH_TASK_WORKING_DIR` k odkazování na tyto adresáře na příkazových řádcích. Nepokoušejte se ručně vytvořit cesty.
>
>

## <a name="task-lifetime"></a>Doba života úkolu
Doba života úkolu primární řídí dobu trvání úlohy celý více instancemi. Při ukončení primární všechny dílčí úkoly budou ukončeny. Ukončovací kód primární, je ukončovací kód úkolu a se proto používá k určení úspěchu nebo neúspěchu úkolu pro účely zkuste to znovu.

Pokud selžou i všechny dílčí úkoly, ukončuje s nenulovou návratový kód, například celé více instancemi úloha se nezdaří. Úkoly s více instancemi je pak ukončena a opakovat až do svého limitu opakování.

Při odstranění úkolu víc instancí službou Batch odstranit také primární server a všechny dílčí úkoly. Všechny dílčí úkol adresáře a jejich soubory jsou odstraněny z výpočetních uzlů, stejně jako u standardní úlohy.

[TaskConstraints] [ net_taskconstraints] pro víc instancí úlohy, jako [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime] [ net_taskconstraint_maxwallclock], a [RetentionTime] [ net_taskconstraint_retention] jsou vlastnosti, neuplatňují jak jsou pro standardní úlohy a použít na primárním a všechny dílčí úkoly. Nicméně pokud změníte [RetentionTime] [ net_taskconstraint_retention] vlastnost po přidání úkoly s více instancemi úlohou, tato změna se aplikuje jenom na primární úlohy. Všechny dílčí úkoly pokračovat v používání původní [RetentionTime][net_taskconstraint_retention].

Seznam posledních úkolů výpočetním uzlu odráží id dílčího úkolu, pokud poslední úloha byla součástí úkoly s více instancemi.

## <a name="obtain-information-about-subtasks"></a>Získání informací o dílčí úkoly
Chcete-li získat informace o dílčí úkoly pomocí knihovny Batch .NET, zavolejte [CloudTask.ListSubtasks] [ net_task_listsubtasks] metody. Tato metoda vrátí informace o všech dílčích úkolů a informace o výpočetním uzlu, který spouští úkoly. Z těchto informací můžete určit kořenový adresář každému Dílčímu úkolu, id fondu, jeho aktuální stav, ukončovací kód a další. Tyto informace můžete použít v kombinaci s [PoolOperations.GetNodeFile] [ poolops_getnodefile] metoda získat soubory dílčí úkol. Všimněte si, že tato metoda nevrací informace pro primárního úkolu (id 0).

> [!NOTE]
> Pokud není uvedeno jinak, metody rozhraní Batch .NET pracovat s více instancemi [CloudTask] [ net_task] samotný použít *pouze* na primární úlohy. Například při volání [CloudTask.ListNodeFiles] [ net_task_listnodefiles] metoda v úkolu víc instancí, jsou vráceny pouze soubory primární úloh.
>
>

Následující fragment kódu ukazuje, jak získat informace o dílčí úkol, jakož i vyžádat obsah souboru z uzlů, na kterých spuštěn.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Ukázka kódu
[MultiInstanceTasks] [ github_mpi] ukázku kódu na Githubu ukazuje, jak používat úkoly s více instancemi ke spouštění [MS MPI] [ msmpi_msdn] aplikace na Výpočetní uzly služby batch. Postupujte podle kroků v [přípravy](#preparation) a [provádění](#execution) ke spuštění ukázky.

### <a name="preparation"></a>Příprava
1. První dva kroky v [jak kompilovat a spouštět jednoduchý program MS MPI][msmpi_howto]. Tím splňuje požadavky pro následující krok.
2. Sestavení *vydání* verzi [MPIHelloWorld] [ helloworld_proj] ukázkový program MPI. Toto je program, který se spustí na výpočetních uzlech ve úkoly s více instancemi.
3. Vytvořit soubor zip obsahující `MPIHelloWorld.exe` (které jste vytvořili v kroku 2) a `MSMpiSetup.exe` (který jste si stáhli v kroku 1). Tento soubor zip nahrajete jako balíček aplikace v dalším kroku.
4. Použití [webu Azure portal] [ portal] vytvoření dávky [aplikace](batch-application-packages.md) nazývá "MPIHelloWorld" a zadejte soubor zip, který jste vytvořili v předchozím kroku jako verzi "1.0" balíček aplikace. Zobrazit [odesílat a spravovat aplikace](batch-application-packages.md#upload-and-manage-applications) Další informace.

> [!TIP]
> Sestavení *vydání* verzi `MPIHelloWorld.exe` tak, že není nutné zahrnout všechny další závislosti (například `msvcp140d.dll` nebo `vcruntime140d.dll`) v balíčku aplikace.
>
>

### <a name="execution"></a>Spouštěcí
1. Stáhněte si [azure-batch-samples] [ github_samples_zip] z Githubu.
2. Otevřete MultiInstanceTasks **řešení** v sadě Visual Studio 2017. `MultiInstanceTasks.sln` Řešení soubor se nachází v:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Zadejte přihlašovací údaje účtu Batch a Storage v `AccountSettings.settings` v **Microsoft.Azure.Batch.Samples.Common** projektu.
4. **Sestavte a spusťte** MultiInstanceTasks řešení k provedení rozhraní MPI ukázkovou aplikaci na výpočetní uzlech ve fondu služby Batch.
5. *Volitelné*: Použití [webu Azure portal] [ portal] nebo [Batch Explorer] [ batch_labs] prozkoumat ukázkový fond, úlohy a úkolů ("MultiInstanceSamplePool"," MultiInstanceSampleJob","MultiInstanceSampleTask") před odstraněním zdroje.

> [!TIP]
> Můžete si stáhnout [Visual Studio Community] [ visual_studio] zdarma, pokud nemáte Visual Studio.
>
>

Výstup z `MultiInstanceTasks.exe` je podobný následujícímu:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Další postup
* Tento článek popisuje blogu Microsoft HPC a Batch tým Azure [MPI podporu pro Linux v Azure Batch][blog_mpi_linux]a obsahuje informace o používání [OpenFOAM] [ openfoam] pomocí služby Batch. Můžete najít ukázky kódu Pythonu pro [OpenFOAM příkladů uvedených na Githubu][github_mpi].
* Zjistěte, jak [vytvořit fondy výpočetních uzlů s Linuxem](batch-linux-nodes.md) pro použití ve vašich řešeních pro Azure Batch MPI.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Přehled více instancemi"
