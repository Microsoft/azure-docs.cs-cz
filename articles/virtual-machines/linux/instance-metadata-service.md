---
title: Azure Instance Metadata Service
description: Přečtěte si o Instance Metadata Service Azure a o tom, jak poskytuje informace o aktuálně spuštěných instancích virtuálních počítačů.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: 79a583575acc6e3b3b2551046d57355bbf74a663
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966798"
---
# <a name="azure-instance-metadata-service-imds"></a>Instance Metadata Service Azure (IMDS)

Služba Azure Instance Metadata Service (IMDS) poskytuje informace o aktuálně spuštěných instancích virtuálních počítačů a dá se použít ke správě a konfiguraci virtuálních počítačů.
Tyto informace zahrnují SKU, úložiště, konfigurace sítě a nadcházející události údržby. Úplný seznam dat, která jsou k dispozici, najdete v tématu [rozhraní API pro metadata](#metadata-apis).
Instance Metadata Service je k dispozici pro spouštění instancí virtuálních počítačů a sady škálování virtuálních počítačů. Všechna rozhraní API podporují virtuální počítače vytvořené nebo spravované pomocí [Azure Resource Manager](/rest/api/resources/). Pouze koncové body s omezením a sítě podporují jenom klasické virtuální počítače (non ARM) a jsou ověřené jenom v omezeném rozsahu.

Azure IMDS je koncový bod REST, který je dostupný na dobře známé IP adrese, která není směrovatelný ( `169.254.169.254` ), dá se k němu získat přístup jenom z virtuálního počítače. Komunikace mezi virtuálním počítačem a IMDS nikdy neopustí hostitele.
Osvědčeným postupem je, že klienti HTTP při dotazování na IMDS a považovat za stejné jako při dotazování na webové proxy servery v rámci virtuálního počítače `169.254.169.254` [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="security"></a>Zabezpečení

Koncový bod Instance Metadata Service je přístupný jenom v rámci spuštěné instance virtuálního počítače na IP adrese, která není směrovatelný. Kromě toho bude `X-Forwarded-For` služba odmítla všechny žádosti s hlavičkou.
Žádosti musí také obsahovat `Metadata: true` hlavičku, aby bylo zajištěno, že skutečný požadavek byl přímo určen a není součástí neúmyslného přesměrování.

> [!IMPORTANT]
> Instance Metadata Service není kanál pro citlivá data. Koncový bod je otevřen pro všechny procesy na virtuálním počítači. Informace vystavené prostřednictvím této služby by se měly považovat za sdílené informace pro všechny aplikace běžící v rámci virtuálního počítače.

## <a name="usage"></a>Využití

### <a name="accessing-azure-instance-metadata-service"></a>Přístup k Azure Instance Metadata Service

Pokud chcete získat přístup k Instance Metadata Service, vytvořte virtuální počítač z [Azure Resource Manager](/rest/api/resources/) nebo [Azure Portal](https://portal.azure.com)a postupujte podle následujících ukázek.
Další příklady dotazů na IMDS najdete v tématu [ukázky metadat instance Azure](https://github.com/microsoft/azureimds).

Níže je uvedený ukázkový kód pro načtení všech metadat pro instanci, pro přístup ke konkrétnímu zdroji dat, viz část [rozhraní API pro metadata](#metadata-apis) . 

**Žádost**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01"
```

**Response** (Odpověď)

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je poměrně vytištěn z důvodu čitelnosti.

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "licenseType":  "Windows_Client",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osProfile": {
            "adminUsername": "admin",
            "computerName": "examplevmname",
            "disablePasswordAuthentication": "true"
        },
        "osType": "linux",
        "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
        "plan": {
            "name": "planName",
            "product": "planProduct",
            "publisher": "planPublisher"
        },
        "platformFaultDomain": "36",
        "platformUpdateDomain": "42",
        "publicKeys": [{
                "keyData": "ssh-rsa 0",
                "path": "/home/user/.ssh/authorized_keys0"
            },
            {
                "keyData": "ssh-rsa 1",
                "path": "/home/user/.ssh/authorized_keys1"
            }
        ],
        "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
        "resourceGroupName": "macikgo-test-may-23",
        "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
        "securityProfile": {
            "secureBootEnabled": "true",
            "virtualTpmEnabled": "false"
        },
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
                "caching": "None",
                "createOption": "Empty",
                "diskSizeGB": "1024",
                "image": {
                    "uri": ""
                },
                "lun": "0",
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampledatadiskname",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }],
            "imageReference": {
                "id": "",
                "offer": "UbuntuServer",
                "publisher": "Canonical",
                "sku": "16.04.0-LTS",
                "version": "latest"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "createOption": "FromImage",
                "diskSizeGB": "30",
                "diffDiskSettings": {
                    "option": "Local"
                },
                "encryptionSettings": {
                    "enabled": "false"
                },
                "image": {
                    "uri": ""
                },
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampleosdiskname",
                "osType": "Linux",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }
        },
        "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    },
    "network": {
        "interface": [{
            "ipv4": {
               "ipAddress": [{
                    "privateIpAddress": "10.144.133.132",
                    "publicIpAddress": ""
                }],
                "subnet": [{
                    "address": "10.144.133.128",
                    "prefix": "26"
                }]
            },
            "ipv6": {
                "ipAddress": [
                 ]
            },
            "macAddress": "0011AAFFBB22"
        }]
    }
}
```

### <a name="data-output"></a>Výstup dat

Ve výchozím nastavení Instance Metadata Service vrátí data ve formátu JSON ( `Content-Type: application/json` ). V případě potřeby ale některá rozhraní API můžou vracet data v různých formátech.
Následující tabulka je odkazem na jiné rozhraní API datových formátů, které může podporovat.

Rozhraní API | Výchozí formát dat | Jiné formáty
--------|---------------------|--------------
/attested | json | žádné
/identity | json | žádné
/instance | json | text
/scheduledevents | json | žádné

Pokud chcete získat přístup k nevýchozímu formátu odpovědi, v žádosti určete požadovaný formát jako parametr řetězce dotazu. Například:

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Pro uzly listů v/metadata/instance `format=json` nefunguje. Pro tyto dotazy je `format=text` nutné explicitně zadat, protože výchozí formát je JSON.

### <a name="versioning"></a>Správa verzí

U Instance Metadata Service se používá verze a určení verze rozhraní API v požadavku HTTP je povinné.

Podporované verze rozhraní API: 
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01

> [!NOTE]
> V tuto chvíli je nainstalovaná verze 2020-10-01 a v každé oblasti možná není dostupná.

Při přidávání novějších verzí je k zajištění kompatibility stále k dispozici starší verze, pokud vaše skripty mají závislosti na konkrétních formátech dat.

Pokud není zadána žádná verze, je vrácena chyba se seznamem nejnovějších podporovaných verzí.

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad označuje chybový stav, pokud není zadána verze, odpověď je poměrně vytištěna pro čitelnost.

**Žádost**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance"
```

**Response** (Odpověď)

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

## <a name="metadata-apis"></a>Rozhraní API pro metadata

Metadata Service obsahuje více rozhraní API představujících různé zdroje dat.

Rozhraní API | Popis | Představená verze
----|-------------|-----------------------
/attested | Viz [Attestation data](#attested-data) | 2018-10-01
/identity | Viz [získání přístupového tokenu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) . | 2018-02-01
/instance | Viz [rozhraní API instance](#instance-api) | 2017-04-02
/scheduledevents | Viz [Scheduled Events](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>Rozhraní API instance

Rozhraní API instance zpřístupňuje důležitá metadata pro instance virtuálních počítačů, včetně virtuálních počítačů, sítí a úložiště. K následujícím kategoriím lze přistupovat prostřednictvím instance/Compute:

Data | Popis | Představená verze
-----|-------------|-----------------------
azEnvironment | Prostředí Azure, ve kterém je spuštěný virtuální počítač | 2018-10-01
customData | Tato funkce je momentálně zakázaná. Tuto dokumentaci budeme aktualizovat, jakmile bude k dispozici. | 2019-02-01
isHostCompatibilityLayerVm | Určuje, jestli se virtuální počítač spouští na vrstvě kompatibility hostitele. | 2020-06-01
licenseType | Typ licence pro [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Upozorňujeme, že tato možnost je k dispozici pouze pro virtuální počítače s podporou AHB. | 2020-09-01
location | Oblast Azure, ve které je spuštěný virtuální počítač | 2017-04-02
name | Název virtuálního počítače | 2017-04-02
offer | Informace o nabídce pro image virtuálního počítače a jsou k dispozici jenom pro Image nasazené z Galerie imagí Azure | 2017-04-02
osProfile.adminUsername | Určuje název účtu správce. | 2020-07-15
osProfile. ComputerName | Určuje název počítače. | 2020-07-15
osProfile. disablePasswordAuthentication | Určuje, jestli je zakázané ověřování heslem. Všimněte si, že toto je přítomné jenom pro virtuální počítače se systémem Linux. | 2020-10-01
osType | Linux nebo Windows | 2017-04-02
placementGroupId | [Skupina umístění](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) vaší sady škálování virtuálních počítačů | 2017-08-01
rozhraní | [Plánování](/rest/api/compute/virtualmachines/createorupdate#plan) obsahující název, produkt a vydavatele pro virtuální počítač, pokud se jedná o Azure Marketplace image | 2018-04-02
platformUpdateDomain |  [Aktualizujte doménu](../manage-availability.md) , ve které je spuštěný virtuální počítač. | 2017-04-02
platformFaultDomain | [Doména selhání](../manage-availability.md) , ve kterém je spuštěný virtuální počítač | 2017-04-02
Zprostředkovatel | Poskytovatel virtuálního počítače | 2018-10-01
publicKeys | [Kolekce veřejných klíčů](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) přiřazených k virtuálnímu počítači a cestám | 2018-04-02
vydavatel | Vydavatel image virtuálního počítače | 2017-04-02
resourceGroupName | [Skupina prostředků](../../azure-resource-manager/management/overview.md) pro virtuální počítač | 2017-08-01
resourceId | [Plně kvalifikované](/rest/api/resources/resources/getbyid) ID prostředku | 2019-03-11
skladové | Konkrétní SKU pro bitovou kopii virtuálního počítače | 2017-04-02
securityProfile. secureBootEnabled | Určuje, jestli je na virtuálním počítači povolené zabezpečené spouštění přes UEFI. | 2020-06-01
securityProfile.virtualTpmEnabled | Určuje, jestli je na virtuálním počítači povolený čip TPM (Virtual Trusted Platform Module). | 2020-06-01
storageProfile | Viz [profil úložiště](#storage-metadata) | 2019-06-01
subscriptionId | Předplatné Azure pro virtuální počítač | 2017-08-01
tags | [Značky](../../azure-resource-manager/management/tag-resources.md) pro virtuální počítač  | 2017-08-01
tagsList | Značky formátované jako pole JSON pro snazší programovou analýzu  | 2019-06-04
verze | Verze image virtuálního počítače | 2017-04-02
vmId | [Jedinečný identifikátor](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) pro virtuální počítač | 2017-04-02
vmScaleSetName | [Název sady škálování virtuálního počítače](../../virtual-machine-scale-sets/overview.md) pro sadu škálování virtuálního počítače | 2017-12-01
vmSize | [Velikost virtuálního počítače](../sizes.md) | 2017-04-02
zóna | [Zóna dostupnosti](../../availability-zones/az-overview.md) virtuálního počítače | 2017-12-01

### <a name="sample-1-tracking-vm-running-on-azure"></a>Ukázka 1: sledování virtuálního počítače běžícího na Azure

Jako poskytovatel služeb budete možná potřebovat sledovat počet virtuálních počítačů, na kterých běží váš software, nebo mít agenty, kteří potřebují ke sledování jedinečnosti virtuálního počítače. Aby bylo možné získat jedinečné ID pro virtuální počítač, použijte `vmId` pole z instance metadata Service.

**Žádost**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Response** (Odpověď)

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-containers-data-partitions-based-faultupdate-domain"></a>Ukázka 2: umístění kontejnerů, selhání nebo aktualizace na základě datových oddílů

V některých scénářích je umístění různých replik dat primárním významem. Například [umístění repliky HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) nebo umístění kontejneru přes [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) vám může vyžadovat, abyste věděli, na `platformFaultDomain` kterém `platformUpdateDomain` virtuální počítač běží.
K provedení těchto rozhodnutí můžete použít také [zóny dostupnosti](../../availability-zones/az-overview.md) pro instance.
Tato data můžete zadávat přímo prostřednictvím Instance Metadata Service.

**Žádost**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Response** (Odpověď)

```text
0
```

### <a name="sample-3-getting-more-information-about-the-vm-during-support-case"></a>Ukázka 3: získání dalších informací o virtuálním počítači během případu podpory

Jako poskytovatel služeb můžete obdržet volání podpory, kde byste chtěli získat další informace o virtuálním počítači. Dotazování zákazníků na sdílení výpočetních metadat může poskytnout základní informace o tom, že profesionální pracovník podpory ví o typu virtuálního počítače v Azure.

**Žádost**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

**Response** (Odpověď)

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je poměrně vytištěn z důvodu čitelnosti.

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "Windows-Server-2012-R2-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

### <a name="sample-4-getting-azure-environment-where-the-vm-is-running"></a>Ukázka 4: získání prostředí Azure, ve kterém je virtuální počítač spuštěný

Azure má různé cloudy svrchovan jako [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Někdy potřebujete prostředí Azure, abyste mohli provádět určitá rozhodnutí za běhu. Následující příklad ukazuje, jak lze dosáhnout tohoto chování.

**Žádost**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Response** (Odpověď)

```text
AzurePublicCloud
```

Cloud a hodnoty prostředí Azure jsou uvedené níže.

 Cloud   | Prostředí Azure
---------|-----------------
[Všechny všeobecně dostupné globální oblasti Azure](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure (Čína) 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure (Německo)](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Síťová metadata 

Síťová metadata jsou součástí rozhraní API instance. V rámci koncového bodu instance/sítě jsou k dispozici následující kategorie sítě.

Data | Popis | Představená verze
-----|-------------|-----------------------
IPv4/privateIpAddress | Místní IPv4 adresa virtuálního počítače | 2017-04-02
IPv4/publicIpAddress | Veřejná IPv4 adresa virtuálního počítače | 2017-04-02
podsíť/adresa | Adresa podsítě virtuálního počítače | 2017-04-02
podsíť/předpona | Předpona podsítě, příklad 24 | 2017-04-02
IPv6/ipAddress | Místní IPv6 adresa virtuálního počítače | 2017-04-02
macAddress | Adresa MAC virtuálního počítače | 2017-04-02

> [!NOTE]
> Všechny odpovědi rozhraní API jsou řetězce JSON. Všechny následující příklady odpovědí jsou poměrně vytištěny pro čitelnost.

#### <a name="sample-1-retrieving-network-information"></a>Ukázka 1: načítání informací o síti

**Žádost**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Response** (Odpověď)

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je poměrně vytištěn z důvodu čitelnosti.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="sample-2-retrieving-public-ip-address"></a>Ukázka 2: načtení veřejné IP adresy

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Metadata úložiště

Metadata úložiště jsou součástí rozhraní API instance pod koncovým bodem instance/COMPUTE/storageProfile.
Poskytuje podrobnosti o discích úložiště přidružených k virtuálnímu počítači. 

Profil úložiště virtuálního počítače je rozdělen do tří kategorií: odkaz na obrázek, disk s operačním systémem a datové disky.

Objekt odkazu na bitovou kopii obsahuje následující informace o imagi operačního systému:

Data    | Popis
--------|-----------------
id      | ID prostředku
offer   | Nabídka platformy nebo Image Marketplace
vydavatel | Vydavatel obrázku
skladové     | SKU image
verze | Verze image platformy nebo webu Marketplace

Objekt disku operačního systému obsahuje následující informace o disku s operačním systémem, který používá virtuální počítač:

Data    | Popis
--------|-----------------
vyrovnávací | Požadavky na ukládání do mezipaměti
createOption | Informace o tom, jak byl virtuální počítač vytvořen
diffDiskSettings | Nastavení dočasného disku
diskSizeGB | Velikost disku v GB
image   | Virtuální pevný disk zdrojové image uživatele
(     | Logické číslo jednotky disku
managedDisk | Parametry spravovaného disku
name    | Název disku
virtuálního     | Virtuální pevný disk
writeAcceleratorEnabled | Bez ohledu na to, jestli je na disku povolená writeAccelerator

Pole datových disků obsahuje seznam datových disků připojených k virtuálnímu počítači. Každý objekt datového disku obsahuje následující informace:

Data    | Popis
--------|-----------------
vyrovnávací | Požadavky na ukládání do mezipaměti
createOption | Informace o tom, jak byl virtuální počítač vytvořen
diffDiskSettings | Nastavení dočasného disku
diskSizeGB | Velikost disku v GB
encryptionSettings | Nastavení šifrování disku
image   | Virtuální pevný disk zdrojové image uživatele
managedDisk | Parametry spravovaného disku
name    | Název disku
osType  | Typ operačního systému zahrnutý na disku
virtuálního     | Virtuální pevný disk
writeAcceleratorEnabled | Bez ohledu na to, jestli je na disku povolená writeAccelerator

Následující příklad ukazuje, jak zadat dotaz na informace o úložišti virtuálního počítače.

**Žádost**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Response** (Odpověď)

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je poměrně vytištěn z důvodu čitelnosti.

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

## <a name="vm-tags"></a>Značky VM

Značky virtuálních počítačů jsou součástí rozhraní API instancí pod koncovým bodem instance/výpočty/značky.
Na VIRTUÁLNÍm počítači Azure možná byly aplikovány značky, aby je bylo možné logicky uspořádat do taxonomie. Značky přiřazené k virtuálnímu počítači se dají načíst pomocí níže uvedeného požadavku.

**Žádost**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Response** (Odpověď)

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags`Pole je řetězec, jehož značky jsou odděleny středníky. Tento výstup může být problémem, pokud se středníky používají v samotných značkách. Pokud je analyzátor napsán pro programové extrakci značek, měli byste spoléhat na `tagsList` pole. `tagsList`Pole je pole JSON bez oddělovačů, a proto je snazší ho analyzovat.

**Žádost**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04"
```

**Response** (Odpověď)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

## <a name="attested-data"></a>Ověřená data

Součástí scénáře, který obsluhuje Instance Metadata Service, je poskytnout záruky, že poskytnutá data přicházejí z Azure. Tyto informace podepisujeme, aby image na webu Marketplace mohly mít jistotu, že se jedná o image běžící v Azure.

### <a name="sample-1-getting-attested-data"></a>Ukázka 1: získání ověřených dat

> [!NOTE]
> Všechny odpovědi rozhraní API jsou řetězce JSON. Následující příklad odpovědí je poměrně vytištěn z důvodu čitelnosti.

**Žádost**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

Verze API-Version je povinné pole. Podporované verze rozhraní API najdete v [části věnované používání](#usage) .
Hodnota nonce je nepovinný řetězec s deseti číslicemi. Pokud není zadán, vrátí IMDS aktuální časové razítko UTC na svém místě.

> [!NOTE]
> Z důvodu mechanismu ukládání do mezipaměti IMDS může být vrácena hodnota náhodně v mezipaměti.

**Response** (Odpověď)

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je poměrně vytištěn z důvodu čitelnosti.

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Objekt BLOB podpisu je verze dokumentu s podpisem [PKCS7](https://aka.ms/pkcs7) . Obsahuje certifikát použitý k podepsání spolu s určitými podrobnostmi specifickými pro virtuální počítače. U virtuálních počítačů ARM to zahrnuje vmId, SKU, nonce, subscriptionId, časové razítko pro vytváření a vypršení platnosti dokumentu a informace o plánu k imagi. Informace o plánu se naplní jenom pro Azure Marketplace image. U klasických virtuálních počítačů (ne ARM) je zaručeno, že se naplní pouze vmId. Certifikát se dá extrahovat z odpovědi a použít k ověření, že odpověď je platná a přichází z Azure.
Dokument obsahuje následující pole:

Data | Popis | Představená verze
-----|-------------|-----------------------
licenseType | Typ licence pro [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Upozorňujeme, že tato možnost je k dispozici pouze pro virtuální počítače s podporou AHB. | 2020-09-01
generované | Řetězec, který může být volitelně poskytnutý požadavkem. Pokud nebyla zadána žádná hodnota nonce, použije se aktuální časové razítko UTC. | 2018-10-01
rozhraní | [Azure Marketplace plán obrázku](/rest/api/compute/virtualmachines/createorupdate#plan). Obsahuje ID plánu (název), obrázek produktu nebo nabídku (produkt) a ID vydavatele (vydavatel). | 2018-10-01
časové razítko/createdOn | Časové razítko UTC pro vytvoření podepsaného dokumentu | 2018-20-01
časové razítko/expiresOn | Časové razítko UTC pro vypršení platnosti podepsaného dokumentu | 2018-10-01
vmId |  [Jedinečný identifikátor](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) pro virtuální počítač | 2018-10-01
subscriptionId | Předplatné Azure pro virtuální počítač | 2019-04-30
skladové | Konkrétní SKU pro bitovou kopii virtuálního počítače | 2019-11-01

> [!NOTE]
> U klasických virtuálních počítačů (ne ARM) je zaručeno, že se naplní pouze vmId.

### <a name="sample-2-validating-that-the-vm-is-running-in-azure"></a>Ukázka 2: ověření, že virtuální počítač běží v Azure

Dodavatelé na webu Marketplace chtějí zajistit, aby byl software licencován pro spouštění pouze v Azure. Pokud někdo zkopíruje virtuální pevný disk z místního počítače do místní aplikace, musí mít možnost ho detekovat. Voláním do Instance Metadata Service můžou dodavatelé na webu Marketplace získat podepsaná data, která garantuje odpověď jenom z Azure.

> [!NOTE]
> Vyžaduje instalaci JQ.

**Žádost**

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Response** (Odpověď)

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Ověřte, že signatura pochází z Microsoft Azure a zkontrolujte chyby v řetězu certifikátů.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

> [!NOTE]
> Z důvodu mechanismu ukládání do mezipaměti IMDS může být vrácena hodnota náhodně v mezipaměti.

Hodnota nonce v podepsaném dokumentu se dá porovnávat, pokud jste v počátečním požadavku zadali parametr nonce.

> [!NOTE]
> Certifikát pro veřejný cloud a Cloud z svrchovaného cloudu se liší.

Cloud | Certifikát
------|------------
[Všechny všeobecně dostupné globální oblasti Azure](https://azure.microsoft.com/regions/) | *. metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *. metadata.azure.us
[Azure (Čína) 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *. metadata.azure.cn
[Azure (Německo)](https://azure.microsoft.com/overview/clouds/germany/)                | *. metadata.microsoftazure.de

> [!NOTE]
> Došlo k známému problému kolem certifikátu použitého k podepsání. Certifikáty nemusí mít přesnou shodu `metadata.azure.com` pro veřejný cloud. Ověřování certifikace by proto mělo umožňovat běžný název z jakékoli `.metadata.azure.com` subdomény.

V případech, kdy se zprostředkující certifikát nedá stáhnout kvůli omezením sítě během ověřování, jde zprostředkující certifikát připnout. Azure ale převezme certifikáty podle standardních postupů PKI. Připnuté certifikáty by se musely aktualizovat, když dojde k přechodu. Pokaždé, když se naplánuje změna aktualizace zprostředkujícího certifikátu, bude se aktualizovat blog Azure a budou se zákazníkům Azure informovat. Zprostředkující certifikáty najdete [tady](https://www.microsoft.com/pki/mscorp/cps/default.htm). Zprostředkující certifikáty pro jednotlivé oblasti se můžou lišit.

> [!NOTE]
> Zprostředkující certifikát pro Azure Čína 21Vianet bude z globální kořenové certifikační autority DigiCert místo Baltimore.
I když jste připnuli zprostředkující certifikáty pro Azure Čína jako součást změny kořenového certifikační autority, bude nutné aktualizovat zprostředkující certifikáty.

## <a name="managed-identity-via-metadata-service"></a>Spravovaná identita prostřednictvím Metadata Service

Na virtuálním počítači se dá povolit spravovaná identita přiřazená systémem nebo se k virtuálnímu počítači dá přiřadit aspoň jedna spravovaná identita přiřazená uživateli.
Tokeny pro spravované identity je pak možné požadovat od Instance Metadata Service. Tyto tokeny se dají použít k ověřování s ostatními službami Azure, jako je Azure Key Vault.

Podrobné pokyny k povolení této funkce najdete v tématu [získání přístupového tokenu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events-via-metadata-service"></a>Scheduled Events přes Metadata Service
Můžete získat stav naplánovaných událostí prostřednictvím služby metadat a pak může uživatel zadat sadu akcí, které se budou provádět na těchto událostech.  Podrobnosti najdete v tématu [Scheduled Events](scheduled-events.md) . 

## <a name="regional-availability"></a>Dostupnost podle oblastí

Služba je **všeobecně dostupná** ve všech cloudech Azure.

## <a name="sample-code-in-different-languages"></a>Ukázkový kód v různých jazycích

Ukázky volání služby metadat pomocí různých jazyků uvnitř virtuálního počítače:

Jazyk      | Příklad
--------------|----------------
Bash          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Přejít            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="error-and-debugging"></a>Chyba a ladění

Pokud se nenašel datový prvek nebo dojde k chybnému požadavku, Instance Metadata Service vrátí standardní chyby protokolu HTTP. Například:

Stavový kód HTTP | Důvod
----------------|-------
200 OK |
400 – Chybný požadavek | `Metadata: true` `format=json` Při dotazování na uzel typu list chybí záhlaví nebo chybějící parametr.
404 Nenalezeno | Požadovaný element neexistuje.
Metoda 405 není povolená. | `GET`Jsou podporovány pouze požadavky.
410 pryč | Opakovat po uplynutí určité doby na maximum 70 sekund
429 – Příliš mnoho požadavků | Rozhraní API aktuálně podporuje maximálně 5 dotazů za sekundu.
Chyba služby 500     | Zkusit znovu za chvíli

### <a name="known-issues-and-faq"></a>Známé problémy a nejčastější dotazy

1. Zobrazuje se chyba `400 Bad Request, Required metadata header not specified` . Co to znamená?
   * Instance Metadata Service vyžaduje, aby se hlavička `Metadata: true` předala v žádosti. Předání této hlavičky v volání REST umožňuje přístup k Instance Metadata Service.
1. Proč mi nezískávám výpočetní informace pro svůj virtuální počítač?
   * V současné době Instance Metadata Service podporuje jenom instance vytvořené pomocí Azure Resource Manager.
1. Tento virtuální počítač jsem vytvořil přes Azure Resource Manager a během zálohování. Proč se mi nezobrazuje informace o metadatech COMPUTE?
   * Pro všechny virtuální počítače vytvořené po SEP 2016 přidejte [značku](../../azure-resource-manager/management/tag-resources.md) , která začne zobrazovat metadata Compute. Pro starší virtuální počítače (vytvořené před SEP 2016) přidejte nebo odeberte rozšíření nebo datové disky k instancím virtuálních počítačů a aktualizujte metadata.
1. Nezobrazuje se všechna data naplněná pro novou verzi
   * Pro všechny virtuální počítače vytvořené po SEP 2016 přidejte [značku](../../azure-resource-manager/management/tag-resources.md) , která začne zobrazovat metadata Compute. Pro starší virtuální počítače (vytvořené před SEP 2016) přidejte nebo odeberte rozšíření nebo datové disky k instancím virtuálních počítačů a aktualizujte metadata.
1. Proč se mi zobrazuje chyba `500 Internal Server Error` `410 Resource Gone` ?
   * Opakujte požadavek na základě exponenciálního systému nebo jiných metod popsaných v tématu [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults). Pokud s tím budou dál problémy, vytvořte v Azure Portal pro virtuální počítač problém podpory.
1. Budou to fungovat pro instance sady škálování virtuálních počítačů?
   * Služba metadat pro instance sady škálování je k dispozici.
1. Aktualizoval (a) jsem moje značky v Virtual Machine Scale Sets, ale nezobrazují se v instancích na rozdíl od virtuálních počítačů s jednou instancí?
   * V současné době se Tagy pro sady škálování zobrazují jenom na virtuálním počítači při restartování, obnovení Image nebo změně disku na instanci.
1. Vypršel časový limit žádosti o mé volání služby?
   * Volání metadat je nutné provést z primární IP adresy přiřazené k primární síťové kartě virtuálního počítače. V případě, že jste změnili trasy, musí existovat trasa pro adresu 169.254.169.254/32 v místní směrovací tabulce vašeho virtuálního počítače.
   * <details>
        <summary>Ověřuje se tabulka směrování.</summary>

        1. Vypíšete místní směrovací tabulku pomocí příkazu, například `netstat -r` a vyhledejte položku IMDS (např.):
            ```console
            ~$ netstat -r
            Kernel IP routing table
            Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
            default         _gateway        0.0.0.0         UG        0 0          0 eth0
            168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
            169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
            172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
            ```
        1. Ověřte, zda trasa existuje pro `169.254.169.254` , a poznamenejte si odpovídající síťové rozhraní (např. `eth0` ).
        1. Vypíše konfiguraci rozhraní pro odpovídající rozhraní ve směrovací tabulce (Všimněte si, že se může změnit přesný název konfiguračního souboru)
            ```console
            ~$ cat /etc/netplan/50-cloud-init.yaml
            network:
            ethernets:
                eth0:
                    dhcp4: true
                    dhcp4-overrides:
                        route-metric: 100
                    dhcp6: false
                    match:
                        macaddress: 00:0d:3a:e4:c7:2e
                    set-name: eth0
            version: 2
            ```
        1. Pokud používáte dynamickou IP adresu, poznamenejte si adresu MAC. Pokud používáte statickou IP adresu, můžete si uvedené IP adresy nebo adresu MAC poznamenat.
        1. Potvrďte, že rozhraní odpovídá primární síťové kartě virtuálního počítače a primární IP adrese. Primární síťovou kartu nebo IP adresu najdete v části Konfigurace sítě v Azure Portal nebo jejich vyhledáním [pomocí Azure CLI](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-show). Poznamenejte si veřejné a privátní IP adresy (a adresu MAC, pokud používáte rozhraní příkazového řádku). Příklad rozhraní příkazového řádku PowerShellu:
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: ipexample606 True 00-0D-3A-E4-C7-2E
            ```
        1. Pokud se neshodují, aktualizujte směrovací tabulku tak, aby byla primární síťová karta/IP cílem.
    </details>

## <a name="support-and-feedback"></a>Podpora a váš názor

Pošlete nám svůj názor a komentáře https://feedback.azure.com .

Pokud chcete získat podporu pro službu, vytvořte problém podpory v Azure Portal pro virtuální počítač, ke kterému nemůžete po dlouhou dobu pokusů získat odpověď na metadata.
Použijte typ problému `Management` a `Instance Metadata Service` jako kategorii vyberte.

![Podpora metadat instance](./media/instance-metadata-service/InstanceMetadata-support.png "Snímek obrazovky: otevření případu podpory při potížích s Instance Metadata Service")

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace:
1. [Získání přístupového tokenu pro virtuální počítač](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).
1. [Scheduled Events](scheduled-events.md)