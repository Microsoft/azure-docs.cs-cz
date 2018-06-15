---
title: Použití úkolů s více instancemi ke spouštění aplikací MPI - Azure Batch | Microsoft Docs
description: Zjistěte, jak ke spouštění aplikací rozhraní MPI (Message Passing) pomocí typu úlohy s více instancemi v Azure Batch.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 5/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0fb5ea21c6403369cbcb60df58c0f70a57a61d4e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30160752"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Pomocí úkolů s více instancemi ke spouštění aplikací rozhraní MPI (Message Passing) ve službě Batch

Úkoly s více instancemi umožňují spustit úlohu Azure Batch na několika výpočetních uzlech současně. Tyto úlohy povolit scénáře jako aplikací rozhraní MPI (Message Passing) v dávce s vysokým výkonem. V tomto článku, zjistíte, jak provést úkoly s více instancemi pomocí [Batch .NET] [ api_net] knihovny.

> [!NOTE]
> Příklady v tomto článku se zaměřují na Batch .NET, MS-MPI, a Windows výpočetní uzly, zde popsané koncepty úkolů s více instancemi platí pro další platformy a technologie (Python a Intel MPI na uzlech Linux, např.).
>
>

## <a name="multi-instance-task-overview"></a>Přehled úloh s více instancemi
Ve službě Batch, je obvykle každý úkol spustit na jednom výpočetním uzlu – odeslat více úkolů do úlohy, a služba Batch plánuje každý úkol pro spuštění na uzlu. Ale nakonfigurováním úkolu **s více instancemi nastavení**, řekněte dávky na místo toho vytvořte jednu primární úlohu a několik dílčích úkolů, které jsou poté provedeny ve více uzlech.

![Přehled úloh s více instancemi][1]

Při odesílání úlohy s více instancemi nastavení do úlohy, Batch provede několik kroků jedinečný úkoly s více instancemi:

1. Služba Batch vytvoří jednu **primární** a několik **dílčí úkoly** na základě nastavení s více instancemi. Celkový počet úloh (primární plus všechny dílčí úkoly) odpovídá počtu **instance** (výpočetní uzly) určíte v nastavení s více instancemi.
2. Batch označí jeden výpočetních uzlů, jako **hlavní**a plány primární úlohu provést na hlavním serveru. Naplánuje dílčí úkoly pro spuštění ve zbývající části výpočetních uzlů přidělených úkol s více instancemi, jeden dílčí úlohy na uzlu.
3. Primární a všechny dílčí úkoly stahovat **společné soubory prostředků** určíte v nastavení s více instancemi.
4. Po běžných prostředků soubory byly staženy, primární a dílčí úkoly spouštět **koordinaci příkaz** určíte v nastavení s více instancemi. Příkaz koordinaci se obvykle používá k přípravě uzlů pro provádění úlohy. To může zahrnovat spouštění služby na pozadí (například [Microsoft MPI][msmpi_msdn]na `smpd.exe`) a ověření, že uzly jsou připravené ke zpracování zpráv mezi uzly.
5. Primární úloh provede **příkaz aplikace** na hlavní uzel *po* příkaz koordinaci byla úspěšně dokončena, tak, že primární a všechny dílčí úkoly. Příkaz aplikace je příkazový řádek samotný úkol s více instancemi a je proveden pouze primární úlohou. V [MS-MPI][msmpi_msdn]-řešení, to je, kde můžete spuštění vaší aplikace s povolenými MPI pomocí `mpiexec.exe`.

