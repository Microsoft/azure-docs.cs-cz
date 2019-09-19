---
title: Provozovat Linux na výpočetních uzlech virtuálních počítačů – Azure Batch | Microsoft Docs
description: Naučte se zpracovávat paralelní výpočetní úlohy na fondech virtuálních počítačů se systémem Linux v Azure Batch.
services: batch
documentationcenter: python
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc1e80433a09d8a56f2900ab6b24dd3804db56ac
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086776"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Zřizování výpočetních uzlů pro Linux ve fondech Batch

K provozování paralelních výpočetních úloh na virtuálních počítačích se systémem Linux i Windows můžete použít Azure Batch. Tento článek podrobně popisuje, jak vytvořit fondy výpočetních uzlů Linux ve službě Batch pomocí knihoven klienta [Batch Python][py_batch_package] a [Batch .NET][api_net] .

> [!NOTE]
> Balíčky aplikací jsou podporované ve všech fondech služby Batch vytvořených po 5. červenci 2017. Ve fondech služby Batch vytvořených mezi 10. březnem 2016 a 5. červencem 2017 jsou podporované, pouze pokud byl fond vytvořen pomocí konfigurace cloudové služby. Fondy služby Batch vytvořené před 10. březnem 2016 nepodporují balíčky aplikací. Další informace o používání balíčků aplikací k nasazení aplikací do uzlů služby Batch najdete v tématu [Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací služby Batch](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Konfigurace virtuálního počítače
Při vytváření fondu výpočetních uzlů ve službě Batch máte dvě možnosti, ze kterých můžete vybrat velikost uzlu a operační systém: Konfigurace Cloud Services a konfigurace virtuálního počítače.

**Konfigurace služby Cloud Services** poskytuje *pouze* výpočetní uzly Windows. Dostupné velikosti výpočetních uzlů jsou uvedené v seznamu [velikosti pro Cloud Services](../cloud-services/cloud-services-sizes-specs.md)a dostupné operační systémy jsou uvedené v části [vydání hostovaného operačního systému Azure a v matici kompatibility SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Když vytvoříte fond, který obsahuje uzly Azure Cloud Services, zadáte velikost uzlu a řadu operačních systémů, které jsou popsány v předchozích uvedených článcích. Pro fondy výpočetních uzlů Windows se Cloud Services nejčastěji používá.

**Konfigurace virtuálního počítače** poskytuje pro výpočetní uzly image Linux i Windows. Dostupné velikosti výpočetních uzlů jsou uvedené ve [velikosti pro virtuální počítače v Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) a [velikosti pro virtuální počítače v Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Při vytváření fondu, který obsahuje uzly Konfigurace virtuálního počítače, je nutné zadat velikost uzlů, odkaz na bitovou kopii virtuálního počítače a SKU agenta uzlu Batch, které mají být nainstalovány v uzlech.

### <a name="virtual-machine-image-reference"></a>Odkaz na image virtuálního počítače
Služba Batch používá k poskytování výpočetních uzlů v konfiguraci virtuálního počítače službu [Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) . Můžete zadat obrázek z [Azure Marketplace][vm_marketplace]nebo zadat vlastní image, kterou jste připravili. Další informace o vlastních bitových kopiích najdete v tématu [Vytvoření fondu s vlastní imagí](batch-custom-images.md).

Když nakonfigurujete odkaz na image virtuálního počítače, zadáte vlastnosti image virtuálního počítače. Při vytváření odkazu na image virtuálního počítače se vyžadují tyto vlastnosti:

| **Vlastnosti odkazu na obrázek** | **Příklad** |
| --- | --- |
| Vydavatel |Canonical |
| Nabídka |UbuntuServer |
| SKU |14.04.4-LTS |
| Version |latest |

> [!TIP]
> Další informace o těchto vlastnostech a způsobu vypsání imagí na Marketplace najdete v tématu [navigace a výběr imagí virtuálních počítačů se systémem Linux v Azure pomocí](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)rozhraní příkazového řádku nebo PowerShellu. Všimněte si, že ne všechny image Marketplace jsou aktuálně kompatibilní se službou Batch. Další informace najdete v tématu [SKU agenta uzlu](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>SKU agenta uzlu
Agent uzlu služby Batch je program, který běží na všech uzlech ve fondu a poskytuje rozhraní příkazového a řídicího prostředí mezi uzlem a službou Batch. Existují různé implementace agenta uzlu, označované jako SKU, pro různé operační systémy. V podstatě při vytváření konfigurace virtuálního počítače nejprve zadáte odkaz na image virtuálního počítače a pak zadáte agenta uzlu, který se má na bitovou kopii nainstalovat. Obvykle je každá SKU agenta uzlu kompatibilní s několika imagemi virtuálních počítačů. Tady je několik příkladů SKU agenta uzlu:

* batch.node.ubuntu 14.04
* batch.node.centos 7
* Batch. Node. Windows amd64

> [!IMPORTANT]
> Ne všechny image virtuálních počítačů, které jsou k dispozici na webu Marketplace, jsou kompatibilní s aktuálně dostupnými agenty uzlu Batch. Pomocí sad SDK pro Batch můžete zobrazit seznam dostupných SKU agenta uzlu a image virtuálních počítačů, se kterými jsou kompatibilní. Další informace a příklady, jak načíst seznam platných imagí za běhu, najdete v [seznamu imagí virtuálních počítačů](#list-of-virtual-machine-images) dále v tomto článku.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Vytvoření fondu pro Linux: Batch Python
Následující fragment kódu ukazuje příklad použití [klientské knihovny Microsoft Azure Batch pro Python][py_batch_package] k vytvoření fondu výpočetních uzlů Ubuntu serveru. Referenční dokumentaci pro modul Batch v Pythonu najdete v tématu o tom, jak přečíst [balíček v balíčku Azure. Batch][py_batch_docs] .

Tento fragment kódu vytvoří explicitně [element imagereference][py_imagereference] a určí každou jeho vlastnost (Publisher, nabídka, SKU, verze). V produkčním kódu však doporučujeme použít metodu [list_node_agent_skus][py_list_skus] k určení a výběru z dostupné kombinace obrázků a agentů SKU agenta uzlu za běhu.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="14.04.2-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Jak už bylo zmíněno dříve, doporučujeme namísto explicitního vytváření [element imagereference][py_imagereference] použít metodu [list_node_agent_skus][py_list_skus] k dynamickému výběru z aktuálně podporovaných kombinací imagí agent/Marketplace. Následující fragment kódu Python ukazuje, jak použít tuto metodu.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(
    agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id=ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Vytvoření fondu pro Linux: Batch .NET
Následující fragment kódu ukazuje příklad použití klientské knihovny [Batch .NET][nuget_batch_net] k vytvoření fondu výpočetních uzlů Ubuntu serveru. [Referenční dokumentaci k Batch .NET][api_net] najdete na docs.Microsoft.com.

Následující fragment kódu používá [PoolOperations][net_pool_ops]. Metoda [ListNodeAgentSkus][net_list_skus] , která se má vybrat ze seznamu aktuálně podporovaného obrázku na webu Marketplace a kombinací SKU agenta uzlu Tato technika je žádoucí, protože se seznam podporovaných kombinací může časem změnit. Nejčastěji se přidávají podporované kombinace.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

I když předchozí fragment kódu používá [PoolOperations][net_pool_ops]. [ListNodeAgentSkus][net_list_skus] metoda, která dynamicky vypíše a vybere z podporované kombinace obrázků a identifikátorů SKU agenta uzlu (doporučeno), můžete taky nakonfigurovat [element imagereference][net_imagereference] explicitně:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Seznam imagí virtuálních počítačů
Následující tabulka obsahuje seznam imagí virtuálních počítačů Marketplace, které jsou kompatibilní s dostupnými agenty uzlu dávky při poslední aktualizaci tohoto článku. Je důležité si uvědomit, že tento seznam není konečný, protože image a agenti uzlů můžete kdykoli přidat nebo odebrat. Doporučujeme, aby vaše aplikace a služby Batch vždy používaly [list_node_agent_skus][py_list_skus] (Python) nebo [ListNodeAgentSkus][net_list_skus] (Batch .NET) k určení a výběru z aktuálně dostupných SKU.

> [!WARNING]
> Následující seznam se může kdykoli změnit. V rozhraních API služby Batch vždy použijte k dispozici metody **seznamu SKU agenta uzlu list** a při spuštění úloh služby Batch uveďte kompatibilní SKU virtuálních počítačů a agentů uzlů.
>
>

| **Publisher** | **Nabídka** | **SKU image** | **Verze** | **ID SKU agenta uzlu** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| dávka | rendering-centos73 | vykreslování | latest | batch.node.centos 7 |
| dávka | rendering-windows2016 | vykreslování | latest | Batch. Node. Windows amd64 |
| Canonical | UbuntuServer | 16.04-LTS | latest | batch.node.ubuntu 16.04 |
| Canonical | UbuntuServer | 14.04.5-LTS | latest | batch.node.ubuntu 14.04 |
| credativ | Debian | 9 | latest | Batch. Node. debian 9 |
| credativ | Debian | 8 | latest | Batch. Node. Debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | latest | batch.node.centos 7 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | latest | Batch. Node. Windows amd64 |
| microsoft-azure-batch | centos-container | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | centos-container-rdma | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | ubuntu-server-container | 16-04 – LTS | latest | batch.node.ubuntu 16.04 |
| microsoft-azure-batch | ubuntu-server-container-rdma | 16-04 – LTS | latest | batch.node.ubuntu 16.04 |
| MicrosoftWindowsServer | WindowsServer | 2016 – Datacenter | latest | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-smalldisk | latest | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-with-Containers | latest | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | latest | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | latest | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | latest | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter-smalldisk | latest | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | latest | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008 – R2-SP1 – smalldisk | latest | Batch. Node. Windows amd64 |
| OpenLogic | CentOS | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | latest | batch.node.centos 7 |
| Oracle | Oracle – Linux | 7.4 | latest | batch.node.centos 7 |
| SUSE | SLES-HPC | 12-SP2 | latest | Batch. Node. openSUSE 42,1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Připojení k uzlům se systémem Linux pomocí protokolu SSH
Během vývoje nebo při řešení potíží se může stát, že se přihlásíte k uzlům ve fondu. Na rozdíl od výpočetních uzlů Windows nemůžete použít protokol RDP (Remote Desktop Protocol) (RDP) pro připojení k uzlům se systémem Linux. Služba Batch místo toho povoluje přístup SSH na každém uzlu pro vzdálené připojení.

Následující fragment kódu Pythonu vytvoří uživatele na každém uzlu ve fondu, který je vyžadován pro vzdálené připojení. Pak vytiskne informace o připojení zabezpečeného prostředí (SSH) pro každý uzel.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Tady je ukázkový výstup pro předchozí kód pro fond, který obsahuje čtyři uzly Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Místo hesla můžete zadat veřejný klíč SSH při vytváření uživatele v uzlu. V sadě Python SDK použijte parametr **ssh_public_key** v [ComputeNodeUser][py_computenodeuser]. V rozhraní .NET použijte [ComputeNodeUser][net_computenodeuser]. Vlastnost [SshPublicKey][net_ssh_key]

## <a name="pricing"></a>Ceny
Azure Batch je postavená na technologii Azure Cloud Services a platformě Azure Virtual Machines. Samotná služba Batch je bezplatně nabízená, což znamená, že se vám účtují jenom výpočetní prostředky, které spotřebovávají vaše řešení Batch. Když zvolíte **Cloud Services konfiguraci**, bude se vám účtovat na základě [Cloud Services cenové][cloud_services_pricing] struktury. Když zvolíte **konfiguraci virtuálního počítače**, bude se vám účtovat na základě [Virtual Machines cenové][vm_pricing] struktury. 

Pokud nasazujete aplikace do vašich dávkových uzlů pomocí [balíčků aplikací](batch-application-packages.md), účtují se vám také Azure Storage prostředky, které vaše balíčky aplikací spotřebovávají. Obecně platí, že Azure Storage náklady jsou minimální. 

## <a name="next-steps"></a>Další kroky

[Ukázky kódu Pythonu][github_samples_py] v úložišti [Azure-Batch-Samples][github_samples] na GitHubu obsahují skripty, které ukazují, jak provádět běžné operace s dávkou, jako jsou například fondy, úlohy a vytváření úloh. [Soubor Readme][github_py_readme] , který doprovází ukázky Pythonu, obsahuje podrobnosti o tom, jak nainstalovat požadované balíčky.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser?view=azure-dotnet
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure-sdk-for-python.readthedocs.io/batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_skus]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
