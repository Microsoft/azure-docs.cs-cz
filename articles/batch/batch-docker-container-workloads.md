---
title: Úlohy kontejnerů – Azure Batch
description: Zjistěte, jak spouštět a škálovat aplikace z ibi kontejnerů v Azure Batch. Vytvořte fond výpočetních uzlů, které podporují spouštění úloh kontejneru.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254841"
---
# <a name="run-container-applications-on-azure-batch"></a>Spuštění kontejnerových aplikací v Azure Batch

Azure Batch umožňuje spouštět a škálovat velký počet úloh dávkového výpočetního prostředí v Azure. Dávkové úlohy lze spustit přímo na virtuálních počítačích (uzly) ve fondu dávek, ale můžete také nastavit fond dávek pro spouštění úloh v kontejnerech kompatibilních s Dockerem na uzlech. Tento článek ukazuje, jak vytvořit fond výpočetních uzlů, které podporují spuštěné úlohy kontejneru a pak spustit úlohy kontejneru ve fondu.

Měli byste být obeznámeni s koncepty kontejnerů a jak vytvořit fond dávek a úlohu. Příklady kódu používají sady Batch .NET a Python SDK. Můžete také použít jiné dávkové sady SDK a nástroje, včetně portálu Azure, k vytvoření fondů dávek s povoleným kontejnerem a ke spuštění úloh kontejnerů.

## <a name="why-use-containers"></a>Proč používat kontejnery?

Použití kontejnerů poskytuje snadný způsob, jak spustit dávkové úlohy bez nutnosti spravovat prostředí a závislosti pro spouštění aplikací. Kontejnery nasazují aplikace jako lehké, přenosné a soběstačné jednotky, které lze spustit v několika různých prostředích. Například sestavení a testování kontejneru místně, pak nahrát image kontejneru do registru v Azure nebo jinde. Model nasazení kontejneru zajišťuje, že runtime prostředí vaší aplikace je vždy správně nainstalován a nakonfigurován všude, kde hostujete aplikaci. Úlohy založené na kontejnerech v aplikaci Batch mohou také využívat funkce úloh bez kontejnerů, včetně balíčků aplikací a správy souborů prostředků a výstupních souborů.

## <a name="prerequisites"></a>Požadavky

* **Verze sady SDK**: Sady Batch SDK podporují image kontejnerů v následujících verzích:
    * Dávkové rozhraní REST API verze 2017-09-01.6.0
    * Dávková sada SDK .NET verze 8.0.0
    * Dávková sada Python SDK verze 4.0
    * Dávková sada Java SDK verze 3.0
    * Sada SDK verze 3.0 aplikace Batch Node.js

* **Účty**: Ve vašem předplatném Azure je potřeba vytvořit dávkový účet a volitelně účet Azure Storage.

* **Podporovaná image virtuálního počítače**: Kontejnery jsou podporovány jenom ve fondech vytvořených pomocí konfigurace virtuálního počítače, z bitových kopií podrobně popsaných v následující části "Podporované image virtuálních počítačů". Pokud zadáte vlastní image, podívejte se na důležité informace v následující části a požadavky v [Použití spravované vlastní image k vytvoření fondu virtuálních počítačů](batch-custom-images.md).

### <a name="limitations"></a>Omezení

* Batch poskytuje podporu RDMA pouze pro kontejnery spuštěné ve fondech Linuxu

* Pro úlohy kontejnerů Windows se doporučuje zvolit velikost vícejádrového virtuálního počítače pro váš fond.

## <a name="supported-virtual-machine-images"></a>Podporované image virtuálních strojů

