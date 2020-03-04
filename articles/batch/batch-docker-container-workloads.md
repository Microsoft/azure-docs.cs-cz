---
title: Úlohy kontejneru – Azure Batch
description: Naučte se spouštět a škálovat aplikace z imagí kontejnerů na Azure Batch. Vytvořte fond výpočetních uzlů, které podporují spouštění úloh kontejneru.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 03/02/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 81f4e753ffbaaefd5761c9396a6533bac9f212c1
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254841"
---
# <a name="run-container-applications-on-azure-batch"></a>Spustit aplikace kontejneru v Azure Batch

Azure Batch umožňuje spouštět a škálovat velké počty úloh služby Batch computing v Azure. Úlohy Batch lze spustit přímo na virtuálních počítačích (uzlech) ve fondu Batch, ale můžete také nastavit fond dávek pro spouštění úloh v kontejnerech kompatibilních s Docker na uzlech. V tomto článku se dozvíte, jak vytvořit fond výpočetních uzlů, které podporují spouštění úloh kontejneru, a pak spouštějí úlohy kontejneru ve fondu.

Měli byste být obeznámeni se základními pojmy kontejnerů a vytvářením fondu a úloh Batch. Příklady kódu používají sady SDK Batch .NET a Python. Můžete také použít další sady SDK a nástroje sady Batch, včetně Azure Portal, k vytvoření fondů dávek s podporou kontejneru a ke spouštění úloh kontejneru.

## <a name="why-use-containers"></a>Proč používat kontejnery?

Pomocí kontejnerů můžete snadno spouštět úlohy služby Batch bez nutnosti spravovat prostředí a závislosti pro spouštění aplikací. Kontejnery nasazují aplikace jako odlehčené, přenosné a dostatečné jednotky, které mohou běžet v několika různých prostředích. Můžete například sestavit a otestovat kontejner místně a pak nahrát image kontejneru do registru v Azure nebo jinde. Model nasazení kontejneru zajišťuje, že běhové prostředí aplikace je vždy správně nainstalováno a nakonfigurováno všude, kde je aplikace hostována. Úlohy založené na kontejneru v dávce můžou také využívat funkce nekontejnerových úloh, včetně balíčků aplikací a správy souborů prostředků a výstupních souborů.

## <a name="prerequisites"></a>Předpoklady

* **Verze sady SDK**: sady SDK pro Batch podporují image kontejnerů v následujících verzích:
    * Batch REST API verze 2017 -09-01.6.0
    * Batch .NET SDK 8.0.0 verze
    * Sada Batch Python SDK verze 4,0
    * Batch Java SDK verze 3,0
    * Batch Node. js SDK verze 3,0

* **Účty**: v předplatném Azure budete muset vytvořit účet Batch a volitelně účet Azure Storage.

* **Podporovaná image virtuálního počítače**: kontejnery se podporují jenom ve fondech vytvořených s konfigurací virtuálního počítače, a to z imagí popsaných v následující části (podporované image virtuálních počítačů). Pokud zadáte vlastní image, přečtěte si téma informace v následující části a požadavky na [použití spravované vlastní image k vytvoření fondu virtuálních počítačů](batch-custom-images.md).

### <a name="limitations"></a>Omezení

* Batch poskytuje podporu RDMA jenom pro kontejnery běžící na fondech Linux.

* U úloh kontejnerů Windows doporučujeme pro fond zvolit velikost vícejádrových virtuálních počítačů.

## <a name="supported-virtual-machine-images"></a>Podporované image virtuálních počítačů

