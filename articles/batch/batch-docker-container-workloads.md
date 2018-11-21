---
title: Úlohy kontejneru v Azure Batch | Dokumentace Microsoftu
description: Zjistěte, jak ke spouštění aplikací z imagí kontejnerů v Azure Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 11/19/2018
ms.author: danlep
ms.openlocfilehash: 1d915482a3a8b1f6416b50ab52de997a9d33294f
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52262427"
---
# <a name="run-container-applications-on-azure-batch"></a>Spouštění aplikací typu kontejner v Azure Batch

Služba Azure Batch umožňuje spouštět a škálovat velké množství dávkové výpočetní úlohy v Azure. Úkoly služby batch můžete spouštět přímo na virtuálních počítačích (uzly) ve fondu Batch, ale fond služby Batch můžete také nastavit spouštění úloh v kontejnerech kompatibilní s Dockerem na uzlech. Tento článek ukazuje, jak vytvořit fond výpočetních uzlů, které podporují spuštěné úlohy kontejneru a pak spusťte kontejner úloh ve fondu. 

Měli byste se seznámit s koncepty kontejneru a postup vytvoření fondu služby Batch a úlohu. Příklady kódu pomocí rozhraní Batch .NET a sady SDK pro Python. Vytvořit kontejner s podporou fondy služby Batch a ke spouštění úloh kontejneru, můžete použít také dalších sad SDK a nástroje, včetně webu Azure portal.

## <a name="why-use-containers"></a>Proč používat kontejnery?

Pomocí kontejnerů poskytuje snadný způsob, jak spouštět úlohy Batch bez nutnosti spravovat prostředí a závislosti pro spouštění aplikací. Kontejnery nasazení aplikací jako jednoduchý, přenosných a soběstační jednotky, které můžou běžet v několika různých prostředích. Například kompilují a testují kontejner místně potom nahrajete image kontejneru do registru v Azure nebo jinde. Model nasazení kontejneru zajistí, že prostředí modulu runtime aplikace vždy správně nainstalován a nakonfigurován bez ohledu na hostiteli aplikace. Úlohy kontejneru ve službě Batch můžete taky využít výhod funkcí nekontejnerový úloh, včetně balíčků aplikací a Správa prostředků a výstupních souborů. 

## <a name="prerequisites"></a>Požadavky

* **Verze sady SDK**: The sad SDK služby Batch podporu imagí kontejnerů v době následující verze:
    * Rozhraní batch REST API verze 2017-09-01.6.0
    * Batch .NET SDK verze 8.0.0
    * Batch Python SDK verze 4.0
    * Batch Java SDK verze 3.0
    * Batch SDK pro Node.js verze 3.0

* **Účty**: ve vašem předplatném Azure, budete muset vytvořit účet Batch a volitelně účtu služby Azure Storage.

* **Podporované image virtuálního počítače**: kontejnery jsou podporovány pouze v fondy vytvořené s konfigurací virtuálního počítače z imagí najdete v části "podporované Image virtuálních počítačů." Pokud zadáte vlastní image, naleznete v tématu požadavky v oblasti a důležité informace v následující části [použít spravovanou vlastní image k vytvoření fondu virtuálních počítačů](batch-custom-images.md). 

### <a name="limitations"></a>Omezení

* Služba batch poskytuje podporu RDMA pouze pro kontejnery se spuštěnou ve fondech Linux

* Pro úlohy kontejneru Windows se doporučuje výběr vícejádrovými velikosti virtuálního počítače pro váš fond

## <a name="supported-virtual-machine-images"></a>Imagí podporovaných virtuálních počítačů

