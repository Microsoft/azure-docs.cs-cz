---
title: Kontejner zatížení v Azure Batch | Microsoft Docs
description: Zjistěte, jak ke spouštění aplikací z kontejneru bitové kopie v Azure Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 06/04/2018
ms.author: danlep
ms.openlocfilehash: 8ef9d5a8e5212f6715769eecf4fde92a6d0b9d44
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060513"
---
# <a name="run-container-applications-on-azure-batch"></a>Spustit v Azure Batch – aplikace typu kontejner

Azure Batch umožňuje spouštění a škálování velkého počtu výpočetních úloh v Azure batch. Úkoly služby batch můžete spustit přímo u virtuálních počítačů (uzlů) v rámci fondu Batch, ale fondu služby Batch můžete také nastavit ke spouštění úloh v kontejnerech Docker kompatibilní na uzlech. Tento článek ukazuje, jak vytvořit fond výpočetních uzlů, které podporují spuštěné úlohy kontejner a poté spusťte kontejner úlohy ve fondu. 

Měli byste se seznámit s koncepty kontejneru a postup vytvoření fondu služby Batch a úlohy. Příklady kódu pomocí rozhraní Batch .NET a Python SDK. Také můžete použít jiné Batch sady SDK a nástroje, včetně portálu Azure vytvořit kontejner s podporou fondy Batch a ke spouštění úloh kontejneru.

## <a name="why-use-containers"></a>Proč používat kontejnery?

Použití kontejnerů poskytuje snadný způsob, jak spouštět úlohy Batch bez nutnosti ke správě prostředí a závislosti ke spouštění aplikací. Kontejnery nasazení aplikací jako lightweight, přenosné, soběstačný jednotky, které můžou běžet v několika různých prostředích. Můžete například sestavení a testování místně kontejner pak nahrajte image kontejneru na registru v Azure nebo jinde. Model nasazení kontejneru zajistí, že běhové prostředí vaší aplikace je vždy správně nainstalováno a nakonfigurováno bez ohledu na hostovat aplikaci. Úkoly na základě kontejneru ve službě Batch můžete také využít výhod funkcí není kontejner úlohy, včetně balíčků aplikací a správa souborů prostředků a výstupní soubory. 

## <a name="prerequisites"></a>Požadavky

* **Verze sady SDK**: SDK služby Batch podporu kontejneru Image od následující verze:
    * Batch REST API verze 2017-09-01.6.0
    * Batch .NET SDK verze 8.0.0
    * Batch Python SDK verze 4.0
    * Batch Java SDK verze 3.0
    * Batch Node.js SDK verze 3.0

* **Účty**: ve vašem předplatném Azure, musíte vytvořit účet Batch a volitelně účet úložiště Azure.

* **Podporované image virtuálního počítače**: kontejnery jsou podporovány pouze ve fondech vytvořené pomocí konfigurace virtuálního počítače z Image podrobně popsané v následující části, "podporováno bitové kopie virtuálních počítačů." Pokud jste zadali vlastní image, najdete v části požadavky v následující části a požadavky v [použít k vytvoření fondu virtuálních počítačů spravovaných vlastní image](batch-custom-images.md). 

### <a name="limitations"></a>Omezení

* Batch poskytuje podporu RDMA pouze pro kontejnery systémem Linux fondy.

## <a name="supported-virtual-machine-images"></a>Bitové kopie podporovaných virtuálních počítačů

