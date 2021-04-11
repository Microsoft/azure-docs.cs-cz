---
title: Použití úkolů s více instancemi ke spouštění aplikací MPI
description: Naučte se spouštět aplikace MPI (Message Passing Interface) pomocí typu úlohy s více instancemi v Azure Batch.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 02764f8dd8a6bb3e4224b8b44fe78ab7e15ba85d
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219840"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Použití úkolů s více instancemi ke spouštění aplikací rozhraní MPI (Message Passing Interface) ve Batch

Úlohy s více instancemi umožňují spouštět úlohu Azure Batch na několika výpočetních uzlech současně. Tyto úlohy umožňují vysoce výkonné výpočetní scénáře jako aplikace MPI (Message Passing Interface) v dávce. V tomto článku se dozvíte, jak spouštět úlohy s více instancemi pomocí knihovny [Batch .NET](/dotnet/api/microsoft.azure.batch) .

> [!NOTE]
> I když příklady v tomto článku se zaměřují na výpočetní uzly Batch .NET, MS-MPI a Windows, jsou zde popsané koncepty úkolů s více instancemi pro jiné platformy a technologie (například Python a Intel MPI na uzlech Linux).

## <a name="multi-instance-task-overview"></a>Přehled úlohy s více instancemi

V dávce se každý úkol obvykle spouští na jednom výpočetním uzlu – do úlohy odešlete více úkolů a služba Batch naplánuje každou úlohu k provedení na uzlu. Když ale konfigurujete **nastavení více instancí** úlohy, řekněte službě Batch, aby vytvořila jednu primární úlohu a několik dílčích úloh, které se pak spustí na více uzlech.

:::image type="content" source="media/batch-mpi/batch-mpi-01.png" alt-text="Diagram znázorňující přehled nastavení s více instancemi.":::

Když odešlete úlohu s nastavením více instancí do úlohy, dávka provede několik kroků, které jsou jedinečné pro úlohy s více instancemi:

1. Služba Batch vytvoří jeden **primární** a několik dílčích **úloh** na základě nastavení s více instancemi. Celkový počet úloh (primární a všechny dílčí úkoly) odpovídá počtu **instancí** (výpočetních uzlů), které zadáte v nastavení více instancí.
2. Batch označí jeden z výpočetních uzlů jako **Hlavní** a naplánuje primární úlohu, která se má spustit na hlavním serveru. Naplánuje dílčí úkoly, které se mají provést, na zbytek výpočetních uzlů přidělených úloze s více instancemi, jednoho dílčího úkolu na uzel.
3. Primární a všechny dílčí úkoly stáhnou všechny **běžné soubory prostředků** , které zadáte v nastavení více instancí.
4. Po stažení běžných souborů prostředků provede primární a dílčí úkoly **příkaz koordinace** , který zadáte v nastavení více instancí. Příkaz koordinace se obvykle používá k přípravě uzlů pro provedení úlohy. To může zahrnovat spouštění služeb na pozadí (například [Microsoft MPI](/message-passing-interface/microsoft-mpi) `smpd.exe` ) a ověření, že uzly jsou připravené na zpracování zpráv mezi uzly.
5. Primární úloha spustí **příkaz aplikace** na hlavním uzlu *po* úspěšném dokončení příkazu koordinace primárním a všemi dílčími úkoly. Příkaz aplikace je příkazový řádek samotného úkolu s více instancemi a je proveden pouze primárním úkolem. V řešení založeném na [MS-MPI](/message-passing-interface/microsoft-mpi) se jedná o místo, kde spouštíte aplikaci s povoleným MPI pomocí `mpiexec.exe` .

> [!NOTE]
> I když je funkce velmi odlišná, nejedná se o jedinečný typ úlohy, jako je [StartTask](/dotnet/api/microsoft.azure.batch.starttask) nebo [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask). Úloha s více instancemi je jednoduše standardní dávková úloha ([CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) v dávce .NET), jejíž nastavení s více instancemi je nakonfigurované. V tomto článku budeme tento postup označovat jako úlohu s **více instancemi**.

