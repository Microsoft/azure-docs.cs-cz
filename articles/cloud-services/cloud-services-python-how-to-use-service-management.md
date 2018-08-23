---
title: Použití rozhraní služby API Management (Python) – Průvodce funkcemi
description: Zjistěte, jak programově provést běžné úkoly správy služeb z Pythonu.
services: cloud-services
documentationcenter: python
author: lmazuel
manager: wpickett
editor: ''
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: lmazuel
ms.openlocfilehash: ab2a05c55ee248d8a74f70d36b06277db3a0f38a
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "42056800"
---
# <a name="use-service-management-from-python"></a>Použití správy služeb z Pythonu
Tento průvodce vám ukáže, jak programově provést běžné úkoly správy služeb z Pythonu. **ServiceManagementService** třídy v [sady Azure SDK for Python](https://github.com/Azure/azure-sdk-for-python) podporuje programový přístup k většině funkcí služeb souvisejících se správou, které jsou dostupné v [Azure portál][management-portal]. Tato funkce slouží k vytvoření, aktualizace a odstranění cloudové služby, nasazení, služeb správy dat. a virtuální počítače. Tato funkce může být užitečné při vytváření aplikace, které potřebují programový přístup ke správě služby.

## <a name="WhatIs"> </a>Co je Správa služby?
Azure Service Management API poskytuje programový přístup k většině funkcí správy služeb dostupných prostřednictvím [webu Azure portal][management-portal]. Sada Azure SDK pro Python slouží ke správě cloudových služeb a účtů úložiště.

Pokud chcete používat rozhraní API pro správu služby, budete muset [vytvořit účet Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Koncepty
Zabalí sady Azure SDK for Python [Service Management API][svc-mgmt-rest-api], což je rozhraní REST API. Všechny operace rozhraní API se provádí přes SSL a vzájemně se ověřují pomocí certifikátů X.509 v3. Služba správy lze přistupovat z v rámci služby spuštěné v Azure. Je také přístupné přímo přes Internet z jakékoli aplikace, který může odeslat požadavek HTTPS a přijmout odpověď HTTPS.

## <a name="Installation"> </a>Instalace
Všechny funkce popsané v tomto článku jsou dostupné v `azure-servicemanagement-legacy` balíček, který můžete nainstalovat pomocí pip. Další informace o instalaci (například, pokud je pro vás nová Python) najdete v tématu [instalace Pythonu a sady Azure SDK](../python-how-to-install.md).

## <a name="Connect"> </a>Připojení správy služeb
Pro připojení ke koncovému bodu správy služby, budete potřebovat ID vašeho předplatného Azure a platný certifikát pro správu. Můžete získat ID vašeho předplatného prostřednictvím [webu Azure portal][management-portal].

> [!NOTE]
> Teď můžete použít certifikáty vytvořené pomocí OpenSSL, když běží na Windows. Python 2.7.4 nebo novější je povinný. Doporučujeme použít OpenSSL místo soubor .pfx, protože podpora pro certifikáty .pfx je pravděpodobné, že v budoucnu se odebere.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certifikáty pro správu na Windows/Mac/Linux (OpenSSL)
Můžete použít [OpenSSL](http://www.openssl.org/) k vytvoření certifikátu pro správu. Je nutné vytvořit dva certifikáty, jeden pro server ( `.cer` soubor) a jeden pro klienta ( `.pem` souboru). Chcete-li vytvořit `.pem` souboru, spusťte:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Chcete-li vytvořit `.cer` certifikátu, spusťte:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Další informace o certifikátech pro Azure najdete v tématu [Přehled certifikátů pro Azure Cloud Services](cloud-services-certs-create.md). Úplný popis parametrů OpenSSL, naleznete v dokumentaci na [ http://www.openssl.org/docs/apps/openssl.html ](http://www.openssl.org/docs/apps/openssl.html).

Po vytvoření tyto soubory nahrát `.cer` souboru do Azure. V [webu Azure portal][management-portal]na **nastavení** kartu, vyberte možnost **nahrát**. Všimněte si, kam jste uložili `.pem` souboru.

Po obdržení ID svého předplatného vytvořit certifikát a nahrajte `.cer` souboru do Azure, připojit ke koncovému bodu správy Azure. Připojit pomocí ID předplatného a cestu k předání `.pem` do souboru **ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

V předchozím příkladu `sms` je **ServiceManagementService** objektu. **ServiceManagementService** třída je primární třída používá ke správě služeb Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certifikáty pro správu na Windows (MakeCert)
Můžete vytvořit certifikát podepsaný svým držitelem správy na svém počítači s použitím `makecert.exe`. Otevřít **příkazový řádek sady Visual Studio** jako **správce** a použijte následující příkaz a nahraďte *AzureCertificate* se název certifikátu, kterou chcete použít:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Příkaz vytvoří `.cer` souboru a nainstaluje ho **osobní** úložiště certifikátů. Další informace najdete v tématu [Přehled certifikátů pro Azure Cloud Services](cloud-services-certs-create.md).

Po vytvoření certifikátu, nahrajte `.cer` souboru do Azure. V [webu Azure portal][management-portal]na **nastavení** kartu, vyberte možnost **nahrát**.

Po obdržení ID svého předplatného vytvořit certifikát a nahrajte `.cer` souboru do Azure, připojit ke koncovému bodu správy Azure. Připojte se pomocí ID předplatného a umístění certifikátu v vaše **osobní** úložiště certifikátů **ServiceManagementService** (znovu nahraďte *AzureCertificate* s názvem vašeho certifikátu).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

V předchozím příkladu `sms` je **ServiceManagementService** objektu. **ServiceManagementService** třída je primární třída používá ke správě služeb Azure.

## <a name="ListAvailableLocations"> </a>Seznam dostupných umístění
K zobrazení seznamu umístění, které jsou k dispozici pro hostování služeb, použijte **seznamu\_umístění** metody.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Při vytváření cloudové služby nebo služba úložiště, je třeba zadat platné umístění. **Seznamu\_umístění** metoda vždy vrátí aktuální seznam aktuálně dostupných umístění. V době psaní tohoto návodu dostupná umístění jsou:

* Západní Evropa
* Severní Evropa
* Jihovýchodní Asie
* Východní Asie
* USA – střed
* Střed USA – sever
* Střed USA – jih
* USA – západ
* USA – východ
* Japonsko – východ
* Japonsko – západ
* Brazílie – jih
* Austrálie – východ
* Austrálie – jihovýchod

## <a name="CreateCloudService"> </a>Vytvořit cloudovou službu
Když vytvoříte aplikaci a spustit ho v Azure, kódu a konfigurace společně nazývají Azure [Cloudová služba][cloud service]. (To se označuje jako *hostovaná služba* v dřívějších verzích Azure.) Můžete použít **vytvořit\_hostované\_služby** metoda vytvořte novou hostovanou službu. Vytvoření služby tím, že poskytuje název hostované služby, (který musí být jedinečný v Azure), popisek (automaticky kódovaný ve formátu Base64), popis a umístění.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Můžete vytvořit seznam všechny hostované služby pro vaše předplatné se **seznamu\_hostované\_služby** metoda.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Chcete-li získat informace o konkrétní hostovanou službu, předejte název hostované služby, ke **získat\_hostované\_služby\_vlastnosti** metoda.

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Po vytvoření cloudové služby, nasaďte svůj kód do této služby s využitím **vytvořit\_nasazení** metody.

## <a name="DeleteCloudService"> </a>Odstranění cloudové služby
Cloudové služby můžete odstranit předáním názvu služby **odstranit\_hostované\_služby** metoda.

    sms.delete_hosted_service('myhostedservice')

Než budete moct odstranit službu, musí nejdřív odstranit všechna nasazení služby. Další informace najdete v tématu [odstranit nasazení](#DeleteDeployment).

## <a name="DeleteDeployment"> </a>Odstranit nasazení
Chcete-li odstranit nasazení, použijte **odstranit\_nasazení** metody. Následující příklad ukazuje, jak odstranit nasazení s názvem `v1`:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Vytvoření služby storage
A [službu storage](../storage/common/storage-create-storage-account.md) dává vám přístup k Azure [objekty BLOB](../storage/blobs/storage-python-how-to-use-blob-storage.md), [tabulky](../cosmos-db/table-storage-how-to-use-python.md), a [fronty](../storage/queues/storage-python-how-to-use-queue-storage.md). Pokud chcete vytvořit službu úložiště, musíte jako název služby (mezi 3 až 24 malých písmen a v rámci Azure jedinečný). Potřebujete také popis, popisek (až 100 znaků, automaticky kódovaný ve formátu Base64) a umístění. Následující příklad ukazuje, jak vytvořit službu storage tak, že zadáte umístění:

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

V předchozím příkladu, stav **vytvořit\_úložiště\_účet** operace lze načíst předáním výsledek vrácený z **vytvořit\_úložiště\_ účet** k **získat\_operace\_stav** metody. 

Můžete vytvořit seznam účtů úložiště a jejich vlastnosti **seznamu\_úložiště\_účty** metody.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Odstranění služby storage
Pokud chcete odstranit službu úložiště, předejte název služby úložiště **odstranit\_úložiště\_účet** metoda. Odstraňuje se služba storage se odstraní všechna data uložená ve službě (objekty BLOB, tabulky a fronty).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Zobrazit dostupné operační systémy
K zobrazení seznamu operačních systémů, které jsou k dispozici pro hostování služeb, použijte **seznamu\_provozní\_systémy** metody.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Alternativně můžete použít **seznamu\_provozní\_systému\_rodiny** metodu, která seskupí podle řady operačních systémů.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Vytvoření image operačního systému
Chcete-li přidat bitovou kopii operačního systému do úložiště imagí, použijte **přidat\_operačního systému\_image** metody.

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

K zobrazení seznamu imagí operačního systému, které jsou k dispozici, použijte **seznamu\_os\_imagí** metody. Zahrnuje všechny Image platformy a uživatele imagí.

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

## <a name="DeleteVMImage"> </a>Odstranit image operačního systému
Chcete-li odstranit uživatelskou image, použijte **odstranit\_os\_image** – metoda.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Vytvoření virtuálního počítače
K vytvoření virtuálního počítače, je nejprve potřeba vytvořit [Cloudová služba](#CreateCloudService). Potom vytvořte nasazení virtuálního počítače pomocí **vytvořit\_virtuální\_počítač\_nasazení** metody.

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

## <a name="DeleteVM"> </a>Odstranění virtuálního počítače
Pokud chcete odstranit virtuální počítač, je nejprve odstranit nasazení s použitím **odstranit\_nasazení** metoda.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Cloudové služby můžete odstranit pomocí **odstranit\_hostované\_služby** metody.

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Vytvoření virtuálního počítače z image zachycené virtuálního počítače
K zachycení image virtuálního počítače, byste nejprve volat **zachycení\_virtuálního počítače\_image** metody.

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

Ujistěte se, že se úspěšně zachytil image, použijte **seznamu\_vm\_imagí** rozhraní API. Ujistěte se, že vaše image se zobrazí ve výsledcích.

    images = sms.list_vm_images()

Nakonec vytvořte virtuální počítač s použitím zaznamenané bitové kopie, použijte **vytvořit\_virtuální\_počítač\_nasazení** metoda jako před tím, ale tentokrát předat vm_image_name místo.

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

Další informace o tom, jak zachytit virtuální počítač s Linuxem v modelu nasazení classic, najdete v článku [zachytit virtuální počítač s Linuxem](../virtual-machines/linux/classic/capture-image-classic.md).

Další informace o tom, jak zachytit virtuální počítač s Windows v modelu nasazení classic, najdete v článku [zachytit virtuální počítač s Windows](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="What's Next"> </a>Další kroky
Teď, když jste se naučili základy správy služeb, dostanete [úplné rozhraní API v dokumentaci sady Azure Python SDK](http://azure-sdk-for-python.readthedocs.org/) a provádět složité úlohy snadno ke správě vaší aplikace v Pythonu.

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
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/services/cloud-services/
