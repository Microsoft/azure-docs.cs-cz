---
title: Služba metadat instance Azure
description: Restful rozhraní získat informace o výpočetních, síťových a nadcházející události údržby Linuxu.
services: virtual-machines-linux
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines-linux
ms.subservice: monitoring
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: 3281b4dafa5436c9df760ac8aa3fc82f535b4286
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944873"
---
# <a name="azure-instance-metadata-service"></a>Služba metadat instance Azure

Služba Metadat instance Azure (IMDS) poskytuje informace o aktuálně spuštěných instancích virtuálních počítačů a dá se použít ke správě a konfiguraci virtuálních počítačů.
Poskytnuté informace zahrnují skladovou položku, konfiguraci sítě a nadcházející události údržby. Úplný seznam dat, která je k dispozici, naleznete v tématu [metadata rozhraní API](#metadata-apis).

Služba metadat instance Azure je koncový bod REST přístupný všem virtuálním mům IaaS vytvořeným prostřednictvím [Správce prostředků Azure](https://docs.microsoft.com/rest/api/resources/).
Koncový bod je k dispozici na známé nesměrovatelné`169.254.169.254`IP adrese ( ), ke které lze přistupovat pouze z virtuálního počítačů.

> [!IMPORTANT]
> Tato služba je **obecně dostupná** ve všech oblastech Azure.  Pravidelně přijímá aktualizace, aby zpřístupnit nové informace o instancích virtuálních strojů. Tato stránka odráží aktuální rozhraní [API metadat, která jsou](#metadata-apis) k dispozici.

## <a name="service-availability"></a>Dostupnost služeb

Služba je dostupná v obecně dostupných oblastech Azure. Ne všechny verze rozhraní API může být k dispozici ve všech oblastech Azure.

Oblasti                                        | Dostupnost?                                 | Podporované verze
-----------------------------------------------|-----------------------------------------------|-----------------
[Všechny obecně dostupné globální oblasti Azure](https://azure.microsoft.com/regions/)     | Obecně dostupné | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Obecně dostupné | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure China 21Vianet](https://www.azure.cn/)                                            | Obecně dostupné | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure (Německo)](https://azure.microsoft.com/overview/clouds/germany/)                    | Obecně dostupné | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15

Verze 2019-11-01 je aktuálně nasazena a nemusí být k dispozici ve všech oblastech.

Tato tabulka je aktualizována, pokud jsou k dispozici aktualizace služeb nebo jsou k dispozici nové podporované verze.

Chcete-li vyzkoušet službu metadat instance, vytvořte virtuální počítač ze [Správce prostředků Azure](https://docs.microsoft.com/rest/api/resources/) nebo na [portálu Azure](https://portal.azure.com) ve výše uvedených oblastech a postupujte podle níže uvedených příkladů.
Další příklady dotazování IMDS lze nalézt na [ukázkách metadat instance Azure](https://github.com/microsoft/azureimds)

## <a name="usage"></a>Využití

### <a name="versioning"></a>Správa verzí

Služba metadat instance je verzí a zadání verze rozhraní API v požadavku HTTP je povinné.

Nejnovější verze jsou uvedeny v této [tabulce dostupnosti](#service-availability).

Při přidávání novějších verzí lze stále přistupovat k starším verzím z důvodu kompatibility, pokud vaše skripty mají závislosti na konkrétních formátech dat.

Pokud není zadána žádná verze, je vrácena chyba se seznamem nejnovějších podporovaných verzí.

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je docela vytištěno pro čitelnost.

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Reakce**

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

### <a name="using-headers"></a>Použití záhlaví

Při dotazování služby metadat instance je `Metadata: true` nutné zadat záhlaví, abyste zajistili, že požadavek nebyl neúmyslně přesměrován.

### <a name="retrieving-metadata"></a>Načítání metadat

Metadata instance jsou dostupná pro spouštění virtuálních počítačů vytvořených a spravovaných pomocí [Správce prostředků Azure](https://docs.microsoft.com/rest/api/resources/). Přístup ke všem kategoriím dat pro instanci virtuálního počítače pomocí následujícího požadavku:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Všechny dotazy metadat instance jsou malá a velká písmena.

### <a name="data-output"></a>Výstup dat

Ve výchozím nastavení služba metadat instance vrací data`Content-Type: application/json`ve formátu JSON ( ). Různá úložiště API však vrátit data v různých formátech, pokud je požadováno.
Následující tabulka je odkazem na jiné formáty dat, které mohou podporovat úložiště API.

rozhraní API | Výchozí formát dat | Jiné formáty
--------|---------------------|--------------
/instance | json | text
/naplánované události | json | Žádná
/ověřeno | json | Žádná

Chcete-li získat přístup k nevýchozímu formátu odpovědi, zadejte požadovaný formát jako parametr řetězce dotazu v požadavku. Například:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Pro listové `format=json` uzly nefunguje. Pro tyto `format=text` dotazy je třeba explicitně zadat, pokud je výchozí formát json.

### <a name="security"></a>Zabezpečení

Koncový bod služby Metadata instance je přístupný pouze z instance spuštěného virtuálního počítače na nesměrovatelné IP adrese. Kromě toho je služba `X-Forwarded-For` odmítnuta jako požadavek s hlavičkou.
Požadavky musí také `Metadata: true` obsahovat záhlaví zajistit, že skutečný požadavek byl přímo určen a není součástí neúmyslné přesměrování.

### <a name="error"></a>Chyba

Pokud není nalezen datový prvek nebo poškozený požadavek, služba metadat instance vrátí standardní chyby HTTP. Například:

Stavový kód HTTP | Důvod
----------------|-------
200 OK |
400 Chybný požadavek | Při `Metadata: true` dotazování na listový uzel chybí záhlaví nebo formát
404 Nenalezeno | Požadovaný prvek neexistuje.
Metoda 405 není povolena. | Podporovány jsou pouze `GET` požadavky.
410 Pryč | Opakujte po určité době po dobu maximálně 70 sekund
429 – Příliš mnoho požadavků | Rozhraní API v současné době podporuje maximálně 5 dotazů za sekundu
Chyba služby 500     | Po určité době opakujte akci

### <a name="examples"></a>Příklady

> [!NOTE]
> Všechny odpovědi rozhraní API jsou řetězce JSON. Všechny následující příklad odpovědi jsou docela-tištěné pro čitelnost.

#### <a name="retrieving-network-information"></a>Načítání informací o síti

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Reakce**

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je docela vytištěno pro čitelnost.

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

#### <a name="retrieving-public-ip-address"></a>Načítání veřejné IP adresy

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Načítání všech metadat pro instanci

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**Reakce**

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je docela vytištěno pro čitelnost.

```json
{
  "compute": {
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
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>Metadata API

Následující rozhraní API jsou k dispozici prostřednictvím koncového bodu metadat:

Data | Popis | Zavedená verze
-----|-------------|-----------------------
ověřeno | Viz [Ověřená data](#attested-data) | 2018-10-01
identity | Spravované identity pro prostředky Azure. Viz [získání přístupového tokenu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
Instance | Viz [Rozhraní API instance](#instance-api) | 2017-04-02
plánované události | Viz [Plánované události](scheduled-events.md) | 2017-08-01

### <a name="instance-api"></a>Instance API

Prostřednictvím rozhraní API instance jsou k dispozici následující kategorie výpočetních prostředků:

> [!NOTE]
> Prostřednictvím koncového bodu metadat jsou následující kategorie přístupné prostřednictvím instance/výpočetní

Data | Popis | Zavedená verze
-----|-------------|-----------------------
azŽivotní prostředí | Prostředí Azure, ve kterém běží virtuální počítač | 2018-10-01
Customdata | Tato funkce je v současné době zakázána a tuto dokumentaci aktualizujeme, jakmile bude k dispozici. | 2019-02-01
location | Oblast Azure, ve které virtuální počítač běží | 2017-04-02
jméno | Název virtuálního_ | 2017-04-02
offer | Informace o nabídce image virtuálního počítače a je k dispozici jenom pro image nasazené z galerie obrázků Azure | 2017-04-02
osTyp | Linux nebo Windows | 2017-04-02
umístěníGroupId | [Skupina umístění](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) škálovací sady virtuálních strojů | 2017-08-01
Plán | [Plán](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) obsahující název, produkt a vydavatel pro virtuální počítač, pokud se jedná o image Azure Marketplace | 2018-04-02
platformaUpdateDomain |  [Aktualizace domény,](manage-availability.md) ve které virtuální hotel běží | 2017-04-02
platformAFaultDomain | [Doména selhání,](manage-availability.md) ve které je virtuální mísa spuštěná | 2017-04-02
Zprostředkovatel | Poskytovatel virtuálního virtuálního vztahu | 2018-10-01
publicKeys | [Kolekce veřejných klíčů](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) přiřazených k virtuálnímu virtuálnímu jemu a cestám | 2018-04-02
vydavatel | Vydavatel image virtuálního aplikace | 2017-04-02
resourceGroupName | [Skupina prostředků](../../azure-resource-manager/management/overview.md) pro váš virtuální počítač | 2017-08-01
resourceId | [Plně kvalifikované](https://docs.microsoft.com/rest/api/resources/resources/getbyid) ID zdroje | 2019-03-11
Sku | Konkrétní skladová položka pro image virtuálního mísa | 2017-04-02
storageProfile | Viz [Profil úložiště](#storage-profile) | 2019-06-01
subscriptionId | Předplatné Azure pro virtuální počítač | 2017-08-01
tags | [Značky](../../azure-resource-manager/management/tag-resources.md) pro váš virtuální počítač  | 2017-08-01
tagySeznam | Značky formátované jako pole JSON pro snadnější programovou analýzu  | 2019-06-04
version | Verze image virtuálního aplikace | 2017-04-02
vmId | [Jedinečný identifikátor](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) virtuálního virtuálního soudu | 2017-04-02
vmScaleSetName | [Škálovací sada virtuálních strojů Název](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) škálovací sady virtuálních strojů | 2017-12-01
vmSize | [Velikost virtuálního počítače](sizes.md) | 2017-04-02
zóna | [Zóna dostupnosti](../../availability-zones/az-overview.md) virtuálního počítače | 2017-12-01

Prostřednictvím rozhraní API instance jsou k dispozici následující kategorie sítě:

> [!NOTE]
> Prostřednictvím koncového bodu metadat jsou prostřednictvím instance/sítě/rozhraní přístupné následující kategorie

Data | Popis | Zavedená verze
-----|-------------|-----------------------
ipv4/privateIpAdresa | Místní adresa IPv4 virtuálního soudu | 2017-04-02
ipv4/publicIpAdresa | Veřejná adresa IPv4 virtuálního virtuálního mísy | 2017-04-02
podsíť/adresa | Adresa podsítě virtuálního soudu | 2017-04-02
podsíť/předpona | Předpona podsítě, příklad 24 | 2017-04-02
adresa ipv6/ipAdresa | Místní adresa IPv6 virtuálního soudu | 2017-04-02
macAddress | Adresa virtuálního počítače mac | 2017-04-02

## <a name="attested-data"></a>Ověřená data

Součástí scénáře obsluhovanéslužbou metadat instance je poskytnutí záruk, že poskytnutá data pocházejí z Azure. Část těchto informací podepisujeme, aby si image marketplace mohly být jistí, že se v Azure nachází jejich image.

### <a name="example-attested-data"></a>Příklad Ověřená data

> [!NOTE]
> Všechny odpovědi rozhraní API jsou řetězce JSON. Následující příklad odpovědi jsou docela vytištěny pro čitelnost.

 **Požadavek**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Api-verze je povinné pole. V [části dostupnosti služby](#service-availability) naleznete podporované verze rozhraní API.
Nonce je volitelný desetimístný řetězec. Pokud není k dispozici, IMDS vrátí aktuální časové razítko UTC na jeho místě. Z důvodu mechanismu ukládání do mezipaměti iMDS dříve uložené hodnoty nonce v mezipaměti může být vrácena.

 **Reakce**

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je docela vytištěno pro čitelnost.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Objekt blob podpisu je podepsaná verze dokumentu [pkcs7.](https://aka.ms/pkcs7) Obsahuje certifikát používaný pro podepisování spolu s podrobnostmi o virtuálním ms, jako je vmId, sku, nonce, subscriptionId, timeStamp pro vytvoření a vypršení platnosti dokumentu a informace o plánu o bitové kopii. Informace o plánu se vyplňují jenom pro image místa Azure Market. Certifikát lze extrahovat z odpovědi a použít k ověření, že odpověď je platná a pochází z Azure.

## <a name="example-scenarios-for-usage"></a>Ukázkové scénáře pro využití  

### <a name="tracking-vm-running-on-azure"></a>Sledování virtuálního počítače spuštěného v Azure

Jako poskytovatel služeb můžete vyžadovat sledování počtu virtuálních počítačů se spuštěným softwarem nebo máte agenty, kteří potřebují sledovat jedinečnost virtuálního počítače. Chcete-li získat jedinečné ID pro virtuální hod, použijte `vmId` pole ze služby metadat instance.

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Reakce**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Umístění kontejnerů do domény selhání nebo aktualizační domény založené na datových oddílech

Pro určité scénáře je umístění různých replik dat prvořadé. Například [umístění replikhdfs](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) nebo umístění kontejneru prostřednictvím [orchestrátoru](https://kubernetes.io/docs/user-guide/node-selection/) můžete potřebovat znát `platformFaultDomain` a `platformUpdateDomain` virtuální počítač běží na.
Můžete také použít [zóny dostupnosti](../../availability-zones/az-overview.md) pro instance, aby se tato rozhodnutí.
Tato data můžete zadat přímo prostřednictvím služby metadat instance.

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Reakce**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Získání dalších informací o virtuálním počítači pro případ podpory

Jako poskytovatel služeb můžete získat volání podpory, kde byste se chtěli dozvědět více informací o virtuálním virtuálním vztahu. Požádat zákazníka o sdílení výpočetních metadat můžete poskytnout základní informace pro pracovníky podpory vědět o druhu virtuálního počítače v Azure.

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Reakce**

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je docela vytištěno pro čitelnost.

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

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Získání prostředí Azure, ve kterém je virtuální počítač spuštěný

Azure má různé suverénní cloudy, jako je [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Někdy potřebujete prostředí Azure, abyste se rozhodli za běhu. Následující ukázka ukazuje, jak lze dosáhnout tohoto chování.

**Požadavek**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Reakce**

```bash
AzurePublicCloud
```

Cloud a hodnoty prostředí Azure jsou uvedeny níže.

 Cloud   | Prostředí Azure
---------|-----------------
[Všechny obecně dostupné globální oblasti Azure](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure (Německo)](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>Získání značek pro virtuální hod

Značky mohou být použity pro váš virtuální počítač Azure logicky uspořádat do taxonomie. Značky přiřazené k virtuálnímu virtuálnímu jemu se načítají pomocí níže uvedeného požadavku.

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Reakce**

```text
Department:IT;Environment:Test;Role:WebRole
```

Pole `tags` je řetězec se značkami oddělenými středníky. To může být problém, pokud středníky jsou použity v samotných značkách. Pokud analyzátor je zapsán programově extrahovat značky, `tagsList` měli byste se spolehnout na pole, které je pole JSON bez oddělovačů a následně snadněji analyzovat.

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=json"
```

**Reakce**

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

### <a name="validating-that-the-vm-is-running-in-azure"></a>Ověření spuštění virtuálního počítače v Azure

Dodavatelé marketplace chtějí zajistit, aby jejich software byl licencován ke spuštění jenom v Azure. Pokud někdo zkopíruje virtuální pevný disk do místního prostředí, měl by mít možnost to zjistit. Voláním do služby metadat instance mohou dodavatelé marketplace získat podepsaná data, která zaručí odezvu jenom z Azure.

> [!NOTE]
> Vyžaduje instalaci jq.

**Požadavek**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Reakce**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
"sku": "RS3-Pro"
}
```

Data | Popis
-----|------------
Nonce | Uživatel zadaný volitelný řetězec s požadavkem. Pokud v požadavku nebylo zadáno žádné nonce, je vráceno aktuální časové razítko UTC.
Plán | [Plánování](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) virtuálního počítače v něm je image Azure Marketplace, obsahuje název, produkt a vydavatele
časové razítko/createdOn | Časové razítko UTC, při kterém byl vytvořen první podepsaný dokument
timestamp/expiresOn | Časové razítko UTC, při kterém vyprší platnost podepsaného dokumentu
vmId |  [Jedinečný identifikátor](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) virtuálního virtuálního soudu
subscriptionId | Předplatné Azure pro virtuální počítač, které bylo zavedeno`2019-04-30`
Sku | Konkrétní skladová položka pro image virtuálního virtuálního montova, zavedená`2019-11-01`

#### <a name="verifying-the-signature"></a>Ověření podpisu

Jakmile získáte výše uvedený podpis, můžete ověřit, zda je podpis od společnosti Microsoft. Můžete také ověřit zprostředkující certifikát a řetěz certifikátů. Nakonec můžete ověřit, zda je ID předplatného správné.

> [!NOTE]
> Certifikát pro veřejný cloud a suverénní cloud se bude lišit.

 Cloud | Certifikát
---------|-----------------
[Všechny obecně dostupné globální oblasti Azure](https://azure.microsoft.com/regions/)     | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | *.metadata.azure.cn
[Azure (Německo)](https://azure.microsoft.com/overview/clouds/germany/)                    | *.metadata.microsoftazure.de

Existuje známý problém kolem certifikátu používaného k podepisování. Certifikáty nemusí mít přesnou `metadata.azure.com` shodu pro veřejný cloud. Proto by ověření certifikace mělo `.metadata.azure.com` umožnit běžný název z libovolné subdomény.

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

V případech, kdy zprostředkující certifikát nelze stáhnout z důvodu síťových omezení během ověřování, lze zprostředkující certifikát připnout. Azure však převede certifikáty podle standardního výkonu pki praxe. Připnuté certifikáty by musely být aktualizovány při přechodu dojde. Kdykoli se plánuje změna aktualizace zprostředkujícího certifikátu, blog Azure se aktualizuje a zákazníci Azure budou upozorněni. Zprostředkující certifikáty naleznete [zde](https://www.microsoft.com/pki/mscorp/cps/default.htm). Zprostředkující certifikáty pro každou oblast se mohou lišit.

### <a name="storage-profile"></a>Profil úložiště

Služba metadat instance může poskytnout podrobnosti o disky úložiště přidružené k virtuálnímu počítače. Tato data lze nalézt na instanci/compute/storageProfile koncového bodu.

Profil úložiště virtuálního počítače je rozdělen do tří kategorií – odkaz na image, disk operačního systému a datové disky.

Referenční objekt obrázku obsahuje následující informace o obrázku operačního systému:

Data    | Popis
--------|-----------------
id      | ID prostředku
offer   | Nabídka platformy nebo image tržiště
vydavatel | Vydavatel obrázků
Sku     | Obrázek sku
version | Verze platformy nebo image tržiště

Objekt disku operačního systému obsahuje následující informace o disku operačního systému používaném virtuálním počítačem:

Data    | Popis
--------|-----------------
Mezipaměti | Požadavky na ukládání do mezipaměti
createOption | Informace o tom, jak byl virtuální virtuální mísa vytvořen
diffDiskSettings | Nastavení dočasného disku
diskVelikostGB | Velikost disku v GB
image   | Zdrojový uživatelský obraz virtuální pevný disk
Lun     | Číslo logické jednotky disku
managedDisk | Parametry spravovaného disku
jméno    | Název disku
Vhd     | Virtuální pevný disk
writeAcceleratorEnabled | Zda je na disku povolen program writeAccelerator

Pole datových disků obsahuje seznam datových disků připojených k virtuálnímu počítače. Každý objekt datového disku obsahuje následující informace:

Data    | Popis
--------|-----------------
Mezipaměti | Požadavky na ukládání do mezipaměti
createOption | Informace o tom, jak byl virtuální virtuální mísa vytvořen
diffDiskSettings | Nastavení dočasného disku
diskVelikostGB | Velikost disku v GB
nastavení šifrování | Nastavení šifrování disku
image   | Zdrojový uživatelský obraz virtuální pevný disk
managedDisk | Parametry spravovaného disku
jméno    | Název disku
osTyp  | Typ operačního systému zahrnutého na disku
Vhd     | Virtuální pevný disk
writeAcceleratorEnabled | Zda je na disku povolen program writeAccelerator

Následuje příklad, jak se dotazovat na informace o úložišti virtuálního zařízení.

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Reakce**

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je docela vytištěno pro čitelnost.

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

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Příklady volání služby metadat pomocí různých jazyků uvnitř virtuálního mísa

Jazyk | Příklad
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Přejít  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>Nejčastější dotazy

1. Dostávám chybu `400 Bad Request, Required metadata header not specified`. Co to znamená?
   * Služba metadat instance vyžaduje, `Metadata: true` aby byla hlavička předána v požadavku. Předání této hlavičky ve volání REST umožňuje přístup ke službě metadat instance.
2. Proč nezískávám informace o výpočtu pro svůj virtuální počítač?
   * Služba metadat instance v současné době podporuje jenom instance vytvořené pomocí Správce prostředků Azure. V budoucnu může být přidána podpora virtuálních virtuálních měn cloudových služeb.
3. Virtuální počítač jsem vytvořil přes Azure Resource Manager před nějakou dobu zpět. Proč se mi nezobrazují informace o výpočetních metadatech?
   * Pro všechny virtuální počítače vytvořené po září 2016 přidejte [značku,](../../azure-resource-manager/management/tag-resources.md) která začne vidět výpočetní metadata. Pro starší virtuální počítače (vytvořené před zářím 2016) přidejte nebo odeberte rozšíření nebo datové disky do virtuálního počítače, abyste aktualizovali metadata.
4. Nevidím všechna data naplněná pro novou verzi
   * Pro všechny virtuální počítače vytvořené po září 2016 přidejte [značku,](../../azure-resource-manager/management/tag-resources.md) která začne vidět výpočetní metadata. Pro starší virtuální počítače (vytvořené před zářím 2016) přidejte nebo odeberte rozšíření nebo datové disky do virtuálního počítače, abyste aktualizovali metadata.
5. Proč se zobrazuje chyba? `500 Internal Server Error`
   * Opakujte svůj požadavek na základě exponenciálního vypnutí systému. Pokud problém přetrvává, obraťte se na podporu Azure.
6. Kde mohu sdílet další otázky/ komentáře?
   * Pošlete své https://feedback.azure.compřipomínky k aplikaci .
7. Fungovalo by to pro instanci škálovací sady virtuálních strojů?
   * Služba Metadata yes je k dispozici pro instance škálovací sady.
8. Jak získám podporu pro službu?
   * Chcete-li získat podporu pro službu, vytvořte problém podpory na portálu Azure pro virtuální počítač, kde po dlouhých opakovaných pokusech nemůžete získat odpověď metadat.
9. Dostal jsem žádost načasovaný na můj hovor na službu?
   * Metadata volání musí být z primární IP adresy přiřazené k primární síťové karty virtuálního počítače, navíc v případě, že jste změnili trasy musí být trasa pro 169.254.0.0/16 adresu ze síťové karty.
10. Aktualizoval i značky ve škálovací sadě virtuálních strojů, ale nezobrazují se v instancích na rozdíl od virtuálních počítačů?
    * V současné době pro ScaleSets značky zobrazit pouze na virtuální počítač při restartování/reimage/nebo změny disku instance.

    ![Podpora metadat instance](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Další kroky

- Další informace o [plánovaných událostech](scheduled-events.md)