Použijte jednu z následujících podporováno Windows nebo Linux bitové kopie k vytvoření fondu virtuálních počítačů výpočetních uzlů pro úlohy kontejneru. Další informace o bitových kopiích Marketplace, které jsou kompatibilní s Batch najdete v tématu [seznam bitové kopie virtuálních počítačů](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Bitových kopií systému Windows

Pro úlohy Windows kontejneru, Batch aktuálně podporuje **Windows Server 2016 Datacenter s kontejnery** bitové kopie v Azure Marketplace. V systému Windows jsou podporované jen Image kontejner Docker.

Můžete také vytvořit vlastní Image z virtuálních počítačů spuštěných Docker v systému Windows.

### <a name="linux-images"></a>Bitové kopie systému Linux

Pro úlohy kontejneru Linux Batch aktuálně podporuje následující Linux Image vydaná Microsoft Azure Batch v Azure Marketplace:

* **CentOS pro kontejner fondech Azure Batch**

* **CentOS (s ovladači RDMA) pro kontejner fondech Azure Batch**

* **Ubuntu Server pro kontejner fondech Azure Batch**

* **Ubuntu Server (s ovladači RDMA) pro kontejner fondech Azure Batch**

Tyto Image jsou podporovány pouze pro použití ve fondech Azure Batch. Tyto funkce:

* Předem nainstalovaná [Moby](https://github.com/moby/moby) kontejner modulu runtime 

* Předem nainstalované ovladače NVIDIA GPU, a společně Zjednodušte nasazení na virtuálních počítačích Azure N-series

* Bitové kopie s nebo bez předem nainstalované ovladače RDMA; Povolit tyto ovladače uzly fondu pro přístup k síti Azure RDMA při nasazení na velikosti podporující RDMA virtuálních počítačů  

Můžete také vytvořit vlastní Image z virtuálních počítačů spuštěných Docker na jednu z distribuce systému Linux, které je kompatibilní s Batch. Pokud zvolíte možnost zadejte svoji vlastní image Linux, postupujte podle pokynů v [použít k vytvoření fondu virtuálních počítačů spravovaných vlastní image](batch-custom-images.md).

Pro podporu Docker na vlastní image, nainstalujte [Docker Community Edition (CE)](https://www.docker.com/community-edition) nebo [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Další informace pro použití vlastní image Linux:

* Abyste mohli využívat výkonu GPU velikostí Azure N-series, při použití vlastní image, nainstalujte před NVIDIA ovladače. Také je potřeba nainstalovat nástroj modulu Docker pro NVIDIA grafickými procesory, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Pro přístup k síti Azure RDMA, použijte velikost virtuálního počítače podporující RDMA. V prostředí HPC CentOS a Ubuntu bitové kopie nepodporuje dávky nejsou nainstalovány potřebné ovladače RDMA. Ke spuštění úlohy MPI může být nutná další konfigurace. V tématu [použití podporující RDMA nebo grafický procesor s podporou instancí ve fondu Batch](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Konfiguraci kontejneru pro fondu služby Batch

Chcete-li povolit spuštění úloh kontejneru fondu služby Batch, je nutné zadat [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) nastavení ve fondu [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) objektu. (Tento článek obsahuje odkazy na referenční dokumentace rozhraní Batch .NET API. Odpovídající nastavení jsou v [Batch Python](/python/api/azure.batch) rozhraní API.)

Fondu s povoleným kontejner můžete vytvořit s nebo bez prefetched kontejneru Image, jak je znázorněno v následujících příkladech. Proces přijetí změn (nebo předběžné načtení) umožňuje předběžné načtení kontejner imagí z úložiště Docker Hub nebo jiný kontejner registru na Internetu. Pro nejlepší výkon použijte [kontejner Azure registru](../container-registry/container-registry-intro.md) ve stejné oblasti jako účet Batch.

Výhodou prefetching kontejneru bitové kopie je, že při prvním spuštění úlohy systémem nemají čekání na bitovou kopii kontejner ke stažení. Konfiguraci kontejneru vrátí kontejner bitové kopie na virtuální počítače při vytvoření fondu. Úlohy, které spustí ve fondu pak můžete odkazovat v seznamu kontejneru bitových kopií a spusťte kontejner možnosti.


### <a name="pool-without-prefetched-container-images"></a>Fond bez prefetched kontejneru obrázků

Ke konfiguraci fondu povoleno kontejneru bez bitových kopií prefetched kontejneru, definovat `ContainerConfiguration` a `VirtualMachineConfiguration` objekty, jak je znázorněno v následujícím příkladu Python. Tento příklad používá Ubuntu Server pro Azure Batch kontejneru fondy image z Marketplace.


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


### <a name="prefetch-images-for-container-configuration"></a>Předběžné načtení Image pro konfiguraci kontejneru

Pokud chcete předběžné načtení Image kontejner ve fondu, přidejte seznam kontejneru bitové kopie (`container_image_names`, v Pythonu) do `ContainerConfiguration`. 

Základní Python tento příklad ukazuje, jak předběžné načtení standardní bitové kopie kontejneru Ubuntu z [úložiště Docker Hub](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(container_image_names=['ubuntu'])

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


Následující příklad jazyka C# příklad předpokládá, chcete-li předběžné načtení image TensorFlow z [úložiště Docker Hub](https://hub.docker.com). Tento příklad obsahuje spouštěcí úkol, který je spuštěn v hostiteli virtuálního počítače na uzlech fondu. Můžete spustit spouštěcí úkol na hostiteli, například připojit souborový server, který je přístupný z kontejnerů.

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


### <a name="prefetch-images-from-a-private-container-registry"></a>Předběžné načtení bitové kopie z registru kontejner privátní

Můžete také předběžné načtení Image kontejneru tak, že k registru serveru privátní kontejneru. V následujícím příkladu `ContainerConfiguration` a `VirtualMachineConfiguration` objekty předběžné načtení privátního TensorFlow image z registru privátní kontejner Azure. Odkaz na obrázek je stejný jako v předchozím příkladu.

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


## <a name="container-settings-for-the-task"></a>Kontejner nastavení úlohy

Chcete-li spusťte kontejner úlohy na výpočetních uzlech, musíte zadat nastavení specifických kontejneru jako jsou úlohy možnosti, bitové kopie na použití a registru.

Použití `ContainerSettings` vlastnost třídy úloh konfigurace nastavení specifických pro kontejner. Tato nastavení jsou definovány [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) třídy.

Pokud spustíte úlohy v kontejneru Image, [cloudu úloh](/dotnet/api/microsoft.azure.batch.cloudtask) a [úkolu Správce úloh](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) požadovat nastavení kontejneru. Ale [spouštěcí úkol](/dotnet/api/microsoft.azure.batch.starttask), [úkol přípravy úlohy](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask), a [úkol uvolnění úlohy](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) nevyžadují nastavení kontejneru (to znamená, že můžete spustit v kontextu kontejneru nebo přímo v uzlu).

Příkazový řádek pro kontejner úloh Azure Batch provádí v pracovním adresáři v kontejneru, který je velmi podobné prostředí, ve kterém Batch nastaví pro regulární úlohu (není kontejner):

* Všechny adresáře rekurzivně níže `AZ_BATCH_NODE_ROOT_DIR` (kořenové adresáře Azure Batch na uzlu) jsou mapovány do kontejneru
* Všechny proměnné prostředí úkolu jsou mapovány do kontejneru
* Pracovní adresář aplikace nastavena, je stejný jako u pravidelné úlohy, abyste mohli používat funkce, jako je například balíčky aplikací a soubory prostředků

Vzhledem k tomu Batch změní výchozí pracovní adresář vaší kontejneru, tato úloha se spustí na jiném ze vstupního bodu typické kontejneru místě (například `c:\` ve výchozím nastavení do kontejneru systému Windows nebo `/` v systému Linux). Ujistěte se, že vaše úloha příkazový řádek nebo kontejneru vstupní bod určuje absolutní cestu, pokud již není nakonfigurována tímto způsobem.

Následující fragment kódu Python ukazuje základní příkazového řádku spuštěné v kontejneru Ubuntu načtený z úložiště Docker Hub. Spusťte kontejner možnosti jsou další argumenty, které mají `docker create` příkaz, který bude spuštěna úloha. Zde `--rm` možnost odebere kontejneru po dokončení této úlohy.

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='ubuntu', 
    container_run_options='--rm')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/echo hello',
    container_settings=task_container_settings
)

```

Následující příklad jazyka C# ukazuje nastavení základního kontejneru pro úlohu cloudu:

```csharp
// Simple container task command

string cmdLine = "c:\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Další postup

* Viz také [loděnice Batch](https://github.com/Azure/batch-shipyard) nástrojů pro snadné nasazení kontejneru zatížení v Azure Batch prostřednictvím [loděnice recepty](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Další informace o instalaci a použití Docker CE v systému Linux, najdete v článku [Docker](https://docs.docker.com/engine/installation/) dokumentaci.

* Další informace o používání vlastních imagí najdete v tématu [použít k vytvoření fondu virtuálních počítačů spravovaných vlastní image ](batch-custom-images.md).

* Další informace o [Moby projektu](https://mobyproject.org/), rozhraní pro vytváření kontejneru systémy.