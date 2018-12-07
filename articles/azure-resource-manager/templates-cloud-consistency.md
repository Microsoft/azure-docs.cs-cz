---
title: Šablony Azure Resource Manageru pro zajištění konzistence cloud | Dokumentace Microsoftu
description: Vývoj šablon Azure Resource Manageru pro zajištění konzistence cloudu. Vytvořit nový nebo aktualizace existujících šablon pro Azure Stack.
services: azure-resource-manager
documentationcenter: na
author: marcvaneijk
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/05/2018
ms.author: mavane
ms.openlocfilehash: fc1ca468545dc3a715fb11dbec459e14e60ade5b
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53017769"
---
# <a name="develop-azure-resource-manager-templates-for-cloud-consistency"></a>Vývoj šablon Azure Resource Manageru pro zajištění konzistence cloudu

Klíčovou výhodou Azure je konzistence. Investic do vývoje pro jedno umístění jsou opakovaně použitelné v jiném. Šablonu je vaše nasazení konzistentní a opakovatelné v prostředích, včetně globální Azure, Azure suverénních cloudech a Azure Stack. Znovupoužití šablony napříč cloudy, ale musíte vzít v úvahu závislosti specifické pro cloud, protože tato příručka vysvětluje.

Microsoft nabízí inteligentní a připravená pro podnikové cloudové služby v mnoha umístěních, včetně:

* Globální platformu Azure podporuje rozšiřující se síť Datacenter spravovaných společností Microsoft v oblastech po celém světě.
* Izolované suverénních cloudech, jako je Azure Germany, Azure Government a Azure China (systém Azure provozovaný společností 21Vianet). Suverénní cloudy poskytnout konzistentní platformu s většinou stejné skvělé funkce, které mají přístup ke globální zákazníky Azure.
* Azure Stack, hybridní Cloudová platforma, která vám umožní poskytovat služby Azure z datového centra vaší organizace. Podniky mohou nastavení služby Azure Stack v jejich vlastních datových center nebo využívání služeb Azure od poskytovatelů služeb s Azure Stack v jejich zařízeních (někdy označované jako hostované oblasti).

V jádru všechny tyto cloudy Azure Resource Manager poskytuje rozhraní API, které umožňuje širokou škálu uživatelská rozhraní ke komunikaci s platformou Azure. Toto rozhraní API poskytuje výkonné funkce infrastruktury jako kódu. Libovolný typ prostředku, který je k dispozici na cloudovou platformu Azure je možné nasadit a nakonfigurovat pomocí Azure Resource Manageru. S jedinou šablonou můžete nasadit a nakonfigurovat kompletní aplikace k provozní koncového stavu.

![Prostředí Azure](./media/templates-cloud-consistency/environments.png)

Konzistence globální Azure a suverénních cloudech hostovaných cloudů a cloudu ve vašem datovém centru vám těžit z Azure Resource Manageru. Můžete využít vaše investice do vývoje napříč tyto cloudy při nastavování založené na šablonách resource nasazení a konfigurace.

Ale i když globální suverénních, prostředí a hybridní cloudy nabízí služby konzistentní, ne všechny cloudy jsou stejné. V důsledku toho můžete vytvořit šablonu se závislostmi na funkce k dispozici pouze v konkrétním cloudu.

Zbývající část tato příručka popisuje oblasti, které je třeba zvážit při plánování pro vývoj nových nebo aktualizaci existující šablony Azure Resource Manageru pro službu Azure Stack. Obecně platí vaše kontrolní seznam by měl obsahovat následující položky:

* Ověřte, že funkce, koncové body, služeb a dalších prostředků v šabloně jsou k dispozici v umístění cílů nasazení.
* V dostupná umístění, ve budete mít přístup napříč cloudy, Store program vnořených šablon a artefaktů konfigurace.
* Použijte dynamické odkazy místo pevného kódování odkazy a prvky.
* Ujistěte se, že parametry šablony, které používáte fungovat v cloudech cílové.
* Ověřte, že jsou k dispozici vlastnosti specifické podle prostředků cílového cloudy.

