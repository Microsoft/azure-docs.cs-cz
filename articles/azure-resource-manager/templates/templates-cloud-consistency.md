---
title: Opakované použití šablon v cloudech
description: Vyvíjejte šablony Azure Resource Manageru, které fungují konzistentně pro různá cloudová prostředí. Vytvořte nové nebo aktualizujte existující šablony pro Azure Stack.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18
ms.openlocfilehash: c5095efef5d4bef44993bdd9cd52dbdef17378a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156102"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>Vývoj šablon ARM pro konzistenci cloudu

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Klíčovou výhodou Azure je konzistence. Investice do rozvoje pro jedno místo jsou opakovaně použitelné v jiném. Šablona Azure Resource Manager (ARM) umožňuje, aby vaše nasazení konzistentní a opakovatelné napříč prostředími, včetně globální Azure, Azure sovereign cloudy a Azure Stack. Chcete-li znovu použít šablony napříč cloudy, ale musíte zvážit závislosti specifické pro cloud, jak vysvětluje tato příručka.

Společnost Microsoft nabízí inteligentní cloudové služby připravené pro podniky na mnoha místech, včetně:

* Globální platforma Azure podporovaná rostoucí sítí datových center spravovaných Microsoftem v oblastech po celém světě.
* Izolované suverénní cloudy, jako je Azure Germany, Azure Government a Azure China 21Vianet. Suverénní cloudy poskytují konzistentní platformu s většinou stejných skvělých funkcí, ke kterým mají globální zákazníci Azure přístup.
* Azure Stack, hybridní cloudová platforma, která vám umožní poskytovat služby Azure z datového centra vaší organizace. Podniky můžou nastavit Azure Stack ve vlastních datových centrech nebo využívat služby Azure od poskytovatelů služeb, kteří ve svých zařízeních spouštějí Azure Stack (někdy označované jako hostované oblasti).

V jádru všech těchto cloudů Azure Resource Manager poskytuje rozhraní API, které umožňuje širokou škálu uživatelských rozhraní pro komunikaci s platformou Azure. Toto rozhraní API poskytuje výkonné možnosti infrastruktury jako kódu. Všechny typy prostředků, které jsou dostupné na cloudové platformě Azure, se dají nasadit a nakonfigurovat pomocí Azure Resource Manageru. Pomocí jediné šablony můžete nasadit a nakonfigurovat úplnou aplikaci do provozního koncového stavu.

![Prostředí Azure](./media/templates-cloud-consistency/environments.png)

Konzistence globálního Azure, suverénních cloudů, hostovaných cloudů a cloudu ve vašem datovém centru vám pomůže těžit ze Správce prostředků Azure. Při nastavování nasazení a konfigurace prostředků založených na šablonách můžete znovu použít investice do vývoje v těchto cloudech.

I když však globální, suverénní, hostované a hybridní cloudy poskytují konzistentní služby, ne všechny cloudy jsou identické. V důsledku toho můžete vytvořit šablonu se závislostmi na funkcích, které jsou k dispozici pouze v určitém cloudu.

Zbývající část této příručky popisuje oblasti, které je třeba zvážit při plánování vývoje nových nebo aktualizaci existujících šablon ARM pro Azure Stack. Obecně platí, že kontrolní seznam by měl obsahovat následující položky:

* Ověřte, zda jsou funkce, koncové body, služby a další prostředky v šabloně dostupné v cílových umístěních nasazení.
* Uklápěte vnořené šablony a konfigurační artefakty do přístupných umístění a zajistěte tak přístup napříč cloudy.
* Místo pevných kódovacích odkazů a prvků používejte dynamické odkazy.
* Ujistěte se, že parametry šablony, které používáte, fungují v cílových cloudech.
* Ověřte, zda jsou cílové cloudy k dispozici vlastnosti specifické pro prostředky.

Úvod k šablonám ARM naleznete v [tématu Nasazení šablony](overview.md).

## <a name="ensure-template-functions-work"></a>Zajistit fungování funkcí šablony

Základní syntaxe šablony ARM je JSON. Šablony používají nadmnožinu JSON, která rozšiřuje syntaxi o výrazy a funkce. Jazykový procesor šablony je často aktualizován, aby podporoval další funkce šablony. Podrobné vysvětlení dostupných funkcí šablony naleznete v tématu [ARM template functions](template-functions.md).

