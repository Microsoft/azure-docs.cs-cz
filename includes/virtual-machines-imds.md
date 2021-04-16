---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: 3da4f8f946b11985d93be35fa2748e7f25015a71
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564554"
---
Služba Azure Instance Metadata Service (IMDS) poskytuje informace o aktuálně spuštěných instancích virtuálních počítačů. Můžete ji použít ke správě a konfiguraci virtuálních počítačů.
Tyto informace zahrnují SKU, úložiště, konfigurace sítě a nadcházející události údržby. Úplný seznam dostupných dat najdete v části [Souhrn kategorií koncových bodů](#endpoint-categories).

IMDS je k dispozici pro spuštěné instance virtuálních počítačů a instance sady škálování virtuálních počítačů. Všechny koncové body podporují virtuální počítače vytvořené a spravované pomocí [Azure Resource Manager](/rest/api/resources/). Virtuální počítače, které jsou vytvořené pomocí modelu nasazení Classic, podporují jenom kategorie s ověřenou a síťovou částí kategorie instance. Ověřený koncový bod funguje tak pouze v omezeném rozsahu.

IMDS je REST API, který je k dispozici na dobře známé IP adrese, která není směrovatelný `169.254.169.254` . K němu máte přístup jenom z virtuálního počítače. Komunikace mezi virtuálním počítačem a IMDS nikdy neopustí hostitele.
Klienti HTTP při dotazování IMDS vynechá webové proxy servery v rámci virtuálního počítače a považovat `169.254.169.254` za stejné jako [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="usage"></a>Využití

### <a name="access-azure-instance-metadata-service"></a>Přístup k Azure Instance Metadata Service

Pokud chcete získat přístup k IMDS, vytvořte virtuální počítač z [Azure Resource Manager](/rest/api/resources/) nebo [Azure Portal](https://portal.azure.com)a použijte následující ukázky.
Další příklady najdete v tématu [ukázky metadat instance Azure](https://github.com/microsoft/azureimds).

Zde je ukázkový kód pro načtení všech metadat pro instanci. Chcete-li získat přístup ke konkrétnímu zdroji dat, přečtěte si téma [kategorie koncových bodů](#endpoint-categories) , kde najdete přehled všech dostupných funkcí.

**Žádost**

> [!IMPORTANT]
> Tento příklad obchází proxy servery. Při dotazování na IMDS je **nutné** obejít proxy servery. Další informace najdete v tématu [proxy](#proxies) .

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | jq
```

---

**Response** (Odpověď)

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je poměrně vytištěn z důvodu čitelnosti.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>Zabezpečení a ověřování

Instance Metadata Service je k dispozici pouze v rámci spuštěné instance virtuálního počítače na IP adrese, která není směrovatelný. Virtuální počítače jsou omezené na interakci s metadaty a funkcemi, které se vztahují samy na sebe. Rozhraní API je pouze HTTP a nikdy neopouští hostitele.

Aby bylo zajištěno, že požadavky jsou přímo určeny pro IMDS a aby nedocházelo k neúmyslnému nebo nechtěnému přesměrování požadavků, požadavky:
- **Musí** obsahovat hlavičku. `Metadata: true`
- Nesmí **obsahovat** `X-Forwarded-For` hlavičku

Všechny žádosti, které nesplňují **oba** tyto požadavky, budou službou odmítnuty.

> [!IMPORTANT]
> IMDS není **kanál** pro citlivá data. Rozhraní API není ověřené a otevřené pro všechny procesy na virtuálním počítači. Informace vystavené prostřednictvím této služby by se měly považovat za sdílené informace pro všechny aplikace běžící v rámci virtuálního počítače.

## <a name="proxies"></a>Proxy

IMDS není **určený k** použití za proxy serverem a není tak podporován. Většina klientů protokolu HTTP nabízí možnost zakázat proxy na vašich žádostech a tato funkce musí být využívána při komunikaci s IMDS. Podrobnosti najdete v dokumentaci ke klientovi.

> [!IMPORTANT]
> I když ve svém prostředí neznáte žádnou konfiguraci proxy serveru, **musíte přepsat všechna výchozí nastavení proxy serveru klienta**. Konfigurace proxy serveru se můžou automaticky zjistit a nedaří se jim jim obejít takové konfigurace, které vám umožní v budoucnu změnit konfiguraci počítače.

## <a name="rate-limiting"></a>Omezování rychlosti

Obecně platí, že požadavky na IMDS jsou omezeny na 5 požadavků za sekundu. Žádosti překračující tuto prahovou hodnotu budou odmítnuty s 429 odpověďmi. Žádosti do kategorie [spravované identity](#managed-identity) jsou omezené na 20 požadavků za sekundu a 5 souběžných požadavků.

## <a name="http-verbs"></a>Příkazy HTTP

V současné době jsou podporovány následující příkazy protokolu HTTP:

| Příkaz | Description |
|------|-------------|
| `GET` | Načíst požadovaný prostředek

## <a name="parameters"></a>Parametry

Koncové body můžou podporovat požadované nebo volitelné parametry. Podrobnosti najdete v tématu [schéma](#schema) a dokumentace pro konkrétní koncový bod.

### <a name="query-parameters"></a>Parametry dotazů

Koncové body IMDS podporují parametry řetězce dotazu HTTP. Například: 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

Určuje parametry:

| Name | Hodnota |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

Žádosti s duplicitními názvy parametrů dotazu budou odmítnuty.

### <a name="route-parameters"></a>Parametry směrování

U některých koncových bodů, které vracejí větší objekty blob JSON, podporujeme připojení parametrů trasy k koncovému bodu žádosti, aby se vyfiltroval podmnožina odpovědi: 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
Parametry odpovídají indexům nebo klíčům, které by se použily k procházení objektu JSON při interakci s analyzovanou reprezentací.

Například `/metatadata/instance` vrátí objekt JSON:
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
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
            },
            ...
        ]
    }
}
```

Pokud chceme odpovědět na tuto odpověď pouze na výpočetní vlastnost, pošlete nám požadavek: 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

Podobně platí, že pokud chceme filtrovat na vnořenou vlastnost nebo konkrétní element pole, budeme připojovat klíče dál: 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
by měl filtrovat na první prvek z `Network.interface` vlastnosti a vracet:

```json
{
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
}
```

> [!NOTE]
> Při filtrování na uzel typu list `format=json` nefunguje. Pro tyto dotazy je `format=text` nutné explicitně zadat, protože výchozí formát je JSON.

## <a name="schema"></a>Schéma

### <a name="data-format"></a>Formát dat

Ve výchozím nastavení vrátí IMDS data ve formátu JSON ( `Content-Type: application/json` ). Nicméně koncové body, které podporují filtrování odpovědí (viz [parametry směrování](#route-parameters)), podporují i formát `text` .

Pokud chcete získat přístup k nevýchozímu formátu odpovědi, v žádosti určete požadovaný formát jako parametr řetězce dotazu. Například:

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

V odpovědích JSON budou všechny primitivní prvky typu `string` a chybějící nebo nepoužité hodnoty jsou vždycky zahrnuté, ale budou nastavené na prázdný řetězec.

### <a name="versioning"></a>Správa verzí

IMDS má verzi a určení verze rozhraní API v požadavku HTTP je povinné. Jedinou výjimkou z tohoto požadavku je koncový bod [verze](#versions) , který lze použít k dynamickému načtení dostupných verzí rozhraní API.

Při přidávání novějších verzí je k zajištění kompatibility stále k dispozici starší verze, pokud vaše skripty mají závislosti na konkrétních formátech dat.

Pokud neurčíte verzi, zobrazí se chyba se seznamem nejnovějších podporovaných verzí:
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

#### <a name="supported-api-versions"></a>Podporované verze rozhraní API
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
- 2020-12-01
- 2021-01-01

### <a name="swagger"></a>Swagger

Úplná definice Swagger pro IMDS je k dispozici na adrese: https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>Regionální dostupnost

Služba je **všeobecně dostupná** ve všech cloudech Azure.

## <a name="root-endpoint"></a>Kořenový koncový bod

Kořenový koncový bod je `http://169.254.169.254/metadata` .

## <a name="endpoint-categories"></a>Kategorie koncových bodů

Rozhraní IMDS API obsahuje několik kategorií koncového bodu, které představují různé zdroje dat, z nichž každý obsahuje jeden nebo více koncových bodů. Podrobnosti najdete v každé kategorii.

| Kořen kategorie | Description | Představená verze |
|---------------|-------------|--------------------|
| `/metadata/attested` | Viz [Attestation data](#attested-data) | 2018-10-01
| `/metadata/identity` | Viz [spravovaná identita prostřednictvím IMDS](#managed-identity) . | 2018-02-01
| `/metadata/instance` | Viz [metadata instance](#instance-metadata) | 2017-04-02
| `/metadata/loadbalancer` | Viz [načtení metadat Load Balancer přes IMDS](#load-balancer-metadata) . | 2020-10-01
| `/metadata/scheduledevents` | Zobrazit [Scheduled Events přes IMDS](#scheduled-events) | 2017-08-01
| `/metadata/versions` | Zobrazit [verze](#versions) | –

## <a name="versions"></a>Verze

> [!NOTE]
> Tato funkce byla vydaná společně s verzí 2020-10-01, která se v tuto chvíli zavádí a možná není dostupná v každé oblasti.

### <a name="list-api-versions"></a>Vypsat verze rozhraní API

Vrátí sadu podporovaných verzí rozhraní API.

```
GET /metadata/versions
```

#### <a name="parameters"></a>Parametry

Žádné (tento koncový bod je bez verze).

#### <a name="response"></a>Odpověď

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>Metadata instance

### <a name="get-vm-metadata"></a>Získání metadat virtuálního počítače

Zpřístupňuje důležitá metadata pro instanci virtuálního počítače, včetně výpočetních prostředků, sítě a úložiště. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>Parametry

| Name | Požadováno/volitelné | Popis |
|------|-------------------|-------------|
| `api-version` | Povinné | Verze, která se používá k obsluhování žádosti
| `format` | Volitelné | Formát ( `json` nebo `text` ) odpovědi. * Poznámka: může být vyžadováno při použití parametrů Request

Tento koncový bod podporuje filtrování odpovědí prostřednictvím [parametrů směrování](#route-parameters).

#### <a name="response"></a>Odpověď

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Rozpis schématu:

**Výpočetní služby**

| Data | Description | Představená verze |
|------|-------------|--------------------|
| `azEnvironment` | Prostředí Azure, ve kterém je spuštěný virtuální počítač | 2018-10-01
| `customData` | Tato funkce je zastaralá a zakázaná. Byl nahrazen `userData` | 2019-02-01
| `evictionPolicy` | Nastaví, jak bude [virtuální počítač s přímým](../articles/virtual-machines/spot-vms.md) vyřazením. | 2020-12-01
| `isHostCompatibilityLayerVm` | Určuje, jestli se virtuální počítač spouští na vrstvě kompatibility hostitele. | 2020-06-01
| `licenseType` | Typ licence pro [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Tato možnost je k dispozici pouze pro virtuální počítače s podporou AHB. | 2020-09-01
| `location` | Oblast Azure, ve které je spuštěný virtuální počítač | 2017-04-02
| `name` | Název virtuálního počítače | 2017-04-02
| `offer` | Informace o nabídce pro image virtuálního počítače a jsou k dispozici jenom pro Image nasazené z Galerie imagí Azure | 2017-04-02
| `osProfile.adminUsername` | Určuje název účtu správce. | 2020-07-15
| `osProfile.computerName` | Určuje název počítače. | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Určuje, jestli je zakázané ověřování heslem. Toto je k dispozici pouze pro virtuální počítače se systémem Linux. | 2020-10-01
| `osType` | Linux nebo Windows | 2017-04-02
| `placementGroupId` | [Skupina umístění](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) vaší sady škálování virtuálních počítačů | 2017-08-01
| `plan` | [Plánování](/rest/api/compute/virtualmachines/createorupdate#plan) obsahující název, produkt a vydavatele pro virtuální počítač, pokud se jedná o Azure Marketplace image | 2018-04-02
| `platformUpdateDomain` |  [Aktualizujte doménu](../articles/virtual-machines/availability.md) , ve které je spuštěný virtuální počítač. | 2017-04-02
| `platformFaultDomain` | [Doména selhání](../articles/virtual-machines/availability.md) , ve kterém je spuštěný virtuální počítač | 2017-04-02
| `priority` | Priorita virtuálního počítače. Další informace najdete v části [virtuální počítače s přímým](../articles/virtual-machines/spot-vms.md) odkazem. | 2020-12-01
| `provider` | Poskytovatel virtuálního počítače | 2018-10-01
| `publicKeys` | [Kolekce veřejných klíčů](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) přiřazených k virtuálnímu počítači a cestám | 2018-04-02
| `publisher` | Vydavatel image virtuálního počítače | 2017-04-02
| `resourceGroupName` | [Skupina prostředků](../articles/azure-resource-manager/management/overview.md) pro virtuální počítač | 2017-08-01
| `resourceId` | [Plně kvalifikované](/rest/api/resources/resources/getbyid) ID prostředku | 2019-03-11
| `sku` | Konkrétní SKU pro bitovou kopii virtuálního počítače | 2017-04-02
| `securityProfile.secureBootEnabled` | Určuje, jestli je na virtuálním počítači povolené zabezpečené spouštění přes UEFI. | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Určuje, jestli je na virtuálním počítači povolený čip TPM (Virtual Trusted Platform Module). | 2020-06-01
| `storageProfile` | Viz profil úložiště níže. | 2019-06-01
| `subscriptionId` | Předplatné Azure pro virtuální počítač | 2017-08-01
| `tags` | [Značky](../articles/azure-resource-manager/management/tag-resources.md) pro virtuální počítač  | 2017-08-01
| `tagsList` | Značky formátované jako pole JSON pro snazší programovou analýzu  | 2019-06-04
| `userData` | Sada dat zadaná při vytvoření virtuálního počítače pro použití během zřizování nebo po jeho zřízení (s kódováním base64)  | 2021-01-01
| `version` | Verze image virtuálního počítače | 2017-04-02
| `vmId` | [Jedinečný identifikátor](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) pro virtuální počítač | 2017-04-02
| `vmScaleSetName` | [Název sady škálování virtuálního počítače](../articles/virtual-machine-scale-sets/overview.md) pro sadu škálování virtuálního počítače | 2017-12-01
| `vmSize` | [Velikost virtuálního počítače](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | [Zóna dostupnosti](../articles/availability-zones/az-overview.md) virtuálního počítače | 2017-12-01

**Profil úložiště**

Profil úložiště virtuálního počítače je rozdělen do tří kategorií: odkaz na obrázek, disk s operačním systémem a datové disky.

Objekt odkazu na bitovou kopii obsahuje následující informace o imagi operačního systému:

| Data | Description |
|------|-------------|
| `id` | ID prostředku
| `offer` | Nabídka platformy nebo Image Marketplace
| `publisher` | Vydavatel obrázku
| `sku` | SKU image
| `version` | Verze image platformy nebo webu Marketplace

Objekt disku operačního systému obsahuje následující informace o disku s operačním systémem, který používá virtuální počítač:

| Data | Description |
|------|-------------|
| `caching` | Požadavky na ukládání do mezipaměti
| `createOption` | Informace o tom, jak byl virtuální počítač vytvořen
| `diffDiskSettings` | Nastavení dočasného disku
| `diskSizeGB` | Velikost disku v GB
| `image`   | Virtuální pevný disk zdrojové image uživatele
| `lun`     | Logické číslo jednotky disku
| `managedDisk` | Parametry spravovaného disku
| `name`    | Název disku
| `vhd`     | Virtuální pevný disk
| `writeAcceleratorEnabled` | Bez ohledu na to, jestli je na disku povolená writeAccelerator

Pole datových disků obsahuje seznam datových disků připojených k virtuálnímu počítači. Každý objekt datového disku obsahuje následující informace:

Data | Description |
-----|-------------|
| `caching` | Požadavky na ukládání do mezipaměti
| `createOption` | Informace o tom, jak byl virtuální počítač vytvořen
| `diffDiskSettings` | Nastavení dočasného disku
| `diskSizeGB` | Velikost disku v GB
| `encryptionSettings` | Nastavení šifrování disku
| `image` | Virtuální pevný disk zdrojové image uživatele
| `managedDisk` | Parametry spravovaného disku
| `name` | Název disku
| `osType` | Typ operačního systému zahrnutý na disku
| `vhd` | Virtuální pevný disk
| `writeAcceleratorEnabled` | Bez ohledu na to, jestli je na disku povolená writeAccelerator

**Síť**

| Data | Description | Představená verze |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | Místní IPv4 adresa virtuálního počítače | 2017-04-02
| `ipv4.publicIpAddress` | Veřejná IPv4 adresa virtuálního počítače | 2017-04-02
| `subnet.address` | Adresa podsítě virtuálního počítače | 2017-04-02
| `subnet.prefix` | Předpona podsítě, příklad 24 | 2017-04-02
| `ipv6.ipAddress` | Místní IPv6 adresa virtuálního počítače | 2017-04-02
| `macAddress` | Adresa MAC virtuálního počítače | 2017-04-02

### <a name="get-user-data"></a>Získání uživatelských dat

Při vytváření nového virtuálního počítače můžete zadat sadu dat, která se mají použít během nebo po zřízení virtuálního počítače, a načíst je prostřednictvím IMDS. Pokud chcete nastavit uživatelská data, využijte [tu](https://aka.ms/ImdsUserDataArmTemplate)šablonu pro rychlý Start. Následující ukázka ukazuje, jak načíst tato data prostřednictvím IMDS.

> [!NOTE]
> Tato funkce je vydaná s verzí `2021-01-01` a závisí na aktualizaci platformy Azure, která se v tuto chvíli zavádí a která ještě nemusí být dostupná v každé oblasti.

> [!NOTE]
> Bezpečnostní upozornění: IMDS je otevřen pro všechny aplikace na virtuálním počítači, citlivá data by neměla být umístěna v uživatelských datech.


#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text" | base64 --decode
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text" | base64 --decode
```

---


#### <a name="sample-1-tracking-vm-running-on-azure"></a>Ukázka 1: sledování virtuálního počítače běžícího na Azure

Jako poskytovatel služeb budete možná potřebovat sledovat počet virtuálních počítačů, na kterých běží váš software, nebo mít agenty, kteří potřebují ke sledování jedinečnosti virtuálního počítače. Aby bylo možné získat jedinečné ID pro virtuální počítač, použijte `vmId` pole z instance metadata Service.

**Žádost**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**Response** (Odpověď)

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>Ukázka 2: umístění různých replik dat

V některých scénářích je umístění různých replik dat primárním významem. Například [umístění repliky HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) nebo umístění kontejneru přes [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) vám může vyžadovat, abyste věděli, na `platformFaultDomain` kterém `platformUpdateDomain` virtuální počítač běží.
K provedení těchto rozhodnutí můžete použít také [zóny dostupnosti](../articles/availability-zones/az-overview.md) pro instance.
Tato data můžete zadávat přímo přes IMDS.

**Žádost**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Response** (Odpověď)

```
0
```

#### <a name="sample-3-get-vm-tags"></a>Ukázka 3: získání značek VM

Značky virtuálních počítačů jsou součástí rozhraní API instancí pod koncovým bodem instance/výpočty/značky.
Na VIRTUÁLNÍm počítači Azure možná byly aplikovány značky, aby je bylo možné logicky uspořádat do taxonomie. Značky přiřazené k virtuálnímu počítači se dají načíst pomocí níže uvedeného požadavku.

**Žádost**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Response** (Odpověď)

```
Department:IT;ReferenceNumber:123456;TestStatus:Pending
```

`tags`Pole je řetězec, jehož značky jsou odděleny středníky. Tento výstup může být problémem, pokud se středníky používají v samotných značkách. Pokud je analyzátor napsán pro programové extrakci značek, měli byste spoléhat na `tagsList` pole. `tagsList`Pole je pole JSON bez oddělovačů, a proto je snazší ho analyzovat. TagsList přiřazený k virtuálnímu počítači se dá načíst pomocí níže uvedeného požadavku.

**Žádost**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | jq
```

---

**Response** (Odpověď)

#### <a name="windows"></a>[Windows](#tab/windows/)

```json
{
    "value":  [
                  {
                      "name":  "Department",
                      "value":  "IT"
                  },
                  {
                      "name":  "ReferenceNumber",
                      "value":  "123456"
                  },
                  {
                      "name":  "TestStatus",
                      "value":  "Pending"
                  }
              ],
    "Count":  3
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "ReferenceNumber",
    "value": "123456"
  },
  {
    "name": "TestStatus",
    "value": "Pending"
  }
]
```

---


#### <a name="sample-4-get-more-information-about-the-vm-during-support-case"></a>Ukázka 4: získání dalších informací o virtuálním počítači během případu podpory

Jako poskytovatel služeb můžete obdržet volání podpory, kde byste chtěli získat další informace o virtuálním počítači. Dotazování zákazníků na sdílení výpočetních metadat může poskytnout základní informace o tom, že profesionální pracovník podpory ví o typu virtuálního počítače v Azure.

**Žádost**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Response** (Odpověď)

> [!NOTE]
> Odpověď je řetězec JSON. Následující příklad odpovědi je poměrně vytištěn z důvodu čitelnosti.

#### <a name="windows"></a>[Windows](#tab/windows/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "WindowsServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Windows",
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
    "sku": "2019-Datacenter",
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
            "offer": "WindowsServer",
            "publisher": "MicrosoftWindowsServer",
            "sku": "2019-Datacenter",
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
            "osType": "Windows",
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

#### <a name="linux"></a>[Linux](#tab/linux/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "UbuntuServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Linux",
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
    "publisher": "Canonical",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "18.04-LTS",
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
            "osType": "linux",
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

---

#### <a name="sample-5-get-the-azure-environment-where-the-vm-is-running"></a>Ukázka 5: získání prostředí Azure, ve kterém je virtuální počítač spuštěný

Azure má různé cloudy svrchovan jako [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Někdy potřebujete prostředí Azure, abyste mohli provádět určitá rozhodnutí za běhu. Následující příklad ukazuje, jak lze dosáhnout tohoto chování.

**Žádost**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**Response** (Odpověď)

```
AzurePublicCloud
```

Tady je uvedený Cloud a hodnoty prostředí Azure.

| Cloud | Prostředí Azure |
|-------|-------------------|
| [Všechny všeobecně dostupné globální oblasti Azure](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure (Čína) 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure (Německo)](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-6-retrieve-network-information"></a>Ukázka 6: načtení informací o síti

**Žádost**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

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

#### <a name="sample-7-retrieve-public-ip-address"></a>Ukázka 7: načtení veřejné IP adresy

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>Ověřená data

### <a name="get-attested-data"></a>Získat ověřená data

IMDS pomáhá zajistit záruky, že poskytnutá data přicházejí z Azure. Společnost Microsoft podepisuje tyto informace, takže si můžete ověřit, že image v Azure Marketplace je ta, kterou používáte v Azure.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>Parametry

| Name | Požadováno/volitelné | Popis |
|------|-------------------|-------------|
| `api-version` | Povinné | Verze, která se používá k obsluhování žádosti
| `nonce` | Volitelné | Desítkový řetězec, který slouží jako kryptografická hodnota nonce. Pokud není zadána žádná hodnota, IMDS použije aktuální časové razítko UTC.

#### <a name="response"></a>Odpověď

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> Z důvodu mechanismu ukládání do mezipaměti IMDS může být vrácena hodnota náhodně v mezipaměti.

Objekt BLOB podpisu je verze dokumentu podepsaná pomocí [PKCS7](https://aka.ms/pkcs7). Obsahuje certifikát použitý k podepsání spolu s určitými podrobnostmi specifickými pro virtuální počítače.

Pro virtuální počítače vytvořené pomocí Azure Resource Manager obsahuje dokument `vmId` , `sku` , `nonce` , `subscriptionId` , `timeStamp` pro vytváření a vypršení platnosti dokumentu a informace o plánu k imagi. Informace o plánu se naplní jenom pro Azure Marketplace image.

U virtuálních počítačů vytvořených pomocí modelu nasazení Classic `vmId` je zaručeno, že bude naplněna pouze služba. Certifikát můžete extrahovat z odpovědi a použít ho k potvrzení, že odpověď je platná a přichází z Azure.

Dekódování dokumentu obsahuje následující pole:

| Data | Description | Představená verze |
|------|-------------|--------------------|
| `licenseType` | Typ licence pro [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Tato možnost je k dispozici pouze pro virtuální počítače s podporou AHB. | 2020-09-01
| `nonce` | Řetězec, který může být volitelně poskytnutý požadavkem. Pokud `nonce` nebyla zadána žádná, použije se aktuální koordinovaný světový časový razítko. | 2018-10-01
| `plan` | [Azure Marketplace plán obrázku](/rest/api/compute/virtualmachines/createorupdate#plan). Obsahuje ID plánu (název), obrázek produktu nebo nabídku (produkt) a ID vydavatele (vydavatel). | 2018-10-01
| `timestamp.createdOn` | Časové razítko UTC pro vytvoření podepsaného dokumentu | 2018-20-01
| `timestamp.expiresOn` | Časové razítko UTC pro vypršení platnosti podepsaného dokumentu | 2018-10-01
| `vmId` | [Jedinečný identifikátor](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) pro virtuální počítač | 2018-10-01
| `subscriptionId` | Předplatné Azure pro virtuální počítač | 2019-04-30
| `sku` | Konkrétní SKU pro bitovou kopii virtuálního počítače | 2019-11-01

> [!NOTE]
> Pro klasické virtuální počítače (bez Azure Resource Manager) je zaručena naplnit pouze vmId.

Ukázkový dokument:
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>Ukázka 1: ověření, jestli je virtuální počítač spuštěný v Azure

Dodavatelé v Azure Marketplace chtějí zajistit, aby byl software licencován pro spouštění pouze v Azure. Pokud někdo zkopíruje virtuální pevný disk do místního prostředí, musí být tento dodavatel schopný detekovat. Prostřednictvím IMDS můžou tito dodavatelé získat podepsaná data, která garantuje odpověď jenom z Azure.

> [!NOTE]
> Tato ukázka vyžaduje, aby byl nainstalovaný nástroj JQ.

**Ověřování**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
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

#### <a name="linux"></a>[Linux](#tab/linux/)

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

**Výsledky**

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

Ověřte, že signatura pochází z Microsoft Azure, a podívejte se na chyby v řetězu certifikátů.

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

---

> [!NOTE]
> Z důvodu mechanismu ukládání do mezipaměti IMDS `nonce` může být vrácena dříve hodnota uložená v mezipaměti.

`nonce`V podepsaném dokumentu může být porovnán, pokud jste zadali `nonce` parametr v počátečním požadavku.

> [!NOTE]
> Certifikát pro veřejný cloud a každý z svrchovaného cloudu se budou lišit.

| Cloud | Certifikát |
|-------|-------------|
| [Všechny všeobecně dostupné globální oblasti Azure](https://azure.microsoft.com/regions/) | *. metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *. metadata.azure.us
| [Azure (Čína) 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *. metadata.azure.cn
| [Azure (Německo)](https://azure.microsoft.com/overview/clouds/germany/) | *. metadata.microsoftazure.de

> [!NOTE]
> Certifikáty nemusí mít přesnou shodu `metadata.azure.com` pro veřejný cloud. Z tohoto důvodu by ověřování certifikace mělo umožňovat běžný název z jakékoli `.metadata.azure.com` subdomény.

V případech, kdy se zprostředkující certifikát nedá stáhnout kvůli omezením sítě během ověřování, můžete Zprostředkovaný certifikát připnout. Azure shrnuje certifikáty, což je standardní postupy PKI. Připnuté certifikáty musíte aktualizovat, když dojde k přechodu. Pokaždé, když se naplánuje změna aktualizace zprostředkujícího certifikátu, blog Azure se aktualizuje a zákazníci Azure se budou informovat. 

Zprostředkující certifikáty najdete v [úložišti PKI](https://www.microsoft.com/pki/mscorp/cps/default.htm). Zprostředkující certifikáty pro jednotlivé oblasti se můžou lišit.

> [!NOTE]
> Zprostředkující certifikát pro Azure Čína 21Vianet bude z globální kořenové certifikační autority DigiCert místo Baltimore.
Pokud jste připnuli zprostředkující certifikáty pro Azure Čína jako součást změny autority kořenového řetězu, musí se aktualizovat zprostředkující certifikáty.


## <a name="managed-identity"></a>Spravovaná identita

Na virtuálním počítači se dá povolit spravovaná identita přiřazená systémem. Virtuálnímu počítači můžete přiřadit taky jednu nebo více spravovaných identit přiřazených uživateli.
Pak můžete vyžádat tokeny pro spravované identity z IMDS. Tyto tokeny použijte k ověření u ostatních služeb Azure, například Azure Key Vault.

Podrobné pokyny k povolení této funkce najdete v tématu [získání přístupového tokenu](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="load-balancer-metadata"></a>Load Balancer metadata
Když umístíte virtuální počítač nebo instance sady virtuálních počítačů za Standard Load Balancer Azure, můžete pomocí IMDS načíst metadata týkající se nástroje pro vyrovnávání zatížení a instancí. Další informace najdete v tématu [načtení informací o nástroji pro vyrovnávání zatížení](../articles/load-balancer/instance-metadata-service-load-balancer.md).

## <a name="scheduled-events"></a>Naplánované události
Stav naplánovaných událostí můžete získat pomocí IMDS. Pak může uživatel zadat sadu akcí, které se mají spustit na těchto událostech. Další informace najdete v tématu [naplánované události pro systém Linux](../articles/virtual-machines/linux/scheduled-events.md) nebo [naplánované události pro systém Windows](../articles/virtual-machines/windows/scheduled-events.md).


## <a name="sample-code-in-different-languages"></a>Ukázkový kód v různých jazycích

V následující tabulce jsou uvedeny ukázky volání IMDS pomocí různých jazyků uvnitř virtuálního počítače:

| Jazyk | Příklad |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Přejít | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>Chyby a ladění

Pokud se nenašel datový prvek nebo dojde k chybnému požadavku, Instance Metadata Service vrátí standardní chyby protokolu HTTP. Například:

| Stavový kód HTTP | Důvod |
|------------------|--------|
| `200 OK` | Požadavek byl úspěšný.
| `400 Bad Request` | `Metadata: true` `format=json` Při dotazování na uzel typu list chybí záhlaví nebo chybějící parametr.
| `404 Not Found` | Požadovaný element neexistuje.
| `405 Method Not Allowed` | Na koncovém bodu není podporována metoda HTTP (příkaz).
| `410 Gone` | Opakovat po uplynutí určité doby na maximum 70 sekund
| `429 Too Many Requests` | Překročily se [limity rychlosti](#rate-limiting) rozhraní API.
| `500 Service Error` | Zkusit znovu za chvíli

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

- Zobrazuje se chyba `400 Bad Request, Required metadata header not specified` . Co to znamená?
  - IMDS vyžaduje, aby se hlavička `Metadata: true` předala v žádosti. Předání této hlavičky v volání REST umožňuje přístup k IMDS.

- Proč mi nezískávám výpočetní informace pro svůj virtuální počítač?
  - V současné době IMDS podporuje pouze instance vytvořené pomocí Azure Resource Manager.

- V tuto chvíli jsem virtuální počítač vytvořil před Azure Resource Manager. Proč se mi nezobrazuje informace o metadatech COMPUTE?
  - Pokud jste virtuální počítač vytvořili po září 2016, přidejte [značku](../articles/azure-resource-manager/management/tag-resources.md) , která začne zobrazovat výpočetní metadata. Pokud jste virtuální počítač vytvořili před září 2016, přidejte nebo odeberte rozšíření nebo datové disky do instance virtuálního počítače, aby se metadata aktualizovala.

- Jsou uživatelská data stejná jako vlastní data?
  - Uživatelská data nabízí podobnou funkci pro vlastní data, což vám umožní předat do instance virtuálního počítače vlastní metadata. Rozdílem je, že data uživatelů se načítají prostřednictvím IMDS a jsou trvalá během celé životnosti instance virtuálního počítače. Existující funkce vlastních dat bude i nadále fungovat, jak je popsáno v [tomto článku](https://docs.microsoft.com/azure/virtual-machines/custom-data). Vlastní data ale můžete načíst jenom prostřednictvím složky místní systém, ne přes IMDS.

- Proč se mi nezobrazují všechna data naplněná z nové verze?
  - Pokud jste virtuální počítač vytvořili po září 2016, přidejte [značku](../articles/azure-resource-manager/management/tag-resources.md) , která začne zobrazovat výpočetní metadata. Pokud jste virtuální počítač vytvořili před září 2016, přidejte nebo odeberte rozšíření nebo datové disky do instance virtuálního počítače, aby se metadata aktualizovala.

- Proč se mi zobrazuje chyba `500 Internal Server Error` `410 Resource Gone` ?
  - Opakujte požadavek. Další informace najdete v tématu [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults). Pokud potíže potrvají, vytvořte v Azure Portal pro virtuální počítač problém podpory.

- Budou to fungovat pro instance sady škálování virtuálních počítačů?
  - Ano, IMDS je k dispozici pro instance sady škálování virtuálních počítačů.

- Aktualizoval (a) jsem moje značky ve službě Virtual Machine Scale Sets, ale nezobrazují se v instancích (na rozdíl od virtuálních počítačů s jednou instancí). Nedaří se mi něco?
  - V současné době se pro sady škálování virtuálních počítačů zobrazují pouze virtuální počítače na restartování, obnovení bitové kopie nebo změna disku na instanci.

- Proč se mi v podrobnostech nezobrazuje informace o SKU pro svůj virtuální počítač `instance/compute` ?
  - Pro vlastní image vytvořené z Azure Marketplace platforma Azure neuchovává informace o SKU pro vlastní image a podrobnosti pro všechny virtuální počítače vytvořené z vlastní image. Jedná se o návrh, a proto se v podrobnostech o virtuálním počítači nezobrazuje `instance/compute` .

- Proč vypršel časový limit žádosti o mé volání služby?
  - Volání metadat je nutné provést z primární IP adresy přiřazené k primární síťové kartě virtuálního počítače. Pokud jste změnili své trasy, musí existovat trasa pro adresu 169.254.169.254/32 v místní směrovací tabulce vašeho virtuálního počítače.

    ### <a name="windows"></a>[Windows](#tab/windows/)

    1. Vypsat místní směrovací tabulku a vyhledat položku IMDS Například:
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
    1. Potvrďte, že rozhraní odpovídá primární síťové kartě virtuálního počítače a primární IP adrese. Primární síťovou kartu a IP adresu najdete tak, že si prohlížíte síťovou konfiguraci v Azure Portal, nebo ji vyhledáte pomocí Azure CLI. Všimněte si privátních IP adres (a adresy MAC, pokud používáte rozhraní příkazového řádku). Tady je příklad rozhraní příkazového řádku PowerShellu:
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

    ### <a name="linux"></a>[Linux](#tab/linux/)

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
    1. Potvrďte, že rozhraní odpovídá primární síťové kartě virtuálního počítače a primární IP adrese. Primární síťovou kartu a IP adresu najdete tak, že si prohlížíte síťovou konfiguraci v Azure Portal, nebo ji vyhledáte pomocí Azure CLI. Všimněte si privátních IP adres (a adresy MAC, pokud používáte rozhraní příkazového řádku). Tady je příklad rozhraní příkazového řádku PowerShellu:
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

    ---

- Clustering s podporou převzetí služeb při selhání ve Windows serveru
  - Při dotazování na IMDS s clusteringem s podporou převzetí služeb při selhání je někdy potřeba přidat trasu do směrovací tabulky. Jak na to:

    1. Otevřete příkazový řádek s oprávněními správce.

    1. Spusťte následující příkaz a poznamenejte si adresu rozhraní pro cílovou síť ( `0.0.0.0` ) v tabulce směrování IPv4.

    ```bat
    route print
    ```

    > [!NOTE]
    > Následující příklad výstupu je z virtuálního počítače s Windows serverem s povoleným clusterem s podporou převzetí služeb při selhání. Pro zjednodušení výstup obsahuje jenom tabulku směrování IPv4.

    ```
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

## <a name="support"></a>Podpora

Pokud nemůžete získat odpověď na metadata po několika pokusech, můžete vytvořit problém podpory v Azure Portal.

## <a name="product-feedback"></a>Zpětná vazba k produktu

Svůj názor na produkt a nápady můžete poskytnout kanálu pro zpětnou vazbu uživatelů v části Virtual Machines > Instance Metadata Service [tady](https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627) .

## <a name="next-steps"></a>Další kroky

- [Získání přístupového tokenu pro virtuální počítač](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

- [Naplánované události pro Linux](../articles/virtual-machines/linux/scheduled-events.md)

- [Naplánované události pro Windows](../articles/virtual-machines/windows/scheduled-events.md)
