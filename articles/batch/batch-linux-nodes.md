---
title: Na virtuálním počítači spusťte Linuxových výpočetních uzlů - Azure Batch | Microsoft Docs
description: Zjistěte, jak zpracovat paralelní výpočty úlohy na fondech virtuálních počítačích s Linuxem v Azure Batch.
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
ms.openlocfilehash: abb822483253fc5fce0e76afc2628806fe4485d8
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801758"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Zřídit Linux výpočetních uzlů ve fondech Batch

Azure Batch můžete spouštět úlohy paralelní výpočty u virtuálních počítačů Linux a Windows. Tento článek popisuje, jak vytvořit fondy Linuxových výpočetních uzlů ve službě Batch pomocí obou [Batch Python] [ py_batch_package] a [Batch .NET] [ api_net] knihovny klienta.

> [!NOTE]
> Balíčky aplikací jsou podporované ve všech fondech služby Batch vytvořených po 5. červenci 2017. Ve fondech služby Batch vytvořených mezi 10. březnem 2016 a 5. červencem 2017 jsou podporované, pouze pokud byl fond vytvořen pomocí konfigurace cloudové služby. Fondy služby Batch vytvořené před 10. březnem 2016 nepodporují balíčky aplikací. Další informace o používání balíčků aplikací k nasazení aplikací do uzlů služby Batch najdete v tématu [Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací služby Batch](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Konfigurace virtuálního počítače
Když vytvoříte fond výpočetních uzlů ve službě Batch, máte dvě možnosti, ze kterého můžete vybrat velikost uzlu a operační systém: Konfigurace cloudových služeb a konfigurace virtuálního počítače.

**Konfigurace služby Cloud Services** poskytuje *pouze* výpočetní uzly Windows. K dispozici výpočetní uzel velikosti jsou uvedeny v [velikosti pro cloudové služby](../cloud-services/cloud-services-sizes-specs.md), a dostupných operačních systémů, jsou uvedeny v [verze hostovaného operačního systému Azure a kompatibilních sad SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Když vytvoříte fond, který obsahuje uzly Azure Cloud Services, je třeba zadat velikost uzlu řada operačních systémů, které jsou popsány v výše uvedených článcích. Pro fondy Windows výpočetní uzly, se nejčastěji používá cloudové služby.

**Konfigurace virtuálního počítače** poskytuje, Linux a Windows Image pro výpočetní uzly. K dispozici výpočetní uzel velikosti jsou uvedeny v [velikosti virtuálních počítačů v Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) a [velikosti virtuálních počítačů v Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Když vytvoříte fond, který obsahuje uzly konfigurace virtuálního počítače, je nutné zadat velikost uzlů, odkaz na obrázek virtuálního počítače a agenta uzlu Batch SKU být nainstalovány na uzlu.

### <a name="virtual-machine-image-reference"></a>Odkaz na obrázek virtuálního počítače
Použití služby Batch [sady škálování virtuálního počítače](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) zajistit výpočetní uzly v konfiguraci virtuálního počítače. Můžete zadat bitovou kopii [Azure Marketplace][vm_marketplace], nebo zadejte vlastní obrázek, který jste připravili. Další informace o vlastních imagí najdete v tématu [vytvoření fondu pomocí vlastní image](batch-custom-images.md).

Když konfigurujete odkaz bitové kopie virtuálního počítače, můžete zadat vlastnosti na bitové kopie virtuálního počítače. Následující vlastnosti jsou požadovány při vytvoření odkaz na obrázek virtuálního počítače:

| **Vlastnosti referenční bitové kopie** | **Příklad** |
| --- | --- |
| Vydavatel |Canonical |
| Nabídka |UbuntuServer |
| Skladová jednotka (SKU) |14.04.4-LTS |
| Verze |nejnovější |

> [!TIP]
> Další informace o těchto vlastností a jak zobrazit Marketplace obrázků v [vyhledání a vyberte Image virtuálních počítačů Linux v Azure pomocí rozhraní CLI nebo Powershellu](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Upozorňujeme, že ne všechny bitové kopie Marketplace jsou momentálně kompatibilní s Batch. Další informace najdete v tématu [uzlu agenta SKU](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Uzel agenta SKU
Agent uzlu Batch je program, který běží na každém uzlu ve fondu a poskytuje rozhraní příkazu a řízení mezi uzlu a služby Batch. Existují různé implementace uzlu agenta, označuje jako SKU, pro různé operační systémy. V podstatě při vytváření konfigurace virtuálního počítače, nejprve zadat odkaz na obrázek virtuálního počítače a pak zadejte uzlu agenta k instalaci na bitovou kopii. Obvykle každého uzlu agenta SKU je kompatibilní s více bitových kopií virtuálního počítače. Tady je několik příkladů uzlu agenta SKU:

* batch.Node.Ubuntu 14.04
* batch.Node.centos 7
* batch.Node.Windows amd64

> [!IMPORTANT]
> Ne všechny bitové kopie virtuálních počítačů, které jsou k dispozici na webu Marketplace jsou kompatibilní s aktuálně k dispozici agenty uzlu Batch. Pomocí sady SDK služby Batch seznam agent k dispozici uzel SKU a bitové kopie virtuálních počítačů, ke kterým jsou kompatibilní. Najdete v článku [bitové kopie seznamu virtuálních počítačů](#list-of-virtual-machine-images) dále v tomto článku Další příklady a další informace o tom, jak načíst seznam platných obrázků za běhu.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Vytvoření fondu Linux: Batch Python
Následující fragment kódu ukazuje příklad použití [Microsoft Azure Batch klientské knihovny pro jazyk Python] [ py_batch_package] vytvořit fond Ubuntu Server výpočetních uzlů. Referenční dokumentace pro modulu Batch Python najdete na [azure.batch balíček] [ py_batch_docs] na dokumentaci pro čtení.

Vytvoří tento fragment kódu [elementu ImageReference] [ py_imagereference] explicitně a určí, každý z jeho vlastnosti (vydavatel, nabídku, SKU a verzi). V produkčním kódu, ale doporučujeme použít [list_node_agent_skus] [ py_list_skus] metoda, abyste zjistili a vyberte z dostupných bitové kopie a uzlu agenta SKU kombinace za běhu.

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

Jak je uvedeno nahoře, doporučujeme místo vytvoření [elementu ImageReference] [ py_imagereference] explicitně, použijte [list_node_agent_skus] [ py_list_skus] metoda dynamicky vybrat z kombinace bitovou kopii agenta nebo Marketplace aktuálně podporované uzlu. Následující fragment kódu Python ukazuje, jak použít tuto metodu.

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

## <a name="create-a-linux-pool-batch-net"></a>Vytvoření fondu Linux: Batch .NET
Následující fragment kódu ukazuje příklad použití [Batch .NET] [ nuget_batch_net] klientské knihovny vytvořit fond Ubuntu Server výpočetních uzlů. Můžete najít [Batch .NET referenční dokumentaci k nástroji] [ api_net] na docs.microsoft.com.

Následující kód používá fragment kódu [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] metoda vyberte ze seznamu aktuálně podporované kombinace SKU agenta Marketplace pro bitové kopie a uzlu. Tento postup je žádoucí, protože seznam podporovaných kombinací může občas změnit. Nejčastěji jsou přidány podporovaných kombinací.

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

I když fragmentu předchozí používá [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] metodu pro dynamicky seznam a vyberte z podporované bitové kopie a uzlu agenta SKU kombinace (doporučeno), můžete také nakonfigurovat [elementu ImageReference] [ net_imagereference] explicitně:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Seznam bitové kopie virtuálních počítačů
Následující tabulka uvádí Marketplace Image virtuálních počítačů, které jsou kompatibilní s dostupných agentů uzlu Batch při poslední aktualizace v tomto článku. Je důležité si uvědomit, že tento seznam není spolehlivý, protože bitové kopie a agenty uzlu může přidat nebo odebrat kdykoli. Doporučujeme vaší aplikací a služeb Batch vždy použít [list_node_agent_skus] [ py_list_skus] (Python) nebo [ListNodeAgentSkus] [ net_list_skus] () Batch .NET) k určení a vyberte z aktuálně dostupné edice.

> [!WARNING]
> V následujícím seznamu může kdykoli změnit. Vždy nutné použít **agent uzlu seznamu SKU** metody, které jsou k dispozici v rozhraní API služby Batch k zobrazení seznamu kompatibilní virtuální počítač a uzlu agenta SKU při spuštění úlohy Batch.
>
>

| **Publisher** | **Nabídka** | **Obrázek SKU** | **Verze** | **Uzel agenta SKU ID** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| dávka | vykreslování centos73 | Vykreslování | nejnovější | batch.Node.centos 7 |
| dávka | vykreslování windows2016 | Vykreslování | nejnovější | batch.Node.Windows amd64 |
| Canonical | UbuntuServer | 16.04-LTS | nejnovější | batch.Node.Ubuntu 16.04 |
| Canonical | UbuntuServer | 14.04.5-LTS | nejnovější | batch.Node.Ubuntu 14.04 |
| Credativ | Debian | 9 | nejnovější | batch.Node.debian 9 |
| Credativ | Debian | 8 | nejnovější | batch.Node.debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | nejnovější | batch.Node.centos 7 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | nejnovější | batch.Node.Windows amd64 |
| Microsoft-azure-batch | kontejner centos | 7 – 4 | nejnovější | batch.Node.centos 7 |
| Microsoft-azure-batch | centos. kontejneru rdma | 7 – 4 | nejnovější | batch.Node.centos 7 |
| Microsoft-azure-batch | Ubuntu server – kontejner | 16-04-lts | nejnovější | batch.Node.Ubuntu 16.04 |
| Microsoft-azure-batch | Ubuntu server kontejneru rdma | 16-04-lts | nejnovější | batch.Node.Ubuntu 16.04 |
| MicrosoftWindowsServer | WindowsServer | 2016 Datacenter | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016. Datacenter smalldisk | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016 datového centra s kontejnery | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012 – Datacenter-smalldisk | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | nejnovější | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008 R2 SP1 smalldisk | nejnovější | batch.Node.Windows amd64 |
| OpenLogic | CentOS | 7.4 | nejnovější | batch.Node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | nejnovější | batch.Node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | nejnovější | batch.Node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | nejnovější | batch.Node.centos 7 |
| Oracle | Oracle Linux | 7.4 | nejnovější | batch.Node.centos 7 |
| SUSE | SLES-HPC | 12 SP2 | nejnovější | batch.Node.opensuse 42.1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Připojení k Linuxových uzlů pomocí protokolu SSH
Během vývoje nebo při řešení potíží možná bude nutné se přihlásit k uzly ve fondu. Na rozdíl od Windows výpočetní uzly nemůžete použít protokol RDP (Remote Desktop) pro připojení k Linuxových uzlů. Místo toho služba Batch umožňuje přístup k SSH na každém uzlu vzdáleného připojení.

Následující fragment kódu Python vytvoří uživatele na každém uzlu ve fondu, který je vyžadován pro připojení ke vzdálené. Potom zobrazí informace o připojení zabezpečené shell (SSH) pro každý uzel.

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

Namísto hesla můžete zadat veřejný klíč SSH při vytváření uživatele na uzlu. V sadě SDK pro Python, použijte **ssh_public_key** parametr na [ComputeNodeUser][py_computenodeuser]. V rozhraní .NET, použijte [ComputeNodeUser][net_computenodeuser].[ Parametru SshPublicKey] [ net_ssh_key] vlastnost.

## <a name="pricing"></a>Ceny
Azure Batch je založený na technologii cloudových služeb Azure a virtuálních počítačích Azure. Služba Batch samotný se nabízí zadarmo, což znamená, že budou účtovat pouze pro výpočetní prostředky, vaše řešení Batch využívat. Pokud vyberete **konfigurace cloudových služeb**, budou se vám účtovat na základě [ceny cloudové služby] [ cloud_services_pricing] struktury. Pokud vyberete **konfigurace virtuálního počítače**, budou se vám účtovat na základě [ceny virtuálních počítačů] [ vm_pricing] struktury. 

Pokud nasazujete aplikace na uzly Batch pomocí [balíčky aplikací](batch-application-packages.md), se účtují poplatky za prostředky Azure Storage, aby balíčky aplikací používat. Obecně platí jsou minimální, náklady na úložiště Azure. 

## <a name="next-steps"></a>Další postup
### <a name="batch-python-tutorial"></a>Kurz k Batch Pythonu
Pro více podrobný kurz o tom, jak pracovat s Batch pomocí Python, podívejte se na [Začínáme s klientem Azure Batch Python](batch-python-tutorial.md). Jeho doprovodné [ukázka kódu] [ github_samples_pyclient] zahrnuje podpůrná funkce `get_vm_config_for_distro`, který ukazuje další technika, jak získat konfiguraci virtuálního počítače.

### <a name="batch-python-code-samples"></a>Ukázek kódu služby batch Python
[Ukázky kódu jsou Python] [ github_samples_py] v [azure-batch-samples] [ github_samples] úložišti na Githubu obsahovat skripty, které ukazují, jak provádět běžné operace Batch, například fond, úlohy a vytváření úlohy. [README] [ github_py_readme] doprovodný Python ukázky obsahuje podrobnosti o tom, jak nainstalovat požadované balíčky.

### <a name="batch-forum"></a>Fórum služby Batch
[Fóru služby Azure Batch] [ forum] na webu MSDN je skvělým místem popisují Batch a klást otázky týkající se služby. Užitečné pro čtení "připnutý" účtuje a zveřejněte svoje otázky, kterým dochází při sestavování řešení Batch.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
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
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
