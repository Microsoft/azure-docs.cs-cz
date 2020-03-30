---
title: Použití úloh s více instancemi ke spouštění aplikací MPI – Azure Batch | Dokumenty společnosti Microsoft
description: Zjistěte, jak spustit aplikace rozhraní MPI (Message Passing Interface) pomocí typu úlohy více instancí v Azure Batch.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 03/13/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1896fea3c401299b4f77235ab3c02d85708b7041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023663"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Použití úloh s více instancemi ke spuštění aplikací MPI rozhraní mpi (Message Passing Interface) v aplikaci Batch

Úlohy s více instancemi umožňují spouštět úlohu Azure Batch na více výpočetních uzlech současně. Tyto úlohy umožňují scénáře vysoce výkonné výpočetní techniky, jako je message passing interface (MPI) aplikace v batch. V tomto článku se dozvíte, jak provádět úlohy s více instancemi pomocí knihovny [Batch .NET.][api_net]

> [!NOTE]
> Zatímco příklady v tomto článku se zaměřují na výpočetní uzly Batch .NET, MS-MPI a Windows, koncepty úloh s více instancemi, které jsou zde popsány, se vztahují na jiné platformy a technologie (například Python a Intel MPI v uzlech Linux).
>
>

## <a name="multi-instance-task-overview"></a>Přehled úloh s více instancemi
V batch, každý úkol je obvykle spuštěn na jednom výpočetním uzlu – odešlete více úkolů do úlohy a dávkové služby naplánuje každý úkol pro spuštění na uzlu. Však konfigurací **úlohy více instancí nastavení**, můžete říct Batch místo toho vytvořit jeden primární úkol a několik dílčích úkolů, které jsou pak provedeny na více uzlů.

![Přehled úloh s více instancemi][1]

Při odeslání úlohy s nastavením více instancí do úlohy provede Batch několik kroků, které jsou jedinečné pro úlohy s více instancemi:

1. Služba Batch vytvoří jeden **primární** a několik **dílčích úkolů** na základě nastavení více instancí. Celkový počet úloh (primární plus všechny dílčí úkoly) odpovídá počtu **instancí** (výpočetních uzlů), které zadáte v nastavení více instancí.
2. Dávka označí jeden z výpočetních uzlů jako **hlavní**a naplánuje provedení primární úlohy na předloze. Naplánuje provádění dílčích úkolů ve zbývajících výpočetních uzlech přidělených úlohě s více instancemi, což je jeden dílčí úkol na uzel.
3. Primární a všechny dílčí úkoly stáhnou všechny **běžné soubory prostředků,** které zadáte v nastavení více instancí.
4. Po stažení běžných souborů prostředků spouštějí primární a dílčí úkoly **příkaz koordinace,** který zadáte v nastavení více instancí. Příkaz koordinace se obvykle používá k přípravě uzlů pro provádění úlohy. To může zahrnovat spuštění služeb na pozadí `smpd.exe`(například [Microsoft MPI][msmpi_msdn]) a ověření, že uzly jsou připraveny ke zpracování zpráv mezi uzly.
5. Primární úloha provede **příkaz aplikace** v hlavním uzlu *po* úspěšném dokončení příkazu koordinace primárním a všemi dílčími úkoly. Příkaz aplikace je příkazový řádek samotné úlohy s více instancemi a je prováděn pouze primární úlohou. V řešení založeném na [MS-MPI][msmpi_msdn]se toto je stavu, kde spouštíte aplikaci s podporou MPI pomocí `mpiexec.exe`aplikace .

> [!NOTE]
> Ačkoli je funkčně odlišný, "úloha více instancí" není jedinečnýtyp úkolu, jako je [StartTask][net_starttask] nebo [JobPreparationTask][net_jobprep]. Úloha více instancí je jednoduše standardní dávková úloha[(CloudTask][net_task] in Batch .NET), jejíž nastavení více instancí byla nakonfigurována. V tomto článku se odkazuje na to jako **úloha více instancí**.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Požadavky na úlohy s více instancemi
Úlohy s více instancemi vyžadují fond s **povolenou meziuzlovou komunikací**a **se zakázaným prováděním souběžných úloh**. Chcete-li zakázat souběžné provádění úloh, nastavte vlastnost [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool) na 1.

