---
title: Na virtuálním počítači spusťte Linuxových výpočetních uzlů – Azure Batch | Dokumentace Microsoftu
description: Zjistěte, jak zpracovat paralelní výpočetní úlohy na fondech virtuálních počítačů s Linuxem ve službě Azure Batch.
services: batch
documentationcenter: python
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 713583a6a184a583145c610b4e014f56941efa4c
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113507"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Zřízení výpočetních uzlů s Linuxem ve fondech služby Batch

Azure Batch můžete použít ke spouštění paralelních výpočetních úloh na virtuální počítače s Linuxem a Windows. Tento článek podrobně popisuje, jak vytvořit fondy výpočetních uzlů s Linuxem ve službě Batch pomocí [Pythonu služby Batch] [ py_batch_package] a [Batch .NET] [ api_net]klientské knihovny.

> [!NOTE]
> Balíčky aplikací jsou podporované ve všech fondech služby Batch vytvořených po 5. červenci 2017. Ve fondech služby Batch vytvořených mezi 10. březnem 2016 a 5. červencem 2017 jsou podporované, pouze pokud byl fond vytvořen pomocí konfigurace cloudové služby. Fondy služby Batch vytvořené před 10. březnem 2016 nepodporují balíčky aplikací. Další informace o používání balíčků aplikací k nasazení aplikací do uzlů služby Batch najdete v tématu [Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací služby Batch](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Konfigurace virtuálního počítače
Při vytváření fondu výpočetních uzlů ve službě Batch, máte dvě možnosti, ze kterého se má vybrat velikost uzlu a operační systém: Konfigurace služby Cloud Services a konfiguraci virtuálního počítače.

**Konfigurace služby Cloud Services** poskytuje *pouze* výpočetní uzly Windows. Velikosti dostupných výpočetních uzlů, které jsou uvedeny v [velikosti pro Cloud Services](../cloud-services/cloud-services-sizes-specs.md), a dostupných operačních systémů uvedených v [SDK kompatibility přehled verzí hostovaného operačního systému Azure a](../cloud-services/cloud-services-guestos-update-matrix.md). Při vytváření fondu, který obsahuje uzly Azure Cloud Services, můžete zadat velikost uzlu a řada operačního systému, které jsou popsány v výše uvedených článcích. Pro fondy Windows výpočetních uzlů, Cloud Services se nejčastěji používá.

**Konfigurace virtuálního počítače** nabízí Image Linux i Windows pro výpočetní uzly. Velikosti dostupných výpočetních uzlů, které jsou uvedeny v [velikosti virtuálních počítačů v Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) a [velikosti virtuálních počítačů v Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Při vytváření fondu, který obsahuje uzly z konfigurace virtuálního počítače, je nutné zadat velikost uzlů, referenční image virtuálního počítače a služby Batch agenta uzlu SKU na uzly instalovat.

### <a name="virtual-machine-image-reference"></a>Referenční image virtuálního počítače
Použití služby Batch [škálovací sady virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) poskytnout výpočetních uzlů v konfiguraci virtuálního počítače. Můžete zadat image z [Azure Marketplace][vm_marketplace], nebo vlastní Image, kterou jste připravili. Další informace o vlastních imagích najdete v části [vytvoření fondu s použitím vlastní image](batch-custom-images.md).

Když nakonfigurujete referenční image virtuálního počítače, můžete zadat vlastnosti na Image virtuálních počítačů. Následující vlastnosti jsou požadovány při vytvoření referenční image virtuálního počítače:

| **Vlastnosti odkazu na Image** | **Příklad** |
| --- | --- |
| Vydavatel |Canonical |
| Nabídka |UbuntuServer |
| Skladová jednotka (SKU) |14.04.4-LTS |
| Verze |nejnovější |

> [!TIP]
> Další informace o těchto vlastností a jak zobrazit seznam imagí Marketplace v [vyhledání a výběr imagí virtuálních počítačů Linux v Azure pomocí Powershellu nebo rozhraní příkazového řádku](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Všimněte si, že ne všechny Image Marketplace aktuálně kompatibilní službou Batch. Další informace najdete v tématu [SKU agenta uzlu](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>SKU agenta uzlu
Agent uzlu Batch je program, který se spustí na každém uzlu ve fondu a poskytuje rozhraní příkazu a řízení mezi uzlem a služby Batch. Existují různé implementace agenta uzlu, označované jako skladové položky, pro různé operační systémy. V podstatě při vytváření konfigurace virtuálního počítače, nejprve zadat referenční image virtuálního počítače a zadejte agenta uzlu k instalaci na obrázku. Obvykle každého agenta uzlu SKU je kompatibilní s více imagí virtuálních počítačů. Tady je pár příkladů SKU agenta uzlu:

* batch.Node.Ubuntu 14.04
* batch.Node.centos 7
* batch.Node.Windows amd64

> [!IMPORTANT]
> Ne všechny Image virtuálních počítačů, které jsou k dispozici na webu Marketplace jsou kompatibilní s aktuálně k dispozici agentů uzlů služby Batch. Pomocí sad SDK služby Batch můžete zobrazit seznam SKU agenta uzlu k dispozici a imagí virtuálních počítačů, ke kterým jsou kompatibilní. Zobrazit [Image seznam virtuálních počítačů](#list-of-virtual-machine-images) dále v tomto článku pro další informace a příklady toho, jak načíst seznam platných obrázků za běhu.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Vytvoření fondu s Linuxem: Pythonu služby Batch
Následující fragment kódu ukazuje příklad, jak používat [klientské knihovny Microsoft Azure Batch pro Python] [ py_batch_package] a vytvořit tak fond Ubuntu Server výpočetních uzlů. Referenční dokumentace pro modul Pythonu služby Batch najdete v [azure.batch balíčku] [ py_batch_docs] na webu Docs pro čtení.

Tento fragment kódu vytvoří [ImageReference] [ py_imagereference] explicitně a určuje jeho vlastnosti (vydavatele, nabídky, SKU, verze). V produkčním kódu, ale doporučujeme použít [list_node_agent_skus] [ py_list_skus] metodu pro určení a vyberte z dostupných image a uzel agenta SKU kombinace za běhu.

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
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Jak už bylo zmíněno dříve, doporučujeme místo vytvoření [ImageReference] [ py_imagereference] explicitně, je použít [list_node_agent_skus] [ py_list_skus] metoda dynamicky vybrat si z kombinace image agenta/Marketplace aktuálně podporované uzlu. Následující fragment kódu Python ukazuje, jak použít tuto metodu.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Vytvoření fondu s Linuxem: rozhraní Batch .NET
Následující fragment kódu ukazuje příklad, jak používat [Batch .NET] [ nuget_batch_net] klientské knihovny a vytvořit tak fond Ubuntu Server výpočetních uzlů. Můžete najít [referenční dokumentaci rozhraní Batch .NET] [ api_net] na webu docs.microsoft.com.

Následující kód používá fragment kódu [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] pro výběr ze seznamu aktuálně podporované kombinace SKU agenta Marketplace pro image a uzel. Tato technika je žádoucí, protože může čas od času změnit seznam podporovaných kombinací. Podporované kombinace nejčastěji, jsou přidány.

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

Ačkoli předchozí fragment kódu používá [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] metoda dynamicky seznam a vyberte z podporované image a uzel agenta SKU kombinace (doporučeno), můžete taky nakonfigurovat [ImageReference] [ net_imagereference] explicitně:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Seznam imagí virtuálních počítačů
V následující tabulce jsou uvedeny Marketplace Image virtuálních počítačů, které jsou kompatibilní s dostupných agentů uzlů služby Batch, poslední aktualizace v tomto článku. Je důležité si uvědomit, že tento seznam není konečný, protože imagí a agenty uzlu může přidat nebo odebrat kdykoli. Doporučujeme vám, že vaše aplikace Batch a služby vždy používejte [list_node_agent_skus] [ py_list_skus] (Python) nebo [ListNodeAgentSkus] [ net_list_skus] () Batch .NET) a určit, vyberte z aktuálně dostupné skladové položky.

> [!WARNING]
> V následujícím seznamu může kdykoli změnit. Vždy používat **SKU agenta uzlu seznamu** metody, které jsou k dispozici v rozhraní API služby Batch do seznamu kompatibilních virtuálních počítačů a SKU agenta uzlu při spouštění dávkových úloh.
>
>

| **Publisher** | **Nabídka** | **Image SKU** | **Verze** | **Agenta uzlu SKU ID** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| dávka | vykreslování centos73 | vykreslování | nejnovější | batch.Node.centos 7 |
| dávka | vykreslování windows2016 | vykreslování | nejnovější | batch.Node.Windows amd64 |
| Canonical | UbuntuServer | 16.04-LTS | nejnovější | batch.Node.Ubuntu 16.04 |
| Canonical | UbuntuServer | 14.04.5-LTS | nejnovější | batch.Node.Ubuntu 14.04 |
| credativ | Debian | 9 | nejnovější | batch.Node.debian 9 |
| credativ | Debian | 8 | nejnovější | batch.Node.debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | nejnovější | batch.Node.centos 7 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | nejnovější | batch.Node.Windows amd64 |
| Microsoft-azure-batch | centos kontejneru | 7 – 4 | nejnovější | batch.Node.centos 7 |
| Microsoft-azure-batch | centos. kontejner rdma | 7 – 4 | nejnovější | batch.Node.centos 7 |
| Microsoft-azure-batch | Ubuntu-server-container | 16-04-lts | nejnovější | batch.Node.Ubuntu 16.04 |
| Microsoft-azure-batch | Ubuntu server kontejneru rdma | 16-04-lts | nejnovější | batch.Node.Ubuntu 16.04 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-smalldisk | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016 Datacenter s kontejnery | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter-smalldisk | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008 R2 SP1 smalldisk | nejnovější | batch.Node.Windows amd64 |
| OpenLogic | CentOS | 7.4 | nejnovější | batch.Node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | nejnovější | batch.Node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | nejnovější | batch.Node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | nejnovější | batch.Node.centos 7 |
| Oracle | Oracle Linux | 7.4 | nejnovější | batch.Node.centos 7 |
| SUSE | SLES-HPC | 12 SP2 | nejnovější | batch.Node.opensuse 42.1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Připojte se k uzly s Linuxem pomocí protokolu SSH
Při vývoji nebo při řešení potíží možná bude nezbytné pro přihlášení k uzlům ve fondu. Na rozdíl od Windows výpočetních uzlů nemůžete použít protokol RDP (Remote Desktop) pro připojení k uzly s Linuxem. Místo toho povolí služba Batch přístup přes SSH na každém uzlu pro vzdálená připojení.

Následující fragment kódu Python vytvoří uživatele na každém uzlu ve fondu, která je potřebná pro připojení ke vzdálené. Potom zobrazí informace o připojení (SSH secure shell) pro každý uzel.

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

Tady je ukázkový výstup předchozího kódu pro fond, který obsahuje čtyři uzly s Linuxem:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Místo hesla můžete zadat veřejný klíč SSH při vytváření uživatele v uzlu. V sadě Python SDK používat **ssh_public_key** parametru u [ComputeNodeUser][py_computenodeuser]. V rozhraní .NET, použijte [ComputeNodeUser][net_computenodeuser].[ SshPublicKey] [ net_ssh_key] vlastnost.

## <a name="pricing"></a>Ceny
Služba Azure Batch je postavené na technologii Azure Cloud Services a Azure Virtual Machines. Samotné služby Batch se nabízí zdarma, což znamená, že se vám účtovat jenom výpočetní prostředky, že váš účet Batch spotřebuje. Při výběru **konfigurace služby Cloud Services**, bude se vám účtovat na základě [ceny Cloud Services] [ cloud_services_pricing] struktury. Při výběru **konfigurace virtuálního počítače**, bude se vám účtovat na základě [ceny Virtual Machines] [ vm_pricing] struktury. 

Jestliže nasadíte aplikace do uzlů služby Batch pomocí [balíčky aplikací](batch-application-packages.md), se vám také účtovat prostředky služby Azure Storage, balíčky aplikací používat. Obecně platí jsou minimální náklady na úložiště Azure. 

## <a name="next-steps"></a>Další postup

[Ukázky kódu Pythonu] [ github_samples_py] v [azure-batch-samples] [ github_samples] obsahují skripty, které ukazují, jak provádět úložišti na Githubu běžné operace služby Batch, jako je například fondu, úloh a vytváření úlohy. [README] [ github_py_readme] , který doprovází Pythonu ukázky obsahuje podrobnosti o tom, jak nainstalovat požadované balíčky.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure-sdk-for-python.readthedocs.io/batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: https://docs.microsoft.com/python/api/azure.batch.models.computenodeuser
[py_imagereference]: https://docs.microsoft.com/python/api/azure.mgmt.batch.models.imagereference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
