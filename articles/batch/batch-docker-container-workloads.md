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
ms.date: 02/26/2018
ms.author: danlep
ms.openlocfilehash: fc8af53b0e0cfbe19a6509e8d126646badd0abbb
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="run-container-applications-on-azure-batch"></a>Spustit v Azure Batch – aplikace typu kontejner

Azure Batch umožňuje spouštění a škálování velké počty výpočetních úloh v Azure batch. Dosud spustili úkoly služby Batch přímo na virtuálních počítačích (VM) v rámci fondu Batch, ale teď můžete nastavit fondu služby Batch ke spouštění úloh v kontejnerech Docker.

Použití kontejnerů poskytuje snadný způsob, jak spouštět úlohy Batch nemusíte spravovat balíčky aplikací a závislosti. Kontejnery nasazení aplikací jako lightweight, přenosné, soběstačný jednotky, které můžou běžet v různých prostředích. Můžete například sestavení a testování místně kontejner pak nahrajte image kontejneru na registru v Azure nebo jinde. Model nasazení kontejneru zajistí, že běhové prostředí vaší aplikace je vždy správně nainstalováno a nakonfigurováno, bez ohledu na to, kde hostovat aplikaci. Tento kurz ukazuje, jak vytvořit fond výpočetních uzlů, které podporují spuštěné úlohy kontejneru pomocí .NET SDK služby Batch a jak spouštět úlohy kontejner ve fondu.

Tento článek předpokládá znalost koncepty kontejner Docker a postup vytvoření fondu služby Batch a úlohy pomocí sady .NET SDK. Fragmenty kódu jsou určené pro použití v aplikaci klienta podobně jako [DotNetTutorial ukázka](batch-dotnet-get-started.md), a jsou příklady kódu by potřebujete podporovat aplikace typu kontejner ve službě Batch.


## <a name="prerequisites"></a>Požadavky

* Verze sady SDK: SDK služby Batch podporu kontejneru Image od následující verze:
    * Batch REST API verze 2017-09-01.6.0
    * Batch .NET SDK verze 8.0.0
    * Batch Python SDK verze 4.0
    * Batch Java SDK verze 3.0
    * Batch Node.js SDK verze 3.0

* Účty: Na účtu Azure, musíte vytvořit účet Batch a volitelně účet úložiště Azure.

* Podporované image virtuálního počítače. Kontejnery jsou podporovány pouze ve fondech vytvořené pomocí konfigurace virtuálního počítače z bitové kopie, které jsou podrobně popsané v následující části, "podporováno bitové kopie virtuálních počítačů."

* Pokud jste zadali vlastní image, musí vaše aplikace používat ověřování Azure Active Directory (Azure AD) aby bylo možné spustit na základě kontejneru úlohy. Pokud používáte image Azure Marketplace, nepotřebujete ověřování Azure AD; ověření pomocí sdíleného klíče budou fungovat. Podpora služby Azure AD ve službě Azure Batch je zdokumentovaná v tématu [Ověřování řešení služby Batch pomocí Active Directory](batch-aad-auth.md).


## <a name="supported-virtual-machine-images"></a>Bitové kopie podporovaných virtuálních počítačů

Je třeba zadat Windows nebo Linux bitovou kopii k vytvoření fondu virtuálních počítačů výpočetní uzly.

### <a name="windows-images"></a>Bitových kopií systému Windows

Pro úlohy Windows kontejneru Batch aktuálně podporuje vlastní Image, které vytvoříte z virtuálních počítačů spuštěných Docker v systému Windows, nebo můžete použít Windows Server 2016 Datacenter s kontejnery bitovou kopii z Azure Marketplace. Tento image je kompatibilní s `batch.node.windows amd64` uzlu agenta ID SKU. Typ kontejneru podporována je aktuálně omezená na Docker.

### <a name="linux-images"></a>Bitové kopie systému Linux

Pro úlohy kontejneru Linux, Batch aktuálně podporuje pouze vlastních bitových kopií, které vytvoříte z virtuálních počítačů spuštěných na následující Linuxových distribucích Docker: Ubuntu 16.04 LTS nebo CentOS 7.3. Pokud zvolíte možnost zadejte svoji vlastní image Linux, postupujte podle pokynů v [použít k vytvoření fondu virtuálních počítačů spravovaných vlastní image](batch-custom-images.md).

