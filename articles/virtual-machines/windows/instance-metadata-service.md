---
title: Instance Metadata Service Azure
description: Rozhraní RESTful pro získání informací o výpočetní, síťové a nadcházející události údržby virtuálního počítače s Windows.
services: virtual-machines-windows
documentationcenter: ''
author: KumariSupriya
manager: harijayms
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: 901e075572e0ed73dc7d0633941311c04b4f3c1c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358356"
---
# <a name="azure-instance-metadata-service"></a>Služba metadat instance Azure

Azure Instance Metadata Service poskytuje informace o spuštěných instancích virtuálních počítačů, které se dají použít ke správě a konfiguraci virtuálních počítačů.
To zahrnuje informace, jako jsou SKU, konfigurace sítě a nadcházející události údržby. Další informace o tom, jaké typy informací jsou k dispozici, najdete v tématu [rozhraní API pro metadata](#metadata-apis).

Instance Metadata Service Azure je koncový bod REST dostupný všem virtuálním počítačům s IaaS vytvořeným prostřednictvím [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).
Koncový bod je k dispozici na dobře známé IP adrese, která není směrovatelný (`169.254.169.254`), ke které se dá získat přístup jenom z virtuálního počítače.

> [!IMPORTANT]
> Tato služba je **všeobecně dostupná** ve všech oblastech Azure.  Pravidelně přijímá aktualizace k vystavování nových informací o instancích virtuálních počítačů. Tato stránka odráží aktuální dostupné [rozhraní API metadat](#metadata-apis) .

## <a name="service-availability"></a>Dostupnost služeb

Služba je dostupná v všeobecně dostupných oblastech Azure. Ne všechny verze rozhraní API můžou být dostupné ve všech oblastech Azure.

Oblasti                                        | Dostupnosti?                                 | Podporované verze
-----------------------------------------------|-----------------------------------------------|-----------------
[Všechny všeobecně dostupné globální oblasti Azure](https://azure.microsoft.com/regions/)     | Obecná dostupnost | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Obecná dostupnost | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30
[Azure (Čína)](https://www.azure.cn/)                                                     | Obecná dostupnost | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30
[Azure Německo](https://azure.microsoft.com/overview/clouds/germany/)                    | Obecná dostupnost | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30

Tato tabulka je aktualizována, pokud jsou k dispozici aktualizace služby a nové podporované verze.

Pokud chcete vyzkoušet Instance Metadata Service, vytvořte virtuální počítač z [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) nebo [Azure Portal](https://portal.azure.com) ve výše uvedených oblastech a použijte následující příklady.

## <a name="usage"></a>Využití

### <a name="versioning"></a>Správa verzí

U Instance Metadata Service se používá verze a určení verze rozhraní API v požadavku HTTP je povinné.

Můžete zobrazit nejnovější verze uvedené v této [tabulce dostupnosti](#service-availability).

Při přidávání novějších verzí je k zajištění kompatibility stále k dispozici starší verze, pokud vaše skripty mají závislosti na konkrétních formátech dat.

Pokud není zadána žádná verze, je vrácena chyba se seznamem nejnovějších podporovaných verzí.

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je poměrně vytištěn z důvodu čitelnosti.

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Odpověď**

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

### <a name="using-headers"></a>Používání hlaviček

Při dotazování na Instance Metadata Service musíte zadat hlavičku `Metadata: true`, aby se zajistilo, že se žádost neúmyslně přesměrovala.

### <a name="retrieving-metadata"></a>Načítání metadat

Metadata instance jsou k dispozici pro spuštění virtuálních počítačů vytvořených nebo spravovaných pomocí [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Přístup ke všem kategoriím dat pro instanci virtuálního počítače pomocí tohoto požadavku:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> U všech dotazů na metadata instance se rozlišují velká a malá písmena.

### <a name="data-output"></a>Výstup dat

Ve výchozím nastavení Instance Metadata Service vrátí data ve formátu JSON (`Content-Type: application/json`). V případě potřeby ale jiná rozhraní API vrací data v různých formátech.
Následující tabulka je odkazem na jiné rozhraní API datových formátů, které může podporovat.

API | Výchozí formát dat | Jiné formáty
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | Žádná
/attested | json | Žádná

Pokud chcete získat přístup k nevýchozímu formátu odpovědi, v žádosti určete požadovaný formát jako parametr řetězce dotazu. Příklad:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Pro uzly typu list nefunguje `format=json`. Pro tyto dotazy `format=text` nutné explicitně zadat, pokud je výchozí formát JSON.

### <a name="security"></a>Zabezpečení

Koncový bod Instance Metadata Service je přístupný jenom v rámci spuštěné instance virtuálního počítače na IP adrese, která není směrovatelný. Kromě toho bude služba odmítla všechny žádosti s hlavičkou `X-Forwarded-For`.
Požadavky musí obsahovat také hlavičku `Metadata: true`, aby bylo zajištěno, že skutečný požadavek byl přímo zamýšlen a není součástí neúmyslného přesměrování.

### <a name="error"></a>Chyba

Pokud se nenašel datový prvek nebo dojde k chybnému požadavku, Instance Metadata Service vrátí standardní chyby protokolu HTTP. Příklad:

Stavový kód HTTP | Důvod
----------------|-------
200 OK |
400 Chybný požadavek | Chybějící záhlaví `Metadata: true` nebo chybějící formát při dotazování na uzel typu list
404 – Nenalezeno | Požadovaný element neexistuje.
Metoda 405 není povolená. | Jsou podporovány pouze požadavky `GET` a `POST`
429 příliš mnoho požadavků | Rozhraní API aktuálně podporuje maximálně 5 dotazů za sekundu.
Chyba služby 500     | Zkusit znovu za chvíli

### <a name="examples"></a>Příklady

> [!NOTE]
> Všechny odpovědi rozhraní API jsou řetězce JSON. Všechny následující příklady odpovědí jsou poměrně vytištěny pro čitelnost.

#### <a name="retrieving-network-information"></a>Načítání informací o síti

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Odpověď**

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

#### <a name="retrieving-public-ip-address"></a>Načítání veřejné IP adresy

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Načítání všech metadat pro instanci

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-03-11"
```

**Odpověď**

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je poměrně vytištěn z důvodu čitelnosti.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "westus",
    "name": "jubilee",
    "offer": "Windows-10",
    "osType": "Windows",
    "placementGroupId": "",
    "plan": {
        "name": "",
        "product": "",
        "publisher": ""
    },
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "MicrosoftWindowsDesktop",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "rs4-pro",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "17134.345.59",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_D1",
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

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Načítají se metadata na virtuálním počítači s Windows.

**Požadavek**

Metadata instance lze v systému Windows načíst prostřednictvím `curl` programu:

```powershell
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-03-11 | select -ExpandProperty Content
```

Nebo pomocí rutiny `Invoke-RestMethod` PowerShellu:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2019-03-11 -Method get
```

**Odpověď**

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je poměrně vytištěn z důvodu čitelnosti.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "placementGroupId": "",
    "plan": {
        "name": "",
        "product": "",
        "publisher": ""
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "MicrosoftSQLServer",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "Enterprise",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmScaleSetName": "",
    "vmSize": "Standard_DS2",
    "zone": ""
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>Rozhraní API pro metadata

#### <a name="the-following-apis-are-available-through-the-metadata-endpoint"></a>Prostřednictvím koncového bodu metadat jsou k dispozici následující rozhraní API:

Data | Popis | Představená verze
-----|-------------|-----------------------
ověřuje přítomnost | Viz [Attestation data](#attested-data) | 2018-10-01
identita | Spravované identity pro prostředky Azure. Viz [získání přístupového tokenu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) . | 2018-02-01
instance | Viz [rozhraní API instance](#instance-api) | 2017-04-02
scheduledevents | Viz [Scheduled Events](scheduled-events.md) | 2017-08-01

#### <a name="instance-api"></a>Rozhraní API instance
##### <a name="the-following-compute-categories-are-available-through-the-instance-api"></a>V rozhraní API instance jsou k dispozici následující výpočetní kategorie:

> [!NOTE]
> Prostřednictvím koncového bodu metadat jsou k dispozici následující kategorie prostřednictvím instance/Compute.

Data | Popis | Představená verze
-----|-------------|-----------------------
azEnvironment | Prostředí Azure, ve kterém je spuštěný virtuální počítač | 2018-10-01
customData | Zobrazit [vlastní data](#custom-data) | 2019-02-01
location | Oblast Azure, ve které je spuštěný virtuální počítač | 2017-04-02
jméno | Název virtuálního počítače | 2017-04-02
offer | Informace o nabídce pro image virtuálního počítače a jsou k dispozici jenom pro Image nasazené z Galerie imagí Azure | 2017-04-02
osType | Linux nebo Windows | 2017-04-02
placementGroupId | [Skupina umístění](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) vaší sady škálování virtuálních počítačů | 2017-08-01
plánování | [Plánování](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) obsahující název, produkt a vydavatele pro virtuální počítač, pokud se jedná o Azure Marketplace image | 2018-04-02
platformUpdateDomain |  [Aktualizujte doménu](manage-availability.md) , ve které je spuštěný virtuální počítač. | 2017-04-02
platformFaultDomain | [Doména selhání](manage-availability.md) , ve kterém je spuštěný virtuální počítač | 2017-04-02
Zprostředkovatel | Poskytovatel virtuálního počítače | 2018-10-01
publicKeys | [Kolekce veřejných klíčů](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) přiřazených k virtuálnímu počítači a cestám | 2018-04-02
publisher | Vydavatel image virtuálního počítače | 2017-04-02
resourceGroupName | [Skupina prostředků](../../azure-resource-manager/management/overview.md) pro virtuální počítač | 2017-08-01
resourceId | [Plně kvalifikované](https://docs.microsoft.com/rest/api/resources/resources/getbyid) ID prostředku | 2019-03-11
skj | Konkrétní SKU pro bitovou kopii virtuálního počítače | 2017-04-02
subscriptionId | Předplatné Azure pro virtuální počítač | 2017-08-01
značek | [Značky](../../azure-resource-manager/resource-group-using-tags.md) pro virtuální počítač  | 2017-08-01
tagsList | Značky formátované jako pole JSON pro snazší programovou analýzu  | 2019-06-04
version | Verze image virtuálního počítače | 2017-04-02
vmId | [Jedinečný identifikátor](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) pro virtuální počítač | 2017-04-02
vmScaleSetName | [Název škálovací sady virtuálního počítače](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pro sadu škálování virtuálního počítače | 2017-12-01
vmSize | [Velikost virtuálního počítače](sizes.md) | 2017-04-02
zóna | [Zóna dostupnosti](../../availability-zones/az-overview.md) virtuálního počítače | 2017-12-01

##### <a name="the-following-network-categories-are-available-through-the-instance-api"></a>V rozhraní API instance jsou k dispozici následující kategorie sítě:

> [!NOTE]
> Prostřednictvím koncového bodu metadat jsou k dispozici následující kategorie prostřednictvím instance/sítě/rozhraní.

Data | Popis | Představená verze
-----|-------------|-----------------------
ipv4/privateIpAddress | Místní IPv4 adresa virtuálního počítače | 2017-04-02
IPv4/publicIpAddress | Veřejná IPv4 adresa virtuálního počítače | 2017-04-02
podsíť/adresa | Adresa podsítě virtuálního počítače | 2017-04-02
podsíť/předpona | Předpona podsítě, příklad 24 | 2017-04-02
ipv6/ipAddress | Místní IPv6 adresa virtuálního počítače | 2017-04-02
macAddress | Adresa MAC virtuálního počítače | 2017-04-02

## <a name="attested-data"></a>Ověřená data

Metadata instance reagují na koncovém bodu http na 169.254.169.254. Součástí scénáře, který obsluhuje Instance Metadata Service, je zajištění záruky, že data přicházející z Azure pocházejí. Tyto informace podepisujeme, aby image na webu Marketplace mohly mít jistotu, že se jedná o image běžící v Azure.

### <a name="example-attested-data"></a>Příklad ověřených dat

> [!NOTE]
> Všechny odpovědi rozhraní API jsou řetězce JSON. Následující příklad odpovědí je poměrně vytištěn z důvodu čitelnosti.

 **Požadavek**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Verze API-Version je povinné pole. Podporované verze rozhraní API najdete v [části dostupnost služby](#service-availability) .
Hodnota nonce je nepovinný řetězec s deseti číslicemi. Hodnota nonce může být použita ke sledování požadavku a není-li zadána, v řetězci kódovaném odpovědí je vráceno aktuální časové razítko UTC.

 **Odpověď**

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je poměrně vytištěn z důvodu čitelnosti.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> Objekt BLOB podpisu je verze dokumentu s podpisem [PKCS7](https://aka.ms/pkcs7) . Obsahuje certifikát použitý k podepsání spolu s podrobnostmi o virtuálním počítači, jako je například vmId, nonce, subscriptionId, časové razítko pro vytvoření a vypršení platnosti dokumentu a informace o plánu k imagi. Informace o plánu se naplní jenom pro image na místě na trhu Azure. Certifikát se dá extrahovat z odpovědi a použít k ověření, že odpověď je platná a přichází z Azure.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Načítají se ověřená metadata ve virtuálním počítači s Windows.

 **Požadavek**

Metadata instance lze v systému Windows načíst pomocí `curl`nástroje PowerShell:

 ```bash
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Nebo pomocí rutiny `Invoke-RestMethod`:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

Verze API-Version je povinné pole. Podporované verze rozhraní API najdete v části dostupnost služby.
Hodnota nonce je nepovinný řetězec s deseti číslicemi. Hodnota nonce může být použita ke sledování požadavku a není-li zadána, v řetězci kódovaném odpovědí je vráceno aktuální časové razítko UTC.

 **Odpověď**

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je poměrně vytištěn z důvodu čitelnosti.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> Objekt BLOB podpisu je verze dokumentu s podpisem [PKCS7](https://aka.ms/pkcs7) . Obsahuje certifikát použitý k podepsání spolu s podrobnostmi o virtuálním počítači, jako je například vmId, nonce, subscriptionId, časové razítko pro vytvoření a vypršení platnosti dokumentu a informace o plánu k imagi. Informace o plánu se naplní jenom pro image na místě na trhu Azure. Certifikát se dá extrahovat z odpovědi a použít k ověření, že odpověď je platná a přichází z Azure.


## <a name="example-scenarios-for-usage"></a>Příklady scénářů použití  

### <a name="tracking-vm-running-on-azure"></a>Sledování virtuálního počítače spuštěného v Azure

Jako poskytovatel služeb budete možná potřebovat sledovat počet virtuálních počítačů, na kterých běží váš software, nebo mít agenty, kteří potřebují ke sledování jedinečnosti virtuálního počítače. Aby bylo možné získat jedinečné ID pro virtuální počítač, použijte pole `vmId` z Instance Metadata Service.

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Odpověď**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Umístění kontejnerů do domény selhání nebo aktualizační domény založené na datových oddílech 

V některých scénářích je umístění různých replik dat primárním významem. Například [umístění repliky HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) nebo umístění kontejneru přes [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) vám může vyžadovat, abyste věděli `platformFaultDomain` a `platformUpdateDomain`, na kterém je virtuální počítač spuštěný.
K provedení těchto rozhodnutí můžete použít také [zóny dostupnosti](../../availability-zones/az-overview.md) pro instance.
Tato data můžete zadávat přímo prostřednictvím Instance Metadata Service.

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Odpověď**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Získání dalších informací o virtuálním počítači pro případ podpory

Jako poskytovatel služeb můžete obdržet volání podpory, kde byste chtěli získat další informace o virtuálním počítači. Dotazování zákazníků na sdílení výpočetních metadat může poskytnout základní informace o tom, že profesionální pracovník podpory ví o typu virtuálního počítače v Azure. 

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Odpověď**

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je poměrně vytištěn z důvodu čitelnosti.

```json
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Získání prostředí Azure, ve kterém je virtuální počítač spuštěný

Azure má různé cloudy svrchovan jako [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Někdy potřebujete prostředí Azure, abyste mohli provádět určitá rozhodnutí za běhu. Následující příklad ukazuje, jak lze dosáhnout tohoto chování.

**Požadavek**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Odpověď**
```bash
AzurePublicCloud
```

Níže jsou uvedené oblasti a hodnoty prostředí Azure.

 Oblasti | Prostředí Azure
---------|-----------------
[Všechny všeobecně dostupné globální oblasti Azure](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure (Čína)](https://azure.microsoft.com/global-infrastructure/china)                   | AzureChinaCloud
[Azure Německo](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>Získávání značek pro virtuální počítač

Na VIRTUÁLNÍm počítači Azure možná byly aplikovány značky, aby je bylo možné logicky uspořádat do taxonomie. Značky přiřazené k virtuálnímu počítači se dají načíst pomocí níže uvedeného požadavku.

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Odpověď**

```text
Department:IT;Environment:Test;Role:WebRole
```

Pole `tags` je řetězec, jehož značky jsou odděleny středníky. To může být problém, pokud se v samotných značkách používají středníky. Pokud je analyzátor napsán pro programové extrakci značek, měli byste spoléhat na pole `tagsList`, které je polem JSON bez oddělovačů, a následně je snazší ho analyzovat.

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=JSON"
```

**Odpověď**

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

Dodavatelé na webu Marketplace chtějí zajistit, aby byl software licencován pro spouštění pouze v Azure. Pokud někdo zkopíruje virtuální pevný disk do místního prostředí, pak by měl mít možnost ho detekovat. Voláním do Instance Metadata Service můžou dodavatelé na webu Marketplace získat podepsaná data, která garantuje odpověď jenom z Azure.

> [!NOTE]
> Vyžaduje instalaci JQ.

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

 **Odpověď**

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
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"
}
```

Data | Popis
-----|------------
nonce | Uživatel zadal nepovinný řetězec s požadavkem. Pokud se v požadavku nezadala hodnota nonce, vrátí se aktuální časové razítko UTC.
plánování | [Naplánování](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) virtuálního počítače v tomto Azure Marketplace imagi obsahuje název, produkt a vydavatele.
časové razítko/createdOn | Časové razítko, ve kterém byl vytvořen první podepsaný dokument
časové razítko/expiresOn | Časové razítko, na kterém vyprší platnost podepsaného dokumentu
vmId |  [Jedinečný identifikátor](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) pro virtuální počítač
subscriptionId | Předplatné Azure pro virtuální počítač, které jste zavedli v `2019-04-30`

#### <a name="verifying-the-signature"></a>Ověření podpisu

Jakmile získáte podpis výše, můžete ověřit, že signatura pochází od Microsoftu. Můžete také ověřit zprostředkující certifikát a řetěz certifikátů. Nakonec můžete ověřit, jestli je ID předplatného správné.

> [!NOTE]
> Certifikát pro veřejný cloud a Cloud z svrchovaného cloudu se liší.

 Cloud | Certifikát
---------|-----------------
[Všechny všeobecně dostupné globální oblasti Azure](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Azure (Čína)](https://azure.microsoft.com/global-infrastructure/china/)                  | metadata.azure.cn
[Azure Německo](https://azure.microsoft.com/overview/clouds/germany/)                    | metadata.microsoftazure.de

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

V případech, kdy se zprostředkující certifikát nedá stáhnout kvůli omezením sítě během ověřování, jde zprostředkující certifikát připnout. Azure ale převezme certifikáty podle standardních postupů PKI. Připnuté certifikáty by se musely aktualizovat, když dojde k převrácení. Pokaždé, když se naplánuje změna aktualizace zprostředkujícího certifikátu, bude se aktualizovat blog Azure a budou se zákazníkům Azure informovat. Zprostředkující certifikáty najdete [tady](https://www.microsoft.com/pki/mscorp/cps/default.htm). Zprostředkující certifikáty pro jednotlivé oblasti se můžou lišit.

### <a name="failover-clustering-in-windows-server"></a>Clustering s podporou převzetí služeb při selhání v systému Windows Server

V některých scénářích při dotazování na Instance Metadata Service s clusteringem s podporou převzetí služeb při selhání je nutné přidat trasu do směrovací tabulky.

1. Otevřete příkazový řádek s oprávněními správce.

2. Spusťte následující příkaz a poznamenejte si adresu rozhraní pro cíl sítě (`0.0.0.0`) v tabulce směrování IPv4.

```bat
route print
```

> [!NOTE]
> Následující příklad výstupu z virtuálního počítače s Windows serverem a s povoleným clusterem s podporou převzetí služeb při selhání obsahuje jenom tabulku směrování IPv4 pro jednoduchost.

```bat
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
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

1. Spusťte následující příkaz a použijte adresu rozhraní pro cílové místo v síti (`0.0.0.0`), což je (`10.0.1.10`) v tomto příkladu.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="custom-data"></a>Vlastní data
Instance Metadata Service poskytuje virtuálnímu počítači možnost přístupu k vlastním datům. Binární data musí být menší než 64 KB a k virtuálnímu počítači se poskytuje ve formě kódované v kódování Base64.

Vlastní data Azure je možné do virtuálního počítače vložit přes rozhraní REST API, rutiny PowerShellu, rozhraní příkazového řádku Azure (CLI) nebo šablonu ARM.

Příklad rozhraní příkazového řádku Azure najdete v tématu [vlastní data a Cloud-init na Microsoft Azure](https://azure.microsoft.com/blog/custom-data-and-cloud-init-on-windows-azure/).

Příklad šablony ARM najdete v tématu [nasazení virtuálního počítače s CustomData](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).

Vlastní data jsou k dispozici pro všechny procesy běžící na virtuálním počítači. Je doporučeno, aby zákazníci nevložili tajné informace do vlastních dat.

V současné době je zaručeno, že vlastní data budou k dispozici při zavádění virtuálního počítače. Pokud se na virtuální počítač provedou aktualizace, jako je například přidání disků nebo změna velikosti virtuálního počítače, Instance Metadata Service nebude poskytovat vlastní data. Trvalé poskytování vlastních dat prostřednictvím Instance Metadata Service právě probíhá.

#### <a name="retrieving-custom-data-in-virtual-machine"></a>Načítají se vlastní data na virtuálním počítači.
Instance Metadata Service poskytuje virtuálnímu počítači vlastní data ve formě kódované v kódování Base64. Následující příklad Dekóduje řetězec kódovaný v kódování Base64.

> [!NOTE]
> Vlastní data v tomto příkladu se interpretují jako řetězec ASCII, který čte "moje vlastní data".

**Požadavek**

```bash
curl -H "Metadata:true" "http://169.254.169.254/metadata/instance/compute/customData?api-version=2019-02-01&&format=text" | base64 --decode
```

**Odpověď**

```text
My custom data.
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Příklady volání služby metadat pomocí různých jazyků v rámci virtuálního počítače 

Jazyk | Příklad:
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
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

## <a name="faq"></a>Časté otázky

1. Zobrazuje se chyba `400 Bad Request, Required metadata header not specified`. Co to znamená?
   * Instance Metadata Service vyžaduje, aby byla v požadavku předána hlavička `Metadata: true` záhlaví. Předání této hlavičky v volání REST umožňuje přístup k Instance Metadata Service.
2. Proč mi nezískávám výpočetní informace pro svůj virtuální počítač?
   * V současné době Instance Metadata Service podporuje jenom instance vytvořené pomocí Azure Resource Manager. V budoucnu se může přidat podpora virtuálních počítačů cloudových služeb.
3. Tento virtuální počítač jsem vytvořil přes Azure Resource Manager a během zálohování. Proč se mi nezobrazuje informace o metadatech COMPUTE?
   * Pro všechny virtuální počítače vytvořené po SEP 2016 přidejte [značku](../../azure-resource-manager/resource-group-using-tags.md) , která začne zobrazovat metadata Compute. Pro starší virtuální počítače (vytvořené před SEP 2016) přidejte nebo odeberte rozšíření nebo datové disky k virtuálnímu počítači, aby se metadata aktualizovala.
4. Nezobrazuje se všechna data naplněná pro novou verzi
   * Pro všechny virtuální počítače vytvořené po SEP 2016 přidejte [značku](../../azure-resource-manager/resource-group-using-tags.md) , která začne zobrazovat metadata Compute. Pro starší virtuální počítače (vytvořené před SEP 2016) přidejte nebo odeberte rozšíření nebo datové disky k virtuálnímu počítači, aby se metadata aktualizovala.
5. Proč se mi zobrazuje chyba `500 Internal Server Error`?
   * Opakujte požadavek na základě exponenciálního systému. Pokud se problém nevyřeší, obraťte se na podporu Azure.
6. Kde můžu sdílet další otázky a komentáře?
   * Odešlete komentáře na https://feedback.azure.com.
7. Bude tato práce fungovat pro instanci sady škálování virtuálních počítačů?
   * Služba metadat pro instance sady škálování je k dispozici.
8. Návody získat podporu pro službu?
   * Pokud chcete získat podporu pro službu, vytvořte problém podpory v Azure Portal pro virtuální počítač, ke kterému nemůžete po dlouhou dobu pokusů získat odpověď na metadata.
9. Vypršel časový limit žádosti o mé volání služby?
   * V případě, že jste změnili trasy, musí být volání metadat z primární IP adresy přiřazené síťové kartě virtuálního počítače, a to i v případě, že jste změnili své trasy, musí být trasa pro 169.254.0.0/16 vycházející z vaší síťové karty.
10. Aktualizoval (a) jsem moje značky v sadě škálování virtuálního počítače, ale nezobrazují se v instancích na rozdíl od virtuálních počítačů?
    * V současné době se pro značky ScaleSets zobrazují jenom virtuální počítače na restartování/obnovení Image/nebo se změní na disk.

    ![Podpora metadat instance](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Další kroky

- Další informace o [Scheduled Events](scheduled-events.md)
