---
title: Použití Service Management API (Python) – Průvodce funkcemi
description: Zjistěte, jak programově provádět běžné úlohy správy služby z Pythonu.
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
ms.openlocfilehash: b89f1aad46621d35728934ea068a5893ba674094
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29124795"
---
# <a name="use-service-management-from-python"></a>Použití služby správy z Pythonu
Tento průvodce vám ukáže, jak programově provádět běžné úlohy správy služby z Pythonu. **ServiceManagementService** třídy v [Azure SDK pro jazyk Python](https://github.com/Azure/azure-sdk-for-python) podporuje programový přístup k mnohem týkajících se správy funkcí služby, který je k dispozici v [Azure portál][management-portal]. Tuto funkci můžete vytvářet, aktualizovat a odstraňovat cloudové služby, nasazení, data správy služeb a virtuálních počítačů. Tato funkce může být užitečné při vytváření aplikace, které potřebují programový přístup ke správě služby.

## <a name="WhatIs"></a>Co je služba management?
Azure Service Management API zajišťují programový přístup ke většinu funkcí správy služby k dispozici prostřednictvím [portál Azure][management-portal]. Sada Azure SDK pro Python můžete použít ke správě služby cloud services a účty úložiště.

Chcete-li použít rozhraní API pro správu služby, je potřeba [vytvoření účtu Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"></a>Koncepty
Sada Azure SDK pro Python zabalí [Service Management API][svc-mgmt-rest-api], což je rozhraní REST API. Všechny operace rozhraní API se provádí přes protokol SSL a vzájemně ověřuje pomocí certifikátů X.509 v3. Služba správy je přístupná z v rámci služby spuštěné v Azure. Je také přístupný přímo přes Internet z jakékoli aplikace, která může požadavek HTTPS odesílat a přijímat odpověď protokolu HTTPS.

## <a name="Installation"></a>Instalace
Všechny funkce, které jsou popsané v tomto článku jsou k dispozici v `azure-servicemanagement-legacy` balíček, který můžete nainstalovat pomocí nástroje pip. Další informace o instalaci (například pokud jste ještě Python) najdete v tématu [nainstalovat Python a sady Azure SDK](../python-how-to-install.md).

## <a name="Connect"></a>Připojení služby správy
Pokud chcete připojit ke koncovému bodu služby správy, musíte svoje ID předplatného Azure a certifikát pro správu platné. Můžete získat svoje ID předplatného prostřednictvím [portál Azure][management-portal].

> [!NOTE]
> Teď můžete použít certifikáty vytvořené pomocí OpenSSL spuštěná v systému Windows. Python 2.7.4 nebo pozdější. Protože, doporučujeme používat OpenSSL místo .pfx, podporu pro certifikáty .pfx je pravděpodobně v budoucnu se odebere.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certifikáty pro správu v systému Windows nebo Mac/Linux (OpenSSL)
Můžete použít [OpenSSL](http://www.openssl.org/) vytvořit svůj certifikát pro správu. Je nutné vytvořit dva certifikáty, jeden pro server ( `.cer` souboru) a jeden pro klienta ( `.pem` souboru). Chcete-li vytvořit `.pem` soubor, spustit:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Chcete-li vytvořit `.cer` certifikátů, spustit:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Další informace o Azure certifikáty najdete v tématu [Přehled certifikátů pro Azure Cloud Services](cloud-services-certs-create.md). Úplný popis parametrů OpenSSL, naleznete v dokumentaci na [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Jakmile vytvoříte tyto soubory, nahrajte `.cer` souboru do Azure. V [portál Azure][management-portal]na **nastavení** vyberte **nahrát**. Všimněte si, kam jste uložili `.pem` souboru.

Po obdržení svoje ID předplatného vytvořit certifikát a odeslat `.cer` souboru do Azure, připojení ke koncovému bodu správy Azure. Připojit pomocí ID předplatného a cestu k předání `.pem` do souboru **ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

V předchozím příkladu `sms` je **ServiceManagementService** objektu. **ServiceManagementService** třída je primární třída, která slouží ke správě služby Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certifikáty pro správu v systému Windows (MakeCert)
Můžete vytvořit certifikát podepsaný svým držitelem správy v počítači pomocí `makecert.exe`. Otevřete **příkazového řádku Visual Studia** jako **správce** a použijte následující příkaz, nahraďte *AzureCertificate* se název certifikátu, kterou chcete použít:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Příkaz vytvoří `.cer` souboru a nainstaluje ho **osobní** úložiště certifikátů. Další informace najdete v tématu [Přehled certifikátů pro Azure Cloud Services](cloud-services-certs-create.md).

Po vytvoření certifikátu, nahrajte `.cer` souboru do Azure. V [portál Azure][management-portal]na **nastavení** vyberte **nahrát**.

Po obdržení svoje ID předplatného vytvořit certifikát a odeslat `.cer` souboru do Azure, připojení ke koncovému bodu správy Azure. Připojit pomocí předání ID předplatného a umístění certifikátu v vaše **osobní** úložišti certifikátů **ServiceManagementService** (znovu, nahraďte *AzureCertificate* s názvem vašeho certifikátu).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

V předchozím příkladu `sms` je **ServiceManagementService** objektu. **ServiceManagementService** třída je primární třída, která slouží ke správě služby Azure.

## <a name="ListAvailableLocations"></a>Seznamu dostupných umístění
K zobrazení seznamu umístění, které jsou k dispozici pro hostování služeb, použijte **seznamu\_umístění** metoda.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Při vytváření cloudové služby nebo služba úložiště, je třeba zadat platné umístění. **Seznamu\_umístění** metoda vždy vrátí hodnotu aktuální seznam aktuálně dostupných umístění. Době psaní tohoto textu, jsou k dispozici umístění:

* Západní Evropa
* Severní Evropa
* Jihovýchodní Asie
* Východní Asie
* Střed USA
* Střed USA – sever
* Střed USA – jih
* Západní USA
* Východ USA
* Japonsko – východ
* Japonsko – západ
* Brazílie – jih
* Austrálie – východ
* Austrálie – jihovýchod

## <a name="CreateCloudService"></a>Vytvoření cloudové služby
Když vytvoříte aplikaci a spustíte ho v Azure, kód a konfigurace společně se nazývají Azure [Cloudová služba][cloud service]. (Byla označována jako *hostovaná služba* ve starších verzích Azure.) Můžete použít **vytvořit\_hostované\_služby** metoda vytvořte novou hostovanou službu. Vytvoření služby tím, že poskytuje název hostované služby (které musí být jedinečné v Azure), popisek (automaticky kódovaný formátu Base64), popis a umístění.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Můžete vytvořit seznam všechny hostované služby pro vaše předplatné s **seznamu\_hostované\_služby** metoda.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Chcete-li získat informace o konkrétní hostovanou službu, předat název hostované služby, ke **získat\_hostované\_služby\_vlastnosti** metoda.

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Po vytvoření cloudové služby, nasazení kódu do služby pomocí **vytvořit\_nasazení** metoda.

## <a name="DeleteCloudService"></a>Odstranit cloudové služby
Cloudové služby můžete odstranit pomocí názvu služby k předání **odstranit\_hostované\_služby** metoda.

    sms.delete_hosted_service('myhostedservice')

Před odstraněním služby, musíte nejprve odstranit všechna nasazení pro službu. Další informace najdete v tématu [odstranit nasazení](#DeleteDeployment).

## <a name="DeleteDeployment"></a>Odstranit nasazení
Pokud chcete odstranit nasazení, použijte **odstranit\_nasazení** metoda. Následující příklad ukazuje, jak odstranit nasazení s názvem `v1`:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"></a>Vytvoření služby úložiště
A [služba úložiště](../storage/common/storage-create-storage-account.md) dává vám přístup k Azure [objekty BLOB](../storage/blobs/storage-python-how-to-use-blob-storage.md), [tabulky](../cosmos-db/table-storage-how-to-use-python.md), a [fronty](../storage/queues/storage-python-how-to-use-queue-storage.md). Chcete-li vytvořit službu úložiště, musíte jako název služby (mezi 3 a 24 malých písmen a jedinečný v rámci Azure). Musíte také popis, popisek (až 100 znaků, automaticky kódovaný formátu Base64) a umístění. Následující příklad ukazuje, jak vytvořit službu úložiště tak, že zadáte umístění:

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

V předchozím příkladu, stav **vytvořit\_úložiště\_účet** operaci se dá načíst pomocí předání výsledek vrácený **vytvořit\_úložiště\_ účet** k **získat\_operace\_stav** metoda. 

Můžete vytvořit seznam účtů úložiště a jejich vlastnosti s **seznamu\_úložiště\_účty** metoda.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"></a>Odstranit služba úložiště
Pokud chcete odstranit službu úložiště, předat název služby úložiště na **odstranit\_úložiště\_účet** metoda. Odstraněním úložiště služby se odstraní všechna data uložená ve službě (objekty BLOB, tabulek a front).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"></a>Seznam dostupných operačních systémů
K zobrazení seznamu operačních systémů, které jsou k dispozici pro hostování služeb, použijte **seznamu\_operační\_systémy** metoda.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Alternativně můžete použít **seznamu\_operační\_systému\_rodiny** metodu, která skupiny operační systémy řady.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"></a>Vytvořit bitovou kopii operačního systému
Chcete-li přidat bitovou kopii operačního systému do úložiště bitové kopie, použijte **přidat\_operačního systému\_bitové kopie** metoda.

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

K zobrazení seznamu bitové kopie operačního systému, které jsou k dispozici, použijte **seznamu\_os\_bitové kopie** metoda. Obsahuje všechny Image platformy a uživatele obrázků.

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

## <a name="DeleteVMImage"></a>Odstranit image operačního systému
Chcete-li odstranit uživatelskou image, použijte **odstranit\_operačního systému\_bitové kopie** metoda.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"></a>Vytvoření virtuálního počítače
Pokud chcete vytvořit virtuální počítač, musíte nejprve vytvořit [Cloudová služba](#CreateCloudService). Potom vytvořit nasazení virtuálního počítače pomocí **vytvořit\_virtuální\_počítač\_nasazení** metoda.

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

## <a name="DeleteVM"></a>Odstranění virtuálního počítače
Pokud chcete odstranit virtuální počítač, je nejprve odstranit nasazení s použitím **odstranit\_nasazení** metoda.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Cloudové služby můžete odstranit pomocí **odstranit\_hostované\_služby** metoda.

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Vytvoření virtuálního počítače z image zaznamenané virtuálního počítače
K zachycení image virtuálního počítače, první volání **zaznamenat\_virtuálních počítačů\_image** metoda.

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

Aby byly úspěšně zachytit bitovou kopii, použijte **seznamu\_virtuálních počítačů\_bitové kopie** rozhraní API. Ujistěte se, že vaší image je zobrazen ve výsledcích.

    images = sms.list_vm_images()

Nakonec vytvoření virtuálního počítače pomocí zaznamenané bitové kopie, použijte **vytvořit\_virtuální\_počítač\_nasazení** metoda jako předtím, ale tentokrát předávat vm_image_name místo.

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

Další informace o tom, jak zachytit virtuální počítač s Linuxem v modelu nasazení classic najdete v tématu [zachytit virtuální počítač s Linuxem](../virtual-machines/linux/classic/capture-image-classic.md).

Další informace o tom, jak zachytit virtuální počítač Windows v modelu nasazení classic najdete v tématu [zachytit virtuální počítač Windows](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="What's Next"></a>Další kroky
Teď, když jste se naučili základy používání služby Správa služby, dostanete [referenční dokumentace dokončení rozhraní API pro Azure Python SDK](http://azure-sdk-for-python.readthedocs.org/) a provádět komplexní úlohy snadno ke správě aplikace Python.

Další informace naleznete ve [Středisku pro vývojáře Python](/develop/python/).

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
