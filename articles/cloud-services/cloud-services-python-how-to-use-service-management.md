---
title: Použití rozhraní API pro správu služeb (Python) – průvodce funkcemi
description: Naučte se programově provádět běžné úlohy správy služeb z Pythonu.
services: cloud-services
documentationcenter: python
author: tanmaygore
manager: vashan
editor: ''
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: tagore
ms.openlocfilehash: 135dd92f7af4397f2053ea0bdc15d98dfad93914
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253352"
---
# <a name="use-service-management-from-python"></a>Použití správy služeb z Pythonu
Tato příručka ukazuje, jak programově provádět běžné úlohy správy služeb z Pythonu. Třída **ServiceManagementService** v [azure sdk pro Python](https://github.com/Azure/azure-sdk-for-python) podporuje programový přístup k velké části funkcí souvisejících se správou služeb, která je dostupná na webu Azure [Portal][management-portal]. Pomocí této funkce můžete vytvářet, aktualizovat a odstraňovat cloudové služby, nasazení, služby správy dat a virtuální počítače. Tato funkce může být užitečná při vytváření aplikací, které potřebují programový přístup ke správě služeb.

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>Co je správa služeb?
Rozhraní AZURE Service Management API poskytuje programový přístup k velké části funkce správy služeb dostupné prostřednictvím [portálu Azure][management-portal]. Azure SDK pro Python můžete použít ke správě cloudových služeb a účtů úložiště.

Chcete-li používat rozhraní API pro správu služeb, musíte [vytvořit účet Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="concepts"></a><a name="Concepts"> </a>Koncepty
Sada Azure SDK pro Python zabalí [rozhraní API pro správu služeb][svc-mgmt-rest-api], což je rozhraní REST API. Všechny operace rozhraní API jsou prováděny přes TLS a vzájemně ověřeny pomocí certifikátů X.509 v3. Ke službě pro správu se dá přistupovat v rámci služby spuštěné v Azure. Lze také přistupovat přímo přes Internet z libovolné aplikace, která může odeslat požadavek HTTPS a přijímat odpověď HTTPS.

## <a name="installation"></a><a name="Installation"> </a>Instalace
Všechny funkce popsané v tomto článku `azure-servicemanagement-legacy` jsou k dispozici v balíčku, který můžete nainstalovat pomocí pip. Další informace o instalaci (například pokud jste v Pythonu noví), najdete [v tématu Instalace Pythonu a sady Azure SDK](/azure/developer/python/azure-sdk-install).

## <a name="connect-to-service-management"></a><a name="Connect"> </a>Připojení ke správě služeb
Chcete-li se připojit ke koncovému bodu správy služeb, potřebujete ID předplatného Azure a platný certifikát správy. ID předplatného můžete získat prostřednictvím [portálu Azure][management-portal].

> [!NOTE]
> Nyní můžete používat certifikáty vytvořené pomocí OpenSSL při spuštění v systému Windows. Python 2.7.4 nebo novější je povinný. Doporučujeme použít OpenSSL místo .pfx, protože podpora pro .pfx certifikáty je pravděpodobné, že budou odebrány v budoucnu.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certifikáty správy ve Windows/Mac/Linux (OpenSSL)
K vytvoření certifikátu pro správu můžete použít [OpenSSL.](https://www.openssl.org/) Musíte vytvořit dva certifikáty, jeden pro `.cer` server (soubor) a jeden `.pem` pro klienta (soubor). Chcete-li `.pem` soubor vytvořit, spusťte:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Chcete-li `.cer` vytvořit certifikát, spusťte:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Další informace o certifikátech Azure najdete [v tématu Přehled certifikátů pro Cloudové služby Azure](cloud-services-certs-create.md). Úplný popis parametrů OpenSSL naleznete v dokumentaci k tématu [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html).

Po vytvoření těchto souborů `.cer` nahrajte soubor do Azure. Na [webu Azure Portal][management-portal]na kartě **Nastavení** vyberte **Nahrát**. Všimněte si, `.pem` kam jste soubor uložili.

Po získání ID předplatného vytvořte certifikát a `.cer` nahrajte soubor do Azure a připojte se ke koncovému bodu správy Azure. Připojte se předáním ID předplatného `.pem` a cesty k souboru do **služby ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

V předchozím příkladu `sms` je Objekt **ServiceManagementService.** Třída **ServiceManagementService** je primární třída používaná ke správě služeb Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certifikáty správy v systému Windows (MakeCert)
Pomocí aplikace `makecert.exe`můžete v počítači vytvořit certifikát správy podepsaný svým držitelem. Otevřete **příkazový řádek sady Visual Studio** jako **správce** a použijte následující příkaz, který nahradí *AzureCertificate* názvem certifikátu, který chcete použít:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Příkaz vytvoří `.cer` soubor a nainstaluje jej do úložiště **osobních** certifikátů. Další informace najdete v [tématu Přehled certifikátů pro Cloudové služby Azure](cloud-services-certs-create.md).

Po vytvoření certifikátu nahrajte `.cer` soubor do Azure. Na [webu Azure Portal][management-portal]na kartě **Nastavení** vyberte **Nahrát**.

Po získání ID předplatného vytvořte certifikát a `.cer` nahrajte soubor do Azure a připojte se ke koncovému bodu správy Azure. Připojte se předáním ID předplatného a umístění certifikátu v úložišti **osobních** certifikátů službě **ServiceManagementService** (znovu nahraďte *AzureCertificate* názvem certifikátu).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

V předchozím příkladu `sms` je Objekt **ServiceManagementService.** Třída **ServiceManagementService** je primární třída používaná ke správě služeb Azure.

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>Seznam dostupných míst
Chcete-li uvést umístění, která jsou k dispozici pro hostingové služby, použijte metodu **umístění seznamu.\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Při vytváření cloudové služby nebo služby úložiště je třeba zadat platné umístění. Metoda **\_umístění seznamu** vždy vrátí aktuální seznam aktuálně dostupných umístění. Od tohoto psaní, k dispozici místa jsou:

* Západní Evropa
* Severní Evropa
* Jihovýchodní Asie
* Východní Asie
* USA – střed
* USA – středosever
* USA – středojih
* USA – západ
* USA – východ
* Japonsko – východ
* Japonsko – západ
* Brazílie – jih
* Austrálie – východ
* Austrálie – jihovýchod

## <a name="create-a-cloud-service"></a><a name="CreateCloudService"> </a>Vytvoření cloudové služby
Když vytvoříte aplikaci a spustíte ji v Azure, kód a konfigurace společně se nazývají [cloudová služba][cloud service]Azure . (V dřívějších verzích Azure se označuje jako *hostovaná služba.)* Metodu **create\_hostované\_služby** můžete použít k vytvoření nové hostované služby. Vytvořte službu poskytnutím názvu hostované služby (který musí být jedinečný v Azure), popisek (automaticky kódovaný na base64), popis a umístění.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Můžete seznam všech hostovaných služeb pro vaše předplatné s **seznam\_hostovaných\_služeb** metody.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Chcete-li získat informace o konkrétní hostované službě, předajte název hostované služby metodě **vlastností služby\_get hostované\_služby.\_**

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Po vytvoření cloudové služby nasaďte kód do služby pomocí metody **\_vytvoření nasazení.**

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>Odstranění cloudové služby
Cloudovou službu můžete odstranit předáním názvu služby metodě **odstranění\_hostované služby.\_**

    sms.delete_hosted_service('myhostedservice')

Před odstraněním služby musí být nejprve odstraněna všechna nasazení pro službu. Další informace naleznete [v tématu Odstranění nasazení](#DeleteDeployment).

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>Odstranění nasazení
Chcete-li odstranit nasazení, použijte metodu **\_odstranění nasazení.** Následující příklad ukazuje, jak odstranit `v1`nasazení s názvem :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>Vytvoření služby úložiště
[Služba úložiště](../storage/common/storage-create-storage-account.md) umožňuje přístup k [objektům BLOB](../storage/blobs/storage-python-how-to-use-blob-storage.md)Azure , [tabulkám](../cosmos-db/table-storage-how-to-use-python.md)a [frontám](../storage/queues/storage-python-how-to-use-queue-storage.md). Chcete-li vytvořit službu úložiště, potřebujete název pro službu (mezi 3 a 24 znaky s malou písmena a jedinečné v rámci Azure). Potřebujete také popis, popisek (až 100 znaků, automaticky zakódovaný na base64) a umístění. Následující příklad ukazuje, jak vytvořit službu úložiště zadáním umístění:

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

V předchozím příkladu lze načíst stav operace **\_vytvořit účet úložiště\_** předáním výsledku vráceného **vytvořením\_účtu úložiště\_** metodě get **\_operation\_status.** 

Můžete seznam účtů úložiště a jejich vlastnosti pomocí **metody úložiště seznamu.\_\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>Odstranění služby úložiště
Chcete-li odstranit službu úložiště, předajte název služby úložiště metodě **odstranění\_účtu úložiště.\_** Odstraněním služby úložiště odstraníte všechna data uložená ve službě (objekty BLOB, tabulky a fronty).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>Seznam dostupných operačních systémů
Chcete-li uvést operační systémy, které jsou k dispozici pro hostingové služby, použijte metodu **seznamu\_operačních\_systémů.**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Alternativně můžete použít metodu **rodinoperačního\_\_systému\_seznam,** která seskupuje operační systémy podle rodiny.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>Vytvoření bitové kopie operačního systému
Chcete-li přidat bitovou kopii operačního systému do úložiště obrázků, použijte metodu **add\_os\_image.**

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

Chcete-li vypsat obrázky operačního systému, které jsou k dispozici, použijte metodu **zobrazení operačního\_systému.\_** Obsahuje všechny obrázky platformy a uživatelské obrázky.

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

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>Odstranění bitové kopie operačního systému
Chcete-li odstranit bitovou kopii uživatele, použijte metodu **odstranění\_obrázku operačního systému.\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="create-a-virtual-machine"></a><a name="CreateVM"> </a>Vytvoření virtuálního počítače
Chcete-li vytvořit virtuální počítač, musíte nejprve vytvořit [cloudovou službu](#CreateCloudService). Pak vytvořte nasazení virtuálního počítače pomocí metody **vytvoření\_nasazení\_virtuálního počítače.\_**

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

## <a name="delete-a-virtual-machine"></a><a name="DeleteVM"> </a>Odstranění virtuálního počítače
Chcete-li odstranit virtuální počítač, nejprve odstraňte nasazení pomocí metody **\_odstranění nasazení.**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Cloudovou službu lze potom odstranit pomocí metody **odstranění\_hostované služby.\_**

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Vytvoření virtuálního počítače ze zachycené image virtuálního počítače
Chcete-li zachytit image virtuálního virtuálního mísy, nejprve zavolejte metodu **image vimage\_\_pro digitalizaci.**

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

Chcete-li se ujistit, že jste úspěšně zachytili obrázek, použijte rozhraní API **pro obrázky\_virtuálních videa\_seznamu.** Zkontrolujte, zda je obrázek zobrazen ve výsledcích.

    images = sms.list_vm_images()

Chcete-li konečně vytvořit virtuální počítač pomocí zachycené image, použijte metodu **vytvoření\_nasazení virtuálního\_počítače\_** jako dříve, ale tento čas upálí v vm_image_name místo.

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

Další informace o tom, jak zachytit virtuální počítač SIP v klasickém modelu nasazení, najdete v [tématu Zachycení virtuálního počítače s Linuxem](../virtual-machines/linux/classic/capture-image-classic.md).

Další informace o zachycení virtuálního počítače s Windows v klasickém modelu nasazení najdete v [tématu Zachycení virtuálního počítače s Windows](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="next-steps"></a><a name="What's Next"> </a>Další kroky
Teď, když jste se naučili základy správy služeb, můžete získat přístup k [referenční dokumentaci complete rozhraní API pro Azure Python SDK](https://azure-sdk-for-python.readthedocs.org/) a provádět složité úlohy snadno spravovat aplikaci Pythonu.

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
[svc-mgmt-rest-api]: https://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/azure/cloud-services/