Nové funkce šablony, které se zavádějí do Azure Resource Manageru, nejsou okamžitě dostupné v suverénních cloudech nebo Azure Stacku. Chcete-li úspěšně nasadit šablonu, musí být všechny funkce odkazované v šabloně k dispozici v cílovém cloudu.

Funkce Azure Resource Manageru se vždy nejdřív zavedou do globálního Azure. Pomocí následujícího skriptu Prostředí PowerShell můžete ověřit, jestli jsou nově zavedené funkce šablony dostupné taky v Azure Stacku:

1. Vytvořte klon úložiště GitHub: [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions).

1. Jakmile budete mít místní klon úložiště, připojte se k cílovému Správci prostředků Azure pomocí PowerShellu.

1. Importujte modul psm1 a spusťte rutinu Test-AzureRmTemplateFunctions:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

Skript nasazuje více, minimalizované šablony, z nichž každá obsahuje pouze jedinečné funkce šablony. Výstup skriptu hlásí podporované a nedostupné funkce šablony.

## <a name="working-with-linked-artifacts"></a>Práce s propojenými artefakty

Šablona může obsahovat odkazy na propojené artefakty a může obsahovat prostředek nasazení, který odkazuje na jinou šablonu. Propojené šablony (označované také jako vnořené šablony) jsou načteny Správcem prostředků za běhu. Šablona může také obsahovat odkazy na artefakty pro rozšíření virtuálních počítačů ., Extensions. Tyto artefakty jsou načteny rozšíření virtuálního počítače spuštěné uvnitř virtuálního počítače pro konfiguraci rozšíření virtuálního počítače během nasazení šablony.

Následující části popisují důležité informace o konzistenci cloudu při vývoji šablon, které obsahují artefakty, které jsou mimo hlavní šablonu nasazení.

### <a name="use-nested-templates-across-regions"></a>Použití vnořených šablon napříč oblastmi

Šablony lze rozložit na malé, opakovaně použitelné šablony, z nichž každá má konkrétní účel a lze je znovu použít ve scénářích nasazení. Chcete-li provést nasazení, zadejte jednu šablonu známou jako hlavní nebo hlavní šablona. Určuje prostředky k nasazení, jako jsou virtuální sítě, virtuální počítače a webové aplikace. Hlavní šablona může také obsahovat odkaz na jinou šablonu, což znamená, že šablony můžete vnořit. Podobně vnořená šablona může obsahovat odkazy na jiné šablony. Můžete hnízdit až pět úrovní hluboko.

Následující kód ukazuje, jak parametr templateLink odkazuje na vnořenou šablonu:

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
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

Azure Resource Manager vyhodnotí hlavní šablonu za běhu a načte a vyhodnotí každou vnořenou šablonu. Po načtení všech vnořených šablon se šablona sloučí a bude zahájeno další zpracování.

### <a name="make-linked-templates-accessible-across-clouds"></a>Zpřístupnění propojených šablon napříč cloudy

Zvažte, kde a jak uložit všechny propojené šablony, které používáte. Za běhu Azure Resource Manager načte – a proto vyžaduje přímý přístup k – všechny propojené šablony. Běžnou praxí je použití GitHubu k ukládání vnořených šablon. Úložiště GitHub může obsahovat soubory, které jsou veřejně přístupné prostřednictvím adresy URL. I když tato technika funguje dobře pro veřejný cloud a suverénní cloudy, prostředí Azure Stack může být umístěn v podnikové síti nebo na odpojenévzdáleném umístění, bez odchozího přístupu k Internetu. V těchto případech Azure Resource Manager by se nepodařilo načíst vnořené šablony.

Lepší mašit pro nasazení mezi cloudy je ukládat propojené šablony v umístění, které je přístupné pro cílový cloud. V ideálním případě jsou všechny artefakty nasazení udržovány a nasazeny z kanálu průběžné integrace/průběžného vývoje (CI/CD). Případně můžete ukládat vnořené šablony do kontejneru úložiště objektů blob, ze kterého je Azure Resource Manager může načítat.

Vzhledem k tomu, že úložiště objektů blob v každém cloudu používá jiný koncový bod plně kvalifikovaný název domény (Plně kvalifikovaný název domény), nakonfigurujte šablonu s umístěním propojených šablon se dvěma parametry. Parametry mohou přijímat vstup uživatele v době nasazení. Šablony jsou obvykle vytvářeny a sdíleny více lidmi, takže osvědčeným postupem je použití standardního názvu pro tyto parametry. Konvence pojmenování pomáhají usnadnit opakované vytváření šablon napříč oblastmi, cloudy a autory.

