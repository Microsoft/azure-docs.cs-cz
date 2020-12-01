---
title: Instance Metadata Service Azure pro Windows
description: Přečtěte si o Instance Metadata Service Azure a o tom, jak poskytuje informace o aktuálně spuštěných instancích virtuálních počítačů ve Windows.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: cc0d47807101a3cfbb26e7ea69cc7d117d3f9b31
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435225"
---
# <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

Služba Azure Instance Metadata Service (IMDS) poskytuje informace o aktuálně spuštěných instancích virtuálních počítačů. Můžete ji použít ke správě a konfiguraci virtuálních počítačů.
Tyto informace zahrnují SKU, úložiště, konfigurace sítě a nadcházející události údržby. Úplný seznam dostupných dat najdete v tématu [rozhraní API pro metadata](#metadata-apis).


IMDS je k dispozici pro spuštěné instance virtuálních počítačů a instance sady škálování virtuálních počítačů. Všechna rozhraní API podporují virtuální počítače vytvořené a spravované pomocí [Azure Resource Manager](/rest/api/resources/). Jenom virtuální počítače, které jsou vytvořené pomocí modelu nasazení Classic, podporují jenom koncové body s ověřováním identity a sítě. Ověřený koncový bod funguje tak pouze v omezeném rozsahu.

IMDS je koncový bod REST, který je k dispozici na dobře známé IP adrese, která není směrovatelný ( `169.254.169.254` ). K němu přistupujete jenom z virtuálního počítače. Komunikace mezi virtuálním počítačem a IMDS nikdy neopustí hostitele.
Klienti HTTP při dotazování IMDS vynechá webové proxy servery v rámci virtuálního počítače a považovat `169.254.169.254` za stejné jako [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="security"></a>Zabezpečení

Koncový bod IMDS je přístupný jenom v rámci spuštěné instance virtuálního počítače na IP adrese, která není směrovatelný. Kromě toho bude `X-Forwarded-For` služba odmítla všechny žádosti s hlavičkou.
Požadavky musí obsahovat také `Metadata: true` hlavičku, aby bylo zajištěno, že skutečný požadavek byl přímo určen a není součástí neúmyslného přesměrování.

> [!IMPORTANT]
> IMDS není kanál pro citlivá data. Koncový bod je otevřený pro všechny procesy na virtuálním počítači. Zvažte informace vystavené prostřednictvím této služby jako sdílené informace pro všechny aplikace běžící v rámci virtuálního počítače.

## <a name="usage"></a>Využití

### <a name="access-azure-instance-metadata-service"></a>Přístup k Azure Instance Metadata Service

Pokud chcete získat přístup k IMDS, vytvořte virtuální počítač z [Azure Resource Manager](/rest/api/resources/) nebo [Azure Portal](https://portal.azure.com)a použijte následující ukázky.
Další příklady najdete v tématu [ukázky metadat instance Azure](https://github.com/microsoft/azureimds).

Zde je ukázkový kód pro načtení všech metadat pro instanci. Pokud chcete získat přístup ke konkrétnímu zdroji dat, přečtěte si část [rozhraní API metadat](#metadata-apis) . 

**Žádost**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance?api-version=2020-09-01 | ConvertTo-Json
```
> [!NOTE]
> `-NoProxy`Příznak je k dispozici pouze v PowerShellu 6 nebo novějším. Příznak můžete vynechat, pokud nemáte instalaci proxy serveru.

**Response** (Odpověď)

> [!NOTE]
> Odpověď je řetězec JSON. Přesměrování dotazu REST prostřednictvím `ConvertTo-Json` rutiny pro poměrně tisk

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

Ve výchozím nastavení vrátí IMDS data ve formátu JSON ( `Content-Type: application/json` ). Některá rozhraní API však mohou vracet data v různých formátech, pokud jsou požadovány.
Následující tabulka obsahuje seznam dalších datových formátů, které mohou rozhraní API podporovat.

Rozhraní API | Výchozí formát dat | Jiné formáty
--------|---------------------|--------------
/attested | json | žádné
/identity | json | žádné
/instance | json | text
/scheduledevents | json | žádné

Pokud chcete získat přístup k nevýchozímu formátu odpovědi, v žádosti určete požadovaný formát jako parametr řetězce dotazu. Příklad:

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> U koncových uzlů v `/metadata/instance` `format=json` nefunguje. Pro tyto dotazy je `format=text` nutné explicitně zadat, protože výchozí formát je JSON.

### <a name="version"></a>Verze

IMDS má verzi a určení verze rozhraní API v požadavku HTTP je povinné.

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
> Verze 2020-10-01 možná není v každé oblasti dostupná.

Při přidávání novějších verzí můžete ke kompatibilitě přistupovat starší verze, pokud vaše skripty mají závislosti na konkrétních formátech dat.

Pokud nezadáte žádnou verzi, zobrazí se chybová zpráva se seznamem nejnovějších podporovaných verzí.

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad označuje chybový stav, pokud není zadána verze. Odpověď je poměrně vytištěna z důvodu čitelnosti.

**Žádost**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance
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

IMDS obsahuje několik rozhraní API představujících různé zdroje dat.

Rozhraní API | Popis | Představená verze
----|-------------|-----------------------
/attested | Viz [Attestation data](#attested-data) | 2018-10-01
/identity | Viz [získání přístupového tokenu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) . | 2018-02-01
/instance | Viz [rozhraní API instance](#instance-api) | 2017-04-02
/scheduledevents | Zobrazit [naplánované události](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>Rozhraní API instance

Rozhraní API instance zpřístupňuje důležitá metadata pro instance virtuálních počítačů, včetně virtuálních počítačů, sítí a úložiště. K následujícím kategoriím můžete získat přístup prostřednictvím `instance/compute` :

Data | Popis | Představená verze
-----|-------------|-----------------------
azEnvironment | Prostředí Azure, ve kterém je virtuální počítač spuštěný. | 2018-10-01
customData | Tato funkce je momentálně zakázaná. | 2019-02-01
isHostCompatibilityLayerVm | Určuje, jestli se virtuální počítač spouští na vrstvě kompatibility hostitele. | 2020-06-01
licenseType | Typ licence pro [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Upozorňujeme, že tato možnost je k dispozici pouze pro virtuální počítače s podporou AHB. | 2020-09-01
location | Oblast Azure, ve které je virtuální počítač spuštěný. | 2017-04-02
name | Název virtuálního počítače | 2017-04-02
offer | Informace o nabídce k imagi virtuálního počítače Tento příklad je k dispozici pouze pro Image nasazené z Galerie imagí Azure. | 2017-04-02
osProfile.adminUsername | Určuje název účtu správce. | 2020-07-15
osProfile. ComputerName | Určuje název počítače. | 2020-07-15
osProfile. disablePasswordAuthentication | Určuje, jestli je zakázané ověřování heslem. Všimněte si, že toto je k dispozici pouze pro virtuální počítače se systémem Linux. | 2020-10-01
osType | Linux nebo Windows. | 2017-04-02
placementGroupId | [Skupina umístění](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) vaší sady škálování virtuálních počítačů. | 2017-08-01
rozhraní | [Plán](/rest/api/compute/virtualmachines/createorupdate#plan) obsahující název, produkt a vydavatele pro virtuální počítač, pokud se jedná o Azure Marketplace image. | 2018-04-02
platformUpdateDomain |  [Aktualizujte doménu](../manage-availability.md) , ve které je virtuální počítač spuštěný. | 2017-04-02
platformFaultDomain | [Doména selhání](../manage-availability.md) , ve které je spuštěný virtuální počítač. | 2017-04-02
Zprostředkovatel | Poskytovatel virtuálního počítače. | 2018-10-01
publicKeys | [Kolekce veřejných klíčů](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) přiřazených k virtuálnímu počítači a cestám | 2018-04-02
vydavatel | Vydavatel image virtuálního počítače | 2017-04-02
resourceGroupName | [Skupina prostředků](../../azure-resource-manager/management/overview.md) pro virtuální počítač. | 2017-08-01
resourceId | [Plně kvalifikované](/rest/api/resources/resources/getbyid) ID prostředku. | 2019-03-11
skladové | Konkrétní SKU pro bitovou kopii virtuálního počítače. | 2017-04-02
securityProfile. secureBootEnabled | Určuje, jestli je na virtuálním počítači povolené zabezpečené spouštění přes UEFI. | 2020-06-01
securityProfile.virtualTpmEnabled | Určuje, jestli je na virtuálním počítači povolený čip TPM (Virtual Trusted Platform Module). | 2020-06-01
storageProfile | Viz [profil úložiště](#storage-metadata). | 2019-06-01
subscriptionId | Předplatné Azure pro virtuální počítač. | 2017-08-01
tags | [Značky](../../azure-resource-manager/management/tag-resources.md) pro váš virtuální počítač.  | 2017-08-01
tagsList | Značky formátované jako pole JSON pro snazší programovou analýzu.  | 2019-06-04
verze | Verze image virtuálního počítače | 2017-04-02
vmId | [Jedinečný identifikátor](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) pro virtuální počítač | 2017-04-02
vmScaleSetName | [Název sady škálování virtuálního počítače](../../virtual-machine-scale-sets/overview.md) pro sadu škálování virtuálního počítače. | 2017-12-01
vmSize | Viz [Velikost virtuálního počítače](../sizes.md). | 2017-04-02
zóna | [Zóna dostupnosti](../../availability-zones/az-overview.md) virtuálního počítače. | 2017-12-01

### <a name="sample-1-track-a-vm-running-on-azure"></a>Ukázka 1: sledování virtuálního počítače běžícího na Azure

Jako poskytovatel služeb možná budete potřebovat sledovat počet virtuálních počítačů, na kterých běží váš software, nebo mít agenty, kteří potřebují sledovat jedinečnost virtuálního počítače. Aby bylo možné získat jedinečné ID pro virtuální počítač, použijte `vmId` pole z IMDS.

**Žádost**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Response** (Odpověď)

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-different-data-replicas"></a>Ukázka 2: umístění různých replik dat

V některých scénářích je umístění různých replik dat primárním významem. Například [umístění repliky HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) nebo umístění kontejneru přes [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) vám může vyžadovat, abyste věděli, na `platformFaultDomain` kterém `platformUpdateDomain` virtuální počítač běží.
K provedení těchto rozhodnutí můžete použít také [zóny dostupnosti](../../availability-zones/az-overview.md) pro instance.
Tato data můžete zadávat přímo přes IMDS.

**Žádost**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Response** (Odpověď)

```text
0
```

### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>Ukázka 3: získání dalších informací o virtuálním počítači během případu podpory

Jako poskytovatel služeb můžete obdržet volání podpory, kde chcete získat další informace o virtuálním počítači. V tomto případě může být užitečné, aby zákazník požádal o sdílení výpočetních metadat.

**Žádost**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01
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

### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>Ukázka 4: získání prostředí Azure, ve kterém je virtuální počítač spuštěný

Azure má různé cloudy svrchované služby, například [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Někdy potřebujete prostředí Azure, abyste mohli provádět určitá rozhodnutí za běhu. Následující příklad ukazuje, jak lze dosáhnout tohoto chování.

**Žádost**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Response** (Odpověď)

```text
AzurePublicCloud
```

Tady je uvedený Cloud a hodnoty prostředí Azure.

 Cloud   | Prostředí Azure
---------|-----------------
[Všechny všeobecně dostupné globální oblasti Azure](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure (Čína) 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure (Německo)](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Síťová metadata 

Síťová metadata jsou součástí rozhraní API instance. V rámci koncového bodu jsou k dispozici následující kategorie sítě `instance/network` .

Data | Popis | Představená verze
-----|-------------|-----------------------
IPv4/privateIpAddress | Místní IPv4 adresa virtuálního počítače. | 2017-04-02
IPv4/publicIpAddress | Veřejná IPv4 adresa virtuálního počítače. | 2017-04-02
podsíť/adresa | Adresa podsítě virtuálního počítače. | 2017-04-02
podsíť/předpona | Předpona podsítě. Příklad: 24 | 2017-04-02
IPv6/ipAddress | Místní adresa IPv6 virtuálního počítače. | 2017-04-02
macAddress | Adresa MAC virtuálního počítače. | 2017-04-02

> [!NOTE]
> Všechny odpovědi rozhraní API jsou řetězce JSON. Všechny následující příklady odpovědí jsou poměrně vytištěny pro čitelnost.

#### <a name="sample-1-retrieve-network-information"></a>Ukázka 1: načtení informací o síti

**Žádost**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/network?api-version=2017-08-01
```

**Response** (Odpověď)

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

#### <a name="sample-2-retrieve-public-ip-address"></a>Ukázka 2: načtení veřejné IP adresy

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Metadata úložiště

Metadata úložiště jsou součástí rozhraní API instance v rámci `instance/compute/storageProfile` koncového bodu.
Poskytuje podrobnosti o discích úložiště přidružených k virtuálnímu počítači. 

Profil úložiště virtuálního počítače je rozdělen do tří kategorií: odkaz na obrázek, disk s operačním systémem a datové disky.

Objekt odkazu na bitovou kopii obsahuje následující informace o bitové kopii operačního systému:

Data    | Popis
--------|-----------------
id      | ID prostředku
offer   | Nabídka platformy nebo Image
vydavatel | Vydavatel obrázku
skladové     | SKU image
verze | Verze platformy nebo Image

Objekt disku operačního systému obsahuje následující informace o disku operačního systému používaném virtuálním počítačem:

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
osType  | Typ operačního systému, který je součástí disku
virtuálního     | Virtuální pevný disk
writeAcceleratorEnabled | Bez ohledu na to, jestli `writeAccelerator` je disk povolený

Pole datových disků obsahuje seznam datových disků připojených k virtuálnímu počítači. Každý objekt datového disku obsahuje následující informace:

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
writeAcceleratorEnabled | Bez ohledu na to, jestli `writeAccelerator` je disk povolený

Následující příklad ukazuje, jak zadat dotaz na informace o úložišti virtuálního počítače.

**Žádost**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01
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

Značky virtuálních počítačů jsou součástí rozhraní API instancí v rámci `instance/compute/tags` koncového bodu.
Na VIRTUÁLNÍm počítači Azure možná byly aplikovány značky, aby je bylo možné logicky uspořádat do taxonomie. Pomocí následující žádosti můžete načíst značky přiřazené k virtuálnímu počítači.

**Žádost**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Response** (Odpověď)

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags`Pole je řetězec, jehož značky jsou odděleny středníky. Tento výstup může být problémem, pokud se středníky používají v samotných značkách. Pokud je analyzátor napsán pro programové extrakci značek, měli byste spoléhat na `tagsList` pole. `tagsList`Pole je pole JSON bez oddělovačů, a proto je snazší ho analyzovat.

**Žádost**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04
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

IMDS pomáhá zajistit záruky, že poskytnutá data přicházejí z Azure. Společnost Microsoft podepisuje tyto informace, takže si můžete ověřit, že image v Azure Marketplace je ta, kterou používáte v Azure.

### <a name="sample-1-get-attested-data"></a>Ukázka 1: získání ověřených dat

> [!NOTE]
> Všechny odpovědi rozhraní API jsou řetězce JSON. Následující příklad odpovědí je poměrně vytištěn z důvodu čitelnosti.

**Žádost**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

> [!NOTE]
> Z důvodu mechanismu ukládání do mezipaměti IMDS `nonce` může být vrácena dříve hodnota uložená v mezipaměti.

`Api-version` je povinné pole. Podporované verze rozhraní API najdete v [části věnované používání](#usage) .
`Nonce` je volitelný řetězec s deseti číslicemi. Pokud není zadaný, vrátí IMDS na svém místě aktuální koordinované časové razítko (UTC).

**Response** (Odpověď)

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Objekt BLOB podpisu je verze dokumentu podepsaná pomocí [PKCS7](https://aka.ms/pkcs7). Obsahuje certifikát použitý k podepsání spolu s určitými podrobnostmi specifickými pro virtuální počítače. 

Pro virtuální počítače vytvořené pomocí Azure Resource Manager to zahrnuje `vmId` ,, `sku` , `nonce` `subscriptionId` , `timeStamp` pro vytváření a vypršení platnosti dokumentu a informace o plánu k imagi. Informace o plánu se naplní jenom pro Azure Marketplace image. 

U virtuálních počítačů vytvořených pomocí modelu nasazení Classic `vmId` je zaručeno, že bude naplněna pouze hodnota. Certifikát můžete extrahovat z odpovědi a použít ho k potvrzení, že odpověď je platná a přichází z Azure.

Dokument obsahuje následující pole:

Data | Popis | Představená verze
-----|-------------|-----------------------
licenseType | Typ licence pro [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Upozorňujeme, že tato možnost je k dispozici pouze pro virtuální počítače s podporou AHB. | 2020-09-01
generované | Řetězec, který může být volitelně poskytnutý požadavkem. Pokud `nonce` nebyla zadána žádná, použije se aktuální koordinovaný světový časový razítko. | 2018-10-01
rozhraní | [Azure Marketplace plán obrázku](/rest/api/compute/virtualmachines/createorupdate#plan). Obsahuje ID plánu (název), obrázek produktu nebo nabídku (produkt) a ID vydavatele (vydavatel). | 2018-10-01
časové razítko/createdOn | Koordinovaný světový časový razítko pro čas vytvoření podepsaného dokumentu. | 2018-20-01
časové razítko/expiresOn | Koordinovaný světový časový razítko pro dobu, kdy vypršela platnost podepsaného dokumentu. | 2018-10-01
vmId |  [Jedinečný identifikátor](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) pro virtuální počítač | 2018-10-01
subscriptionId | Předplatné Azure pro virtuální počítač. | 2019-04-30
skladové | Konkrétní SKU pro bitovou kopii virtuálního počítače. | 2019-11-01

### <a name="sample-2-validate-that-the-vm-is-running-in-azure"></a>Ukázka 2: ověření, jestli je virtuální počítač spuštěný v Azure

Dodavatelé v Azure Marketplace chtějí zajistit, aby byl software licencován pro spouštění pouze v Azure. Pokud někdo zkopíruje virtuální pevný disk do místního prostředí, musí být tento dodavatel schopný detekovat. Prostřednictvím IMDS můžou tito dodavatelé získat podepsaná data, která garantuje odpověď jenom z Azure.

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Ověřte, že signatura pochází z Microsoft Azure a zkontrolujte chyby v řetězu certifikátů.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

> [!NOTE]
> Z důvodu mechanismu ukládání do mezipaměti IMDS `nonce` může být vrácena dříve hodnota uložená v mezipaměti.

`nonce`V podepsaném dokumentu může být porovnán, pokud jste zadali `nonce` parametr v počátečním požadavku.

> [!NOTE]
> Certifikát pro veřejný cloud a každý z svrchovaného cloudu se budou lišit.

Cloud | Certifikát
------|------------
[Všechny všeobecně dostupné globální oblasti Azure](https://azure.microsoft.com/regions/) | *. metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *. metadata.azure.us
[Azure (Čína) 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *. metadata.azure.cn
[Azure (Německo)](https://azure.microsoft.com/overview/clouds/germany/)                | *. metadata.microsoftazure.de

> [!NOTE]
> Certifikáty nemusí mít přesnou shodu `metadata.azure.com` pro veřejný cloud. Z tohoto důvodu by ověřování certifikace mělo umožňovat běžný název z jakékoli `.metadata.azure.com` subdomény.

V případech, kdy se zprostředkující certifikát nedá stáhnout kvůli omezením sítě během ověřování, můžete Zprostředkovaný certifikát připnout. Počítejte s tím, že Azure se zavede přes certifikáty, což je standardní postupy infrastruktury veřejných klíčů. Je nutné aktualizovat připnuté certifikáty, když dojde k přechodu. Pokaždé, když se naplánuje změna aktualizace zprostředkujícího certifikátu, blog Azure se aktualizuje a zákazníci Azure se budou informovat. 

Zprostředkující certifikáty najdete v [úložišti PKI](https://www.microsoft.com/pki/mscorp/cps/default.htm). Zprostředkující certifikáty pro jednotlivé oblasti se můžou lišit.

> [!NOTE]
> Zprostředkující certifikát pro Azure Čína 21Vianet pochází z globální kořenové certifikační autority DigiCert namísto Baltimore.
Pokud jste připnuli zprostředkující certifikáty pro Azure Čína jako součást změny autority kořenového řetězu, musí se aktualizovat zprostředkující certifikáty.

## <a name="failover-clustering-in-windows-server"></a>Clustering s podporou převzetí služeb při selhání ve Windows serveru

Při dotazování na IMDS s clusteringem s podporou převzetí služeb při selhání je někdy potřeba přidat trasu do směrovací tabulky. Jak na to:

1. Otevřete příkazový řádek s oprávněními správce.

1. Spusťte následující příkaz a poznamenejte si adresu rozhraní pro cílovou síť ( `0.0.0.0` ) v tabulce směrování IPv4.

```bat
route print
```

> [!NOTE]
> Následující příklad výstupu je z virtuálního počítače s Windows serverem s povoleným clusterem s podporou převzetí služeb při selhání. Pro zjednodušení výstup obsahuje jenom tabulku směrování IPv4.

```text
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

Spusťte následující příkaz a použijte adresu rozhraní pro cílové umístění sítě ( `0.0.0.0` ), což je ( `10.0.1.10` ) v tomto příkladu.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="managed-identity"></a>Spravovaná identita

Na virtuálním počítači se dá povolit spravovaná identita přiřazená systémem. Virtuálnímu počítači můžete přiřadit taky jednu nebo více spravovaných identit přiřazených uživateli.
Pak můžete vyžádat tokeny pro spravované identity z IMDS. Tyto tokeny použijte k ověření u ostatních služeb Azure, například Azure Key Vault.

Podrobné pokyny k povolení této funkce najdete v tématu [získání přístupového tokenu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events"></a>Naplánované události
Stav naplánovaných událostí můžete získat pomocí IMDS. Pak může uživatel zadat sadu akcí, které se mají spustit na těchto událostech. Další informace najdete v tématu [naplánované události](scheduled-events.md). 

## <a name="regional-availability"></a>Regionální dostupnost

Služba je všeobecně dostupná ve všech cloudech Azure.

## <a name="sample-code-in-different-languages"></a>Ukázkový kód v různých jazycích

V následující tabulce jsou uvedeny ukázky volání IMDS pomocí různých jazyků uvnitř virtuálního počítače:

Jazyk      | Příklad
--------------|----------------
C++ (Windows) | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Přejít            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Visual Basic  | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb

## <a name="errors-and-debugging"></a>Chyby a ladění

Pokud se datový prvek nenalezne nebo dojde k chybnému požadavku, vrátí IMDS standardní chyby HTTP. Příklad:

Stavový kód HTTP | Důvod
-----------------|-------
200 OK |
400 – Chybný požadavek | Chybějící `Metadata: true` Hlavička nebo chybějící parametr `format=json` při dotazování na uzel typu list.
404 Nenalezeno  | Požadovaný element neexistuje.
Metoda 405 není povolená. | `GET`Jsou podporovány pouze požadavky.
410 pryč | Opakujte akci po nějaké době po dobu maximálně 70 sekund.
429 – Příliš mnoho požadavků | Rozhraní API aktuálně podporuje maximálně 5 dotazů za sekundu.
Chyba služby 500     | Zkuste to za chvíli znovu.

### <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Zobrazuje se chyba `400 Bad Request, Required metadata header not specified` . Co to znamená?**

IMDS vyžaduje, aby se hlavička `Metadata: true` předala v žádosti. Předání této hlavičky v volání REST umožňuje přístup k IMDS.

**Proč mi nezískávám výpočetní informace pro svůj virtuální počítač?**

V současné době IMDS podporuje pouze instance vytvořené pomocí Azure Resource Manager.

**V tuto chvíli jsem virtuální počítač vytvořil před Azure Resource Manager. Proč se mi nezobrazuje informace o metadatech COMPUTE?**

Pokud jste virtuální počítač vytvořili po září 2016, přidejte [značku](../../azure-resource-manager/management/tag-resources.md) , která začne zobrazovat výpočetní metadata. Pokud jste virtuální počítač vytvořili před září 2016, přidejte nebo odeberte rozšíření nebo datové disky do instance virtuálního počítače, aby se metadata aktualizovala.

**Proč se mi nezobrazují všechna data naplněná z nové verze?**

Pokud jste virtuální počítač vytvořili po září 2016, přidejte [značku](../../azure-resource-manager/management/tag-resources.md) , která začne zobrazovat výpočetní metadata. Pokud jste virtuální počítač vytvořili před září 2016, přidejte nebo odeberte rozšíření nebo datové disky do instance virtuálního počítače, aby se metadata aktualizovala.

**Proč se mi zobrazuje chyba `500 Internal Server Error` `410 Resource Gone` ?**

Opakujte požadavek. Další informace najdete v tématu [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults). Pokud potíže potrvají, vytvořte v Azure Portal pro virtuální počítač problém podpory.

**Budou to fungovat pro instance sady škálování virtuálních počítačů?**

Ano, IMDS je k dispozici pro instance sady škálování virtuálních počítačů.

**Aktualizoval (a) jsem moje značky ve službě Virtual Machine Scale Sets, ale nezobrazují se v instancích (na rozdíl od virtuálních počítačů s jednou instancí). Nedaří se mi něco?**

V současné době se pro sady škálování virtuálních počítačů zobrazují pouze virtuální počítače na restartování, obnovení bitové kopie nebo změna disku na instanci.

**Proč vypršel časový limit žádosti o mé volání služby?**

Volání metadat je nutné provést z primární IP adresy přiřazené k primární síťové kartě virtuálního počítače. Pokud jste změnili své trasy, musí existovat trasa pro adresu 169.254.169.254/32 v místní směrovací tabulce vašeho virtuálního počítače.
   * <details>
        <summary>Ověřuje se tabulka směrování.</summary>

        1. Vypsat místní směrovací tabulku a vyhledat položku IMDS Příklad:
            ```console
            > route print
            IPv4 Route Table
            ===========================================================================
            Active Routes:
            Network Destination        Netmask          Gateway       Interface  Metric
                      0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                    127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                    127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
              127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
                168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
              169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
            ... (continues) ...
            ```
        1. Ověřte, zda trasa existuje pro `169.254.169.254` , a poznamenejte si odpovídající síťové rozhraní (například `172.16.69.7` ).
        1. Vypíše konfiguraci rozhraní a nalezne rozhraní, které odpovídá odkazované tabulce směrování, a označuje tak adresu MAC (fyzickou).
            ```console
            > ipconfig /all
            ... (continues) ...
            Ethernet adapter Ethernet:

               Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
               Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
               Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
               DHCP Enabled. . . . . . . . . . . : Yes
               Autoconfiguration Enabled . . . . : Yes
               Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
               IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
               Subnet Mask . . . . . . . . . . . : 255.255.255.0
            ... (continues) ...
            ```
        1. Potvrďte, že rozhraní odpovídá primární síťové kartě virtuálního počítače a primární IP adrese. Primární síťovou kartu a IP adresu najdete tak, že si prohlížíte síťovou konfiguraci v Azure Portal, nebo ji vyhledáte pomocí Azure CLI. Poznamenejte si veřejné a privátní IP adresy (a adresu MAC, pokud používáte rozhraní příkazového řádku). Tady je příklad rozhraní příkazového řádku PowerShellu:
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: wintest767 True 00-0D-3A-E5-1C-C0
            ```
        1. Pokud se neshodují, aktualizujte tabulku směrování tak, aby byla cílem primární síťová karta a IP adresa.
    </details>

## <a name="support"></a>Podpora

Pokud nemůžete získat odpověď na metadata po několika pokusech, můžete vytvořit problém podpory v Azure Portal.
Jako **typ problému** vyberte možnost **Správa**. V **kategorii kategorie** vyberte **instance metadata Service**.

![Snímek obrazovky s podporou Instance Metadata Service](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Další kroky

[Získání přístupového tokenu pro virtuální počítač](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Naplánované události](scheduled-events.md)