> [!NOTE]
> Dávka [omezuje](batch-quota-limit.md#pool-size-limits) velikost fondu, který má povolenou komunikaci mezi uzlami.


Tento fragment kódu ukazuje, jak vytvořit fond pro úlohy s více instancemi pomocí knihovny Batch .NET.

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
> Pokud se pokusíte spustit úlohu s více instancemi ve fondu s zakázánou komunikaci internode nebo s hodnotou *maxTasksPerNode* větší než 1, úloha je nikdy naplánována-- zůstane neomezeně dlouho ve stavu "aktivní". 


### <a name="use-a-starttask-to-install-mpi"></a>Instalace technologie MPI pomocí funkce StartTask
Chcete-li spustit aplikace MPI s úlohou s více instancemi, musíte nejprve nainstalovat implementaci MPI (ms-MPI nebo Intel MPI, například) na výpočetní uzly ve fondu. Toto je vhodná doba k použití [StartTask][net_starttask], který se spustí vždy, když uzel připojí do fondu nebo je restartován. Tento fragment kódu vytvoří starttask, který určuje instalační balíček MS-MPI jako [soubor prostředků][net_resourcefile]. Příkazový řádek počáteční úlohy je proveden po stažení souboru prostředků do uzlu. V tomto případě příkazový řádek provádí bezobslužnou instalaci MS-MPI.

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
Když zvolíte [velikost podporující RDMA,](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) jako je A9 pro výpočetní uzly ve vašem fondu batch, vaše aplikace MPI může využít vysoce výkonné sítě vzdáleného přímého přístupu do paměti Azure s nízkou latencí (RDMA).

Vyhledejte velikosti určené jako "RDMA schopné" v následujících článcích:

* **CloudServiceKonfigurace** fondy

  * [Velikosti cloudových služeb](../cloud-services/cloud-services-sizes-specs.md) (pouze Windows)
* **Fondy virtualmachinekonfigurace**

  * [Velikosti pro virtuální počítače v Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Velikosti pro virtuální počítače v Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> Chcete-li využít rdma na [linuxových výpočetních uzlech](batch-linux-nodes.md), musíte použít **Intel MPI** na uzlech. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Vytvoření úlohy s více instancemi s dávkou .NET
Teď, když jsme pokryli požadavky fondu a instalaci balíčku MPI, vytvořme úlohu s více instancemi. V tomto úryvku vytvoříme standardní [CloudTask][net_task]a pak nakonfigurujeme jeho vlastnost [MultiInstanceSettings.][net_multiinstance_prop] Jak již bylo zmíněno dříve, úloha s více instancemi není odlišným typem úlohy, ale standardní úlohou Batch nakonfigurovanou s nastavením více instancí.

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

## <a name="primary-task-and-subtasks"></a>Primární úkol a dílčí úkoly
Při vytváření nastavení více instancí pro úlohu zadáte počet výpočetních uzlů, které mají úlohu provést. Při odeslání úkolu do úlohy vytvoří služba Dávka jeden **primární** úkol a dostatek **dílčích úkolů,** které společně odpovídají zadanému počtu uzlů.

Těmto úkolům je přiřazeno celé id v rozsahu 0 až *numberOfInstances* - 1. Úkol s id 0 je primární úkol a všechny ostatní ID jsou dílčí úkoly. Pokud například vytvoříte následující nastavení více instancí pro úlohu, primární úkol bude mít id 0 a dílčí úkoly by měly id 1 až 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Hlavní uzel
Při odeslání úlohy s více instancemi služba Batch označí jeden z výpočetních uzlů jako hlavní uzel a naplánuje spuštění primárního úkolu v hlavním uzlu. Dílčí úkoly jsou naplánovány na provedení na zbývajících uzlech přidělených úlohě s více instancemi.

## <a name="coordination-command"></a>Příkaz Koordinace
**Příkaz koordinace** je proveden primárním i dílčím úkolem.

Vyvolání příkazu koordinace blokuje--Batch neprovede příkaz aplikace, dokud se příkaz koordinace úspěšně nevrátí pro všechny dílčí úkoly. Příkaz koordinace by proto měl spustit všechny požadované služby na pozadí, ověřit, zda jsou připraveny k použití, a poté ukončit. Například tento příkaz koordinace pro řešení pomocí MS-MPI verze 7 spustí službu SMPD na uzlu a ukončí se:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Všimněte si `start` použití v tomto příkazu koordinace. To je nutné, `smpd.exe` protože aplikace nevrátí ihned po spuštění. Bez použití příkazu [start][cmd_start] by se tento příkaz koordinace nevrátil, a proto by zablokoval spuštění příkazu aplikace.

## <a name="application-command"></a>Příkaz Aplikace
Jakmile primární úloha a všechny dílčí úkoly dokončí provádění příkazu koordinace, je příkazový řádek úlohy s více instancemi proveden *pouze*primární úlohou . Nazýváme to **příkaz aplikace,** abychom ho odlišili od příkazu koordinace.

U aplikací MS-MPI použijte příkaz aplikace ke spuštění aplikace `mpiexec.exe`s podporou mpi s aplikací . Zde je například příkaz aplikace pro řešení používající MS-MPI verze 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Protože MS-MPI `mpiexec.exe` používá `CCP_NODES` proměnnou ve výchozím nastavení (viz [Proměnné prostředí)](#environment-variables)hlavní příkazový řádek aplikace výše ji vylučuje.
>
>

## <a name="environment-variables"></a>Proměnné prostředí
Batch vytvoří několik [proměnných prostředí specifických][msdn_env_var] pro úlohy s více instancemi na výpočetních uzlech přidělených k úloze s více instancemi. Vaše příkazové řádky koordinace a aplikace mohou odkazovat na tyto proměnné prostředí, stejně jako skripty a programy, které spouštějí.

Následující proměnné prostředí jsou vytvořeny službou Batch pro použití úlohami s více instancemi:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Podrobné informace o těchto a dalších proměnných prostředí dávkového výpočetního uzlu, včetně jejich obsahu a viditelnosti, naleznete v [tématu Proměnné prostředí výpočetního uzlu][msdn_env_var].

> [!TIP]
> Ukázka kódu MPI dávky Linuxu obsahuje příklad použití několika těchto proměnných prostředí. Skript [Bash s kohotovací cmd][coord_cmd_example] stahuje běžné soubory aplikací a vstupů z Azure Storage, umožňuje sdílenou složku síťového systému souborů (NFS) v hlavním uzlu a konfiguruje ostatní uzly přidělené úlohě více instancí jako klienty systému souborů NFS.
>
>

## <a name="resource-files"></a>Soubory prostředků
Existují dvě sady souborů prostředků, které je třeba vzít v úvahu pro úlohy s více instancemi: **běžné soubory prostředků,** které jsou staženy *všechny* úkoly (primární i dílčí úkoly), a **soubory prostředků** určené pro samotnou úlohu s více instancemi, které se stahují *pouze primární* úkol.

V nastavení více instancí pro úlohu můžete zadat jeden nebo více **běžných souborů prostředků.** Tyto běžné soubory prostředků se stahují z [Azure Storage](../storage/common/storage-introduction.md) do **sdíleného adresáře úloh** každého uzlu primárními a všemi dílčími úkoly. Ke sdílenému adresáři úlohy můžete přistupovat `AZ_BATCH_TASK_SHARED_DIR` z příkazových řádů aplikace a koordinace pomocí proměnné prostředí. Cesta `AZ_BATCH_TASK_SHARED_DIR` je identická na každém uzlu přiděleném úlohě s více instancemi, takže můžete sdílet jeden příkaz koordinace mezi primárními a všemi dílčími úkoly. Batch "nesdílí" adresář ve smyslu vzdáleného přístupu, ale můžete jej použít jako bod připojení nebo sdílení, jak je uvedeno výše v tipu na proměnné prostředí.

Soubory prostředků, které zadáte pro samotnou úlohu s více instancemi, `AZ_BATCH_TASK_WORKING_DIR`se ve výchozím nastavení stáhnou do pracovního adresáře úlohy , ve výchozím nastavení. Jak již bylo zmíněno, na rozdíl od běžných souborů prostředků pouze primární úkol stahuje soubory prostředků určené pro samotnou úlohu s více instancemi.

> [!IMPORTANT]
> Vždy používejte proměnné `AZ_BATCH_TASK_SHARED_DIR` prostředí `AZ_BATCH_TASK_WORKING_DIR` a odkazovat na tyto adresáře v příkazových řádcích. Nepokoušejte se vytvořit cesty ručně.
>
>

## <a name="task-lifetime"></a>Životnost úlohy
Životnost primárního úkolu řídí životnost celé úlohy s více instancemi. Při primární ukončení jsou ukončeny všechny dílčí úkoly. Ukončovací kód primární ho je ukončovací kód úlohy a proto se používá k určení úspěchu nebo neúspěchu úlohy pro účely opakování.

Pokud některý z dílčích úkolů nezdaří, ukončení s nenulový návratový kód, například celý úloha více instancí nezdaří. Úloha s více instancemi je pak ukončena a opakována až do limitu opakování.

Když odstraníte úlohu s více instancemi, primární a všechny dílčí úkoly jsou také odstraněny službou Batch. Všechny podúkolové adresáře a jejich soubory jsou odstraněny z výpočetních uzlů, stejně jako u standardní úlohy.

[TaskConstraints][net_taskconstraints] pro úlohu s více instancemi, jako jsou vlastnosti [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock]a [RetentionTime,][net_taskconstraint_retention] jsou dodrženy stejně jako pro standardní úlohu a platí pro primární a všechny dílčí úkoly. Pokud však změníte vlastnost [RetentionTime][net_taskconstraint_retention] po přidání úlohy více instancí do úlohy, bude tato změna použita pouze pro primární úlohu. Všechny dílčí úkoly nadále používat původní [RetentionTime][net_taskconstraint_retention].

Seznam nedávných úkolů výpočetního uzlu odráží id dílčího úkolu, pokud byl nedávný úkol součástí úlohy s více instancemi.

## <a name="obtain-information-about-subtasks"></a>Získání informací o dílčích úkolech
Chcete-li získat informace o dílčích úkolech pomocí knihovny Batch .NET, zavolejte metodu [CloudTask.ListSubtasks.][net_task_listsubtasks] Tato metoda vrátí informace o všech dílčích úkolech a informace o výpočetním uzlu, který úkoly provedl. Na těchto informacích můžete určit kořenový adresář každého dílčího úkolu, ID fondu, jeho aktuální stav, ukončovací kód a další. Tyto informace můžete použít v kombinaci s [PoolOperations.GetNodeFile][poolops_getnodefile] metoda získat soubory dílčího úkolu. Všimněte si, že tato metoda nevrací informace pro primární úkol (id 0).

> [!NOTE]
> Není-li uvedeno jinak, batch .NET metody, které pracují na multi-instance [CloudTask][net_task] sám použít *pouze* pro primární úlohu. Například při volání [CloudTask.ListNodeFiles][net_task_listnodefiles] metoda na úlohu s více instancemi, jsou vráceny pouze soubory primární úlohy.
>
>

Následující fragment kódu ukazuje, jak získat informace o dílčích úkolech, stejně jako obsah souboru požadavku z uzlů, na kterých byly provedeny.

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
Ukázka kódu [MultiInstanceTasks][github_mpi] na GitHubu ukazuje, jak použít úlohu s více instancemi ke spuštění aplikace [MS-MPI][msmpi_msdn] v dávkových výpočetních uzlech. Postupujte podle kroků v [příprava](#preparation) a [spuštění](#execution) spustit ukázku.

### <a name="preparation"></a>Příprava
1. Postupujte podle prvních dvou kroků v [části Kompilace a spuštění jednoduchého programu MS-MPI][msmpi_howto]. To splňuje předpoklady pro následující krok.
2. Vytvořte *verzi* ukázkového programu [MPIMPI.][helloworld_proj] Jedná se o program, který bude spuštěn na výpočetních uzlech úlohou s více instancemi.
3. Vytvořte soubor zip `MPIHelloWorld.exe` obsahující (který jste `MSMpiSetup.exe` vytvořili krok 2) a (který jste stáhli krok 1). Tento soubor zip nahrajete jako balíček aplikace v dalším kroku.
4. Pomocí [portálu Azure][portal] vytvořte dávkovou [aplikaci](batch-application-packages.md) s názvem "MPIHelloWorld" a zadejte soubor ZIP, který jste vytvořili v předchozím kroku jako verzi "1.0" balíčku aplikace. Další informace najdete [v tématu Nahrávání a správa aplikací.](batch-application-packages.md#upload-and-manage-applications)

> [!TIP]
> Vytvořte *verzi* `MPIHelloWorld.exe` verze aplikace, abyste nemuseli do balíčku aplikace zahrnout `msvcp140d.dll` `vcruntime140d.dll`žádné další závislosti (například nebo).
>
>

### <a name="execution"></a>Spouštěcí
1. Stáhněte si [azure-batch-samples][github_samples_zip] z GitHubu.
2. Otevřete **řešení** MultiInstanceTasks ve Visual Studiu 2019. Soubor `MultiInstanceTasks.sln` řešení je umístěn v:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Zadejte přihlašovací údaje účtu `AccountSettings.settings` batch a úložiště v projektu **Microsoft.Azure.Batch.Samples.Common.**
4. **Sestavení a spuštění** řešení MultiInstanceTasks pro spuštění ukázkové aplikace MPI na výpočetních uzlech ve fondu dávek.
5. *Volitelné*: Před odstraněním prostředků zkontrolujte ukázkový fond, úlohu a úlohu ("MultiInstanceSampleJob", "MultiInstanceSampleJob", "MultiInstanceSampleTask") pomocí [portálu Azure][portal] nebo [Průzkumníka dávek.][batch_labs]

> [!TIP]
> Pokud visual studio nemáte, můžete si zdarma stáhnout [komunitu Sady Visual Studio.][visual_studio]
>
>

Výstup `MultiInstanceTasks.exe` z je podobný následujícímu:

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

## <a name="next-steps"></a>Další kroky
* Blog Microsoft HPC & Azure Batch Team popisuje [podporu MPI pro Linux v Azure Batch][blog_mpi_linux]a obsahuje informace o používání [OpenFOAM][openfoam] s Batch. Ukázky kódu Pythonu pro [příklad OpenFOAM na GitHubu][github_mpi].
* Zjistěte, jak [vytvořit fondy výpočetních uzlů Linuxu](batch-linux-nodes.md) pro použití ve vašich řešeních Azure Batch MPI.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: https://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: https://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
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

[1]: ./media/batch-mpi/batch_mpi_01.png "Přehled více instancí"
