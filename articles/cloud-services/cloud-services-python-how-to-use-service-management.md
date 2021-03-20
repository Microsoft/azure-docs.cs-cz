---
title: Použití příručky rozhraní API pro správu služeb (Python) – Průvodce funkcemi
description: Naučte se programově provádět běžné úlohy správy služeb z Pythonu.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 02993f2b79e37e5e50c20c4ee07220bcbd36edb8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741396"
---
# <a name="use-service-management-from-python"></a>Použití správy služeb z Pythonu

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

V této příručce se dozvíte, jak programově provádět běžné úlohy správy služeb z Pythonu. Třída **ServiceManagementService** v [sadě Azure SDK for Python](https://github.com/Azure/azure-sdk-for-python) podporuje programový přístup k většině funkcí souvisejících se správou služeb, které jsou k dispozici v [Azure Portal][management-portal]. Pomocí této funkce můžete vytvářet, aktualizovat a odstraňovat cloudové služby, nasazení, služby správy dat a virtuální počítače. Tato funkce může být užitečná při sestavování aplikací, které potřebují programový přístup ke správě služeb.

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>Co je Správa služeb?
Azure rozhraní API pro správu služeb poskytuje programový přístup k většině funkcí správy služeb, které jsou dostupné prostřednictvím [Azure Portal][management-portal]. Pomocí sady Azure SDK pro Python můžete spravovat cloudové služby a účty úložiště.

Pokud chcete použít rozhraní API pro správu služeb, musíte [vytvořit účet Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="concepts"></a><a name="Concepts"> </a>Koncepty
Sada Azure SDK pro Python zalomí [rozhraní API pro správu služeb][svc-mgmt-rest-api], což je REST API. Všechny operace rozhraní API se provádí přes protokol TLS a vzájemně se ověřují pomocí certifikátů X. 509 v3. Služba pro správu je k dispozici v rámci služby spuštěné v Azure. Dá se taky získat přímo přes Internet z libovolné aplikace, která může poslat požadavek HTTPS a přijmout odpověď HTTPS.

## <a name="installation"></a><a name="Installation"> </a>Instalace
Všechny funkce popsané v tomto článku jsou k dispozici v `azure-servicemanagement-legacy` balíčku, který můžete nainstalovat pomocí PIP. Další informace o instalaci (například pokud jste novinkou k Pythonu) najdete v tématu [Instalace Pythonu a sady Azure SDK](/azure/developer/python/azure-sdk-install).

## <a name="connect-to-service-management"></a><a name="Connect"> </a>Připojení ke službě Service Management
Pokud se chcete připojit ke koncovému bodu služby Service Management, budete potřebovat své ID předplatného Azure a platný certifikát pro správu. ID vašeho předplatného můžete získat prostřednictvím [Azure Portal][management-portal].

> [!NOTE]
> Nyní můžete použít certifikáty vytvořené pomocí OpenSSL při spuštění v systému Windows. Vyžaduje se Python 2.7.4 nebo novější. Doporučujeme místo souboru. pfx používat OpenSSL, protože podpora certifikátů. PFX je pravděpodobně v budoucnu odebrána.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certifikáty pro správu v systému Windows/Mac/Linux (OpenSSL)
Pomocí [OpenSSL](https://www.openssl.org/) můžete vytvořit certifikát pro správu. Musíte vytvořit dva certifikáty, jeden pro server ( `.cer` soubor) a jeden pro klienta ( `.pem` soubor). Chcete-li vytvořit `.pem` soubor, spusťte příkaz:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
```

Chcete-li vytvořit `.cer` certifikát, spusťte příkaz:

```console
openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer
```

Další informace o certifikátech Azure najdete v tématu [Přehled certifikátů pro azure Cloud Services](cloud-services-certs-create.md). Úplný popis parametrů OpenSSL naleznete v dokumentaci na adrese [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html) .

Po vytvoření těchto souborů nahrajte `.cer` soubor do Azure. V [Azure Portal][management-portal]na kartě **Nastavení** vyberte **Odeslat**. Všimněte si, kam jste `.pem` soubor uložili.

Po získání ID předplatného vytvořte certifikát a nahrajte ho `.cer` do Azure. Připojte se ke koncovému bodu správy Azure. Připojte se pomocí předání ID předplatného a cesty k `.pem` souboru do **ServiceManagementService**.

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = '<path_to_.pem_certificate>'

sms = ServiceManagementService(subscription_id, certificate_path)
```

V předchozím příkladu `sms` je objekt **ServiceManagementService** . Třída **ServiceManagementService** je primární třídou, která se používá ke správě služeb Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certifikáty pro správu ve Windows (MakeCert)
Certifikát pro správu podepsaný svým držitelem můžete na svém počítači vytvořit pomocí `makecert.exe` . Otevřete **příkazový řádek sady Visual Studio** jako **správce** a použijte následující příkaz a nahraďte *AzureCertificate* názvem certifikátu, který chcete použít:

```console
makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"
```

Příkaz vytvoří `.cer` soubor a nainstaluje ho do **osobního** úložiště certifikátů. Další informace najdete v tématu [Přehled certifikátů pro Azure Cloud Services](cloud-services-certs-create.md).

Po vytvoření certifikátu ho nahrajte `.cer` do Azure. V [Azure Portal][management-portal]na kartě **Nastavení** vyberte **Odeslat**.

Po získání ID předplatného vytvořte certifikát a nahrajte ho `.cer` do Azure. Připojte se ke koncovému bodu správy Azure. Připojte se pomocí ID předplatného a umístění certifikátu ve svém **osobním** úložišti certifikátů do **ServiceManagementService** (znovu nahraďte *AzureCertificate* názvem vašeho certifikátu).

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

sms = ServiceManagementService(subscription_id, certificate_path)
```

V předchozím příkladu `sms` je objekt **ServiceManagementService** . Třída **ServiceManagementService** je primární třídou, která se používá ke správě služeb Azure.

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>Zobrazit dostupná umístění
Chcete-li zobrazit seznam umístění, která jsou k dispozici pro hostitelské služby, použijte metodu **seznam \_ umístění** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_locations()
for location in result:
    print(location.name)
```

Když vytváříte cloudovou službu nebo službu úložiště, musíte zadat platné umístění. Metoda **\_ umístění seznamu** vždy vrátí aktuální seznam aktuálně dostupných umístění. V době psaní tohoto textu jsou dostupná umístění:

* West Europe
* Severní Evropa
* Jihovýchodní Asie
* Východní Asie
* USA – střed
* USA – středosever
* Středojižní USA
* USA – západ
* East US
* Japonsko – východ
* Japonsko – západ
* Brazílie – jih
* Austrálie – východ
* Austrálie – jihovýchod

## <a name="create-a-cloud-service"></a><a name="CreateCloudService"> </a>Vytvoření cloudové služby
Když vytvoříte aplikaci a spustíte ji v Azure, kód a konfigurace společně se nazývají [cloudová služba][cloud service]Azure. (Tato služba byla známá jako *hostovaná služba* v dřívějších verzích Azure.) Pomocí metody **Create \_ Hosted \_** Service můžete vytvořit novou hostovanou službu. Vytvořte službu tím, že poskytnete název hostované služby (který musí být v Azure jedinečný), popisek (automaticky kódovaný na base64), popis a umístění.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myhostedservice'
label = 'myhostedservice'
desc = 'my hosted service'
location = 'West US'

sms.create_hosted_service(name, label, desc, location)
```

Pomocí metody **seznam \_ hostovaných \_ služeb** můžete vypsat všechny hostované služby pro vaše předplatné.

```python
result = sms.list_hosted_services()

for hosted_service in result:
    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)
    print('')
```

Chcete-li získat informace o určité hostované službě, předejte název hostované služby metodě **získat \_ \_ \_ vlastnosti hostované služby** .

```python
hosted_service = sms.get_hosted_service_properties('myhostedservice')

print('Service name: ' + hosted_service.service_name)
print('Management URL: ' + hosted_service.url)
print('Location: ' + hosted_service.hosted_service_properties.location)
```

Po vytvoření cloudové služby nasaďte kód do služby pomocí metody **Create \_ Deployment** .

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>Odstranění cloudové služby
Cloudovou službu můžete odstranit předáním názvu služby do metody **Odstranit \_ hostovanou \_ službu** .

```python
sms.delete_hosted_service('myhostedservice')
```

Předtím, než budete moci službu odstranit, je třeba nejprve odstranit všechna nasazení služby. Další informace najdete v tématu [odstranění nasazení](#DeleteDeployment).

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>Odstranění nasazení
K odstranění nasazení použijte metodu **odstranění \_ nasazení** . Následující příklad ukazuje, jak odstranit nasazení s názvem `v1` :

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment('myhostedservice', 'v1')
```

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>Vytvoření služby úložiště
[Služba úložiště](../storage/common/storage-account-create.md) vám poskytne přístup k objektům [BLOB](../storage/blobs/storage-quickstart-blobs-python.md), [tabulkám](../cosmos-db/table-storage-how-to-use-python.md)a [frontám](../storage/queues/storage-python-how-to-use-queue-storage.md)Azure. Pokud chcete vytvořit službu úložiště, budete potřebovat název služby (v rozmezí 3 až 24 malých písmen a jedinečné v rámci Azure). Potřebujete také popis, popisek (až 100 znaků, automaticky kódovaný na base64) a umístění. Následující příklad ukazuje, jak vytvořit službu úložiště zadáním umístění:

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'mystorageaccount'
label = 'mystorageaccount'
location = 'West US'
desc = 'My storage account description.'

result = sms.create_storage_account(name, desc, label, location=location)

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

V předchozím příkladu se stav operace **vytvoření \_ \_ účtu úložiště** dá načíst předáním výsledku vráceného **vytvořením \_ \_ účtu úložiště** do metody **\_ \_ stavu operace Get** . 

Můžete vypsat účty úložiště a jejich vlastnosti pomocí metody **listovat \_ \_ účty úložiště** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_storage_accounts()
for account in result:
    print('Service name: ' + account.service_name)
    print('Location: ' + account.storage_service_properties.location)
    print('')
```

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>Odstranění služby úložiště
Pokud chcete odstranit službu úložiště, předejte název služby úložiště metodě **Odstranit \_ \_ účet úložiště** . Odstraněním služby úložiště se odstraní všechna data uložená ve službě (objekty blob, tabulky a fronty).

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_storage_account('mystorageaccount')
```

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>Vypsat dostupné operační systémy
Chcete-li zobrazit seznam operačních systémů, které jsou k dispozici pro hostingové služby, použijte metodu **seznam \_ operačních \_ systémů** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_operating_systems()

for os in result:
    print('OS: ' + os.label)
    print('Family: ' + os.family_label)
    print('Active: ' + str(os.is_active))
```

Alternativně můžete použít metodu **seznam \_ \_ \_ rodin operačních** systémů, které seskupují operační systémy podle řady.

```python
result = sms.list_operating_system_families()

for family in result:
    print('Family: ' + family.label)
    for os in family.operating_systems:
        if os.is_active:
            print('OS: ' + os.label)
            print('Version: ' + os.version)
    print('')
```

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>Vytvoření bitové kopie operačního systému
K přidání image operačního systému do úložiště imagí použijte metodu **Přidat \_ \_ image operačního** systému.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'mycentos'
label = 'mycentos'
os = 'Linux' # Linux or Windows
media_link = 'url_to_storage_blob_for_source_image_vhd'

result = sms.add_os_image(label, media_link, name, os)

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

K vypsání imagí operačního systému, které jsou k dispozici, použijte metodu **List \_ \_ Image** operačních systémů. Zahrnuje všechny image platforem a uživatelské image.

```python
result = sms.list_os_images()

for image in result:
    print('Name: ' + image.name)
    print('Label: ' + image.label)
    print('OS: ' + image.os)
    print('Category: ' + image.category)
    print('Description: ' + image.description)
    print('Location: ' + image.location)
    print('Media link: ' + image.media_link)
    print('')
```

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>Odstranění image operačního systému
Pokud chcete odstranit uživatelskou image, použijte **metodu \_ odstranění \_ image operačního systému** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.delete_os_image('mycentos')

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

## <a name="create-a-virtual-machine"></a><a name="CreateVM"> </a>Vytvoření virtuálního počítače
Pokud chcete vytvořit virtuální počítač, musíte nejdřív vytvořit [cloudovou službu](#CreateCloudService). Pak vytvořte nasazení virtuálního počítače pomocí metody **vytvoření \_ \_ \_ nasazení virtuálního počítače** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myvm'
location = 'West US'

#Set the location
sms.create_hosted_service(service_name=name,
    label=name,
    location=location)

# Name of an os image as returned by list_os_images
image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

# Destination storage account container/blob where the VM disk
# will be created
media_link = 'url_to_target_storage_blob_for_vm_hd'

# Linux VM configuration, you can use WindowsConfigurationSet
# for a Windows VM instead
linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

os_hd = OSVirtualHardDisk(image_name, media_link)

sms.create_virtual_machine_deployment(service_name=name,
    deployment_name=name,
    deployment_slot='production',
    label=name,
    role_name=name,
    system_config=linux_config,
    os_virtual_hard_disk=os_hd,
    role_size='Small')
```

## <a name="delete-a-virtual-machine"></a><a name="DeleteVM"> </a>Odstranění virtuálního počítače
Chcete-li odstranit virtuální počítač, nejprve odstraňte nasazení pomocí metody **Delete \_ Deployment** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment(service_name='myvm',
    deployment_name='myvm')
```

Cloudovou službu je pak možné odstranit pomocí metody **Delete \_ Hosted \_ Service** .

```python
sms.delete_hosted_service(service_name='myvm')
```

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Vytvoření virtuálního počítače z zaznamenané image virtuálního počítače
Pokud chcete zachytit image virtuálního počítače, napřed volejte **metodu \_ zachycení \_ image virtuálního počítače** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

# replace the below three parameters with actual values
hosted_service_name = 'hs1'
deployment_name = 'dep1'
vm_name = 'vm1'

image_name = vm_name + 'image'
image = CaptureRoleAsVMImage    ('Specialized',
    image_name,
    image_name + 'label',
    image_name + 'description',
    'english',
    'mygroup')

result = sms.capture_vm_image(
        hosted_service_name,
        deployment_name,
        vm_name,
        image
    )
```

Abyste se ujistili, že jste úspěšně zachytili image, použijte rozhraní API pro **Výpis \_ \_ imagí virtuálních počítačů** . Ujistěte se, že se váš obrázek zobrazuje ve výsledcích.

```python
images = sms.list_vm_images()
```

Pokud chcete nakonec vytvořit virtuální počítač pomocí zaznamenané bitové kopie, použijte k tomu metodu **\_ \_ \_ nasazení virtuálního počítače** jako dřív, ale tentokrát tento čas projde vm_image_name.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myvm'
location = 'West US'

#Set the location
sms.create_hosted_service(service_name=name,
    label=name,
    location=location)

sms.create_virtual_machine_deployment(service_name=name,
    deployment_name=name,
    deployment_slot='production',
    label=name,
    role_name=name,
    system_config=linux_config,
    os_virtual_hard_disk=None,
    role_size='Small',
    vm_image_name = image_name)
```

Další informace o tom, jak zachytit virtuální počítač se systémem Linux v modelu nasazení Classic, najdete v tématu [zachycení virtuálního počítače se systémem Linux](/previous-versions/azure/virtual-machines/linux/classic/capture-image-classic).

Další informace o tom, jak zachytit virtuální počítač s Windows v modelu nasazení Classic, najdete v tématu [zachycení virtuálního počítače s Windows](/previous-versions/azure/virtual-machines/windows/classic/capture-image-classic).

## <a name="next-steps"></a><a name="What's Next"> </a>Další kroky
Teď, když jste se naučili základy správy služeb, můžete získat přístup k [kompletní dokumentaci k rozhraní API pro Azure Python SDK](https://azure-sdk-for-python.readthedocs.org/) a provádět složité úlohy ke správě vaší aplikace v Pythonu.

Další informace naleznete ve [Středisku pro vývojáře Python](https://azure.microsoft.com/develop/python/).

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: /previous-versions/azure/ee460799(v=azure.100)


[cloud service]:/azure/cloud-services/