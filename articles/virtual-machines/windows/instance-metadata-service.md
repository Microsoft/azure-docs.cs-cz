---
title: Azure Instance Metadata Service
description: Rozhraní RESTful pro získání informací o výpočetních, síťových a nadcházejících událostech týkajících se virtuálních počítačů.
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
ms.openlocfilehash: 49840c2591bc1a991920b00aec020d4f652c9a50
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168388"
---
# <a name="azure-instance-metadata-service"></a>Služba metadat instance Azure

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

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance?api-version=2020-06-01 | ConvertTo-Json
```
> [!NOTE]
> `-NoProxy`Příznak je k dispozici pouze v PowerShellu 6 nebo novějším. Pokud nemáte instalaci proxy serveru, můžete příznak vynechat.

**Response** (Odpověď)

> [!NOTE]
> Odpověď je řetězec JSON. Náš dotaz REST pošleme prostřednictvím `ConvertTo-Json` rutiny pro poměrně tištěnou tisk.

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
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
        "resourceId": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
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
                    "id": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
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
                    "id": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
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
        "subscriptionId": "8d10da13-8125-4ba9-a717-bf7490507b3d",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
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

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
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

Všimněte si, že když je nová verze uvolněná, může chvíli trvat, než se nasadí do všech oblastí.

Při přidávání novějších verzí je k zajištění kompatibility stále k dispozici starší verze, pokud vaše skripty mají závislosti na konkrétních formátech dat.

Pokud není zadána žádná verze, je vrácena chyba se seznamem nejnovějších podporovaných verzí.

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad označuje chybový stav, pokud není zadána verze, odpověď je poměrně vytištěna pro čitelnost.

**Žádost**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance
```

**Response** (Odpověď)

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
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
location | Oblast Azure, ve které je spuštěný virtuální počítač | 2017-04-02
name | Název virtuálního počítače | 2017-04-02
offer | Informace o nabídce pro image virtuálního počítače a jsou k dispozici jenom pro Image nasazené z Galerie imagí Azure | 2017-04-02
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

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
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

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Response** (Odpověď)

```text
0
```

### <a name="sample-3-getting-more-information-about-the-vm-during-support-case"></a>Ukázka 3: získání dalších informací o virtuálním počítači během případu podpory

Jako poskytovatel služeb můžete obdržet volání podpory, kde byste chtěli získat další informace o virtuálním počítači. Dotazování zákazníků na sdílení výpočetních metadat může poskytnout základní informace o tom, že profesionální pracovník podpory ví o typu virtuálního počítače v Azure.