Můžete použít [Docker Community Edition (CE)](https://www.docker.com/community-edition) nebo [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Pokud chcete využít výhod výkonu GPU velikostí virtuálního počítače VS nebo Azure NC, budete muset nainstalovat ovladače NVIDIA na bitovou kopii. Navíc musíte nainstalovat a spustit nástroj modulu Docker pro NVIDIA grafickými procesory, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

Pro přístup k síti Azure RDMA, použijte virtuální počítače následující formáty: A8, A9, H16r, H16mr nebo NC24r. Potřebné ovladače RDMA jsou nainstalované v CentOS 7.3 HPC a Ubuntu 16.04 LTS bitové kopie z Azure Marketplace. Ke spuštění úlohy MPI může být nutná další konfigurace. V tématu [použití podporující RDMA nebo grafický procesor s podporou instancí ve fondu Batch](batch-pool-compute-intensive-sizes.md).


## <a name="limitations"></a>Omezení

* Batch poskytuje podporu RDMA pouze pro kontejnery systémem Linux fondy.


## <a name="authenticate-using-azure-active-directory"></a>Ověření pomocí služby Azure Active Directory

Pokud používáte vlastní image virtuálního počítače na vytvoření fondu Batch, klientská aplikace musí ověřit pomocí integrovaného ověřování Azure AD (nefunguje ověření pomocí sdíleného klíče). Než spustíte aplikaci, zkontrolujte, zda že je zaregistrovat ve službě Azure AD k vytvoření identity pro něj a určit její oprávnění k ostatním aplikacím.

Navíc pokud použijete vlastní image virtuálního počítače, budete muset udělit IAM řízení přístupu k aplikaci pro přístup image virtuálního počítače. Na portálu Azure, otevřete **všechny prostředky**, vyberte bitovou kopii kontejner a z **přístup k ovládacímu prvku (IAM)** části okna bitové kopie, a klikněte na tlačítko **přidat**. V **přidat oprávnění** okno, zadejte **Role**v **přiřadit přístup**, vyberte **uživatele Azure AD, skupinu nebo aplikaci**, potom v  **Vyberte** zadejte název aplikace.

V aplikaci předat tokenu ověření Azure AD při vytváření klienta Batch pomocí [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_), jak je popsáno v [řešení služby Batch ověření pomocí služby Active Directory](batch-aad-auth.md).


## <a name="reference-a-vm-image-for-pool-creation"></a>Referenční image virtuálního počítače pro vytvoření fondu

V kódu aplikace zadejte odkaz na obrázek virtuálního počítače, který má použít při vytváření výpočetní uzly fondu. To uděláte tak, že vytvoříte [elementu ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) objektu. Můžete určit obrázek, který má používat v jednom z následujících způsobů:

* Pokud používáte vlastní image, zadejte identifikátor prostředku Azure Resource Manageru pro bitovou kopii virtuálního počítače. Identifikátor image má formát cesty, jak je znázorněno v následujícím příkladu:

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    Chcete-li získat toto ID bitové kopie z portálu Azure, otevřete **všechny prostředky**, vyberte vlastní bitovou kopii a z **přehled** části okna bitové kopie, zkopírujte cestu ve **ID prostředku**.

* Pokud používáte [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?page=1&subcategories=windows-based) bitovou kopii, zadejte skupinu parametrů, které popisují bitovou kopii: vydavatele, typ nabídky, SKU a verzi bitové kopie, jak je uvedeno v [seznam bitové kopie virtuálních počítačů](batch-linux-nodes.md#list-of-virtual-machine-images):

  ```csharp
  // Provide a reference to an Azure Marketplace image for
  // "Windows Server 2016 Datacenter with Containers"
  ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-with-Containers",
    version: "latest");
  ```


## <a name="container-configuration-for-batch-pool"></a>Konfiguraci kontejneru pro fondu služby Batch

Fondu Batch je kolekce výpočetních uzlů, na kterých služba Batch provádí úkoly v úloze. Při vytváření fondu, zadejte v konfiguraci virtuálních počítačů pro výpočetní uzly. [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) obsahuje odkaz na objekt [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) objektu. Pokud chcete povolit kontejneru zatížení ve fondu, zadejte `ContainerConfiguration` nastavení. Konfigurace virtuálního počítače je také umístění referenční image a image uzlu agenta SKU ID, jak je znázorněno v následujících příkladech.

Existuje několik možností pro vytvoření fondu. Fond můžete vytvořit s nebo bez bitových kopií prefetched kontejneru. 

Proces přijetí změn (nebo předběžné načtení) umožňuje předběžné načtení Image kontejneru buď z úložiště Docker Hub nebo jiný kontejner registru na Internetu. Výhodou prefetching kontejneru bitové kopie je, že při prvním spuštění úlohy systémem nemají čekání na bitovou kopii kontejner ke stažení. Konfiguraci kontejneru vrátí kontejner bitové kopie na virtuální počítače při vytvoření fondu. Úlohy, které spustí ve fondu pak můžete odkazovat v seznamu kontejneru bitových kopií a spusťte kontejner možnosti.



### <a name="pool-without-prefetched-container-images"></a>Fond bez prefetched kontejneru obrázků

Chcete-li konfigurovat fond bez bitových kopií prefetched kontejneru, definovat `ContainerConfiguration` a `VirtualMachineConfiguration` objekty, jak je znázorněno v následujícím příkladu. Toto a následující příklady předpokládají, že používáte vlastní image Ubuntu 16.04 LTS k modulu Docker nainstalována.

```csharp
// Specify container configuration
ContainerConfiguration containerConfig = new ContainerConfiguration();

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

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-for-container-configuration"></a>Předběžné načtení Image pro konfiguraci kontejneru

Pokud chcete předběžné načtení Image kontejner ve fondu, přidejte seznam kontejneru bitové kopie (`containerImageNames`) do `ContainerConfiguration`a pojmenujte seznamu obrázků. V následujícím příkladu se předpokládá, že používáte vlastní image Ubuntu 16.04 LTS, předběžné načtení image TensorFlow z [úložiště Docker Hub](https://hub.docker.com), a spusťte TensorFlow v spouštěcí úkol.

```csharp
// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Define container settings
TaskContainerSettings startTaskContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu");
StartTask startTaskContainer = new StartTask(
    CommandLine: "<docker-image-command-line>",
    TaskContainerSettings: startTaskContainerSettings);

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Předběžné načtení bitové kopie z registru kontejner privátní

Můžete také předběžné načtení Image kontejneru tak, že k registru serveru privátní kontejneru. V následujícím příkladu `ContainerConfiguration` a `VirtualMachineConfiguration` objekty používat vlastní image Ubuntu 16.04 LTS a předběžné načtení privátního TensorFlow image z registru privátní kontejner Azure.

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

// Commit pool creation
pool.Commit();
```


## <a name="container-settings-for-the-task"></a>Kontejner nastavení úlohy

Při nastavování úlohy ke spuštění na výpočetních uzlech, musíte zadat nastavení pro konkrétní kontejner například úlohy možnosti, bitové kopie na použití a registru.

Konfigurace nastavení specifických pro kontejner, použijte vlastnost ContainerSettings třídy úloh. Tato nastavení jsou definovány [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) třídy.

Pokud spustíte úlohy v kontejneru Image, [cloudu úloh](/dotnet/api/microsoft.azure.batch.cloudtask) a [úkolu Správce úloh](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) požadovat nastavení kontejneru. Ale [spouštěcí úkol](/dotnet/api/microsoft.azure.batch.starttask), [úkol přípravy úlohy](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask), a [úkol uvolnění úlohy](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) nevyžadují nastavení kontejneru (to znamená, že můžete spustit v kontextu kontejneru nebo přímo v uzlu).

Při konfiguraci nastavení kontejneru, všechny adresáře rekurzivně níže `AZ_BATCH_NODE_ROOT_DIR` (kořenové adresáře Azure Batch na uzlu) jsou mapovány do kontejneru, všechny úlohy prostředí proměnné jsou mapovány na kontejneru a příkazového řádku úkolu je provést v kontejneru.

Příklad kódu v [předběžné načtení Image pro konfiguraci kontejneru](#prefetch-images-for-container-configuration) vám ukázal, jak zadat konfiguraci kontejneru pro spouštěcí úkol. Následující příklad kódu ukazuje, jak zadat konfiguraci kontejneru pro úlohu cloudu:

```csharp
// Simple task command

string cmdLine = "<my-command-line>";

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
