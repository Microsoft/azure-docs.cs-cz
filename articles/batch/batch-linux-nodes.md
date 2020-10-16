---
title: Spuštění systému Linux na výpočetních uzlech virtuálních počítačů
description: Naučte se zpracovávat paralelní výpočetní úlohy na fondech virtuálních počítačů se systémem Linux v Azure Batch.
ms.topic: how-to
ms.date: 06/01/2018
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: 9cbf7dcb5b0f6f43b4fc5d69127e817dceea650e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108061"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Zřizování výpočetních uzlů pro Linux ve fondech Batch

K provozování paralelních výpočetních úloh na virtuálních počítačích se systémem Linux i Windows můžete použít Azure Batch. Tento článek podrobně popisuje, jak vytvořit fondy výpočetních uzlů Linux ve službě Batch pomocí knihoven klienta [Batch Python][py_batch_package] a [Batch .NET][api_net] .

> [!NOTE]
> Balíčky aplikací jsou podporované ve všech fondech služby Batch vytvořených po 5. červenci 2017. Ve fondech služby Batch vytvořených mezi 10. březnem 2016 a 5. červencem 2017 jsou podporované, pouze pokud byl fond vytvořen pomocí konfigurace cloudové služby. Fondy služby Batch vytvořené před 10. březnem 2016 nepodporují balíčky aplikací. Další informace o používání balíčků aplikací k nasazení aplikací do uzlů služby Batch najdete v tématu [Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací služby Batch](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Konfigurace virtuálního počítače
Při vytváření fondu výpočetních uzlů ve službě Batch máte dvě možnosti, ze kterých můžete vybrat velikost uzlu a operační systém: Cloud Services konfigurace a konfigurace virtuálního počítače.

**Konfigurace služby Cloud Services** poskytuje *pouze* výpočetní uzly Windows. Dostupné velikosti výpočetních uzlů jsou uvedené v seznamu [velikosti pro Cloud Services](../cloud-services/cloud-services-sizes-specs.md)a dostupné operační systémy jsou uvedené v části [vydání hostovaného operačního systému Azure a v matici kompatibility SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Když vytvoříte fond, který obsahuje uzly Azure Cloud Services, zadáte velikost uzlu a řadu operačních systémů, které jsou popsány v předchozích uvedených článcích. Pro fondy výpočetních uzlů Windows se Cloud Services nejčastěji používá.

**Konfigurace virtuálního počítače** poskytuje pro výpočetní uzly image Linux i Windows. Dostupné velikosti výpočetních uzlů jsou uvedené ve [velikosti pro virtuální počítače v Azure](../virtual-machines/sizes.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) (Linux) a [velikosti pro virtuální počítače v Azure](../virtual-machines/sizes.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json) (Windows). Při vytváření fondu, který obsahuje uzly Konfigurace virtuálního počítače, je nutné zadat velikost uzlů, odkaz na bitovou kopii virtuálního počítače a SKU agenta uzlu Batch, které mají být nainstalovány v uzlech.

### <a name="virtual-machine-image-reference"></a>Odkaz na image virtuálního počítače

Služba Batch používá k poskytování výpočetních uzlů v konfiguraci virtuálního počítače službu [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md) . Můžete zadat obrázek z [Azure Marketplace][vm_marketplace]nebo zadat vlastní image, kterou jste připravili. Další informace o vlastních bitových kopiích najdete v tématu [Vytvoření fondu pomocí Galerie sdílených imagí](batch-sig-images.md).

Když nakonfigurujete odkaz na image virtuálního počítače, zadáte vlastnosti image virtuálního počítače. Při vytváření odkazu na image virtuálního počítače se vyžadují tyto vlastnosti:

| **Vlastnosti odkazu na obrázek** | **Příklad** |
| --- | --- |
| Publisher |Canonical |
| Nabídka |UbuntuServer |
| SKU |18,04 – LTS |
| Verze |nejnovější |

> [!TIP]
> Další informace o těchto vlastnostech a způsobu vypsání imagí na Marketplace najdete v tématu [navigace a výběr imagí virtuálních počítačů se systémem Linux v Azure pomocí rozhraní příkazového řádku nebo PowerShellu](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Všimněte si, že ne všechny image Marketplace jsou aktuálně kompatibilní se službou Batch. Další informace najdete v tématu [SKU agenta uzlu](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>SKU agenta uzlu
Agent uzlu služby Batch je program, který běží na všech uzlech ve fondu a poskytuje rozhraní příkazového a řídicího prostředí mezi uzlem a službou Batch. Existují různé implementace agenta uzlu, označované jako SKU, pro různé operační systémy. V podstatě při vytváření konfigurace virtuálního počítače nejprve zadáte odkaz na image virtuálního počítače a pak zadáte agenta uzlu, který se má na bitovou kopii nainstalovat. Obvykle je každá SKU agenta uzlu kompatibilní s několika imagemi virtuálních počítačů. Tady je několik příkladů SKU agenta uzlu:

* Batch. Node. Ubuntu 18,04
* Batch. Node. CentOS 7
* Batch. Node. Windows amd64

> [!IMPORTANT]
> Ne všechny image virtuálních počítačů, které jsou k dispozici na webu Marketplace, jsou kompatibilní s aktuálně dostupnými agenty uzlu Batch. Pomocí sad SDK pro Batch můžete zobrazit seznam dostupných SKU agenta uzlu a image virtuálních počítačů, se kterými jsou kompatibilní. Další informace a příklady, jak načíst seznam platných imagí za běhu, najdete v [seznamu imagí virtuálních počítačů](#list-of-virtual-machine-images) dále v tomto článku.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Vytvoření fondu pro Linux: Batch Python
Následující fragment kódu ukazuje příklad použití [klientské knihovny Microsoft Azure Batch pro Python][py_batch_package] k vytvoření fondu výpočetních uzlů Ubuntu serveru. Referenční dokumentaci pro modul Batch v Pythonu najdete na adrese [azure.batch v balíčku][py_batch_docs] o čtení dokumentů.

Tento fragment kódu vytvoří explicitně [element imagereference][py_imagereference] a určí každou jeho vlastnost (Publisher, nabídka, SKU, verze). V produkčním kódu však doporučujeme, abyste pomocí metody [list_supported_images][py_list_supported_images] určili a vybrali z dostupné kombinace obrázků a identifikátorů SKU agenta uzlu za běhu.

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
vm_size = "STANDARD_D2_V3"
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
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Jak už bylo zmíněno dříve, doporučujeme, abyste místo toho, abyste [element imagereference][py_imagereference] vytvořili explicitně, používali metodu [list_supported_images][py_list_supported_images] k dynamickému výběru z aktuálně podporovaných kombinací imagí agent/Marketplace. Následující fragment kódu Python ukazuje, jak použít tuto metodu.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Vytvoření fondu pro Linux: Batch .NET
Následující fragment kódu ukazuje příklad použití klientské knihovny [Batch .NET][nuget_batch_net] k vytvoření fondu výpočetních uzlů Ubuntu serveru. [Referenční dokumentaci k Batch .NET][api_net] najdete na docs.Microsoft.com.

Následující fragment kódu používá [PoolOperations][net_pool_ops]. Metoda [ListSupportedImages][net_list_supported_images] , která se má vybrat ze seznamu aktuálně podporovaného obrázku na webu Marketplace a kombinací SKU agenta uzlu Tato technika je žádoucí, protože se seznam podporovaných kombinací může časem změnit. Nejčastěji se přidávají podporované kombinace.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

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

I když předchozí fragment kódu používá [PoolOperations][net_pool_ops]. [ListSupportedImages][net_list_supported_images] metoda, která dynamicky vypíše a vybere z podporované kombinace obrázků a identifikátorů SKU agenta uzlu (doporučeno), můžete taky nakonfigurovat [element imagereference][net_imagereference] explicitně:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Seznam imagí virtuálních počítačů
Pokud chcete získat seznam všech podporovaných imagí virtuálních počítačů Marketplace pro službu Batch a jejich odpovídajících agentů uzlů, použijte prosím [list_supported_images][py_list_supported_images] (Python), [ListSupportedImages][net_list_supported_images] (Batch .NET) nebo odpovídající rozhraní API v příslušné jazykové sadě SDK dle vašeho výběru.

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
Azure Batch je postavená na technologii Azure Cloud Services a platformě Azure Virtual Machines. Samotná služba Batch je bezplatně nabízená, což znamená, že se vám účtují jenom výpočetní prostředky (a související náklady, které zahrnují), které vaše řešení Batch spotřebují. Když zvolíte **Cloud Services konfiguraci**, bude se vám účtovat na základě [Cloud Services cenové][cloud_services_pricing] struktury. Když zvolíte **konfiguraci virtuálního počítače**, bude se vám účtovat na základě [Virtual Machines cenové][vm_pricing] struktury.

Pokud nasazujete aplikace do vašich dávkových uzlů pomocí [balíčků aplikací](batch-application-packages.md), účtují se vám také Azure Storage prostředky, které vaše balíčky aplikací spotřebovávají.

## <a name="next-steps"></a>Další kroky

[Ukázky kódu Pythonu][github_samples_py] v úložišti [Azure-Batch-Samples][github_samples] na GitHubu obsahují skripty, které ukazují, jak provádět běžné operace s dávkou, jako jsou například fondy, úlohy a vytváření úloh. [Soubor Readme][github_py_readme] , který doprovází ukázky Pythonu, obsahuje podrobnosti o tom, jak nainstalovat požadované balíčky.

[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_mgmt]: /dotnet/api/overview/azure/batch
[api_rest]: /rest/api/batchservice/
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser
[net_imagereference]: /dotnet/api/microsoft.azure.batch.imagereference
[net_list_supported_images]: /dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages
[net_pool_ops]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: /rest/api/batchservice/pool/add
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure.github.io/azure-sdk-for-python/ref/Batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_supported_images]: /python/api/azure-batch/azure.batch.operations.AccountOperations
[vm_marketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/