**Žádost**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01
```

**Response** (Odpověď)

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je poměrně vytištěn z důvodu čitelnosti.

```json
{
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
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
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
}
```

### <a name="sample-4-getting-azure-environment-where-the-vm-is-running"></a>Ukázka 4: získání prostředí Azure, ve kterém je virtuální počítač spuštěný

Azure má různé cloudy svrchovan jako [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Někdy potřebujete prostředí Azure, abyste mohli provádět určitá rozhodnutí za běhu. Následující příklad ukazuje, jak lze dosáhnout tohoto chování.

**Žádost**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
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

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/network?api-version=2017-08-01
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

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
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
encryptionSettings | Nastavení šifrování disku
image   | Virtuální pevný disk zdrojové image uživatele
managedDisk | Parametry spravovaného disku
name    | Název disku
osType  | Typ operačního systému zahrnutý na disku
virtuálního     | Virtuální pevný disk
writeAcceleratorEnabled | Bez ohledu na to, jestli je na disku povolená writeAccelerator

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
writeAcceleratorEnabled | Bez ohledu na to, jestli je na disku povolená writeAccelerator

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

Značky virtuálních počítačů jsou součástí rozhraní API instancí pod koncovým bodem instance/výpočty/značky.
Na VIRTUÁLNÍm počítači Azure možná byly aplikovány značky, aby je bylo možné logicky uspořádat do taxonomie. Značky přiřazené k virtuálnímu počítači se dají načíst pomocí níže uvedeného požadavku.

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

Součástí scénáře, který obsluhuje Instance Metadata Service, je poskytnout záruky, že poskytnutá data přicházejí z Azure. Tyto informace podepisujeme, aby image na webu Marketplace mohly mít jistotu, že se jedná o image běžící v Azure.

### <a name="sample-1-getting-attested-data"></a>Ukázka 1: získání ověřených dat

> [!NOTE]
> Všechny odpovědi rozhraní API jsou řetězce JSON. Následující příklad odpovědí je poměrně vytištěn z důvodu čitelnosti.

**Žádost**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

> [!NOTE]
> Z důvodu mechanismu ukládání do mezipaměti IMDS může být vrácena hodnota náhodně v mezipaměti.

Verze API-Version je povinné pole. Podporované verze rozhraní API najdete v [části věnované používání](#usage) .
Hodnota nonce je nepovinný řetězec s deseti číslicemi. Pokud není zadán, vrátí IMDS aktuální časové razítko UTC na svém místě.

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

Data | Popis
-----|------------
generované | Řetězec, který může být volitelně poskytnutý požadavkem. Pokud nebyla zadána žádná hodnota nonce, použije se aktuální časové razítko UTC.
rozhraní | [Azure Marketplace plán obrázku](/rest/api/compute/virtualmachines/createorupdate#plan). Obsahuje ID plánu (název), obrázek produktu nebo nabídku (produkt) a ID vydavatele (vydavatel).
časové razítko/createdOn | Časové razítko UTC pro vytvoření podepsaného dokumentu
časové razítko/expiresOn | Časové razítko UTC pro vypršení platnosti podepsaného dokumentu
vmId |  [Jedinečný identifikátor](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) pro virtuální počítač
subscriptionId | Předplatné Azure pro virtuální počítač představené v `2019-04-30`
skladové | Konkrétní SKU pro bitovou kopii virtuálního počítače, představená v `2019-11-01`

> [!NOTE]
> U klasických virtuálních počítačů (ne ARM) je zaručeno, že se naplní pouze vmId.

### <a name="sample-2-validating-that-the-vm-is-running-in-azure"></a>Ukázka 2: ověření, že virtuální počítač běží v Azure

Dodavatelé na webu Marketplace chtějí zajistit, aby byl software licencován pro spouštění pouze v Azure. Pokud někdo zkopíruje virtuální pevný disk z místního počítače do místní aplikace, musí mít možnost ho detekovat. Voláním do Instance Metadata Service můžou dodavatelé na webu Marketplace získat podepsaná data, která garantuje odpověď jenom z Azure.

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2019-04-30
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
> Z důvodu mechanismu ukládání do mezipaměti IMDS může být vrácena hodnota náhodně v mezipaměti.

Hodnota nonce v podepsaném dokumentu se dá porovnávat, pokud jste v počátečním požadavku zadali parametr nonce.

> [!NOTE]
> Certifikát pro veřejný cloud a každý z svrchovaného cloudu se budou lišit.

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

## <a name="failover-clustering-in-windows-server"></a>Clustering s podporou převzetí služeb při selhání ve Windows serveru

V některých scénářích při dotazování Instance Metadata Service s clusteringem s podporou převzetí služeb při selhání je nutné přidat trasu do směrovací tabulky.

1. Otevřete příkazový řádek s oprávněními správce.

1. Spusťte následující příkaz a poznamenejte si adresu rozhraní pro cílovou síť ( `0.0.0.0` ) v tabulce směrování IPv4.

```bat
route print
```

> [!NOTE]
> Následující příklad výstupu z virtuálního počítače s Windows serverem a s povoleným clusterem s podporou převzetí služeb při selhání obsahuje jenom tabulku směrování IPv4 pro jednoduchost.

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

Spusťte následující příkaz a použijte adresu rozhraní pro cíl sítě ( `0.0.0.0` ), který je ( `10.0.1.10` ) v tomto příkladu.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

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

## <a name="error-and-debugging"></a>Chyba a ladění

Pokud se nenašel datový prvek nebo dojde k chybnému požadavku, Instance Metadata Service vrátí standardní chyby protokolu HTTP. Například:

Stavový kód HTTP | Důvod
-----------------|-------
200 OK |
400 – Chybný požadavek | `Metadata: true` `format=json` Při dotazování na uzel typu list chybí záhlaví nebo chybějící parametr.
404 Nenalezeno  | Požadovaný element neexistuje.
Metoda 405 není povolená. | `GET`Jsou podporovány pouze požadavky.
410 pryč | Opakovat po uplynutí určité doby na maximum 70 sekund
429 – Příliš mnoho požadavků | Rozhraní API aktuálně podporuje maximálně 5 dotazů za sekundu.
Chyba služby 500     | Zkusit znovu za chvíli

### <a name="known-issues-and-faq"></a>Známé problémy a nejčastější dotazy

1. Zobrazuje se chyba `400 Bad Request, Required metadata header not specified` . Co to znamená?
   * Instance Metadata Service vyžaduje, aby se hlavička `Metadata: true` předala v žádosti. Předání této hlavičky v volání REST umožňuje přístup k Instance Metadata Service.
1. Proč mi nezískávám výpočetní informace pro svůj virtuální počítač?
   * V současné době Instance Metadata Service podporuje jenom instance vytvořené pomocí Azure Resource Manager. V budoucnu se může přidat podpora virtuálních počítačů cloudových služeb.
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

        1. Vypsat místní směrovací tabulku a vyhledat položku IMDS (např.):
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
        1. Ověřte, zda trasa existuje pro `169.254.169.254` , a poznamenejte si odpovídající síťové rozhraní (např. `172.16.69.7` ).
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
        1. Potvrďte, že rozhraní odpovídá primární síťové kartě virtuálního počítače a primární IP adrese. Primární síťovou kartu nebo IP adresu najdete na stránce konfigurace sítě na webu Azure Portal nebo jejich vyhledáním [pomocí Azure CLI](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-show). Poznamenejte si veřejné a privátní IP adresy (a adresu MAC, pokud používáte rozhraní příkazového řádku). Příklad rozhraní příkazového řádku PowerShellu:
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
        1. Pokud se neshodují, aktualizujte směrovací tabulku tak, aby byla primární síťová karta/IP cílem.
    </details>

## <a name="support-and-feedback"></a>Podpora a váš názor

Pošlete nám svůj názor a komentáře https://feedback.azure.com .

Pokud chcete získat podporu pro službu, vytvořte problém podpory v Azure Portal pro virtuální počítač, ke kterému nemůžete po dlouhou dobu pokusů získat odpověď na metadata.
Použijte typ problému `Management` a `Instance Metadata Service` jako kategorii vyberte.

![Podpora metadat instance](./media/instance-metadata-service/InstanceMetadata-support.png "Snímek obrazovky: otevření případu podpory při potížích s Instance Metadata Service")

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace:
1.  [Získání přístupového tokenu pro virtuální počítač](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).
2.  [Scheduled Events](scheduled-events.md)