K vytvoření fondu výpočetních uzlů virtuálních počítačů pro úlohy kontejneru použijte jednu z následujících podporovaných imagí Windows nebo Linux. Další informace o imagích Marketplace, které jsou kompatibilní se službou Batch, najdete v tématu [seznam imagí virtuálních počítačů](batch-linux-nodes.md#list-of-virtual-machine-images).

### <a name="windows-support"></a>Podpora Windows

Batch podporuje image Windows serveru s návrhem podpory kontejnerů. Obvykle jsou tyto názvy SKU imagí s příponou `-with-containers` nebo `-with-containers-smalldisk`. [Rozhraní API k vypsání všech podporovaných imagí ve službě Batch](batch-linux-nodes.md#list-of-virtual-machine-images) navíc označuje schopnost `DockerCompatible`, pokud bitová kopie podporuje kontejnery Docker.

Můžete také vytvořit vlastní image z virtuálních počítačů, na kterých běží Docker ve Windows.

### <a name="linux-support"></a>Podpora Linuxu

V případě zatížení kontejnerů pro Linux služba Batch v současné době podporuje následující image Linux publikované pomocí Microsoft Azure Batch v Azure Marketplace bez nutnosti vlastní image.

#### <a name="vm-sizes-without-rdma"></a>Velikosti virtuálních počítačů bez RDMA

- Vydavatel: `microsoft-azure-batch`
  - Nabídka: `centos-container`
  - Nabídka: `ubuntu-server-container`

#### <a name="vm-sizes-with-rdma"></a>Velikosti virtuálních počítačů pomocí RDMA

- Vydavatel: `microsoft-azure-batch`
  - Nabídka: `centos-container-rdma`
  - Nabídka: `ubuntu-server-container-rdma`

Tyto image se podporují jenom pro použití v fondech Azure Batch a jsou zaměřené na spuštění kontejneru Docker. Funkce:

* Předem nainstalovaný modul runtime kontejneru [Moby](https://github.com/moby/moby) kompatibilní s Docker

* Předinstalované ovladače NVIDIA GPU a modul runtime kontejnerů NVIDIA pro zjednodušení nasazení na virtuálních počítačích řady Azure N-Series

* Předem nainstalovaná/předem nakonfigurovaná image s podporou pro velikosti virtuálních počítačů InfiniBand RDMA pro image s příponou `-rdma`. V současné době tyto image nepodporují velikosti virtuálních počítačů SR-IOV IB/RDMA.

Můžete také vytvořit vlastní image z virtuálních počítačů, na kterých běží Docker, na jednom ze distribucí systému Linux, které jsou kompatibilní se službou Batch. Pokud se rozhodnete zadat vlastní image Linux, přečtěte si pokyny v části [použití spravované vlastní image k vytvoření fondu virtuálních počítačů](batch-custom-images.md).

Pro podporu Docker na vlastní image nainstalujte [Docker Community Edition (CE)](https://www.docker.com/community-edition) nebo [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Další pokyny pro použití vlastní image Linux:

* Pokud chcete využít výkon GPU řady N-Series při použití vlastní image, nainstalujte ovladače NVIDIA předem. Také je nutné nainstalovat nástroj Docker Engine pro grafické procesory NVIDIA, [Docker NVIDIA](https://github.com/NVIDIA/nvidia-docker).

* Pro přístup k síti Azure RDMA použijte velikost virtuálního počítače s podporou RDMA. Potřebné ovladače RDMA jsou nainstalovány v CentOS HPC a Ubuntu bitových kopiích podporovaných službou Batch. Ke spuštění úloh MPI může být potřeba další konfigurace. Viz [použití instancí podporujících technologii RDMA nebo GPU ve fondu Batch](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Konfigurace kontejneru pro fond služby Batch

Pokud chcete povolit, aby fond dávek spouštěl úlohy kontejneru, musíte v objektu [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) fondu zadat nastavení [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) . (Tento článek obsahuje odkazy na reference k rozhraní Batch .NET API. Odpovídající nastavení jsou v rozhraní API pro [dávkovou Python](/python/api/overview/azure/batch) .)

Fond s povoleným kontejnerem můžete vytvořit s nebo bez předem načtených imagí kontejnerů, jak je znázorněno v následujících příkladech. Proces pull (nebo předběžného načtení) umožňuje předem načíst image kontejneru z dokovacího centra nebo jiného registru kontejneru na internetu. Nejlepšího výkonu dosáhnete, když použijete službu [Azure Container Registry](../container-registry/container-registry-intro.md) ve stejné oblasti jako účet Batch.

Výhodou předběžného načítání imagí kontejneru je to, že když úlohy poprvé začnou běžet, nemusí čekat na stažení Image kontejneru. Konfigurace kontejneru při vytvoření fondu načte image kontejneru do virtuálních počítačů. Úlohy, které se spouštějí ve fondu, můžou potom odkazovat na seznam imagí kontejneru a možnosti spuštění kontejnerů.


### <a name="pool-without-prefetched-container-images"></a>Fond bez přednačtených imagí kontejneru

Pokud chcete nakonfigurovat fond s podporou kontejneru bez předem načtených imagí kontejneru, definujte `ContainerConfiguration` a `VirtualMachineConfiguration` objekty, jak je znázorněno v následujícím příkladu Pythonu. V tomto příkladu se na webu Marketplace používá Ubuntu Server pro Azure Batch image fondů kontejnerů.


```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


### <a name="prefetch-images-for-container-configuration"></a>Předběžné načtení imagí pro konfiguraci kontejneru

K předběžnému načtení imagí kontejneru do fondu přidejte do `ContainerConfiguration`seznam imagí kontejneru (`container_image_names`v Pythonu).

Následující základní příklad Pythonu ukazuje, jak předběžně vytvořit standardní image kontejneru Ubuntu z [Docker Hub](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub.
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


Následující C# příklad předpokládá, že chcete předběžně vyTensorFlow image z [Docker Hub](https://hub.docker.com). Tento příklad obsahuje spouštěcí úkol, který běží na hostiteli virtuálního počítače na uzlech fondu. Spouštěcí úkol můžete spustit na hostiteli, například pro připojení souborového serveru, který je k dispozici z kontejnerů.

```csharp

ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);
...
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Předběžné načtení imagí z privátního registru kontejnerů

Můžete také vymezit vynechání imagí kontejneru ověřováním na privátním serveru registru kontejneru. V následujícím příkladu objekty `ContainerConfiguration` a `VirtualMachineConfiguration` vyplňují privátní image TensorFlow z privátního služby Azure Container Registry. Odkaz na obrázek je stejný jako v předchozím příkladu.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>Nastavení kontejneru pro úlohu

Chcete-li spustit úlohu kontejneru ve fondu s podporou kontejneru, zadejte nastavení specifické pro kontejner. Nastavení zahrnují obrázek, který se má použít, registru a možnosti spuštění kontejnerů.

* Pro konfiguraci nastavení specifického pro kontejner použijte vlastnost `ContainerSettings` tříd úloh. Tato nastavení jsou definována třídou [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) . Všimněte si, že možnost kontejneru `--rm` nevyžaduje další možnost `--runtime`, protože se postará o dávku.

* Pokud spouštíte úlohy na obrázcích kontejneru, úloha [cloudu](/dotnet/api/microsoft.azure.batch.cloudtask) a [úkol správce úloh](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) vyžadují nastavení kontejneru. Úkol [Spustit úkol](/dotnet/api/microsoft.azure.batch.starttask), [úkol přípravy úlohy](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)a [úkol uvolnění úlohy](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) však nevyžadují nastavení kontejneru (to znamená, že mohou být spuštěny v kontextu kontejneru nebo přímo na uzlu).

### <a name="container-task-command-line"></a>Příkazový řádek úlohy kontejneru

Když spustíte úlohu kontejneru, Batch automaticky použije příkaz [Docker Create](https://docs.docker.com/engine/reference/commandline/create/) k vytvoření kontejneru pomocí Image zadané v úloze. Batch potom řídí provádění úloh v kontejneru.

Stejně jako u úloh služby Batch bez kontejnerů můžete nastavit příkazový řádek pro úlohu kontejneru. Vzhledem k tomu, že dávka automaticky vytvoří kontejner, příkazový řádek pouze určuje příkaz nebo příkazy, které budou spuštěny v kontejneru.

Pokud je image kontejneru pro úlohu Batch nakonfigurovaná pomocí skriptu [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) , můžete příkazový řádek nastavit tak, aby buď použil výchozí vstupní bod, nebo ho přepsat:

* Chcete-li použít výchozí vstupní bod pro Image kontejneru, nastavte příkazový řádek úlohy na prázdný řetězec `""`.

* Chcete-li přepsat výchozí vstupní bod, nebo pokud bitová kopie neobsahuje vstupní bod, nastavte příkazový řádek vhodný pro kontejner, například `/app/myapp` nebo `/bin/sh -c python myscript.py`.

Volitelné [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) jsou další argumenty, které zadáte do příkazu `docker create`, který dávka používá k vytvoření a spuštění kontejneru. Chcete-li například nastavit pracovní adresář pro kontejner, nastavte možnost `--workdir <directory>`. Další možnosti najdete v tématu o [Vytvoření Docker](https://docs.docker.com/engine/reference/commandline/create/) reference.

### <a name="container-task-working-directory"></a>Pracovní adresář úkolu kontejneru

Úloha služby Batch se spustí v pracovním adresáři v kontejneru, který je velmi podobný jako adresářová dávka pro běžný (nekontejnerový) úkol. Všimněte si, že tento pracovní adresář je jiný než [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) , pokud je nakonfigurovaný v imagi, nebo výchozí pracovní adresář kontejneru (`C:\` v kontejneru Windows nebo `/` na kontejneru Linux).

Pro úlohu kontejneru Batch:

* Všechny adresáře rekurzivně pod `AZ_BATCH_NODE_ROOT_DIR` v uzlu hostitele (kořen Azure Batch adresářů) se mapují do kontejneru.
* Všechny proměnné prostředí úlohy jsou namapované na kontejner.
* Úloha pracovního adresáře `AZ_BATCH_TASK_WORKING_DIR` v uzlu je nastavena stejně jako pro běžný úkol a mapována do kontejneru.

Tato mapování umožňují pracovat s úlohami kontejneru podobným způsobem jako úlohy bez kontejneru. Například nainstalujte aplikace pomocí balíčků aplikace, získejte přístup k souborům prostředků z Azure Storage, použijte nastavení prostředí úloh a zachovejte výstupní soubory úlohy po zastavení kontejneru.

### <a name="troubleshoot-container-tasks"></a>Řešení potíží s úlohami kontejneru

Pokud úloha kontejneru neběží podle očekávání, možná budete muset získat informace o konfiguraci WORKDIR nebo ENTRYPOINT pro Image kontejneru. Pokud chcete zobrazit konfiguraci, spusťte příkaz [Docker image prověřit](https://docs.docker.com/engine/reference/commandline/image_inspect/) .

V případě potřeby upravte nastavení úlohy kontejneru na základě bitové kopie:

* Zadejte absolutní cestu na příkazovém řádku úlohy. Pokud se pro příkazový řádek úlohy používá výchozí vstupní bod obrázku, ujistěte se, že je nastavená absolutní cesta.

* V možnostech spuštění kontejneru úlohy změňte pracovní adresář tak, aby odpovídal WORKDIR v imagi. Nastavte například `--workdir /app`.

## <a name="container-task-examples"></a>Příklady úloh kontejneru

Následující fragment kódu Pythonu ukazuje základní příkazový řádek, který je spuštěný v kontejneru vytvořeném z fiktivní image z Docker Hub. Zde, možnost kontejneru `--rm` odstraní kontejner po dokončení úkolu a možnost `--workdir` nastaví pracovní adresář. Příkazový řádek přepíše vstupní bod kontejneru jednoduchým příkazem prostředí, který zapíše malý soubor do pracovního adresáře úkolů na hostiteli.

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage',
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)
```

Následující C# příklad ukazuje základní nastavení kontejneru pro cloudový úkol:

```csharp
// Simple container task command

string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine);
```


## <a name="next-steps"></a>Další kroky

* Podívejte se také na sadu [Batch pro dávkové](https://github.com/Azure/batch-shipyard) zpracování, která umožňuje snadné nasazení kontejnerových úloh v Azure Batch prostřednictvím [receptů v loděnicích](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Další informace o instalaci a použití nástroje Docker CE v systému Linux najdete v dokumentaci k [Docker](https://docs.docker.com/engine/installation/) .

* Další informace o používání vlastních imagí najdete v tématu [použití spravované vlastní image k vytvoření fondu virtuálních počítačů](batch-custom-images.md).

* Přečtěte si další informace o [projektu Moby](https://mobyproject.org/), architektuře pro vytváření kontejnerových systémů.
