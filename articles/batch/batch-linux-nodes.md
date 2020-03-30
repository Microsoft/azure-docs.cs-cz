---
title: Spuštění Linuxu na výpočetních uzlech virtuálních strojů – Azure Batch | Dokumenty společnosti Microsoft
description: Zjistěte, jak zpracovat paralelní výpočetní úlohy ve fondech virtuálních počítačů SIP v Azure Batch.
services: batch
documentationcenter: python
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 977504f41e93e37ae2c5ce9bdb1182a1cfe0a3fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252282"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Zřízení výpočetních uzlů Linuxu ve fondech dávek

Azure Batch můžete použít ke spuštění paralelních výpočetních úloh na virtuálních počítačích Linuxu i Windows. Tento článek podrobně popisuje, jak vytvořit fondy výpočetních uzlů Linuxu ve službě Batch pomocí klientských knihoven [Batch Python][py_batch_package] a [Batch .NET.][api_net]

> [!NOTE]
> Balíčky aplikací jsou podporované ve všech fondech služby Batch vytvořených po 5. červenci 2017. Ve fondech služby Batch vytvořených mezi 10. březnem 2016 a 5. červencem 2017 jsou podporované, pouze pokud byl fond vytvořen pomocí konfigurace cloudové služby. Fondy služby Batch vytvořené před 10. březnem 2016 nepodporují balíčky aplikací. Další informace o používání balíčků aplikací k nasazení aplikací do uzlů služby Batch najdete v tématu [Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací služby Batch](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Konfigurace virtuálního počítače
Když vytvoříte fond výpočetních uzlů v batch, máte dvě možnosti, ze kterých chcete vybrat velikost uzlu a operační systém: Konfigurace cloudových služeb a Konfigurace virtuálního počítače.

**Konfigurace služby Cloud Services** poskytuje *pouze* výpočetní uzly Windows. Dostupné velikosti výpočetních uzlů jsou uvedeny ve [velikostech cloudových služeb](../cloud-services/cloud-services-sizes-specs.md)a dostupné operační systémy jsou uvedeny ve [verzích Azure Guest OS a matici kompatibility sady SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Když vytvoříte fond, který obsahuje uzly Cloudových služeb Azure, zadáte velikost uzlu a rodinu operačního serveru, které jsou popsány ve výše uvedených článcích. Pro fondy výpočetních uzlů Windows se nejčastěji používají cloudové služby.

**Konfigurace virtuálního počítače** poskytuje i bitové kopie Linuxu i Windows pro výpočetní uzly. Dostupné velikosti výpočetních uzlů jsou uvedené v [části Velikosti pro virtuální počítače v Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) a [Velikosti pro virtuální počítače v Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Při vytváření fondu, který obsahuje uzly konfigurace virtuálního počítače, musíte zadat velikost uzlů, odkaz na image virtuálního počítače a skladovou položku agenta uzlu batch, který se má nainstalovat na uzly.

### <a name="virtual-machine-image-reference"></a>Odkaz na image virtuálního počítače

Služba Batch používá [škálovací sady virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) k poskytování výpočetních uzlů v konfiguraci virtuálního počítače. Můžete zadat image z [Azure Marketplace][vm_marketplace]nebo poskytnout vlastní image, kterou jste připravili. Další podrobnosti o vlastních obrázcích najdete [v tématu Vytvoření fondu pomocí Galerie sdílených obrázků](batch-sig-images.md).

Když nakonfigurujete odkaz na image virtuálního počítače, určíte vlastnosti image virtuálního počítače. Při vytváření odkazu na image virtuálního počítače jsou vyžadovány následující vlastnosti:

| **Vlastnosti odkazu na obrázek** | **Příklad** |
| --- | --- |
| Vydavatel |Canonical |
| Nabídka |UbuntuServer |
| Skladová jednotka (SKU) |18.04-LTS |
| Version |nejnovější |

> [!TIP]
> Další informace o těchto vlastnostech a o tom, jak vypsat ibližové obrázky Marketplace v [navigaci a vybrat ibi virtuálních automatů Linuxu v Azure pomocí rozhraní PŘÍKAZOVÉHO PŘÍKAZU nebo PowerShellu](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Všimněte si, že ne všechny image Marketplace jsou aktuálně kompatibilní s Batch. Další informace naleznete v tématu [SKU agenta uzlu](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Skladová položka agenta uzlu
Agent uzlu Batch je program, který běží na každém uzlu ve fondu a poskytuje rozhraní příkazu a řízení mezi uztelem a službou Batch. Existují různé implementace agenta uzlu, známé jako sku, pro různé operační systémy. V podstatě při vytváření konfigurace virtuálního počítače nejprve zadejte odkaz na image virtuálního počítače a pak zadejte agenta uzlu, který se má nainstalovat do bitové kopie. Obvykle každý uzel agent sku je kompatibilní s více image virtuálního počítače. Tady je několik příkladů suna agenta uzlu:

* batch.node.ubuntu 18.04
* batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> Ne všechny image virtuálních strojů, které jsou k dispozici na webu Marketplace, jsou kompatibilní s aktuálně dostupnými agenty uzlů Batch. Pomocí sad Batch SDK můžete uvést seznam dostupných sad SKU agenta uzlu a bitové kopie virtuálních strojů, se kterými jsou kompatibilní. Další informace a příklady, jak načíst seznam platných bitových kopií za běhu, najdete v [seznamu bitových kopií virtuálních strojů](#list-of-virtual-machine-images) dále v tomto článku.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Vytvoření fondu Linuxu: Batch Python
Následující fragment kódu ukazuje příklad, jak pomocí [knihovny klienta Microsoft Azure Batch pro Python][py_batch_package] vytvořit fond výpočetních uzlů Ubuntu Server. Referenční dokumentaci pro modul Batch Python najdete na [azure.batch balíčku][py_batch_docs] na Číst dokumenty.

Tento fragment vytvoří [ImageReference][py_imagereference] explicitně a určuje každou z jeho vlastností (vydavatel, nabídka, skladová položka, verze). V produkčním kódu však doporučujeme použít [metodu list_supported_images][py_list_supported_images] k určení a výběru z dostupných kombinací skladových položk agenta bitové kopie a uzlu za běhu.

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

Jak již bylo zmíněno dříve, doporučujeme místo explicitního vytvoření [ImageReference][py_imagereference] použít [metodu list_supported_images][py_list_supported_images] k dynamickému výběru z aktuálně podporovaných kombinací bitových obrázků agenta uzlu a marketplace. Následující fragment Pythonu ukazuje, jak používat tuto metodu.

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

## <a name="create-a-linux-pool-batch-net"></a>Vytvoření fondu Linuxu: Batch .NET
Následující fragment kódu ukazuje příklad, jak pomocí klientské [knihovny Batch .NET][nuget_batch_net] vytvořit fond výpočetních uzlů Ubuntu Server. Referenční [dokumentaci batch .NET][api_net] naleznete v docs.microsoft.com.

Následující fragment kódu používá [PoolOperations][net_pool_ops]. [ListSupportedImages][net_list_supported_images] metoda vybrat ze seznamu aktuálně podporovaných kombinace image Marketplace a uzel agenta Skladové položky. Tato technika je žádoucí, protože seznam podporovaných kombinací se může čas od času změnit. Nejčastěji jsou přidány podporované kombinace.

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

Přestože předchozí úryvek používá [PoolOperations][net_pool_ops]. [ListSupportedImages][net_list_supported_images] metoda dynamicky seznam a výběr z podporovaných kombinací skladových položek a agenta uzlu (doporučeno), můžete také explicitně nakonfigurovat [ImageReference:][net_imagereference]

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Seznam image virtuálních strojů
Chcete-li získat seznam všech podporovaných image virtuálních strojů Marketplace pro službu Batch a jejich odpovídající uzlové agenty, využijte [list_supported_images][py_list_supported_images] (Python), [ListSupportedImages][net_list_supported_images] (Batch .NET) nebo odpovídající rozhraní API v příslušném jazyce Sady SDK podle vašeho výběru.

## <a name="connect-to-linux-nodes-using-ssh"></a>Připojení k uzlům Linuxu pomocí SSH
Během vývoje nebo při řešení potíží může být nutné přihlásit se k uzlům ve fondu. Na rozdíl od výpočetních uzlů systému Windows nelze k připojení k uzlům Linuxu použít protokol RDP (Remote Desktop Protocol). Místo toho služba Batch umožňuje přístup SSH na každém uzlu pro vzdálené připojení.

Následující fragment kódu Pythonu vytvoří uživatele na každém uzlu ve fondu, který je vyžadován pro vzdálené připojení. Poté vytiskne informace o připojení zabezpečeného prostředí (SSH) pro každý uzel.

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

Tady je ukázkový výstup pro předchozí kód pro fond, který obsahuje čtyři linuxové uzly:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Místo hesla můžete zadat veřejný klíč SSH při vytváření uživatele v uzlu. V pythonu SDK použijte **parametr ssh_public_key** na [ComputeNodeUser][py_computenodeuser]. V rozhraní .NET použijte [nástroj ComputeNodeUser][net_computenodeuser]. [SshPublicKey][net_ssh_key] vlastnost.

## <a name="pricing"></a>Ceny
Azure Batch je integrovaný na Azure Cloud Services a technologii Virtuálních počítačů Azure. Samotná služba Batch je nabízena zdarma, což znamená, že se vám účtují pouze výpočetní prostředky (a související náklady, které s sebou nese) spotřebovává vaše řešení Batch. Když zvolíte **konfiguraci cloudových služeb**, bude vám účtovánpoplatek na základě cenové struktury [cloudových služeb.][cloud_services_pricing] Když zvolíte **konfiguraci virtuálního počítače**, bude se vám účtovat na základě cenové struktury [Virtuální počítače.][vm_pricing]

Pokud nasadíte aplikace do uzlů Batch pomocí [balíčků aplikací](batch-application-packages.md), budou se vám také účtovat prostředky azure storage, které vaše balíčky aplikací spotřebovávají.

## <a name="next-steps"></a>Další kroky

[Ukázky kódu Pythonu][github_samples_py] v úložišti [azure-batch-samples][github_samples] na GitHubu obsahují skripty, které ukazují, jak provádět běžné operace Batch, jako je fond, úloha a vytváření úloh. [Readme,][github_py_readme] který doprovází ukázky Pythonu, má podrobnosti o tom, jak nainstalovat požadované balíčky.

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
[net_list_supported_images]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure.github.io/azure-sdk-for-python/ref/Batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_supported_images]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