Použijte jednu z následujících nepodporuje Windows nebo Linuxové Image k vytvoření fondu virtuálních počítačů výpočetních uzlů pro úlohy kontejneru. Další informace o Image Marketplace, které jsou kompatibilní se službou Batch najdete v tématu [seznamu imagí virtuálních počítačů](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Image Windows

Pro úlohy kontejneru Windows, služba Batch aktuálně podporuje **systému Windows Server 2016 Datacenter s kontejnery** image v Tržišti Azure Marketplace. Ve Windows se podporují pouze imagí kontejnerů Dockeru.

Můžete také vytvořit vlastní Image z virtuálních počítačů Docker a systémem Windows.

### <a name="linux-images"></a>Linuxové Image

Pro úlohy kontejneru Linuxu služba Batch aktuálně podporuje následující Linuxových imagí publikovaných službou Microsoft Azure Batch na webu Azure Marketplace:

* **CentOS pro kontejner fondech Azure Batch**

* **CentOS (s ovladači RDMA) pro kontejner fondech Azure Batch**

* **Server se systémem Ubuntu pro kontejner fondech Azure Batch**

* **Ubuntu serveru (s ovladači RDMA) pro kontejner fondech Azure Batch**

Tyto Image jsou podporovány pouze pro použití ve fondech Azure Batch. Tyto funkce:

* Předem nainstalovaných [Moby](https://github.com/moby/moby) kontejner modulu runtime 

* Předem nainstalovaných ovladačů NVIDIA GPU, zjednodušit nasazení na virtuální počítače Azure řady N-series

* Vaše volba Image s nebo bez předem nainstalovaných ovladačů RDMA. Povolit tyto ovladače uzly fondu pro přístup k síti Azure RDMA, když jsou nasazená v velikosti virtuálních počítačů s podporou RDMA. 

Můžete také vytvořit vlastní Image z virtuálních počítačů Dockeru spuštěném v jednom z Linuxových distribucí, které je kompatibilní s Batch. Pokud se rozhodnete zadat vlastní image Linuxu, přečtěte si pokyny v [použít spravovanou vlastní image k vytvoření fondu virtuálních počítačů](batch-custom-images.md).

Pro podporu Dockeru na vlastní imagi, nainstalujte [Docker Community Edition (CE)](https://www.docker.com/community-edition) nebo [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Další informace pro použití vlastní image Linuxu:

* Abyste mohli využívat výkon GPU velikosti Azure N-series, při použití vlastní image, předem instalace ovladačů NVIDIA. Také je potřeba nainstalovat nástroj modul Docker pro GPU NVIDIA [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Přístup k síti Azure RDMA, použijte pro velikost virtuálního počítače podporující RDMA. Potřebné ovladače RDMA jsou nainstalovány v prostředí HPC CentOS a Ubuntu imagí podporované službou Batch. Další konfigurace může být potřeba ke spouštění úloh MPI. Zobrazit [použití podporující RDMA nebo s podporou grafického procesoru instancí ve fondu Batch](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Konfigurace kontejneru pro fond služby Batch

Pokud chcete povolit fondu služby Batch ke spouštění úloh kontejneru, je nutné zadat [uvolnění](/dotnet/api/microsoft.azure.batch.containerconfiguration) nastavení ve fondu [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) objektu. (Tento článek obsahuje odkazy na referenční dokumentace rozhraní API .NET služby Batch. Odpovídající nastavení [Pythonu služby Batch](/python/api/azure.batch) rozhraní API.)

Kontejner podporující fond můžete vytvořit s nebo bez předem načteného kontejneru obrázků, jak je znázorněno v následujícím příkladu. Proces o přijetí změn (nebo předběžné načtení) umožňuje předběžné načtení Image kontejneru z Docker Hubu nebo jiné registru kontejnerů na Internetu. Pro nejlepší výkon použijte [registr kontejnerů Azure](../container-registry/container-registry-intro.md) ve stejné oblasti jako účet Batch.

Výhodou předběžné načítání imagí kontejneru je, že při prvním spuštění úlohy spuštění nemuseli čekat stáhnout image kontejneru. Konfigurace kontejneru stáhne Image kontejneru do virtuálních počítačů při vytvoření fondu. Úlohy spouštěné ve fondu může poté odkazovat na seznam všech imagí kontejnerů a možnosti spuštění kontejneru.


### <a name="pool-without-prefetched-container-images"></a>Fond bez předem načteného kontejneru obrázků

Chcete-li nakonfigurovat fond kontejneru povoleno bez předem načteného kontejnerové Image, definujte `ContainerConfiguration` a `VirtualMachineConfiguration` objektů, jak je znázorněno v následujícím příkladu Python. Tento příklad používá pro služby Azure Batch z Marketplace image kontejneru fondy Ubuntu Server.


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


### <a name="prefetch-images-for-container-configuration"></a>Předběžné načtení bitové kopie pro konfiguraci kontejneru

Chcete-li předběžné načtení Image kontejnerů ve fondu, přidejte seznam všech imagí kontejnerů (`container_image_names`, v Pythonu) pro `ContainerConfiguration`. 

V následujícím základním příkladu Python ukazuje, jak předběžné načtení ze standardního image kontejneru Ubuntu [Docker Hubu](https://hub.docker.com).

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


Následující C# příklad předpokládá, že chcete předběžné načtení TensorFlow image z [Docker Hubu](https://hub.docker.com). Tento příklad obsahuje spouštěcí úkol, který běží na hostiteli virtuálního počítače na uzly fondu. Můžete spustit spouštěcí úkol na hostiteli, například připojte souborový server, který je přístupný z kontejnerů.

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


### <a name="prefetch-images-from-a-private-container-registry"></a>Předběžné načtení Image v privátním registru kontejneru

Předběžné načtení imagí kontejnerů můžete také zkontrolovat pracovník ostrahy k serveru registru kontejneru soukromého. V následujícím příkladu `ContainerConfiguration` a `VirtualMachineConfiguration` privátní image TensorFlow ze soukromých Azure container registry předběžné načtení objektů. Odkaz na image je stejné jako v předchozím příkladu.

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

Spustit úlohu kontejneru ve fondu povolené kontejneru, zadejte nastavení pro konkrétní kontejner. Nastavení zahrnuje obrázek, který se používá, registru a možnosti spuštění kontejneru.

* Použití `ContainerSettings` vlastnost úlohy třídy ke konfiguraci nastavení pro konkrétní kontejner. Tato nastavení jsou definovány [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) třídy.

* Při spuštění úlohy na Image kontejneru [cloudových úloh](/dotnet/api/microsoft.azure.batch.cloudtask) a [úkol Správce úloh](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) vyžadují nastavení kontejneru. Ale [spouštěcí úkol](/dotnet/api/microsoft.azure.batch.starttask), [úkol přípravy úlohy](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask), a [úkol uvolnění úlohy](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) nevyžadují, aby nastavení kontejneru (to znamená, že můžete spustit v rámci kontextu kontejneru nebo přímo v uzlu).

### <a name="container-task-command-line"></a>Příkazový řádek úkolu kontejneru

Při spuštění kontejneru úloh služby Batch automaticky použije [vytvořit docker](https://docs.docker.com/engine/reference/commandline/create/) příkaz k vytvoření kontejneru pomocí image zadanou v úloze. Batch potom provádění úkolů ovládací prvky v kontejneru. 

Jako s nekontejnerový úkoly služby Batch, nastavíte příkazového řádku pro úlohy kontejneru. Protože služba Batch automaticky vytvoří kontejner, příkazový řádek určuje pouze příkaz nebo příkazy, které se spustí v kontejneru.

Pokud má nakonfigurovanou image kontejneru pro úlohu služby Batch [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) skript, můžete nastavit příkazového řádku k buď použijte výchozí vstupního bodu nebo přepsat: 

* Pokud chcete použít výchozí vstupní bod Image kontejneru, nastavena na prázdný řetězec příkazového řádku úkolu `""`.

* Přepsat výchozí vstupní bod, nebo pokud obrázek nemá ENTRYPOINT, nastavení příkazového řádku vhodné pro kontejneru, například `/app/myapp` nebo `/bin/sh -c python myscript.py`.

Volitelné [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) jsou další argumenty, které poskytnete `docker create` příkazu, služby Batch používá k vytváření a spouštění kontejneru. Například pokud chcete nastavit pracovní adresář pro kontejner, nastavte `--workdir <directory>` možnost. Najdete v článku [vytvořit docker](https://docs.docker.com/engine/reference/commandline/create/) odkaz pro další možnosti.

### <a name="container-task-working-directory"></a>Kontejner úloh pracovní adresář

Úlohu služby Batch kontejner spustí v pracovním adresáři do kontejneru, který je velmi podobný adresáře, který nastaví služby Batch pro běžné úlohy (není kontejner). Všimněte si, že se liší od pracovní adresář [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) Pokud je nakonfigurované v obrázku nebo výchozí kontejner pracovní adresář (`C:\` v kontejneru Windows, nebo `/` v kontejneru Linuxu). 

Kontejner úlohy služby Batch:

* Všechny adresáře rekurzivně níže `AZ_BATCH_NODE_ROOT_DIR` na hostiteli jsou mapovány uzlu (kořenové adresáře služby Azure Batch) do kontejneru
* Všechny proměnné prostředí úkolu se mapují do kontejneru
* Pracovního adresáře úkolu `AZ_BATCH_TASK_WORKING_DIR` na uzlu je stejný jako u běžné úlohy při nastavení a mapovat do kontejneru. 

Tato mapování umožňují pracovat s úlohami kontejneru stejným způsobem jako nekontejnerový úkoly. Například instalace aplikací pomocí balíčků aplikací, přístup k souborům prostředků ze služby Azure Storage, použijte nastavení prostředí úloh a zachovat výstupní soubory úloh po kontejner zastaví.

### <a name="troubleshoot-container-tasks"></a>Řešení potíží s úlohy kontejneru

Pokud vaše úlohy kontejneru neběží podle očekávání, potřebujete získat informace o konfiguraci WORKDIR nebo vstupní Image kontejneru. Pokud chcete zobrazit konfiguraci, spusťte [kontrolovat image dockeru](https://docs.docker.com/engine/reference/commandline/image_inspect/) příkazu. 

V případě potřeby upravte nastavení úlohy kontejneru na základě image:

* Zadejte absolutní cestu v příkazovém řádku úkolu. Pokud na obrázku výchozí vstupní bod se používá pro příkazový řádek úkolu, ujistěte se, že je nastavena na absolutní cestu.

* V možnosti spuštění kontejneru úlohy Změňte pracovní adresář, který odpovídá WORKDIR na obrázku. Například nastavte `--workdir /app`.

## <a name="container-task-examples"></a>Příklady úloh kontejneru

Následující fragment kódu Python ukazuje základní příkazového řádku spuštění v kontejneru vytvoří z image fiktivní získaných z Docker Hubu. Tady `--rm` kontejneru odebere kontejneru po dokončení úlohy a `--workdir` parametr nastaví pracovní adresář. Příkazovém řádku přepíše kontejner vstupního bodu pomocí příkazu jednoduché prostředí, která zapisuje malý soubor do pracovního adresáře úkolu na hostiteli. 

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

Následující příklad jazyka C# ukazuje nastavení základního kontejneru pro úlohu cloudu:

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


## <a name="next-steps"></a>Další postup

* Viz také [Batch loděnice](https://github.com/Azure/batch-shipyard) sadu nástrojů pro snadné nasazování úloh kontejneru v Azure Batch prostřednictvím [loděnice recepty](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Další informace o instalaci a použití Docker CE v Linuxu najdete v článku [Docker](https://docs.docker.com/engine/installation/) dokumentaci.

* Další informace o použití vlastní Image, najdete v části [použít spravovanou vlastní image k vytvoření fondu virtuálních počítačů](batch-custom-images.md).

* Další informace o [Moby projektu](https://mobyproject.org/), rozhraní pro vytváření systémů založených na kontejnerech.