V následujícím kódu `_artifactsLocation` se používá k odkazování na jedno umístění, obsahující všechny artefakty související s nasazením. Všimněte si, že je k dispozici výchozí hodnota. V době nasazení, pokud není `_artifactsLocation`zadána žádná vstupní hodnota , použije se výchozí hodnota. Slouží `_artifactsLocationSasToken` jako vstup pro `sasToken`. Výchozí hodnota by měla být prázdný `_artifactsLocation` řetězec pro scénáře, kde není zabezpečený – například veřejné úložiště GitHub.

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

V celé šabloně jsou odkazy generovány kombinací základního identifikátoru URI (z `_artifactsLocation` `_artifactsLocationSasToken`parametru) s cestou relativní k artefaktu a . Následující kód ukazuje, jak určit odkaz na vnořenou šablonu pomocí funkce šablony uri:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "name": "shared",
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

Pomocí tohoto přístupu se použije `_artifactsLocation` výchozí hodnota parametru. Pokud je třeba propojené šablony načíst z jiného umístění, vstup parametru lze použít v době nasazení k přepsání výchozí hodnoty – není nutná žádná změna samotné šablony.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>Místo pevných kódovacích odkazů použijte _artifactsLocation

Kromě použití pro vnořené šablony `_artifactsLocation` se adresa URL v parametru používá jako základ pro všechny související artefakty šablony nasazení. Některá rozšíření virtuálních zařízení obsahují odkaz na skript uložený mimo šablonu. Pro tato rozšíření byste neměli pevně kódovat odkazy. Například rozšíření Vlastní skript a PowerShell DSC mohou odkazovat na externí skript na GitHubu, jak je znázorněno:

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

Pevné kódování odkazů na skript potenciálně zabraňuje úspěšnému nasazení šablony do jiného umístění. Během konfigurace prostředku virtuálního počítače agent virtuálního počítače spuštěný uvnitř virtuálního počítače iniciuje stahování všech skriptů propojených v rozšíření virtuálního počítače a pak uloží skripty na místní disk virtuálního počítače. Tento přístup funguje jako vnořené odkazy šablon vysvětlené dříve v části "Použít vnořené šablony napříč oblastmi".

Správce prostředků načte vnořené šablony za běhu. Pro rozšíření virtuálních společností načítání všech externích artefaktů se provádí agentvirtuálního soudu. Kromě různých iniciátor načítání artefaktů řešení v definici šablony je stejný. Použijte parametr _artifactsLocation s výchozí hodnotou základní cesty, kde jsou uloženy všechny artefakty `_artifactsLocationSasToken` (včetně skriptů rozšíření virtuálního zařízení) a parametr pro vstup pro sasToken.

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

Chcete-li vytvořit absolutní identifikátor URI artefaktu, upřednostňovanou metodou je použití funkce šablony uri namísto funkce šablony concat. Nahrazením pevně zakódovaných odkazů na skripty v rozšíření virtuálního počítače funkcí šablony uri je tato funkce v šabloně nakonfigurována pro konzistenci cloudu.

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

S tímto přístupem všechny artefakty nasazení, včetně konfiguračních skriptů, mohou být uloženy ve stejném umístění se samotnou šablonou. Chcete-li změnit umístění všech odkazů, stačí zadat jinou základní adresu URL pro _parametry artifactsLocation_.

## <a name="factor-in-differing-regional-capabilities"></a>Faktor v různých regionálních schopnostech