> [!NOTE]
> Když je funkčně distinct, "s více instancemi úloha" není jedinečný úloh typu like [StartTask] [ net_starttask] nebo [JobPreparationTask] [ net_jobprep]. Úkol s více instancemi je jednoduše standardní úlohy Batch ([CloudTask] [ net_task] v Batch .NET) byla nakonfigurována nastavení s více instancemi. V tomto článku, označujeme jako **úkol s více instancemi**.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Požadavky pro úkoly s více instancemi
Úkoly s více instancemi vyžadují fond s **komunikaci mezi uzly povolena**a s **provedení souběžné úlohy zakázáno**. Chcete-li zakázat provádění souběžných úkolů, nastavte [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool#Microsoft_Azure_Batch_CloudPool_MaxTasksPerComputeNode) vlastnost na hodnotu 1.

> [!NOTE]
> Batch [omezení](batch-quota-limit.md#other-limits) velikost fondu, který má komunikaci mezi uzly povolena.


Tento fragment kódu ukazuje, jak vytvořit fond pro úkoly s více instancemi pomocí knihovny Batch .NET.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

> [!NOTE]
> Pokud se pokusíte spustit úlohu s více instancemi ve fondu s komunikací internodium zakázána, nebo s *maxTasksPerNode* hodnotu větší než 1, naplánován nikdy – zůstane po neomezenou dobu ve stavu "aktivní". 
>
> Úkoly s více instancemi lze spustit pouze v uzlů ve fondech vytvořený po 14. prosince 2015.
>
>

### <a name="use-a-starttask-to-install-mpi"></a>StartTask použít k instalaci MPI
Ke spouštění aplikací MPI s více instancemi úloh, musíte nejprve nainstalovat implementace MPI (MS-MPI nebo MPI Intel, např.) na výpočetních uzlech ve fondu. Toto je vhodná doba na použití [StartTask][net_starttask], který provede vždy, když se uzel připojí fondu nebo je restartovat. Tento fragment kódu vytvoří StartTask, která určuje instalační balíček MS-MPI jako [souboru prostředků][net_resourcefile]. Spouštěcí úkol příkazový řádek se spustí po souboru prostředků se stáhne do uzlu. V takovém případě příkazový řádek provede bezobslužnou instalaci MS MPI.

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

### <a name="remote-direct-memory-access-rdma"></a>Vzdálený přímý přístup do paměti (vzdáleného počítače RDMA)
Pokud vyberete [RDMA podporovat velikost](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) například A9 pro výpočetní uzly ve fondu Batch, MPI aplikace mohou využít výhod Azure vysoce výkonné, nízkou latencí paměti vzdálený přímý přístup do (počítače RDMA) sítě.

Vyhledejte velikosti definované jako "Podporující RDMA" v těchto článcích:

* **CloudServiceConfiguration** fondy

  * [Velikosti pro cloudové služby](../cloud-services/cloud-services-sizes-specs.md) (jenom Windows)
* **VirtualMachineConfiguration** fondy

  * [Velikosti virtuálních počítačů v Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Velikosti virtuálních počítačů v Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> Abyste mohli využívat funkce RDMA [Linuxových výpočetních uzlů](batch-linux-nodes.md), je nutné použít **Intel MPI** na uzlech. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Vytvoření úlohy s více instancemi pomocí rozhraní Batch .NET
Teď, když jsme si zahrnutých fondu požadavky a instalace balíčku MPI, vytvoříme úlohu s více instancemi. V tento fragment kódu, vytvoříme standard [CloudTask][net_task], nakonfigurujte její [MultiInstanceSettings] [ net_multiinstance_prop] vlastnost. Jak už bylo zmíněno dříve, není úkol s více instancemi typu distinct úloh, ale standardní úlohy Batch nakonfigurované s nastavením s více instancemi.

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
Když vytvoříte nastavení s více instancemi pro úlohu, je třeba zadat počet výpočetních uzlů, které jsou při provádění úlohy. Při odesílání úloh do úlohy, služba Batch vytvoří jednu **primární** úlohy a dostatek **dílčí úkoly** společně odpovídající počet uzlů, které jste zadali.

Tyto úlohy jsou přiřazeny id celé číslo v rozsahu od 0 do *numberOfInstances* - 1. Úlohu s id 0 je primární úlohy a všechny ostatní ID jsou dílčí úkoly. Například pokud vytvoříte následující nastavení s více instancemi pro úlohu, primární úlohy má id 0 a dílčí úkoly by měla mít ID 1 až 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Hlavní uzel
Při odesílání úlohy s více instancemi služby Batch označí jeden výpočetní uzly jako "hlavní" uzel a primární úloh spustit na hlavní uzel. Dílčí úkoly jsou naplánovány pro spuštění ve zbývající části uzlů přidělených pro úlohu s více instancemi.

## <a name="coordination-command"></a>Příkaz spolupráce
**Koordinaci příkaz** je provedený obě primární a dílčích úkolů.

Vyvolání příkazu koordinaci blokuje – Batch příkaz aplikace nespustí, dokud úspěšně vrátila příkaz spolupráce pro všechny dílčí úkoly. Příkaz koordinaci by proto spustit žádné služby, požadované pozadí, ověřte, zda je připravený k použití a poté ukončete. Například tento příkaz koordinaci pro řešení prostřednictvím MS-MPI verze 7 spustí službu SMPD na uzlu, pak bude ukončen:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Všimněte si použití `start` v tomto příkazu spolupráce. Toto je nutné kvůli `smpd.exe` aplikace nevrátí okamžitě po spuštění. Bez použití [spustit] [ cmd_start] příkaz, tento příkaz koordinaci by vrátit a by proto blokovat příkaz aplikace spuštění.

## <a name="application-command"></a>Příkaz aplikace
Po primární úlohy a všechny dílčí úkoly dokončení provádění příkazu koordinaci, úkolů s více instancemi příkazový řádek se spustí primární úlohou *pouze*. To říkáme **příkaz aplikace** ho odlišuje od příkaz spolupráce.

U aplikací, MS-MPI, použijte příkaz aplikaci k provedení vaší aplikace s povolenými MPI s `mpiexec.exe`. Zde je ukázka, příkaz aplikace pro řešení pomocí MS-MPI verze 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Protože MS-MPI `mpiexec.exe` používá `CCP_NODES` proměnné ve výchozím nastavení (najdete v části [proměnné prostředí](#environment-variables)) příkazového řádku aplikace příkladu výše vyloučí ho.
>
>

## <a name="environment-variables"></a>Proměnné prostředí
Batch vytvoří několik [proměnné prostředí] [ msdn_env_var] specifické pro úkoly na výpočetních uzlů přidělených úkolů s více instancemi s více instancemi. Příkazové řádky koordinace a aplikace můžete odkazovat těchto proměnných prostředí, jak můžete skripty a programů, které se provést.

Následující proměnné prostředí jsou vytvořeny pomocí služby Batch pro použití s více instancemi úlohy:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Úplné podrobnosti o těchto a dalších dávky výpočetní uzel proměnné prostředí, včetně jejich obsah a viditelnost, najdete v části [výpočetní uzel proměnné prostředí][msdn_env_var].

> [!TIP]
> Ukázka kódu Batch Linux MPI obsahuje příklad některé z těchto proměnných prostředí použití. [Koordinaci cmd] [ coord_cmd_example] Bash skript stáhne běžné aplikace a vstupní soubory ze služby Azure Storage, umožňuje sdílené složky systému souborů NFS v hlavní uzel a nakonfiguruje ostatní uzly jako klienti NFS přidělit úkol s více instancemi.
>
>

## <a name="resource-files"></a>Soubory prostředků
Existují dvě sady souborů prostředků vzít v úvahu pro úkoly s více instancemi: **společné soubory prostředků** , *všechny* úkoly stahují (obě primární a dílčí úkoly) a **soubory prostředků** zadaná pro více instancemi úkolů sebe, což *pouze primární* úkolů stahování.

Můžete určit jeden nebo více **společné soubory prostředků** v nastavení s více instancemi pro úlohu. Tyto společné soubory prostředků se stahují z [Azure Storage](../storage/common/storage-introduction.md) do každého uzlu **sdíleného adresáře úkolu** primární a všechny dílčí úkoly. Do sdíleného adresáře úkolu můžete přístup z aplikace a koordinaci příkazové řádky pomocí `AZ_BATCH_TASK_SHARED_DIR` proměnné prostředí. `AZ_BATCH_TASK_SHARED_DIR` Cesta je stejné pro všechny uzly přidělit úkol s více instancemi, proto můžete sdílet jeden koordinaci příkaz mezi primárním serverem a všechny dílčí úkoly. Batch "nesdílí" v adresáři v tom smyslu vzdáleného přístupu, ale můžete ho použít jako přípojný nebo sdílení bodu, jak je uvedeno výše v tip na proměnné prostředí.

Soubory prostředků, které zadáte pro samotný úkol s více instancemi se stáhnou do pracovního adresáře úkolu, `AZ_BATCH_TASK_WORKING_DIR`, ve výchozím nastavení. Jak je uvedeno, na rozdíl od běžných soubory prostředků, pouze primární úkol stáhne soubory prostředků, které jsou zadané pro úlohu s více instancemi sám sebe.

> [!IMPORTANT]
> Vždy použít proměnné prostředí `AZ_BATCH_TASK_SHARED_DIR` a `AZ_BATCH_TASK_WORKING_DIR` odkazovat na tyto adresáře v příkazové řádky. Nepokoušejte se ručně vytvořit cesty.
>
>

## <a name="task-lifetime"></a>Doba platnosti úloh
Doba platnosti primární úlohy řídí životnost úlohu celý s více instancemi. Při ukončení primární, budou ukončeny všechny jeho dílčí úkoly. Ukončovací kód primární kód ukončení úlohy a proto slouží k určení úspěch nebo selhání úlohy pro účely opakování.

Pokud žádný z dílčích úkolů selže, ukončení s návratovým kódem nulová, například celou s více instancemi úloha nezdaří. Úkol s více instancemi je pak byla ukončena a opakovat, až do limitu opakování.

Při odstranění úlohu s více instancemi primárním serverem a všechny dílčí úkoly jsou odstraněny také službou Batch. Všechny dílčí úloha adresáře a jejich soubory se odstraní z výpočetních uzlů, stejně jako u standardní úlohy.

[TaskConstraints] [ net_taskconstraints] pro úlohu s více instancemi, jako [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime] [ net_taskconstraint_maxwallclock], a [RetentionTime] [ net_taskconstraint_retention] vlastnosti, se neuplatňují, jak jsou pro standardní úlohy a použít na primárním a všechny dílčí úkoly. Nicméně pokud změníte [RetentionTime] [ net_taskconstraint_retention] vlastnost po přidání úkolů s více instancemi úlohou, tato změna se aplikuje jenom na primární úlohy. Všechny jeho dílčí úkoly nadále používat původní [RetentionTime][net_taskconstraint_retention].

Seznam posledních úkolů výpočetním uzlu odráží id dílčí úlohy, pokud poslední úloha byla součástí úlohu s více instancemi.

## <a name="obtain-information-about-subtasks"></a>Získání informací o dílčí úkoly
Chcete-li získat informace o dílčí úkoly pomocí knihovny Batch .NET, zavolejte [CloudTask.ListSubtasks] [ net_task_listsubtasks] metoda. Tato metoda vrátí informace o všechny dílčí úkoly a informace o výpočetním uzlu, která spouští úkoly. Z těchto informací můžete určit každý dílčí úlohy kořenový adresář, id fondu, jeho aktuální stav, ukončovacího kódu a další. Tyto informace můžete použít v kombinaci s [PoolOperations.GetNodeFile] [ poolops_getnodefile] metoda získat soubory dílčí úlohy. Všimněte si, že tato metoda nevrátí informace pro primární úlohu (id 0).

> [!NOTE]
> Pokud není uvedeno jinak, metody rozhraní Batch .NET, které pracují na s více instancemi [CloudTask] [ net_task] samotné použít *pouze* primární úlohy. Například při volání [CloudTask.ListNodeFiles] [ net_task_listnodefiles] metodu úlohu s více instancemi, jsou vráceny pouze soubory primární úkolu.
>
>

Následující fragment kódu ukazuje, jak získat informace o dílčí úlohy a také žádosti o obsah souboru z uzlů, ve kterých provést.

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
[MultiInstanceTasks] [ github_mpi] ukázka kódu na Githubu ukazuje, jak se použije ke spuštění úlohy s více instancemi [MS-MPI] [ msmpi_msdn] aplikace na Batch výpočetních uzlů. Postupujte podle kroků v [přípravy](#preparation) a [provádění](#execution) ke spuštění ukázky.

### <a name="preparation"></a>Příprava
1. První dva postupujte podle [postup zkompilování a spuštění jednoduchý program MS-MPI][msmpi_howto]. To splňuje předpoklady pro následující krok.
2. Sestavení *verze* verzi [MPIHelloWorld] [ helloworld_proj] ukázka MPI programu. Toto je program, který se spustí na výpočetních uzlech úlohou s více instancemi.
3. Vytvořte soubor zip obsahující `MPIHelloWorld.exe` (které je vytvořené v kroku 2) a `MSMpiSetup.exe` (který jste stáhli krok 1). Tento soubor zip budete nahrát jako balíček aplikace v dalším kroku.
4. Použití [portál Azure] [ portal] k vytvoření dávky [aplikace](batch-application-packages.md) nazývá "MPIHelloWorld" a zadejte soubor zip, který jste vytvořili v předchozím kroku jako verze "1.0" balíček aplikace. V tématu [odesílat a spravovat aplikace](batch-application-packages.md#upload-and-manage-applications) Další informace.

> [!TIP]
> Sestavení *verze* verzi `MPIHelloWorld.exe` tak, že nemáte žádné další závislosti patří (například `msvcp140d.dll` nebo `vcruntime140d.dll`) v balíčku aplikace.
>
>

### <a name="execution"></a>Provedení
1. Stažení [azure-batch-samples] [ github_samples_zip] z Githubu.
2. Otevřete MultiInstanceTasks **řešení** v sadě Visual Studio 2015 nebo novější. `MultiInstanceTasks.sln` Řešení soubor je umístěný ve:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Zadejte přihlašovací údaje účtu Batch a úložiště v `AccountSettings.settings` v **Microsoft.Azure.Batch.Samples.Common** projektu.
4. **Sestavení a spuštění** řešení MultiInstanceTasks provést MPI ukázkovou aplikaci na výpočetní uzlech ve fondu služby Batch.
5. *Volitelné*: použití [portál Azure] [ portal] nebo [BatchLabs] [ batch_labs] prozkoumat ukázkové fondu, úlohy a úkolů (" MultiInstanceSamplePool","MultiInstanceSampleJob","MultiInstanceSampleTask") před odstraněním prostředky.

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
* Popisuje Microsoft HPC & Azure Batch Team blog [MPI podporu pro systém Linux na Azure Batch][blog_mpi_linux]a obsahuje informace o používání [OpenFOAM] [ openfoam] pomocí služby Batch. Ukázky kódu Pythonu pro můžete najít [příklad OpenFOAM na Githubu][github_mpi].
* Zjistěte, jak [vytvořit fondy Linuxových výpočetních uzlů](batch-linux-nodes.md) pro použití v Azure Batch MPI řešení.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchLabs/
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

[1]: ./media/batch-mpi/batch_mpi_01.png "Přehled s více instancemi"