Pomocí jedné z následujících podporovaných iložek Windows nebo Linuxu vytvořte fond výpočetních uzlů virtuálních her pro úlohy kontejnerů. Další informace o inacích Marketplace, které jsou kompatibilní s Batch, najdete v [seznamu iobrazů virtuálních strojů](batch-linux-nodes.md#list-of-virtual-machine-images).

### <a name="windows-support"></a>Podpora systému Windows

Batch podporuje bitové kopie serveru systému Windows, které mají označení podpory kontejneru. Obvykle tyto názvy sku obrázku `-with-containers` jsou `-with-containers-smalldisk`suffixed s nebo . Kromě toho [rozhraní API pro seznam všech podporovaných bitových kopií v Batch](batch-linux-nodes.md#list-of-virtual-machine-images) bude označení `DockerCompatible` schopnosti, pokud image podporuje kontejnery Dockeru.

Můžete také vytvořit vlastní image z virtuálních počítačů se systémem Docker v systému Windows.

### <a name="linux-support"></a>Podpora Linuxu

Pro úlohy kontejnerů Linuxbatch aktuálně podporuje následující ibi Linux uveřejnil Microsoft Azure Batch na Azure Marketplace bez nutnosti vlastní image.

#### <a name="vm-sizes-without-rdma"></a>Velikosti virtuálních počítače bez RDMA

- Vydavatel:`microsoft-azure-batch`
  - Nabízejí:`centos-container`
  - Nabízejí:`ubuntu-server-container`

#### <a name="vm-sizes-with-rdma"></a>Velikosti virtuálních počítače s RDMA

- Vydavatel:`microsoft-azure-batch`
  - Nabízejí:`centos-container-rdma`
  - Nabízejí:`ubuntu-server-container-rdma`

Tyto image jsou podporované jenom pro použití ve fondech Azure Batch a jsou zaměřené na spuštění kontejneru Dockeru. Jsou vybaveny:

* Předinstalovaný runtime kontejneru [Moby](https://github.com/moby/moby) kompatibilní s Dockerem

* Předinstalované ovladače NVIDIA GPU a runtime kontejneru NVIDIA pro zjednodušení nasazení na virtuálních počítačích Azure řady N

* Předinstalovaná/předkonfigurovaná bitová kopie s podporou velikostí virtuálních virtuálních zařízení `-rdma`Infiniband RDMA pro obrazy s příponou aplikace . V současné době tyto obrázky nepodporují velikosti virtuálních her SR-IOV IB/RDMA.

Můžete také vytvořit vlastní image z virtuálních počítačů se systémem Docker na jedné z distribucí Linuxu, která je kompatibilní s Batch. Pokud se rozhodnete poskytnout vlastní bitovou kopii Linuxu, přečtěte si pokyny [k vytvoření fondu virtuálních počítačů pomocí spravované vlastní bitové kopie](batch-custom-images.md).

Pro podporu Dockeru na vlastní image nainstalujte [Docker Community Edition (CE)](https://www.docker.com/community-edition) nebo [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Další důležité informace o použití vlastní bitové kopie Linuxu:

* Chcete-li při použití vlastní bitové kopie využít výhod výkonu GPU u velikostí řady Azure N, předinstalujte ovladače NVIDIA. Také je třeba nainstalovat Nástroj Docker Engine pro GRAFICKÉ PROCESORY NVIDIA, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Chcete-li získat přístup k síti Azure RDMA, použijte velikost virtuálního počítače podporujícího RDMA. Potřebné ovladače RDMA jsou nainstalovány v obrázcích CentOS HPC a Ubuntu podporovaných batch. Ke spuštění úloh MPI může být potřeba další konfigurace. Viz [Použití instancí podporujících technologii RDMA nebo GPU ve fondu dávek](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Konfigurace kontejneru pro fond dávek

Chcete-li povolit fond dávek ke spuštění úloh kontejnerů, musíte zadat nastavení [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) v objektu [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) fondu. (Tento článek obsahuje odkazy na odkaz rozhraní API batch .NET. Odpovídající nastavení jsou v [rozhraní API dávkového](/python/api/overview/azure/batch) pythonu.)

Můžete vytvořit fond s povoleným kontejnerem s nebo bez přednačtených iobrazek kontejneru, jak je znázorněno v následujících příkladech. Proces vyžádat (nebo předběžného načtení) umožňuje předem načíst ibi kontejnerů z dockerového centra nebo jiného registru kontejnerů na Internetu. Pro dosažení nejlepšího výkonu použijte [registr kontejnerů Azure](../container-registry/container-registry-intro.md) ve stejné oblasti jako účet Batch.

Výhodou předběžného načítání iobrazek kontejneru je, že při prvním spuštění úloh nemusí čekat na stažení image kontejneru. Konfigurace kontejneru vytáhne image kontejneru do virtuálních počítačů při vytvoření fondu. Úlohy, které běží ve fondu pak odkazovat na seznam iimages kontejneru a možnosti spuštění kontejneru.


### <a name="pool-without-prefetched-container-images"></a>Fond bez přednačtených iontů kontejnerů

Chcete-li nakonfigurovat fond s povoleným kontejnerem bez přednačtených iobrazek kontejneru, definujte `ContainerConfiguration` a `VirtualMachineConfiguration` objekty, jak je znázorněno v následujícím příkladu Pythonu. Tento příklad používá image fondů kontejnerů Ubuntu Server pro Azure Batch z marketplace.


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


### <a name="prefetch-images-for-container-configuration"></a>Předběžné načtení bitových kopií pro konfiguraci kontejneru

Chcete-li předem načíst image kontejneru ve`container_image_names`fondu, přidejte `ContainerConfiguration`seznam ikcí kontejneru ( , v Pythonu) do .

Následující základní příklad Pythonu ukazuje, jak předem načíst standardní image kontejneru Ubuntu z [Docker Hubu](https://hub.docker.com).

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


Následující příklad Jazyka C# předpokládá, že chcete přednačíst image TensorFlow z [Docker Hubu](https://hub.docker.com). Tento příklad zahrnuje počáteční úlohu, která běží v hostiteli virtuálního počítači na uzlech fondu. Můžete spustit počáteční úlohu v hostiteli, například připojit souborový server, který je přístupný z kontejnerů.

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


### <a name="prefetch-images-from-a-private-container-registry"></a>Předběžné načtení bitových kopií z registru soukromého kontejneru

Můžete také předem načíst image kontejneru ověřováním na soukromý server registru kontejneru. V následujícím příkladu `ContainerConfiguration` `VirtualMachineConfiguration` a objekty předem načíst image privátní TensorFlow z registru privátní kontejner Azure. Odkaz na obrázek je stejný jako v předchozím příkladu.

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

Chcete-li spustit úlohu kontejneru ve fondu s povolenou kontejnerem, zadejte nastavení specifická pro kontejner. Nastavení zahrnují bitovou kopii, která má být používána, registru a možností spuštění kontejneru.

* `ContainerSettings` Vlastnost tříd úlohy slouží ke konfiguraci nastavení specifických pro kontejner. Tato nastavení jsou definována [taskcontainersettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) třídy. Všimněte `--rm` si, že možnost kontejneru nevyžaduje další `--runtime` možnost, protože je postaráno batch.

* Pokud spustíte úlohy na ifotky kontejneru, [úlohy cloudu](/dotnet/api/microsoft.azure.batch.cloudtask) a [úlohy správce úloh](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) vyžadují nastavení kontejneru. [Počáteční úloha](/dotnet/api/microsoft.azure.batch.starttask), [úloha přípravy úlohy](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)a [úloha uvolnění úlohy](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) však nevyžadují nastavení kontejneru (to znamená, že mohou být spuštěny v kontextu kontejneru nebo přímo v uzlu).

### <a name="container-task-command-line"></a>Příkazový řádek úlohy kontejneru

Když spustíte úlohu kontejneru, Batch automaticky použije příkaz [vytvoření dockeru](https://docs.docker.com/engine/reference/commandline/create/) k vytvoření kontejneru pomocí bitové kopie zadané v úloze. Batch pak řídí provádění úloh v kontejneru.

Stejně jako u dávkových úloh bez kontejneru nastavíte příkazový řádek pro úlohu kontejneru. Protože Batch automaticky vytvoří kontejner, příkazový řádek určuje pouze příkaz nebo příkazy, které budou spuštěny v kontejneru.

Pokud je bitová kopie kontejneru pro dávkovou úlohu nakonfigurována pomocí skriptu [ENTRYPOINT,](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) můžete příkazový řádek nastavit tak, aby používal výchozí ENTRYPOINT nebo jej přepsal:

* Chcete-li použít výchozí entrypoint bitové kopie kontejneru, `""`nastavte příkazový řádek úlohy na prázdný řetězec .

* Chcete-li přepsat výchozí entrypoint nebo pokud obraz nemá ENTRYPOINT, nastavte příkazový řádek vhodný `/app/myapp` pro `/bin/sh -c python myscript.py`kontejner, například nebo .

Volitelné [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) jsou další argumenty, které zadáte příkazu, `docker create` který Batch používá k vytvoření a spuštění kontejneru. Chcete-li například nastavit pracovní adresář pro `--workdir <directory>` kontejner, nastavte možnost. Viz [docker vytvořit](https://docs.docker.com/engine/reference/commandline/create/) odkaz pro další možnosti.

### <a name="container-task-working-directory"></a>Pracovní adresář úloh kontejneru

Úloha dávkového kontejneru se provede v pracovním adresáři v kontejneru, který je velmi podobný adresáři Batch, který nastavuje pro běžnou úlohu (bez kontejneru). Všimněte si, že tento pracovní adresář se liší od [WORKDIR,](https://docs.docker.com/engine/reference/builder/#workdir) `C:\` pokud je nakonfigurován `/` v bitové kopii nebo výchozí pracovní adresář kontejneru ( v kontejneru systému Windows nebo v kontejneru Linuxu).

Pro úlohu dávkového kontejneru:

* Všechny adresáře rekurzivně `AZ_BATCH_NODE_ROOT_DIR` pod na hostitelském uzlu (kořen adresáře Azure Batch) jsou mapovány do kontejneru
* Všechny proměnné prostředí úloh jsou mapovány do kontejneru
* Pracovní adresář `AZ_BATCH_TASK_WORKING_DIR` úloh y v uzlu je nastaven stejně jako u běžné úlohy a mapován do kontejneru.

Tato mapování umožňují pracovat s úkoly kontejneru v podstatě stejným způsobem jako úkoly bez kontejneru. Můžete například instalovat aplikace pomocí balíčků aplikací, přistupovat k souborům prostředků z Azure Storage, používat nastavení prostředí úloh a zachovat výstupní soubory úloh po zastavení kontejneru.

### <a name="troubleshoot-container-tasks"></a>Poradce při potížích s úlohami kontejneru

Pokud úloha kontejneru neběží podle očekávání, budete pravděpodobně muset získat informace o konfiguraci workdir nebo entrypoint image kontejneru. Chcete-li zobrazit konfiguraci, spusťte příkaz [kontrola image dockeru.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

V případě potřeby upravte nastavení úlohy kontejneru na základě obrázku:

* Zadejte absolutní cestu v příkazovém řádku úkolu. Pokud se pro příkazový řádek úkolu použije výchozí entrypoint obrázku, ujistěte se, že je nastavena absolutní cesta.

* V možnostech spuštění kontejneru úlohy změňte pracovní adresář tak, aby odpovídal workdirv bitové kopii. Například set `--workdir /app`.

## <a name="container-task-examples"></a>Příklady úloh kontejneru

Následující fragment Pythonu zobrazuje základní příkazový řádek spuštěný v kontejneru vytvořeném z fiktivní image vytažené z Docker Hubu. Zde možnost `--rm` kontejneru odebere kontejner po dokončení úkolu `--workdir` a možnost nastaví pracovní adresář. Příkazový řádek přepíše kontejner ENTRYPOINT jednoduchým příkazem prostředí, který zapíše malý soubor do pracovního adresáře úlohy na hostiteli.

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

Následující příklad jazyka C# ukazuje základní nastavení kontejneru pro úlohu cloudu:

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

* Přečtěte si také sadu nástrojů [Dávková loděnice,](https://github.com/Azure/batch-shipyard) kde najdete snadné nasazení úloh kontejnerů v azure batch prostřednictvím [receptů loděnice](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Další informace o instalaci a používání Dockeru CE na Linuxu najdete v dokumentaci [dockeru.](https://docs.docker.com/engine/installation/)

* Další informace o používání vlastních bitových kopií najdete [v tématu Vytvoření fondu virtuálních počítačů pomocí spravované vlastní bitové kopie](batch-custom-images.md).

* Další informace o [projektu Moby](https://mobyproject.org/), což je rámec pro vytváření kontejnerových systémů.