Úvod do šablon Azure Resource Manageru, najdete v článku [nasazení šablony](resource-group-overview.md#template-deployment).

## <a name="ensure-template-functions-work"></a>Ujistěte se, že šablony funkce

Základní syntaxe šablony Resource Manageru je JSON. Šablony používat nadstavbu JSON rozšíření syntaxe s výrazy a funkce. Procesor jazyka šablony je často aktualizována o podporu funkcí další šablony. Podrobné vysvětlení funkcí k dispozici šablony najdete v tématu [funkce šablon Azure Resource Manageru](resource-group-template-functions.md).

Nové funkce šablony, které přináší do Azure Resource Manageru nejsou okamžitě dostupné v suverénních cloudech nebo Azure Stack. Pokud chcete nasadit šablonu úspěšně, musí být všechny funkce odkazované v šabloně dostupné na cílový cloud. 

Možnosti služby Azure Resource Manageru vždy představíme na global Azure nejprve. Chcete-li ověřit, zda nově zavedená šablony funkce jsou také k dispozici ve službě Azure Stack můžete použít následující příkaz powershellu: 

1. Ujistěte se, klon úložiště GitHub: [ https://github.com/marcvaneijk/arm-template-functions ](https://github.com/marcvaneijk/arm-template-functions).

1. Jakmile máte místní klon úložiště, připojte se do cílového počítače Azure Resource Manageru pomocí Powershellu.

1. Importujte modul psm1 a spusťte rutinu Test-AzureRmTemplateFunctions:

  ```powershell
  # Import the module
  Import-module <path to local clone>\AzureRmTemplateFunctions.psm1

  # Execute the Test-AzureRmTemplateFunctions cmdlet
  Test-AzureRmTemplateFunctions -path <path to local clone>
  ```

Skript nasadí více, minimalizaci šablon, každá obsahuje pouze jedinečné šablony funkce. Výstup skriptu sestavy podporované a není k dispozici šablona funkce.

## <a name="working-with-linked-artifacts"></a>Práce s propojených artefaktů

Šablony mohou obsahovat odkazy na propojených artefaktů a obsahovat nasazení prostředku, který odkazuje na jinou šablonu. Propojené šablony (také označované jako vnořené šablony) se načítají pomocí Správce prostředků v době běhu. Šablony mohou také obsahovat odkazy na artefakty pro rozšíření virtuálních počítačů (VM). Tyto artefakty jsou načítána pro rozšíření virtuálních počítačů běžících v rámci virtuálních počítačů pro konfiguraci rozšíření virtuálního počítače během nasazování šablony. 

Následující části popisují důležité informace týkající se cloudu konzistenci při vývoji šablon, které obsahují artefakty, které jsou mimo hlavní nasazení šablony.

### <a name="use-nested-templates-across-regions"></a>Použití vnořené šablony napříč oblastmi

Šablony lze rozložit na malých, opakovaně použitelných šablon, z nichž každá má konkrétní účel a lze opětovně použít napříč scénáři nasazení. Ke spuštění nasazení, zadáte s jednou šablonou označované jako hlavní nebo hlavní šablony. Určuje prostředky, které nasadíte, třeba virtuálních sítí, virtuální počítače a webové aplikace. Hlavní šablony může obsahovat také odkaz na jinou šablonu, což znamená, že je možné vnořovat šablony. Vnořené šablony, mohou obsahovat odkazy na další šablony. Je možné vnořovat až pět úrovní do hloubky.

Následující kód ukazuje, jak templateLink parametr odkazuje na vnořené šablony:

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Azure Resource Manager vyhodnotí jako hlavní šablony za běhu a načte a vyhodnocuje každý vnořené šablony. Všechny vnořené šablony se načítají, sloučí šablony a další zpracování je zahájeno.

### <a name="make-linked-templates-accessible-across-clouds"></a>Zpřístupněte propojenými šablonami napříč cloudy

Vezměte v úvahu kde a jak ukládat všechny propojené šablony, můžete použít. Za běhu, načte Azure Resource Manageru a proto vyžaduje přímý přístup k – případný připojený šablony. Běžnou praxí je použití Githubu pro ukládání vnořených šablon. Úložiště GitHub může obsahovat soubory, které jsou přístupné veřejně prostřednictvím adresy URL. Přestože tento postup funguje dobře pro veřejný cloud a suverénních cloudech, může být umístěn v podnikové síti, nebo ve vzdáleném odpojeném umístění, bez jakékoli odchozí internetový přístup prostředí Azure Stack. V těchto případech by selhat Azure Resource Manageru pro načtení vnořené šablony. 

Doporučeno pro nasazení cloudu je pro uložení do umístění, které je přístupné pro cílový cloud propojenými šablonami. V ideálním případě jsou všechny artefakty nasazení udržuje v a nasazení z kanálu průběžné integrace a vývoj (CI/CD). Alternativně můžete uložit vnořené šablony v kontejneru úložiště objektů blob, ze kterého můžete Azure Resource Manageru je načíst. 

Protože úložiště objektů blob v každém z nich používá koncový bod různých plně kvalifikovaný název domény (FQDN), nakonfigurujte šablonu s umístěním propojenými šablonami se dvěma parametry. Parametry můžete při nasazení přijímají vstup uživatele. Šablony jsou obvykle vytvořené a sdílí více uživatelů, takže osvědčeným postupem je použít standardní název pro tyto parametry. Zásady vytváření názvů pomohou vytvořit více opakovaně použitelné šablony napříč oblastmi, cloudů a autoři.

V následujícím kódu `_artifactsLocation` se používá tak, aby odkazoval na jediném místě, který obsahuje všechny artefakty související s implementací. Všimněte si, že výchozí hodnota je k dispozici. V době nasazení, pokud není zadaná žádná vstupní hodnota pro `_artifactsLocation`, použije se výchozí hodnota. `_artifactsLocationSasToken` Se používá jako vstup pro `sasToken`. Výchozí hodnota by měla být prázdný řetězec pro scénáře, ve kterém `_artifactsLocation` není zabezpečen – například veřejného úložiště GitHub.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

V šabloně, odkazy jsou generovány kombinací základního identifikátoru URI (z `_artifactsLocation` parametr) s artefaktů relativní cestu a `_artifactsLocationSasToken`. Následující kód ukazuje, jak zadat odkaz na vnořené šablony pomocí funkce šablony uri:

```json
"resources": [
  {
    "name": "shared",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Pomocí tohoto přístupu, výchozí hodnota `_artifactsLocation` parametr se používá. Pokud potřebujete propojenými šablonami se má načíst z jiného umístění, vstupní parametr je možné přepsat výchozí hodnotu v době nasazení, není nutná žádná změna k samotné šablony.

### <a name="use-artifactslocation-instead-of-hardcoding-links"></a>_ArtifactsLocation nahrazujícím hardcoding odkazy

Kromě se používají pro vnořené šablony, adresy URL v `_artifactsLocation` parametr se používá jako základ pro všechna související artefakty šablonu nasazení. Některá rozšíření virtuálního počítače zahrnout odkaz na skript uložené mimo šablonu. Pro tato rozšíření by měl pevně odkazy. Rozšíření vlastních skriptů a prostředí PowerShell DSC může například odkazu na externí skript na Githubu, jak je znázorněno: 

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

Hardcoding odkazy na skript potenciálně zabraňuje šablony úspěšně nasazení do jiného umístění. Během konfigurace prostředku virtuálního počítače agenta virtuálního počítače spuštěného ve virtuálním počítači zahájí stahování všech skriptů v rozšíření virtuálního počítače a pak uloží skripty na místním disku Virtuálního počítače. Tento přístup funkcí jako odkazy vnořené šablony dřív vysvětlené v části "Použití vnořené šablony napříč oblastmi".

Resource Manager načte vnořené šablony za běhu. Pro rozšíření virtuálních počítačů načítání všechny externí artefakty se provádí pomocí agenta virtuálního počítače. Kromě různých iniciátoru artifact načítání řešení v definici šablony je stejný. Použijte parametr _artifactsLocation s výchozí hodnotou základní cesta kde jsou uložené všechny artefakty (včetně skripty rozšíření virtuálního počítače) a `_artifactsLocationSasToken` parametr pro vstup pro sasToken.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

K sestavení kompletních absolutní identifikátor URI artefaktu, je použití funkce šablony uri namísto funkce šablony concat upřednostňovanou metodou. Nahrazením pevně zakódované odkazy na skripty v rozšíření virtuálního počítače pomocí šablony funkce identifikátoru uri je tato funkce v šabloně nakonfigurován pro cloud konzistence.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

S tímto přístupem všechny artefakty nasazení, včetně konfigurace, skripty, mohou být uloženy ve stejném umístění se samotné šablony. Chcete-li změnit umístění všechny odkazy, stačí zadat jinou základní adresu URL pro _artifactsLocation parametry.

## <a name="factor-in-differing-regional-capabilities"></a>Zvážit lišící se místní funkce

Agilní vývoj a nepřetržitý tok aktualizace a zavedení do Azure, nové služby [oblastí se může lišit](https://azure.microsoft.com/regions/services/) ve skupině dostupnosti služeb nebo aktualizace. Po důkladné interní testování nových služeb nebo aktualizací do existujících služeb obvykle zavedení do malé cílovou skupinu zákazníků účasti v programu ověření. Po ověření úspěšného zákazníka služby nebo aktualizace jsou k dispozici v podmnožině oblastí s Azure, pak zavedeny do více oblastí, nasazeny v suverénních cloudech a potenciálně k dispozici také zákazníkům Azure Stack.

Znalost, oblastí Azure a cloudy mohou lišit v jejich dostupných služeb, můžete rozhodnutí některé proaktivní o vaší šablony. Je dobrým začátkem prozkoumáním dostupných poskytovatelích prostředků pro cloud. Poskytovatel prostředků říká sadu prostředků a operací, které jsou k dispozici pro službu Azure.

Šablona nasadí a nakonfiguruje prostředky. Typ prostředku je poskytované poskytovatelem prostředků. Poskytovateli prostředků compute (Microsoft.Compute), například obsahuje více typů prostředků, jako je například virtuálních počítačů a availabilitySets. Každý poskytovatel prostředků nabízí rozhraní API do Azure Resource Manageru určené kontrakt běžné umožňuje konzistentní vzhledem k aplikacím, jednotné prostředí pro vytváření obsahu napříč všechny poskytovatele prostředků. Poskytovatele prostředků, která je dostupná v globální Azure, ale nemusí být dostupné v suverénních cloudů nebo určitá oblast Azure Stack.

![Poskytovatelé prostředků](./media/templates-cloud-consistency/resource-providers.png) 

Pokud chcete ověřit poskytovatelé prostředků, které jsou k dispozici v dané cloudové, spusťte následující skript v rozhraní příkazového řádku Azure ([rozhraní příkazového řádku](/cli/azure/install-azure-cli)):

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Také vám pomůže následující rutiny Powershellu najdete v dostupných poskytovatelů prostředků:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Ověřit verzi všechny typy prostředků

Sada vlastností je společné pro všechny typy prostředků, ale každý prostředek má také svůj vlastní specifické vlastnosti. Nové funkce a související vlastnosti jsou přidány k existujícím typům prostředků v některých případech pomocí nové verze rozhraní API. Prostředek v šabloně má svůj vlastní vlastnost verze API - `apiVersion`. Tato Správa verzí zajišťuje, existující konfigurace prostředků v šabloně není ovlivněn změny na platformě.

Zavedení do existující typy prostředků v Azure globálním nových verzích rozhraní API nemusí být hned dostupné ve všech oblastech, suverénní cloudy nebo Azure Stack. Chcete-li zobrazit seznam dostupných poskytovatelů prostředků, typy prostředků a verze rozhraní API pro cloud, můžete použít Průzkumníka prostředků na webu Azure portal. Vyhledávání pro Průzkumníka prostředků v nabídce všechny služby. Rozbalte uzel poskytovatelé v Průzkumníku prostředků k vrácení všech dostupných poskytovatelích prostředků, jejich typy prostředků a verze rozhraní API v tomto cloudu.

Pro zobrazení seznamu k dispozici verze rozhraní API pro všechny typy prostředků v daném cloudu v Azure CLI, spusťte následující skript:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Můžete také použít následující rutinu Powershellu:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Odkazovat na umístění prostředků s parametrem

Šablonu je vždy nasazena do skupiny prostředků, které se nacházejí v oblasti. Kromě samotné nasazení každého prostředku v šabloně má také vlastnost umístění, která se používá k určení oblasti, kterou chcete nasadit v. K vývoji šablony pro zajištění konzistence cloudu, musíte dynamické způsob, jak odkazovat na umístění prostředků, protože každé služby Azure Stack může obsahovat názvy jedinečné umístění. Obvykle jsou nasazené prostředky ve stejné oblasti jako skupina prostředků, ale pro zajištění podpory scénářů, jako je dostupnost aplikace mezi různými oblastmi, může být užitečné k rozšíření prostředků napříč oblastmi.

I když můžete pevně nastavit názvy oblastí při zadání vlastnosti prostředku v šabloně, tento přístup nezaručuje, že šablona je možné nasadit do jiných prostředí Azure Stack, protože název oblasti se pravděpodobně neexistuje existuje.

Tak, aby vyhovovaly různých oblastech, přidejte umístění služby vstupní parametr šablony s výchozí hodnotou. Pokud není zadána žádná hodnota během nasazení bude použita výchozí hodnota. 

Funkce šablony `[resourceGroup()]` vrátí objekt, který obsahuje následující dvojice klíč/hodnota:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Pomocí odkazu na klíč umístění objektu v defaultValue vstupní parametr, Azure Resource Manageru, za běhu, nahradí `[resourceGroup().location]` funkce šablony s názvem umístění skupiny prostředků pro nasazení šablony.

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "name": "storageaccount1",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "location": "[parameters('location')]",
    ...
```

Pomocí této šablony funkce můžete nasadit šablonu do jakéhokoli cloudu bez předem i znalost názvů oblastí. Umístění pro určitý prostředek v šabloně se navíc může lišit od umístění skupiny prostředků. V takovém případě můžete nakonfigurovat pomocí dalších vstupních parametrů pro tento konkrétní prostředek, zatímco jiné prostředky ve stejné šabloně stále používají vstupní parametr počáteční umístění.

### <a name="track-versions-using-api-profiles"></a>Sledujte verze pomocí profilů rozhraní API

Může být velmi obtížné udržovat přehled o všech dostupných poskytovatelů prostředků a související verze rozhraní API, které se nacházejí ve službě Azure Stack. Například v době psaní, nejnovější verze rozhraní API pro **Microsoft.Compute/availabilitySets** v Azure je `2018-04-01`, zatímco je k dispozici verze rozhraní API, která je společná pro Azure a Azure Stack `2016-03-30`. Verze společného rozhraní API pro **Microsoft.Storage/storageAccounts** sdílen všech umístěních Azure a Azure Stack je `2016-01-01`, zatímco nejnovější verze rozhraní API v Azure je `2018-02-01`.

Z tohoto důvodu Resource Manageru představil nový koncept profily rozhraní API do šablon. Bez profilů rozhraní API, se nakonfigurují každého prostředku v šabloně `apiVersion` element, který popisuje verzi rozhraní API pro tento konkrétní prostředek.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
            "apiVersion": "2016-03-30",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

Verze rozhraní API profilu funguje jako alias pro jednu verzi rozhraní API na typ prostředku, které jsou společné pro Azure a Azure Stack. Místo určení verze rozhraní API pro každý prostředek v šabloně, zadejte pouze profilu verze rozhraní API v nový kořenový prvek s názvem `apiProfile` a vynechat možnost `apiVersion` – element pro jednotlivé prostředky.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

Profil rozhraní API se zajistí, že verze rozhraní API jsou k dispozici v lokalitách, takže není potřeba ručně ověřit apiVersions, které jsou k dispozici v konkrétním umístění. K zajištění, že verze rozhraní API odkazuje váš profil rozhraní API jsou k dispozici v prostředí Azure Stack, operátoři Azure stacku musí zachovat aktuální řešení na základě zásad pro podporu. Pokud systém je zastaralý, bude považován za nesplňuje předpisy, a musí se aktualizovat prostředí víc než šest měsíců.

Profil rozhraní API není požadovaný element v šabloně. I když přidáte element, pouze použije se pro prostředky, které ne `apiVersion` je zadán. Tento prvek umožňuje postupné změny, ale nevyžaduje žádné změny existujících šablon.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>Kontrola odkazů na koncový bod:

Prostředky mohou mít odkazy na další služby na platformě. Veřejnou IP adresu můžete například mít veřejný název DNS přiřadit k ní. Veřejný cloud, suverénních cloudech a řešení pro Azure Stack mají své vlastní obory názvů odlišné koncový bod. Ve většině případů prostředku vyžaduje pouze předpony jako vstup v šabloně. Azure Resource Manageru za běhu, přidá hodnotu koncového bodu na ni. Některé hodnoty koncového bodu je potřeba explicitně zadat v šabloně. 

> [!NOTE]
> K vývoji šablony pro cloud konzistence, není pevně koncový bod obory názvů.

Následující dva příklady jsou běžné obory názvů koncový bod, který je potřeba explicitně zadat při vytváření prostředku:

* Účty Storage (blob, queue, table a file)
* Připojovací řetězce pro databáze a mezipaměti Azure Redis

Koncový bod obory názvů lze také ve výstupu šablony jako informace pro uživatele po dokončení nasazení. Následují běžné příklady:

* Účty Storage (blob, queue, table a file)
* Připojovací řetězce (MySql, systému SQL Server, SQLAzure, vlastní, NotificationHub, služby Service Bus, centra událostí, ApiHub, DocDb, mezipaměti RedisCache, PostgreSQL)
* Traffic Manager
* Popisek_názvu_domény veřejné IP adresy
* Cloud Services

Obecně se vyhýbejte koncové body pevně zakódované v šabloně. Osvědčeným postupem je použití funkce šablon odkaz k načtení dynamicky koncových bodů. Pro příklad, koncový bod nejčastěji pevně zakódované je obor názvů koncový bod pro účty úložiště. Každý účet úložiště má jedinečný název FQDN, která je vytvořena zřetězením názvu účtu úložiště, s oborem názvů koncový bod. Účet úložiště objektů blob s názvem mystorageaccount1 výsledky v různých plně kvalifikovaných názvů domén v závislosti na cloudu:

* **mystorageaccount1.BLOB.Core.Windows.NET** při vytváření na globální cloud Azure.
* **mystorageaccount1.BLOB.Core.chinacloudapi.CN** při v cloud Azure China.

Následující funkce šablony odkaz načte obor názvů koncového bodu z poskytovatele prostředků úložiště:

```json
"diskUri":"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Tak, že nahradíte hodnotu pevně zakódované koncový bod účtu úložiště se `reference` funkce šablony, můžete použít stejnou šablonu k nasazení do různých prostředí úspěšně bez provedení změn v referenci koncového bodu.

### <a name="refer-to-existing-resources-by-unique-id"></a>Odkazovat na existující prostředky podle jedinečné ID.

Můžete také odkazovat na existující prostředek z stejného nebo jiného prostředku, skupiny a v rámci stejného předplatného nebo jiné předplatné, ve stejném tenantovi ve stejném cloudu. Pokud chcete načíst vlastnosti prostředku, musíte použít jedinečný identifikátor pro vlastní prostředek. `resourceId` Funkce šablony načte jedinečné ID prostředku, jako je SQL Server jako ukazuje následující kód: 

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Pak můžete použít `resourceId` funkci uvnitř `reference` funkce šablony se načíst vlastnosti databáze. Vrácený objekt obsahuje `fullyQualifiedDomainName` vlastnost, která obsahuje hodnotu úplné koncový bod. Tato hodnota je načten v době běhu a poskytuje obor názvů specifických pro prostředí koncového bodu cloudu. K definování připojovacího řetězce bez hardcoding obor názvů koncový bod, mohou odkazovat na vlastnost vrácený objekt přímo v připojovacím řetězci, jak je znázorněno:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Vezměte v úvahu vlastností prostředku

Konkrétní prostředky v rámci prostředí Azure Stack mají jedinečné vlastnosti, které je třeba zvážit v šabloně.

### <a name="ensure-vm-images-are-available"></a>Ujistěte se, že jsou dostupné Image virtuálních počítačů

Azure poskytuje bohatou výběr imagí virtuálních počítačů. Tyto Image jsou vytvořeny a připravený pro nasazení Microsoftem a partnery. Obrázky tvoří základ pro virtuální počítače na platformě. Ale šablonu konzistentní vzhledem k aplikacím cloudu by měla odkazovat na dostupné parametry pouze – zejména vydavatele, nabídky a skladové položky imagí virtuálních počítačů, která je k dispozici pro globální Azure, Azure suverénních cloudech nebo řešení Azure Stack.

K načtení seznamu dostupných imagí virtuálních počítačů v umístění, spusťte následující příkaz rozhraní příkazového řádku Azure:

```azurecli-interactive
az vm image list -all
```

Můžete načíst stejného seznamu pomocí rutiny prostředí Azure PowerShell [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) a zadejte umístění, které chcete, aby se `-Location` parametru. Příklad:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRMVMImage
```

Tento příkaz trvá několik minut, než se vrátí všechny dostupné Image v oblasti západní Evropa globální cloud Azure.

Pokud jste provedli tyto Image virtuálního počítače k dispozici ke službě Azure Stack, bude využívat veškeré dostupné úložiště. Přizpůsobí i nejmenší jednotka škálování služby Azure Stack vám umožní vybrat obrázky, které chcete přidat do prostředí.

Následující příklad kódu ukazuje konzistentní přístup k odkazování na vydavatele, nabídky a skladové položky parametrů v šablonách Azure Resource Manageru:

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>Zkontrolujte místní velikosti virtuálních počítačů

K vývoji šablony pro zajištění konzistence cloudu, budete muset Ujistěte se, že požadovanou velikost virtuálního počítače je k dispozici ve všech cílových prostředí. Velikosti virtuálních počítačů jsou seskupení výkonové charakteristiky a možnosti. Některé velikosti virtuálních počítačů, závisí na hardwaru, na které poběží virtuálním počítači. Například pokud chcete nasazení virtuálního počítače GPU optimalizované, hardware, na kterém běží hypervisor vyžaduje hardwarové GPU.

Když Microsoft zavádí novou velikost virtuálního počítače, který má určité závislosti na hardwaru, velikost virtuálního počítače je obvykle k dispozici nejprve v malou podmnožinu oblasti v cloudu Azure. Později je k dispozici do jiných oblastí a cloudy. Pokud chcete mít jistotu, že velikost virtuálního počítače existuje v každém z nich, kterého nasadíte do, můžete načíst dostupné velikosti pomocí následujícího příkazu rozhraní příkazového řádku Azure:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Pro prostředí Azure PowerShell použijte:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Úplný seznam dostupných služeb, naleznete v tématu [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Zkontrolujte použití služby Azure Managed Disks v Azure stacku

Spravované disky popisovač úložiště pro tenanta služby Azure. Místo explicitně vytvořit účet úložiště a zadáte identifikátor URI virtuálního pevného disku (VHD) můžete použít spravované disky implicitně provádět tyto akce při nasazení virtuálního počítače. Spravované disky zlepšují dostupnost tak, že všechny disky z virtuálních počítačů ve stejné skupině dostupnosti do jiného úložiště jednotek. Kromě toho existujícího VHD lze převést z úrovně Standard na Premium storage s výrazně méně výpadků.

I když jsou spravované disky na plán služby pro službu Azure Stack, se momentálně nepodporují. Dokud je neschválíte, vám umožní vytvářet konzistentní cloudové šablon pro Azure Stack explicitním zadáním virtuálních pevných disků pomocí `vhd` element v šabloně prostředku virtuálního počítače, jak je znázorněno:

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Naopak, můžete zadat konfiguraci spravovaných disků v šabloně, odeberte `vhd` element z konfigurace disku.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Stejné změny budou také použity [datové disky](../virtual-machines/windows/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Ověřte, zda rozšíření virtuálních počítačů jsou k dispozici ve službě Azure Stack

Je potřeba vzít v úvahu cloudu konzistence použití [rozšíření virtuálních počítačů](../virtual-machines/windows/extensions-features.md) ke konfiguraci prostředků uvnitř virtuálního počítače. Ne všechna rozšíření virtuálních počítačů jsou dostupné ve službě Azure Stack. Šablonu můžete určit zdroje vyhrazené k rozšíření virtuálního počítače, vytváření závislostí a podmínky v rámci šablony.

Například pokud chcete nakonfigurovat virtuální počítač s Microsoft SQL Server, rozšíření virtuálního počítače nakonfigurovat SQL Server jako součást nasazení šablony. Zvažte, co se stane, pokud šablona nasazení také obsahuje aplikační server nakonfigurovaný pro vytvoření databáze na virtuální počítač s SQL serverem. Kromě také pomocí rozšíření virtuálního počítače pro aplikační servery, můžete nakonfigurovat závislosti aplikačního serveru při úspěšném návratu prostředků rozšíření virtuálního počítače s SQL serverem. Tento přístup zajišťuje, že virtuální počítač s SQL serverem je nakonfigurované a dostupné aplikační server je nastaven na vytvoření databáze.

Deklarativní přístup šablony umožňuje definovat koncový stav prostředků a jejich závislosti mezi, zatímco platformu logiku potřebnou pro závislosti.

#### <a name="check-that-vm-extensions-are-available"></a>Zkontrolujte, zda rozšíření virtuálních počítačů jsou k dispozici

Existuje řada typů rozšíření virtuálního počítače. Při vytváření šablony pro cloud konzistence, ujistěte se, že abyste použili rozšíření, které jsou k dispozici ve všech oblastech šablona cílí.

Chcete-li načíst seznam rozšíření virtuálních počítačů, které jsou k dispozici pro konkrétní oblast (v tomto příkladu `myLocation`), spusťte následující příkaz rozhraní příkazového řádku Azure:

```azurecli-interactive
az vm extension image list --location myLocation
```

Můžete taky spustit rutinu prostředí Azure PowerShell [Get-AzureRmVmImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) rutiny a použít `-Location` k určení umístění image virtuálního počítače. Příklad:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Ujistěte se, že jsou k dispozici verze

Vzhledem k tomu, že rozšíření virtuálních počítačů jsou prostředky Resource Manageru první strany, mají své vlastní verze rozhraní API. Jak ukazuje následující kód je typ rozšíření virtuálního počítače vnořeného prostředku v poskytovateli prostředků Microsoft.Compute.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

Verze rozhraní API prostředku rozšíření virtuálního počítače musí být k dispozici ve všech umístěních, který chcete cílit pomocí šablony. Závislost umístění funguje jako poskytovatel prostředků verze rozhraní API dostupnost popsáno dříve v části "Ověření verze všem typům prostředků".

Chcete-li načíst seznam dostupných verzí rozhraní API pro rozšíření prostředku virtuálního počítače, použijte [Get-AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider) rutinu s **Microsoft.Compute** poskytovatele prostředků, jak je znázorněno:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Můžete také použít rozšíření virtuálních počítačů ve škálovacích sadách virtuálních počítačů. Platí stejné podmínky umístění. K vývoji šablony pro cloud konzistence, ujistěte se, že verze rozhraní API jsou k dispozici ve všech umístěních, které plánujete nasazení. K načtení verze rozhraní API prostředku rozšíření virtuálního počítače pro škálovací sady pomocí stejné rutiny jako před ale zadat virtuálního počítače škálovací sady typ prostředku, jak je znázorněno:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Každé konkrétní rozšíření je také systémovou správou verzí. Tato verze je zobrazena ve `typeHandlerVersion` vlastnost rozšíření virtuálního počítače. Ujistěte se, že verze určená v `typeHandlerVersion` element ze šablony virtuálního počítače rozšíření jsou k dispozici v umístění, kde ji plánujete nasadit šablonu. Například následující kód určuje verzi 1.7:

```json
{
    "name": "MyCustomScriptExtension",
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...   
```

Chcete-li načíst seznam dostupných verzí pro konkrétní rozšíření virtuálního počítače, použijte [Get-AzureRmVMExtensionImage](/powershell/module/azurerm.compute/get-azurermvmextensionimage) rutiny. Následující příklad načte dostupných verzí pro rozšíření PowerShell DSC (Desired State Configuration) virtuálního počítače z **myLocation**:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Chcete-li získat seznam vydavatelů, použijte [Get-AzureRmVmImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) příkazu. Chcete-li požádat o typ, použijte [Get-AzureRmVMExtensionImageType](/powershell/module/azurerm.compute/get-azurermvmextensionimagetype) commend.

## <a name="tips-for-testing-and-automation"></a>Tipy pro automatizaci a testování

Je obtížné udržovat přehled o všech souvisejících nastavení, možnosti a omezení při vytváření šablony. Běžným přístupem je pro vývoj a testování šablony proti jedna Cloudová před cílí v jiných umístěních. Ale dříve, které testy jsou prováděny v procesu vytváření, méně Poradce při potížích a přepisování kódu, které váš vývojový tým bude muset udělat. Nasazení, která selžou z důvodu závislosti umístění může být časově náročná řešení potíží. Proto doporučujeme, abyste automatizované testování co nejdříve ve vývojovém cyklu. Nakonec budete potřebovat méně času vývoj a méně prostředků a konzistentní vzhledem k aplikacím cloudové artefakty se stanou ještě větší hodnotu.

Na následujícím obrázku je typickým příkladem proces vývoje pro tým pomocí integrovaného vývojového prostředí (IDE). V různých fázích na časové ose jsou spouštěny v různých testovacích typy. Zde dva vývojáři už pracují ve stejném řešení, ale tento scénář se vztahuje stejnou měrou na jednoho vývojáře nebo velkém týmu. Každý vývojář obvykle vytvoří místní kopii centrálním úložišti, povolení jednotlivá pracovat na místní kopie bez dopadu na ostatní, kdo může pracovat na stejné soubory.

![Pracovní postup](./media/templates-cloud-consistency/workflow.png) 

Vezměte v úvahu následující tipy pro automatizaci a testování:

* Ujistěte se, použijte testovacích nástrojů. Například Visual Studio Code a Visual Studio zahrnují technologie IntelliSense a dalších funkcí, které vám pomůžou ověřit své šablony.
* Ke zlepšení kvality kódu během vývoje na místní sběrnici IDE, proveďte analýzu statického kódu pomocí testů jednotek a integrační testy. 
* Pro ještě lepší možnosti během počáteční vývoje, testování částí a integrační testy by měl pouze zobrazit upozornění, pokud je nalezen problém a pokračujte s testy. Díky tomu můžete identifikovat problémy řešit a stanovení priorit pořadí změny, které jsou také označovány jako nasazení řízený testováním (TDD).
* Mějte na paměti, že některé testy lze provést bez připojení k Azure Resource Manageru. Jiné, jako je testování nasazení šablony vyžadují Resource Manageru k provedení určité akce, které nelze provést v režimu offline.
* Testování nasazení šablony pro ověření rozhraní API není roven skutečné nasazení. Navíc i v případě nasazení šablony z místního souboru, všechny odkazy na vnořené šablony v šabloně se načítají pomocí Správce prostředků přímo a artefakty odkazuje rozšíření virtuálních počítačů jsou načítána pro agenta virtuálního počítače spuštěné v nasazeném virtuálním počítači.

## <a name="next-steps"></a>Další postup

* [Aspekty šablon Azure Resource Manageru](../azure-stack/user/azure-stack-develop-templates.md)
* [Osvědčené postupy pro šablony Azure Resource Manageru](resource-group-authoring-templates.md)