Díky agilnímu vývoji a nepřetržitému toku aktualizací a nových služeb zavedených do Azure [se oblasti mohou lišit](https://azure.microsoft.com/regions/services/) v dostupnosti služeb nebo aktualizací. Po přísném interním testování jsou nové služby nebo aktualizace stávajících služeb obvykle zavedeny malému publiku zákazníků, kteří se účastní ověřovacího programu. Po úspěšném ověření zákazníka jsou služby nebo aktualizace zpřístupněny v rámci podmnožiny oblastí Azure, pak zavedeny do více oblastí, zavedeny do suverénních cloudů a potenciálně dostupné i pro zákazníky Azure Stack.

S vědomím, že oblasti Azure a cloudy se mohou lišit v jejich dostupných služeb, můžete provést některá proaktivní rozhodnutí o vašich šablonách. Dobrým začátkem je prozkoumání dostupných poskytovatelů prostředků pro cloud. Poskytovatel prostředků vám řekne sadu prostředků a operací, které jsou k dispozici pro službu Azure.

Šablona nasazuje a konfiguruje prostředky. Typ prostředku je poskytován poskytovatelem prostředků. Například poskytovatel výpočetních prostředků (Microsoft.Compute) poskytuje více typů prostředků, jako jsou virtualMachines a availabilitySets. Každý poskytovatel prostředků poskytuje rozhraní API pro Azure Resource Manager definované společnou smlouvou, což umožňuje konzistentní a jednotné vytváření prostředí napříč všemi poskytovateli prostředků. Poskytovatel prostředků, který je k dispozici v globálním Azure, však nemusí být k dispozici v suverénním cloudu nebo oblasti Azure Stack.

![Poskytovatelé prostředků](./media/templates-cloud-consistency/resource-providers.png)

Chcete-li ověřit zprostředkovatele prostředků, které jsou k dispozici v daném cloudu, spusťte následující skript v rozhraní příkazového řádku Azure ([CLI](/cli/azure/install-azure-cli)):

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

K zobrazení dostupných poskytovatelů prostředků můžete také zobrazit následující rutinu prostředí PowerShell:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Ověření verze všech typů prostředků

Sada vlastností je společná pro všechny typy prostředků, ale každý prostředek má také své vlastní specifické vlastnosti. Nové funkce a související vlastnosti jsou přidány do existujících typů prostředků občas prostřednictvím nové verze rozhraní API. Prostředek v šabloně má vlastní vlastnost `apiVersion`verze rozhraní API - . Tato správa verzí zajišťuje, že existující konfigurace prostředků v šabloně není ovlivněna změnami na platformě.

Nové verze rozhraní API zavedené pro stávající typy prostředků v globálním Azure nemusí být okamžitě dostupné ve všech oblastech, suverénních cloudech nebo Azure Stacku. Chcete-li zobrazit seznam dostupných poskytovatelů prostředků, typů prostředků a verzí rozhraní API pro cloud, můžete použít Průzkumníka prostředků na webu Azure Portal. Vyhledejte Průzkumníka prostředků v nabídce Všechny služby. Rozbalte uzel Zprostředkovatelé v Průzkumníku prostředků a vraťte všechny dostupné poskytovatele prostředků, jejich typy prostředků a verze rozhraní API v tomto cloudu.

Chcete-li vypsat dostupnou verzi rozhraní API pro všechny typy prostředků v daném cloudu v rozhraní příkazového příkazu Azure, spusťte následující skript:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Můžete také použít následující rutinu prostředí PowerShell:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Odkazovat na umístění zdrojů s parametrem

Šablona je vždy nasazena do skupiny prostředků, která se nachází v oblasti. Kromě samotného nasazení má každý prostředek v šabloně také vlastnost umístění, kterou používáte k určení oblasti, ve které chcete nasadit. Chcete-li vytvořit šablonu pro konzistenci cloudu, potřebujete dynamický způsob, jak odkazovat na umístění prostředků, protože každý Azure Stack může obsahovat jedinečné názvy umístění. Prostředky se obvykle nasazují ve stejné oblasti jako skupina prostředků, ale pro podporu scénářů, jako je dostupnost aplikací mezi oblastmi, může být užitečné rozložit prostředky mezi oblasti.

I když můžete při zadávání vlastností prostředků v šabloně použít název oblasti, tento přístup nezaručuje, že šablonu lze nasadit do jiných prostředí Azure Stack, protože název oblasti s největší pravděpodobností neexistuje.

Chcete-li přizpůsobit různé oblasti, přidejte umístění vstupního parametru do šablony s výchozí hodnotou. Výchozí hodnota bude použita, pokud během nasazení není zadána žádná hodnota.

Funkce `[resourceGroup()]` šablony vrátí objekt, který obsahuje následující dvojice klíč/hodnota:

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

Odkazem na klíč umístění objektu ve výchozíhodnota vstupního parametru Azure Resource Manager `[resourceGroup().location]` za běhu nahradí funkci šablony názvem umístění skupiny prostředků, do které se šablona nasazuje.

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
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    ...
```

Pomocí této funkce šablony můžete nasadit šablonu do libovolného cloudu, aniž byste předem znali názvy oblastí. Kromě toho umístění pro konkrétní prostředek v šabloně se může lišit od umístění skupiny prostředků. V takovém případě jej můžete nakonfigurovat pomocí dalších vstupních parametrů pro daný prostředek, zatímco ostatní prostředky ve stejné šabloně stále používají vstupní parametr počátečního umístění.

### <a name="track-versions-using-api-profiles"></a>Sledování verzí pomocí profilů rozhraní API

Může být velmi náročné sledovat všechny dostupné zprostředkovatele prostředků a související verze rozhraní API, které jsou k dispozici v Azure Stack. Například v době psaní nejnovější verze rozhraní API pro **Microsoft.Compute/availabilitySets** v Azure je `2018-04-01`, zatímco `2016-03-30`dostupná verze rozhraní API společná pro Azure a Azure Stack je . Společná verze rozhraní API pro **Microsoft.Storage/storageAccounts sdílená** `2016-01-01`mezi všemi umístěními Azure `2018-02-01`a Azure Stack je , zatímco nejnovější verze rozhraní API v Azure je .

Z tohoto důvodu Resource Manager představil koncept profilů rozhraní API do šablon. Bez profilů rozhraní API je každý `apiVersion` prostředek v šabloně nakonfigurován s prvkem, který popisuje verzi rozhraní API pro daný prostředek.

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
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "mystorageaccount",
      "location": "[parameters('location')]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    },
    {
      "type": "Microsoft.Compute/availabilitySets",
      "apiVersion": "2016-03-30",
      "name": "myavailabilityset",
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

Verze profilu rozhraní API funguje jako alias pro jednu verzi rozhraní API pro každý typ prostředku, který je společný pro Azure a Azure Stack. Namísto určení verze rozhraní API pro každý prostředek v šabloně zadáte pouze verzi `apiProfile` profilu rozhraní `apiVersion` API v novém kořenovém prvku s názvem a vynechete prvek pro jednotlivé prostředky.

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
            "type": "Microsoft.Storage/storageAccounts",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
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

Profil rozhraní API zajišťuje, že verze rozhraní API jsou k dispozici napříč umístěními, takže není nutné ručně ověřit apiVersions, které jsou k dispozici v určitém umístění. Chcete-li zajistit, že verze rozhraní API, na které odkazuje váš profil rozhraní API, jsou k dispozici v prostředí Azure Stack, musí operátoři Azure Stacku udržovat řešení aktuální na základě zásad podpory. Pokud je systém více než šest měsíců zastaralý, je považován za nedodržení a prostředí musí být aktualizováno.

Profil rozhraní API není povinný prvek v šabloně. I v případě, že přidáte prvek, bude použit `apiVersion` pouze pro prostředky, pro které není zadán žádný. Tento prvek umožňuje postupné změny, ale nevyžaduje žádné změny existujících šablon.

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
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
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

## <a name="check-endpoint-references"></a>Kontrola odkazů na koncové ho dohledu

Prostředky mohou mít odkazy na jiné služby na platformě. Ve veřejné IP adrese může být například přiřazen veřejný název DNS. Veřejný cloud, suverénní cloudy a azure zásobníkřešení mají své vlastní odlišné obory názvů koncových bodů. Ve většině případů prostředek vyžaduje pouze předponu jako vstup v šabloně. Za běhu Azure Resource Manager připojí hodnotu koncového bodu k němu. Některé hodnoty koncového bodu musí být explicitně zadány v šabloně.

> [!NOTE]
> Chcete-li vyvíjet šablony pro konzistenci cloudu, nepevně zakódujte obory názvů koncových bodů.

Následující dva příklady jsou běžné obory názvů koncových bodů, které je třeba explicitně zadat při vytváření prostředku:

* Účty úložiště (objekt blob, fronta, tabulka a soubor)
* Připojovací řetězce pro databáze a Azure Cache pro Redis

Obory názvů koncových bodů lze také použít ve výstupu šablony jako informace pro uživatele po dokončení nasazení. Následují běžné příklady:

* Účty úložiště (objekt blob, fronta, tabulka a soubor)
* Připojovací řetězce (MySql, SQLServer, SQLAzure, Vlastní, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Traffic Manager
* domainNameLabel veřejné IP adresy
* Cloud Services

Obecně se vyhněte pevně zakódované koncové body v šabloně. Osvědčeným postupem je použít funkci šablony odkazu k dynamickému načtení koncových bodů. Například koncový bod nejčastěji pevně zakódovaný je obor názvů koncového bodu pro účty úložiště. Každý účet úložiště má jedinečný hlavní název názvu, který je vytvořen zřetězením názvu účtu úložiště s oborem názvů koncového bodu. Účet úložiště objektů blob s názvem mystorageaccount1 má za následek různé hlavní název skupiny dat v závislosti na cloudu:

* **mystorageaccount1.blob.core.windows.net** při vytvoření v globálním cloudu Azure.
* **mystorageaccount1.blob.core.chinacloudapi.cn** když se vytvoří v cloudu Azure China 21Vianet.

Následující funkce šablony odkazu načte obor názvů koncového bodu od zprostředkovatele prostředků úložiště:

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Nahrazením pevně zakódované hodnoty koncového bodu `reference` účtu úložiště funkcí šablony můžete použít stejnou šablonu k úspěšnému nasazení do různých prostředí bez provedení jakýchkoli změn odkazu na koncový bod.

### <a name="refer-to-existing-resources-by-unique-id"></a>Odkazovat na existující prostředky podle jedinečného ID

Můžete také odkazovat na existující prostředek ze stejné nebo jiné skupiny prostředků a v rámci stejného předplatného nebo jiného předplatného v rámci stejného klienta ve stejném cloudu. Chcete-li načíst vlastnosti prostředku, musíte použít jedinečný identifikátor pro samotný prostředek. Funkce `resourceId` šablony načte jedinečné ID prostředku, jako je SQL Server, jak ukazuje následující kód:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Potom můžete použít `resourceId` funkci `reference` uvnitř funkce šablony k načtení vlastností databáze. Návratový objekt `fullyQualifiedDomainName` obsahuje vlastnost, která obsahuje hodnotu úplného koncového bodu. Tato hodnota se načítá za běhu a poskytuje koncový bod koncový bod specifický pro cloudové prostředí. Chcete-li definovat připojovací řetězec bez pevného kódování oboru názvů koncového bodu, můžete odkazovat na vlastnost vráceného objektu přímo v připojovacím řetězci, jak je znázorněno:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Zvažte vlastnosti prostředků

Konkrétní prostředky v prostředí azure zásobníku mají jedinečné vlastnosti, které je třeba zvážit ve vaší šabloně.

### <a name="ensure-vm-images-are-available"></a>Ujistěte se, že jsou k dispozici image virtuálních mispozií

Azure poskytuje bohatý výběr ibi virtuálních počítači. Tyto bitové kopie jsou vytvořeny a připraveny k nasazení společností Microsoft a partnery. Obrázky tvoří základ pro virtuální počítačna platformě. Šablona konzistentní s cloudem by však měla odkazovat pouze na dostupné parametry – zejména na vydavatele, nabídku a skladovou položku ibi virtuálních her, které jsou k dispozici pro globální Azure, suverénní cloudy Azure nebo řešení Azure Stack.

Pokud chcete načíst seznam dostupných ibi virtuálních počítačů v umístění, spusťte následující příkaz Azure CLI:

```azurecli-interactive
az vm image list -all
```

Můžete načíst stejný seznam s rutinou Azure PowerShell [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) a `-Location` určit umístění, které chcete s parametrem. Například:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Tento příkaz trvá několik minut vrátit všechny dostupné image v oblasti západní Evropy globálního cloudu Azure.

Pokud jste tyto image virtuálních počítačových dat zpřístupnili azure zásobníku, bude spotřebováno všechny dostupné úložiště. Azure Stack umožňuje vybrat i ty nejmenší jednotky škálování, které chcete přidat do prostředí.

Následující ukázka kódu ukazuje konzistentní přístup k odkazování na parametry vydavatele, nabídky a skladové položky v šablonách ARM:

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

### <a name="check-local-vm-sizes"></a>Kontrola místních velikostí virtuálních počítače

Chcete-li vytvořit šablonu pro konzistenci cloudu, musíte se ujistit, že požadovaná velikost virtuálního počítače je k dispozici ve všech cílových prostředích. Velikosti virtuálních počítače jsou seskupení charakteristik a schopností výkonu. Některé velikosti virtuálních zařízení závisí na hardwaru, na který virtuální počítač běží. Například pokud chcete nasadit virtuální počítač optimalizovaný pro GPU, hardware, který spouští hypervisor, musí mít hardwarové grafické procesory.

Když Microsoft zavádí novou velikost virtuálního počítače, který má určité hardwarové závislosti, velikost virtuálního počítače se obvykle zpřístupní jako první v malé podmnožině oblastí v cloudu Azure. Později je k dispozici pro jiné oblasti a mraky. Chcete-li se ujistit, že velikost virtuálního počítače existuje v každém cloudu, do kterých nasadíte, můžete načíst dostupné velikosti pomocí následujícího příkazu Azure CLI:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Pro Azure PowerShell použijte:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Úplný seznam dostupných služeb naleznete v tématu [Produkty dostupné podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Kontrola využití spravovaných disků Azure v Azure Stacku

Spravované disky zpracovávají úložiště pro klienta Azure. Namísto explicitního vytvoření účtu úložiště a určení identifikátoru URI pro virtuální pevný disk (VHD) můžete tyto akce při nasazení virtuálního počítače implicitně použít pomocí spravovaných disků. Spravované disky zvyšují dostupnost umístěním všech disků z virtuálních počítačů ve stejné sadě dostupnosti do různých úložných jednotek. Kromě toho lze stávající virtuální ženapřet ze standardního úložiště na úložiště Premium s výrazně nižšími prostoji.

Přestože spravované disky jsou na plánu pro Azure Stack, nejsou aktuálně podporovány. Dokud nejsou, můžete vyvíjet šablony konzistentní s cloudem pro Azure Stack `vhd` explicitním zadáním virtuálních dispozí za použití prvku v šabloně pro prostředek virtuálního počítače, jak je znázorněno:

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

Chcete-li naopak zadat konfiguraci spravovaného disku `vhd` v šabloně, odeberte prvek z konfigurace disku.

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

Stejné změny platí také [datové disky](../../virtual-machines/windows/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Ověřte, že rozšíření virtuálních počítačových společností jsou dostupná v Azure Stacku

Dalším aspektem konzistence cloudu je použití [rozšíření virtuálních strojů](../../virtual-machines/windows/extensions-features.md) ke konfiguraci prostředků uvnitř virtuálního počítače. Ne všechna rozšíření virtuálních počítačových společností jsou k dispozici v Azure Stacku. Šablona můžete určit prostředky vyhrazené pro rozšíření virtuálního počítače, vytváření závislostí a podmínek v rámci šablony.

Například pokud chcete nakonfigurovat virtuální ho s microsoft sql server, rozšíření virtuálního počítače můžete nakonfigurovat SQL Server jako součást nasazení šablony. Zvažte, co se stane, pokud šablona nasazení obsahuje také aplikační server nakonfigurovaný k vytvoření databáze na virtuálním počítači se systémem SQL Server. Kromě použití rozšíření virtuálního počítače pro aplikační servery můžete nakonfigurovat závislost aplikačního serveru při úspěšném vrácení prostředku rozšíření virtuálního počítače SQL Server. Tento přístup zajišťuje, že virtuální modul se systémem SQL Server je nakonfigurovaný a dostupný, když je aplikační server instruován k vytvoření databáze.

Deklarativní přístup šablony umožňuje definovat koncový stav prostředků a jejich vzájemné závislosti, zatímco platforma se stará o logiku požadovanou pro závislosti.

#### <a name="check-that-vm-extensions-are-available"></a>Zkontrolujte, zda jsou k dispozici rozšíření virtuálních virtuálních míchačů.

Existuje mnoho typů rozšíření virtuálních počítačů. Při vývoji šablony pro konzistenci cloudu, ujistěte se, že používat pouze rozšíření, které jsou k dispozici ve všech oblastech cíle šablony.

Chcete-li načíst seznam rozšíření virtuálních počítače, které jsou k `myLocation`dispozici pro konkrétní oblast (v tomto příkladu ), spusťte následující příkaz y Azure CLI:

```azurecli-interactive
az vm extension image list --location myLocation
```

Můžete také spustit rutinu Azure PowerShell [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) a použít `-Location` k určení umístění image virtuálního počítače. Například:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Ujistěte se, že jsou k dispozici verze

Vzhledem k tomu, že rozšíření virtuálních společností jsou prostředky Správce prostředků první strany, mají vlastní verze rozhraní API. Jak ukazuje následující kód, typ rozšíření virtuálního počítače je vnořený prostředek ve zprostředkovateli prostředků Microsoft.Compute.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

Verze rozhraní API prostředku rozšíření virtuálního počítače musí být k dispozici ve všech umístěních, na které chcete cílit pomocí šablony. Závislost umístění funguje stejně jako dostupnost verze rozhraní API poskytovatele prostředků, která byla popsána dříve v části Ověření verze všech typů prostředků.

Chcete-li načíst seznam dostupných verzí rozhraní API pro prostředek rozšíření virtuálního počítače, použijte rutinu [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) s poskytovatelem prostředků **Microsoft.Compute,** jak je znázorněno:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Rozšíření virtuálních zařízení můžete taky použít ve škálovacích sadách virtuálních strojů. Platí stejné podmínky umístění. Chcete-li vytvořit šablonu pro konzistenci cloudu, ujistěte se, že verze rozhraní API jsou k dispozici ve všech umístěních, do kterých máte v plánu nasadit. Chcete-li načíst verze rozhraní API prostředku rozšíření virtuálního počítače pro škálovací sady, použijte stejnou rutinu jako dříve, ale zadejte typ prostředku škálovacísady virtuálních strojů, jak je znázorněno:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Každé konkrétní rozšíření je také verzí. Tato verze se `typeHandlerVersion` zobrazí ve vlastnosti rozšíření virtuálního zařízení. Ujistěte se, že `typeHandlerVersion` verze zadaná v prvku rozšíření virtuálních zařízení šablony jsou k dispozici v umístěních, kde máte v plánu nasadit šablonu. Například následující kód určuje verzi 1.7:

```json
{
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "name": "MyCustomScriptExtension",
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

Chcete-li načíst seznam dostupných verzí pro konkrétní rozšíření virtuálního počítače, použijte rutinu [Get-AzureRmVMExtensionImage.](/powershell/module/az.compute/get-azvmextensionimage) Následující příklad načte dostupné verze pro rozšíření virtuálního počítače PowerShell DSC (konfigurace požadovaného stavu) z **myLocation**:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Chcete-li získat seznam vydavatelů, použijte příkaz [Get-AzureRmVmImagePublisher.](/powershell/module/az.compute/get-azvmimagepublisher) Chcete-li požádat o typ, použijte [Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) commend.

## <a name="tips-for-testing-and-automation"></a>Tipy pro testování a automatizaci

Při vytváření šablony je vhodné sledovat všechna související nastavení, možnosti a omezení. Běžným přístupem je vývoj a testování šablon proti jednomu cloudu před cílením na jiná umístění. Však dříve, že testy jsou prováděny v procesu vytváření, méně řešení potíží a přepisování kódu váš vývojový tým bude muset udělat. Nasazení, která se nezdaří z důvodu závislostí umístění může být časově náročné řešení potíží. Proto doporučujeme automatické testování co nejdříve v cyklu vytváření. Nakonec budete potřebovat méně času na vývoj a méně prostředků a vaše artefakty konzistentní v cloudu budou ještě cennější.

Následující obrázek ukazuje typický příklad procesu vývoje pro tým pomocí integrovaného vývojového prostředí (IDE). V různých fázích časové osy jsou prováděny různé typy testů. Zde dva vývojáři pracují na stejném řešení, ale tento scénář platí stejně pro jednoho vývojáře nebo velký tým. Každý vývojář obvykle vytvoří místní kopii centrálního úložiště, což každému z nich umožňuje pracovat na místní kopii bez dopadu na ostatní, kteří mohou pracovat na stejných souborech.

![Pracovní postup](./media/templates-cloud-consistency/workflow.png)

Zvažte následující tipy pro testování a automatizaci:

* Využijte testovací nástroje. Například Visual Studio Code a Visual Studio zahrnují IntelliSense a další funkce, které vám pomohou ověřit šablony.
* Chcete-li zlepšit kvalitu kódu během vývoje na místní IDE, proveďte analýzu statického kódu s testy částí a integračními testy.
* Pro ještě lepší zkušenosti během počátečního vývoje by testy částí a integrační testy měly upozornit pouze v případě, že je zjištěn problém, a pokračovat v testech. Tímto způsobem můžete identifikovat problémy, které je třeba řešit, a určit pořadí změn, označované také jako nasazení řízené testováním (TDD).
* Uvědomte si, že některé testy lze provést bez připojení ke Správci prostředků Azure. Jiné, jako je testování nasazení šablony, vyžadují Resource Manager provádět určité akce, které nelze provést offline.
* Testování šablony nasazení proti ověřovacímu rozhraní API se nerovná skutečnému nasazení. Také i v případě, že nasadíte šablonu z místního souboru, všechny odkazy na vnořené šablony v šabloně jsou načteny správceprostředků přímo a artefakty odkazované rozšíření virtuálních počítače jsou načteny agentvirtuálního počítače spuštěné uvnitř nasazeného virtuálního počítače.

## <a name="next-steps"></a>Další kroky

* [Důležité informace o šablonách Azure Resource Manageru](/azure-stack/user/azure-stack-develop-templates)
* [Doporučené postupy pro šablony ARM](template-syntax.md)
