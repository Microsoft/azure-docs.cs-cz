---
title: Spuštění systému Linux na výpočetních uzlech virtuálních počítačů
description: Naučte se zpracovávat paralelní výpočetní úlohy na fondech virtuálních počítačů se systémem Linux v Azure Batch.
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: c711ec0d035b9b59ec7628a51fe3cff26de358bc
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683696"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Zřizování výpočetních uzlů pro Linux ve fondech Batch

K provozování paralelních výpočetních úloh na virtuálních počítačích se systémem Linux i Windows můžete použít Azure Batch. Tento článek podrobně popisuje, jak vytvořit fondy výpočetních uzlů Linux ve službě Batch pomocí knihoven klienta [Batch Python](https://pypi.python.org/pypi/azure-batch) a [Batch .NET](/dotnet/api/microsoft.azure.batch) . 

## <a name="virtual-machine-configuration"></a>Konfigurace virtuálního počítače

Při vytváření fondu výpočetních uzlů ve službě Batch máte dvě možnosti, ze kterých můžete vybrat velikost uzlu a operační systém: Cloud Services konfigurace a konfigurace virtuálního počítače. Fondy [konfigurací virtuálních počítačů](nodes-and-pools.md#virtual-machine-configuration) se skládají z virtuálních počítačů Azure, které se dají vytvořit z imagí Linux nebo Windows. Když vytváříte fond s konfigurací virtuálního počítače, zadáte [dostupnou velikost výpočetního uzlu](../virtual-machines/sizes.md), odkaz na bitovou kopii virtuálního počítače, který se má nainstalovat na uzly, a SKU agenta uzlu služby Batch (program, který běží na jednotlivých uzlech a poskytuje rozhraní mezi uzlem a službou Batch).

### <a name="virtual-machine-image-reference"></a>Odkaz na image virtuálního počítače

Služba Batch používá k poskytování výpočetních uzlů v konfiguraci virtuálního počítače službu [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md) . Můžete zadat obrázek z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)nebo [použít galerii sdílených imagí k přípravě vlastní image](batch-sig-images.md).

Když vytvoříte odkaz na image virtuálního počítače, musíte zadat následující vlastnosti:

| **Vlastnost odkazu na obrázek** | **Příklad** |
| --- | --- |
| Publisher |Canonical |
| Nabídka |UbuntuServer |
| Skladová položka |18,04 – LTS |
| Verze |nejnovější |

> [!TIP]
> Další informace o těchto vlastnostech a o tom, jak zadat image z Marketplace, najdete v článku o [hledání imagí virtuálních počítačů Linux v Azure Marketplace pomocí Azure CLI](../virtual-machines/linux/cli-ps-findimage.md). Všimněte si, že některé image Marketplace nejsou aktuálně kompatibilní se službou Batch.

### <a name="list-of-virtual-machine-images"></a>Seznam imagí virtuálních počítačů

Ne všechny image na Marketplace jsou kompatibilní s aktuálně dostupnými agenty uzlu Batch. Pokud chcete zobrazit seznam všech podporovaných imagí virtuálních počítačů Marketplace pro službu Batch a jejich odpovídajících SKU agenta uzlu, použijte [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) (Python), [ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) (Batch .NET) nebo odpovídající rozhraní API v jiné jazykové sadě SDK.

### <a name="node-agent-sku"></a>SKU agenta uzlu

[Agent uzlu služby Batch](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) je program, který běží na všech uzlech ve fondu a poskytuje rozhraní příkazového a řídicího prostředí mezi uzlem a službou Batch. Existují různé implementace agenta uzlu, označované jako SKU, pro různé operační systémy. V podstatě při vytváření konfigurace virtuálního počítače nejprve zadáte odkaz na image virtuálního počítače a pak zadáte agenta uzlu, který se má na bitovou kopii nainstalovat. Obvykle je každá SKU agenta uzlu kompatibilní s několika imagemi virtuálních počítačů. Tady je několik příkladů SKU agenta uzlu:

- Batch. Node. Ubuntu 18,04
- Batch. Node. CentOS 7
- Batch. Node. Windows amd64

## <a name="create-a-linux-pool-batch-python"></a>Vytvoření fondu pro Linux: Batch Python

Následující fragment kódu ukazuje příklad použití [klientské knihovny Microsoft Azure Batch pro Python](https://pypi.python.org/pypi/azure-batch) k vytvoření fondu výpočetních uzlů Ubuntu serveru. Další podrobnosti o modulu Batch Python najdete v [referenční dokumentaci](/python/api/overview/azure/batch).

Tento fragment kódu vytvoří explicitně [element imagereference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) a určí každou jeho vlastnost (Publisher, nabídka, SKU, verze). V produkčním kódu však doporučujeme použít metodu [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) k výběru z dostupné kombinace obrázku a kombinací SKU agenta uzlu za běhu.

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
# virtual machine image to install on the nodes
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent
# to install on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Jak už bylo zmíněno dříve, doporučujeme použít metodu [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) k dynamickému výběru z aktuálně podporované kombinace pro Image agent/Marketplace (místo explicitního vytváření [element imagereference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) ). Následující fragment kódu Python ukazuje, jak použít tuto metodu.

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
# reference and the Batch node agent to be installed on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Vytvoření fondu pro Linux: Batch .NET

Následující fragment kódu ukazuje příklad použití klientské knihovny [Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) k vytvoření fondu výpočetních uzlů Ubuntu serveru. Další informace o službě Batch .NET najdete v [referenční dokumentaci](/dotnet/api/microsoft.azure.batch).

Následující fragment kódu používá metodu [PoolOperations. ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) k výběru ze seznamu aktuálně podporovaného obrázku na webu Marketplace a kombinací SKU agenta uzlu. Tato technika se doporučuje, protože se seznam podporovaných kombinací může časem změnit. Nejčastěji se přidávají podporované kombinace.

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

Přestože předchozí fragment kódu používá metodu [PoolOperations. istSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) k dynamickému vypsání a výběru z podporované kombinace obrázků a identifikátorů SKU agenta uzlu (doporučeno), můžete také explicitně nakonfigurovat [element imagereference](/dotnet/api/microsoft.azure.batch.imagereference) :

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

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

Tento kód bude mít výstup podobný následujícímu příkladu. V takovém případě fond obsahuje čtyři uzly Linux.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Místo hesla můžete zadat veřejný klíč SSH při vytváření uživatele v uzlu. V sadě Python SDK použijte parametr **ssh_public_key** v [ComputeNodeUser](/python/api/azure-batch/azure.batch.models.computenodeuser). V rozhraní .NET použijte vlastnost [ComputeNodeUser. SshPublicKey](/dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey) .

## <a name="pricing"></a>Ceny

Azure Batch je postavená na technologii Azure Cloud Services a platformě Azure Virtual Machines. Samotná služba Batch je bezplatně nabízená, což znamená, že se vám účtují jenom výpočetní prostředky (a související náklady, které zahrnují), které vaše řešení Batch spotřebují. Když zvolíte **konfiguraci virtuálního počítače**, bude se vám účtovat na základě [Virtual Machines cenové](https://azure.microsoft.com/pricing/details/virtual-machines/) struktury.

Pokud nasazujete aplikace do vašich dávkových uzlů pomocí [balíčků aplikací](batch-application-packages.md), účtují se vám také Azure Storage prostředky, které vaše balíčky aplikací spotřebovávají.

## <a name="next-steps"></a>Další kroky

- Prozkoumejte [ukázky kódu Pythonu](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch) v [úložišti GitHub Azure-Batch-Samples](https://github.com/Azure/azure-batch-samples) , abyste viděli, jak provádět běžné operace s dávkou, jako jsou například fondy, úlohy a vytváření úloh. [Soubor Readme](https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md) , který doprovází ukázky Pythonu, obsahuje podrobnosti o tom, jak nainstalovat požadované balíčky.
- Přečtěte si o použití [virtuálních počítačů s nízkou prioritou](batch-low-pri-vms.md) se službou Batch.