## <a name="requirements-for-multi-instance-tasks"></a>Požadavky na úlohy s více instancemi

Úkoly s více instancemi vyžadují fond se **zapnutou komunikací mezi uzly** a **Souběžné spouštění úloh je zakázané**. Chcete-li zakázat souběžné provádění úloh, nastavte vlastnost [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) na hodnotu 1.

> [!NOTE]
> Batch [omezuje](batch-quota-limit.md#pool-size-limits) velikost fondu, který má povolenou komunikaci mezi uzly.

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
myCloudPool.TaskSlotsPerNode = 1;
```

> [!NOTE]
> Pokud se pokusíte spustit úlohu s více instancemi ve fondu s zakázáním komunikace mezi uzly nebo s hodnotou *taskSlotsPerNode* větší než 1, úloha není nikdy naplánována – zůstane neomezeně ve stavu "aktivní".

### <a name="use-a-starttask-to-install-mpi"></a>Použití StartTask k instalaci MPI

Pokud chcete spouštět aplikace MPI s úlohou s více instancemi, musíte nejdřív do výpočetních uzlů ve fondu nainstalovat MPI implementaci (například MS-MPI nebo Intel MPI). To je vhodný čas pro použití [StartTask](/dotnet/api/microsoft.azure.batch.starttask), který se spustí pokaždé, když se uzel připojí k fondu, nebo se restartuje. Tento fragment kódu vytvoří StartTask, který určuje instalační balíček MS-MPI jako [soubor prostředků](/dotnet/api/microsoft.azure.batch.resourcefile). Příkazový řádek spouštěcího úkolu se spustí po stažení souboru prostředků do uzlu. V takovém případě příkazový řádek provede bezobslužnou instalaci MS-MPI.

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

### <a name="remote-direct-memory-access-rdma"></a>Přímý přístup do paměti vzdáleného počítače (RDMA)

Když pro výpočetní uzly ve fondu služby Batch zvolíte [Velikost podporující RDMA](../virtual-machines/sizes-hpc.md?toc=/azure/virtual-machines/windows/toc.json) , jako je třeba, může vaše aplikace MPI využívat výhod sítě RDMA (Remote Direct Memory Access) s nízkou latencí v Azure.

Vyhledejte velikosti zadané jako "RDMA podporující" ve [velikosti pro virtuální počítače v Azure](../virtual-machines/sizes.md) (pro VirtualMachineConfiguration fondy) nebo [velikosti pro Cloud Services](../cloud-services/cloud-services-sizes-specs.md) (pro fondy CloudServicesConfiguration).

> [!NOTE]
> Pokud chcete využít výhod RDMA na [výpočetních uzlech pro Linux](batch-linux-nodes.md), musíte na uzlech použít **Intel MPI** .

## <a name="create-a-multi-instance-task-with-batch-net"></a>Vytvoření úlohy s více instancemi pomocí batch .NET

Teď, když jsme pokryli požadavky na fond a instalaci balíčku MPI, pojďme vytvořit úlohu s více instancemi. V tomto fragmentu kódu vytvoříme standardní [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask)a pak nakonfigurujeme jeho vlastnost [MultiInstanceSettings](/dotnet/api/microsoft.azure.batch.cloudtask) . Jak už bylo zmíněno dříve, úloha více instancí není jedinečný typ úlohy, ale standardní úloha dávky konfigurovaná s nastavením více instancí.

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

Při vytváření nastavení s více instancemi pro úlohu zadáte počet výpočetních uzlů, ve kterých se má úloha spustit. Když úlohu odešlete do úlohy, vytvoří služba Batch jednu **primární** úlohu a dostatek **dílčích úloh** , které společně odpovídají počtu zadaných uzlů.

Těmto úkolům je přiřazeno celé číslo ID v rozsahu od 0 do *numberOfInstances* -1. Úloha s ID 0 je primární úlohou a všechna ostatní ID jsou dílčí úkoly. Například pokud pro úlohu vytvoříte následující nastavení s více instancemi, primární úkol by měl mít ID 0 a dílčí úkoly budou mít ID 1 až 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Hlavní uzel

Když odešlete úlohu s více instancemi, služba Batch označí jeden z výpočetních uzlů jako hlavní uzel a naplánuje spuštění primární úlohy na hlavním uzlu. U dílčích úkolů je naplánováno spuštění ve zbývající části uzlů přidělených úloze s více instancemi.

## <a name="coordination-command"></a>Příkaz koordinace

**Příkaz koordinace** je proveden primárním i dílčím úkolem.

Vyvolání příkazu koordinace je blokováno – dávka nespustí příkaz aplikace, dokud se příkaz koordinace nevrátil úspěšně pro všechny dílčí úkoly. Příkaz koordinace by proto měl spustit všechny požadované služby na pozadí, ověřit, že jsou připravené k použití, a pak ukončit. Například tento příkaz koordinace pro řešení pomocí MS-MPI verze 7 spustí službu SMPD na uzlu a pak ukončí:

`cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d`

Všimněte si použití `start` v tomto příkazu koordinace. To je nutné, protože `smpd.exe` aplikace se po provedení nevrátí hned. Bez použití příkazu Start by tento příkaz koordinace nevrátil, a proto zablokoval spuštění příkazu aplikace.

## <a name="application-command"></a>Příkaz aplikace

Jakmile primární úkol a všechny dílčí úkoly dokončí příkaz koordinace, příkazový řádek úlohy s více instancemi se spustí *jenom* primární úlohou. Tento **příkaz aplikace** voláme, aby se lišil od příkazu koordinace.

Pro aplikace MS-MPI použijte příkaz aplikace a spusťte aplikaci s podporou MPI `mpiexec.exe` . Například tady je příkaz aplikace pro řešení s použitím MS-MPI verze 7:

`cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe`

> [!NOTE]
> Vzhledem k tomu, že MS-MPI `mpiexec.exe` používá `CCP_NODES` proměnnou ve výchozím nastavení (viz [proměnné prostředí](#environment-variables)), výše uvedená příkazová řádková aplikace ji vyloučí.

## <a name="environment-variables"></a>Proměnné prostředí

Batch vytvoří několik [proměnných prostředí](batch-compute-node-environment-variables.md) specifických pro úlohy s více instancemi výpočetních uzlů přidělených úloze s více instancemi. Na tyto proměnné prostředí se můžou odkazovat příkazové řádky vaší koordinace a aplikace, stejně jako můžou skripty a programy, které provádějí.

Následující proměnné prostředí vytváří služba Batch pro použití v úlohách s více instancemi:

- `CCP_NODES`
- `AZ_BATCH_NODE_LIST`
- `AZ_BATCH_HOST_LIST`
- `AZ_BATCH_MASTER_NODE`
- `AZ_BATCH_TASK_SHARED_DIR`
- `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Úplné podrobnosti o těchto a dalších proměnných prostředí výpočetních uzlů pro dávkové zpracování, včetně jejich obsahu a viditelnosti, najdete v tématu [proměnné prostředí výpočetního uzlu](batch-compute-node-environment-variables.md).

> [!TIP]
> [Ukázka kódu MPI služby Batch pro Linux](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch/article_samples/mpi) obsahuje příklad toho, jak lze použít několik těchto proměnných prostředí.

## <a name="resource-files"></a>Soubory prostředků

Existují dvě sady souborů prostředků, které je třeba vzít v úvahu pro úlohy s více instancemi: **běžné soubory prostředků** , které *všechny* úlohy stahují (primární i dílčí úkoly), a **soubory prostředků** zadané pro vlastní úlohu s více instancemi, které se pouze stahují *primární* úlohy.

V nastaveních více instancí úlohy můžete určit jeden nebo více **běžných souborů prostředků** . Tyto běžné soubory prostředků se stáhnou z [Azure Storage](../storage/common/storage-introduction.md) do **sdíleného adresáře úkolů** každého uzlu na základě primárního a všech dílčích úkolů. Ke sdílenému adresáři úlohy můžete přistupovat z aplikace a příkazového řádku pro koordinaci pomocí `AZ_BATCH_TASK_SHARED_DIR` proměnné prostředí. `AZ_BATCH_TASK_SHARED_DIR`Cesta je shodná na všech uzlech přidělených k úloze s více instancemi, takže můžete sdílet jeden příkaz koordinace mezi primárním a dílčím úkolem. Batch nesdílí adresář ve smyslu vzdáleného přístupu, ale můžete ho použít jako přípojný nebo sdílený bod, jak je uvedeno výše v popisu proměnné prostředí.

Soubory prostředků, které zadáte pro samotný úkol s více instancemi, se ve výchozím nastavení stáhnou do pracovního adresáře úlohy `AZ_BATCH_TASK_WORKING_DIR` . Jak je uvedeno na rozdíl od běžných souborů prostředků, stáhne pouze primární úlohy soubory prostředků zadané pro vlastní úlohu s více instancemi.

> [!IMPORTANT]
> Vždy používejte proměnné prostředí `AZ_BATCH_TASK_SHARED_DIR` a `AZ_BATCH_TASK_WORKING_DIR` k odkazování na tyto adresáře na příkazových řádcích. Nepokoušejte se vytvořit cesty ručně.

## <a name="task-lifetime"></a>Doba života úlohy

Doba života primární úlohy řídí dobu života celé úlohy s více instancemi. Při primárním ukončení se ukončí všechny dílčí úkoly. Ukončovací kód primární služby je ukončovací kód úkolu, který se proto používá k určení úspěchu nebo neúspěchu úkolu pro účely opakování.

Pokud některý z dílčích úkolů selže, ukončí se nenulovým návratovým kódem, například celá úloha s více instancemi selže. Úloha s více instancemi se pak ukončí a zopakuje, až do jejího limitu opakování.

Když odstraníte úlohu s více instancemi, odstraní se i hlavní a všechny dílčí úkoly služby Batch. Všechny adresáře podúloh a jejich soubory jsou z výpočetních uzlů smazány stejně jako u standardních úloh.

[TaskConstraints](/dotnet/api/microsoft.azure.batch.taskconstraints) pro úlohu s více instancemi, jako jsou vlastnosti [MaxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount), [proměnné maxwallclocktime](/dotnet/api/microsoft.azure.batch.taskconstraints.maxwallclocktime)a [RetentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) , se respektují tak, jak jsou pro standardní úlohu, a platí pro primární a všechny dílčí úkoly. Pokud však změníte vlastnost theRetentionTime po přidání úlohy s více instancemi do úlohy, tato změna se použije pouze pro primární úlohu a všechny dílčí úkoly budou dál používat původní RetentionTime.

Seznam úkolů v nedávném výpočetním uzlu odráží ID dílčího úkolu, pokud byl poslední úkol součástí úlohy s více instancemi.

## <a name="obtain-information-about-subtasks"></a>Získat informace o dílčích úkolech

Chcete-li získat informace o dílčích úkolech pomocí knihovny Batch .NET, zavolejte metodu [CloudTask. ListSubtasks](/dotnet/api/microsoft.azure.batch.cloudtask.listsubtasks) . Tato metoda vrací informace o všech dílčích úkolech a informace o výpočetním uzlu, který provedl úlohy. Z těchto informací můžete určit kořenový adresář každého dílčího úkolu, ID fondu, jeho aktuální stav, ukončovací kód a další. Tyto informace můžete použít v kombinaci s metodou [PoolOperations. GetNodeFile](/dotnet/api/microsoft.azure.batch.pooloperations.getnodefile) pro získání souborů dílčího úkolu. Všimněte si, že tato metoda nevrací informace pro primární úlohu (ID 0).

> [!NOTE]
> Pokud není uvedeno jinak, metody Batch .NET, které pracují s více instancemi [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) , se vztahují *pouze* na primární úlohu. Například při volání metody [CloudTask. ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask.listnodefiles) u úlohy s více instancemi se vrátí pouze soubory primární úlohy.

Následující fragment kódu ukazuje, jak získat informace o dílčím úkolu a také požadovat obsah souboru z uzlů, ve kterých byly provedeny.

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

Ukázka kódu [MultiInstanceTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks) na GitHubu ukazuje, jak používat úlohu s více instancemi ke spuštění aplikace [MS-MPI](/message-passing-interface/microsoft-mpi) na výpočetních uzlech služby Batch. Pomocí následujícího postupu spusťte ukázku.

### <a name="preparation"></a>Příprava

1. Stáhněte si [sadu MPI SDK pro MS-a](/message-passing-interface/microsoft-mpi) proveďte instalaci. Po instalaci můžete ověřit, že jsou nastavené proměnné prostředí MS-MPI.
1. Sestavte *prodejní* verzi programu [MPIHelloWorld](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld) Sample MPI. Toto je program, který bude spuštěn na výpočetních uzlech pomocí úlohy více instancí.
1. Vytvořte soubor ZIP obsahující `MPIHelloWorld.exe` (který jste vytvořili v kroku 2) a `MSMpiSetup.exe` (který jste si stáhli v kroku 1). Tento soubor zip nahrajete jako balíček aplikace v dalším kroku.
1. Pomocí [Azure Portal](https://portal.azure.com) vytvořte [aplikaci](batch-application-packages.md) Batch s názvem "MPIHelloWorld" a zadejte soubor zip, který jste vytvořili v předchozím kroku, jako verze "1,0" balíčku aplikace. Další informace najdete v tématu [nahrání a Správa aplikací](batch-application-packages.md#upload-and-manage-applications) .

> [!TIP]
> Sestavování *vydání* verze `MPIHelloWorld.exe` zajistí, že nemusíte do balíčku aplikace zahrnout žádné další závislosti (například `msvcp140d.dll` nebo `vcruntime140d.dll` ).

### <a name="execution"></a>Spuštění

1. Stáhněte si [soubor Azure-Batch-Samples. zip](https://github.com/Azure/azure-batch-samples/archive/master.zip) z GitHubu.
1. Otevřete **řešení** MultiInstanceTasks v aplikaci Visual Studio 2019. `MultiInstanceTasks.sln`Soubor řešení je umístěný v:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
1. Zadejte přihlašovací údaje služby Batch a účtu úložiště v `AccountSettings.settings` projektu **Microsoft.Azure.Batch. Samples. Common** .
1. **Sestavte a spusťte** řešení MultiInstanceTasks, které spustí ukázkovou aplikaci MPI na výpočetních uzlech ve fondu Batch.
1. *Volitelné*: před odstraněním prostředků použijte [Azure Portal](https://portal.azure.com) nebo [Batch Explorer](https://azure.github.io/BatchExplorer/) a Projděte si ukázkový fond, úlohu a úlohu ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask").

> [!TIP]
> Pokud ještě nemáte Visual Studio, můžete si zdarma stáhnout [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/) .

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

## <a name="next-steps"></a>Další kroky

- Další informace o [podpoře MPI pro Linux najdete na Azure Batch](https://docs.microsoft.com/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch).
- Naučte se [vytvářet fondy výpočetních uzlů Linux](batch-linux-nodes.md) pro použití ve vašich Azure Batchch řešeních MPI.
