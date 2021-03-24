---
title: Opakované použití šablon napříč cloudy
description: Vývoj šablon Azure Resource Manager (šablony ARM), které pracují konzistentně pro různá cloudová prostředí. Vytvořte nové nebo aktualizujte existující šablony pro Azure Stack.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 120e98ebebec1436c9171f6076baccd35be9b843
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104888817"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>Vývoj šablon ARM pro cloudovou konzistenci

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Klíčovou výhodou Azure je konzistence. Investice do vývoje pro jedno umístění se znovu používají v jiné. Šablona Azure Resource Manager (šablona ARM) umožňuje nasazení konzistentně a opakovaně v různých prostředích, včetně globálního cloudu Azure, Azure svrchovaného cloudu a Azure Stack. Aby bylo možné opakovaně používat šablony napříč cloudy, je třeba vzít v úvahu, že v této příručce jsou vysvětleny specifické závislosti cloudu.

Microsoft nabízí inteligentní cloudové služby připravené pro podnikové prostředí v mnoha umístěních, včetně těchto:

* Globální platforma Azure podporovaná rostoucí sítí datových center spravovaných Microsoftem v oblastech po celém světě.
* Izolované svrchované cloudy, jako je Azure Německo, Azure Government a Azure Čína 21Vianet. Svrchované cloudy poskytují konzistentní platformu s většinou stejných skvělých funkcí, ke kterým mají globální zákazníci Azure přístup.
* Azure Stack, hybridní cloudová platforma, která vám umožní poskytovat služby Azure z datového centra vaší organizace. Podniky můžou nastavit Azure Stack ve svých vlastních datacentrech nebo využívat služby Azure od poskytovatelů služeb a spouštět je Azure Stack ve svých zařízeních (někdy označované jako hostované oblasti).

V jádru všech těchto cloudů Azure Resource Manager poskytuje rozhraní API, které umožňuje komunikaci s platformou Azure širokou škálu uživatelských rozhraní. Toto rozhraní API poskytuje výkonné možnosti infrastruktury jako kódu. Jakýkoli typ prostředku, který je dostupný na cloudové platformě Azure, se dá nasadit a nakonfigurovat s Azure Resource Manager. S jedinou šablonou můžete nasadit a nakonfigurovat kompletní aplikaci do provozního koncového stavu.

![Prostředí Azure](./media/templates-cloud-consistency/environments.png)

Výhodou Azure Resource Manager je konzistence globálních Azure, cloudů z svrchovaného prostředí, hostovaných cloudů a cloudového datového centra. Při nastavování nasazení a konfigurace prostředků založených na šablonách můžete znovu použít své investice do vývoje napříč těmito cloudy.

I když globální, svrchované, hostované a hybridní cloudy poskytují konzistentní služby, ale ne všechny cloudy jsou stejné. V důsledku toho můžete vytvořit šablonu se závislostmi na funkcích dostupných pouze v konkrétním cloudu.

Zbytek této příručky popisuje oblasti, které je potřeba vzít v úvahu při plánování vývoje nových nebo aktualizovaných existujících šablon ARM pro Azure Stack. Kontrolní seznam by měl obecně zahrnovat následující položky:

* Ověřte, zda jsou funkce, koncové body, služby a další prostředky v šabloně k dispozici v cílových umístěních nasazení.
* Vnořené šablony a konfigurační artefakty ukládejte v dostupných umístěních a zajistěte tak přístup mezi cloudy.
* Používejte dynamické odkazy namísto odkazů a prvků pevného kódování.
* Zajistěte, aby parametry šablony používané v cílových cloudech pracovaly.
* Ověřte, zda jsou pro cílové cloudy k dispozici vlastnosti specifické pro daný prostředek.

Úvod do šablon ARM najdete v tématu [template Deployment](overview.md).

## <a name="ensure-template-functions-work"></a>Zajištění fungování funkcí šablon

Základní syntaxí šablony ARM je JSON. Šablony používají nadmnožinu JSON a rozšiřují syntaxi pomocí výrazů a funkcí. Procesor šablon jazyka se často aktualizuje pro podporu dalších funkcí šablon. Podrobné vysvětlení dostupných funkcí šablon najdete v tématu [funkce šablon ARM](template-functions.md).

Nové funkce šablon, které se zavádějí do Azure Resource Manager nejsou hned dostupné v cloudech svrchovan nebo Azure Stack. Aby bylo možné šablonu nasadit úspěšně, musí být všechny funkce odkazované v této šabloně k dispozici v cílovém cloudu.

Funkce Azure Resource Manager se vždycky do globálního Azure nasadí dřív. Pomocí následujícího skriptu prostředí PowerShell můžete ověřit, zda nově zavedené funkce šablony jsou také k dispozici v Azure Stack:

1. Vytvořte klon úložiště GitHub: [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions) .

1. Jakmile budete mít místní klon úložiště, připojte se k Azure Resource Manager cíle pomocí PowerShellu.

1. Importujte modul psm1 a spusťte rutinu Test-AzureRmTemplateFunctions:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

Skript nasadí několik minimalizovaných šablon, z nichž každý obsahuje pouze jedinečné funkce šablon. Výstup skriptu hlásí podporované a nedostupné funkce šablony.

## <a name="working-with-linked-artifacts"></a>Práce s propojenými artefakty

Šablona může obsahovat odkazy na propojené artefakty a obsahovat prostředek nasazení, který odkazuje na jinou šablonu. Propojené šablony (označované také jako vnořená šablona) jsou načteny Správce prostředků za běhu. Šablona může také obsahovat odkazy na artefakty pro rozšíření virtuálních počítačů (VM). Tyto artefakty jsou načteny rozšířením virtuálního počítače spuštěným uvnitř virtuálního počítače za účelem konfigurace rozšíření virtuálního počítače během nasazování šablony.

Následující části popisují požadavky na konzistenci cloudu při vývoji šablon, které obsahují artefakty, které jsou externí pro hlavní šablonu nasazení.

### <a name="use-nested-templates-across-regions"></a>Použití vnořených šablon napříč oblastmi

Šablony je možné rozložit do malých, opakovaně použitelných šablon, z nichž každá má konkrétní účel a dá se použít v rámci scénářů nasazení. Chcete-li spustit nasazení, zadejte jednu šablonu známou jako hlavní nebo hlavní šablonu. Určuje prostředky pro nasazení, jako jsou virtuální sítě, virtuální počítače a webové aplikace. Hlavní šablona může také obsahovat odkaz na jinou šablonu, což znamená, že můžete vnořovat šablony. Podobně vnořená šablona může obsahovat odkazy na jiné šablony. Můžete vnořit až pět úrovní do hloubky.

Následující kód ukazuje, jak parametr templateLink odkazuje na vnořenou šablonu:

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2020-10-01",
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

Azure Resource Manager vyhodnocuje hlavní šablonu za běhu a načte a vyhodnotí každou vnořenou šablonu. Po načtení všech vnořených šablon se šablona sloučí a iniciuje se další zpracování.

### <a name="make-linked-templates-accessible-across-clouds"></a>Zpřístupnění propojených šablon v cloudech

Zvažte, kde a jak uložit všechny propojené šablony, které používáte. Za běhu Azure Resource Manager načíst, a proto vyžaduje přímý přístup k – všem propojeným šablonám. Běžným postupem je použití GitHubu k ukládání vnořených šablon. Úložiště GitHub může obsahovat soubory, které jsou veřejně přístupné prostřednictvím adresy URL. I když tato technika funguje dobře pro veřejný cloud a cloudy v svrchovaném prostředí, může se Azure Stack prostředí nacházet v podnikové síti nebo na odpojeném vzdáleném umístění bez jakéhokoli odchozího internetového přístupu. V těchto případech by Azure Resource Manager nepodařilo načíst vnořené šablony.

Lepším postupem při nasazení mezi několika cloudy je uložení vašich propojených šablon do umístění, které je přístupné pro cílový Cloud. V ideálním případě jsou všechny artefakty nasazení udržovány v a nasazeny z kanálu průběžné integrace nebo průběžného vývoje (CI/CD). Případně můžete vnořené šablony Uložit do kontejneru úložiště objektů blob, ze kterého Azure Resource Manager je mohou načíst.

Vzhledem k tomu, že úložiště objektů BLOB v jednotlivých cloudech používá jiný koncový bod plně kvalifikovaný název domény (FQDN), nakonfigurujte šablonu s umístěním propojených šablon se dvěma parametry. Parametry můžou přijímat vstupy uživatele v době nasazení. Šablony jsou obvykle vytvořené a sdílené více lidmi, proto doporučujeme pro tyto parametry použít standardní název. Zásady pojmenování usnadňují použití šablon v různých oblastech, cloudech a tvůrcích.

V následujícím kódu `_artifactsLocation` se používá k odkazování na jedno umístění, které obsahuje všechny artefakty související s nasazením. Všimněte si, že je zadána výchozí hodnota. Pokud v době nasazení není zadána žádná vstupní hodnota pro `_artifactsLocation` , je použita výchozí hodnota. `_artifactsLocationSasToken`Slouží jako vstup pro `sasToken` . Výchozí hodnota by měla být prázdný řetězec pro scénáře, ve kterých `_artifactsLocation` není zabezpečený – například veřejné úložiště GitHub.

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

V rámci šablony jsou odkazy generovány kombinací základního identifikátoru URI (z `_artifactsLocation` parametru) s cestou relativního artefaktu a `_artifactsLocationSasToken` . Následující kód ukazuje, jak zadat odkaz na vnořenou šablonu pomocí funkce šablony identifikátoru URI:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
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

Při použití tohoto přístupu `_artifactsLocation` je použita výchozí hodnota parametru. Pokud je potřeba načíst propojené šablony z jiného umístění, můžete zadat vstup parametru v době nasazení, aby se potlačila výchozí hodnota – nemusíte dělat žádné změny samotné šablony.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>Místo zakódujeme odkazů použít _artifactsLocation

Kromě použití pro vnořené šablony se adresa URL v `_artifactsLocation` parametru používá jako základ pro všechny související artefakty šablony nasazení. Některá rozšíření virtuálních počítačů zahrnují odkaz na skript uložený mimo šablonu. Pro tato rozšíření byste neměli nekódujte pevně odkazy. Například vlastní skript a rozšíření PowerShell DSC se můžou připojit k externímu skriptu na GitHubu, jak je znázorněno níže:

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

Zakódujeme odkazy na skript potenciálně brání tomu, aby se šablona mohla úspěšně nasazovat do jiného umístění. Během konfigurace prostředku virtuálního počítače zahájí agent virtuálního počítače v rámci virtuálního počítače stahování všech skriptů propojených v rozšíření virtuálního počítače a potom tyto skripty uloží na místní disk virtuálního počítače. Tento přístup funguje podobně jako vnořené odkazy na šablony popsané dříve v části použití vnořených šablon napříč oblastmi.

Správce prostředků načte vnořené šablony za běhu. Pro rozšíření virtuálních počítačů se k načtení všech externích artefaktů provede agent virtuálního počítače. Kromě jiného iniciátoru načtení artefaktu je řešení v definici šablony stejné. Použijte parametr _artifactsLocation s výchozí hodnotou základní cesty, kde jsou uloženy všechny artefakty (včetně skriptů rozšíření virtuálního počítače) a `_artifactsLocationSasToken` parametr pro vstup pro sasToken.

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

Chcete-li vytvořit absolutní identifikátor URI artefaktu, upřednostňovanou metodou je použít funkci šablony identifikátoru URI namísto funkce Concat Template. Pokud nahradíte pevně zakódované odkazy na skripty v rozšíření virtuálního počítače pomocí funkce šablony identifikátoru URI, je tato funkce v šabloně nakonfigurovaná na konzistenci cloudu.

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

S tímto přístupem můžete všechny artefakty nasazení, včetně konfiguračních skriptů, ukládat ve stejném umístění se šablonou samotné. Chcete-li změnit umístění všech odkazů, stačí zadat jinou základní adresu URL pro _parametry artifactsLocation_.

## <a name="factor-in-differing-regional-capabilities"></a>Faktor lišící se různými možnostmi regionálního výkonu

Díky agilnímu vývoji a průběžnému toku aktualizací a nových služeb zavedených do Azure se [můžou oblasti lišit](https://azure.microsoft.com/regions/services/) v dostupnosti služeb nebo aktualizací. Po striktním interním testování jsou nové služby nebo aktualizace stávajících služeb obvykle zavedeny malým uživatelům, kteří se účastní ověřovacího programu. Po úspěšném ověření od zákazníka jsou služby nebo aktualizace zpřístupněny v rámci podmnožiny oblastí Azure a pak byly zavedeny do více oblastí, které byly zavedeny do cloudů z svrchovaného prostředí a mohou být dostupné i pro Azure Stack zákazníky.

S vědomím, že se oblasti a cloudy Azure můžou v dostupných službách lišit, můžete k šablonám vytvořit některá proaktivní rozhodnutí. Dobrým místem, kde začít, je prozkoumání dostupných poskytovatelů prostředků pro Cloud. Poskytovatel prostředků oznamuje sadu prostředků a operací, které jsou k dispozici pro službu Azure.

Šablona nasazuje a konfiguruje prostředky. Typ prostředku je poskytován poskytovatelem prostředků. Například poskytovatel prostředků COMPUTE (Microsoft. COMPUTE) poskytuje více typů prostředků, například virtualMachines a availabilitySets. Každý poskytovatel prostředků poskytuje rozhraní API, které Azure Resource Manager definováno běžným kontraktem, což umožňuje konzistentní jednotné prostředí pro vytváření napříč všemi poskytovateli prostředků. Poskytovatel prostředků, který je k dispozici v globálním Azure, ale nemusí být k dispozici v rámci svrchovaného cloudu ani Azure Stack oblasti.

![Poskytovatelé prostředků](./media/templates-cloud-consistency/resource-providers.png)

Pokud chcete ověřit poskytovatele prostředků, kteří jsou k dispozici v daném cloudu, spusťte následující skript v rozhraní příkazového řádku Azure ([CLI](/cli/azure/install-azure-cli)):

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

K zobrazení dostupných zprostředkovatelů prostředků můžete použít taky tuto rutinu PowerShellu:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Ověřit verzi všech typů prostředků

Sada vlastností je společná pro všechny typy prostředků, ale každý prostředek má také vlastní specifické vlastnosti. Nové funkce a související vlastnosti se přidávají do stávajících typů prostředků v době pomocí nové verze rozhraní API. Prostředek v šabloně má svou vlastní vlastnost verze API- `apiVersion` . Tato verze zajišťuje, že existující konfigurace prostředků v šabloně není ovlivněná změnami na platformě.

Nové verze rozhraní API, které se zavedly do stávajících typů prostředků v globálním Azure, nemusí být hned dostupné ve všech oblastech, cloudech svrchovaných nebo Azure Stack. Pokud chcete zobrazit seznam dostupných zprostředkovatelů prostředků, typů prostředků a verzí rozhraní API pro Cloud, můžete použít Průzkumník prostředků v Azure Portal. V nabídce všechny služby vyhledejte Průzkumník prostředků. Rozbalte uzel poskytovatelé v Průzkumník prostředků a vraťte všechny dostupné poskytovatele prostředků, jejich typy prostředků a verze rozhraní API v tomto cloudu.

Pokud chcete zobrazit dostupnou verzi rozhraní API pro všechny typy prostředků v daném cloudu v Azure CLI, spusťte tento skript:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Můžete také použít následující rutinu PowerShellu:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Podívejte se na umístění prostředků s parametrem.

Šablona se vždycky nasadí do skupiny prostředků, která se nachází v nějaké oblasti. Kromě samotného nasazení má každý prostředek v šabloně také vlastnost Location, kterou použijete k určení oblasti pro nasazení v nástroji. Abyste mohli vytvořit šablonu pro cloudovou konzistenci, potřebujete dynamický způsob, jak odkazovat na umístění prostředků, protože každý Azure Stack může obsahovat jedinečné názvy umístění. Prostředky jsou obvykle nasazené ve stejné oblasti jako skupina prostředků, ale pro podporu scénářů, jako je například dostupnost aplikace mezi oblastmi, může být užitečné rozložit prostředky napříč oblastmi.

I když byste při zadávání vlastností prostředků v šabloně mohli nekódujte pevně názvy oblastí, tento přístup nezaručuje, že šablonu lze nasadit do jiných Azure Stack prostředí, protože název oblasti pravděpodobně neexistuje.

Pro přizpůsobení různých oblastí přidejte do šablony umístění vstupního parametru s výchozí hodnotou. Pokud během nasazování není zadaná žádná hodnota, použije se výchozí hodnota.

Funkce šablony `[resourceGroup()]` vrátí objekt, který obsahuje následující páry klíč/hodnota:

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

Odkazem na klíč umístění objektu v poli se vstupním parametrem se Azure Resource Manager za běhu nahraďte `[resourceGroup().location]` funkci šablony názvem umístění skupiny prostředků, do které šablona nasadí šablonu.

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

Pomocí této funkce šablony můžete nasadit šablonu do libovolného cloudu, aniž byste museli znát názvy oblastí předem. Umístění konkrétního prostředku v šabloně se navíc může lišit od umístění skupiny prostředků. V takovém případě ji můžete nakonfigurovat pomocí dalších vstupních parametrů pro konkrétní prostředek, zatímco ostatní prostředky ve stejné šabloně stále používají vstupní parametr počáteční umístění.

### <a name="track-versions-using-api-profiles"></a>Sledování verzí pomocí profilů rozhraní API

Může být velmi náročné sledovat všechny dostupné poskytovatele prostředků a související verze API, které jsou k dispozici v Azure Stack. V době psaní můžete například nejnovější verzi rozhraní API pro **Microsoft. COMPUTE/availabilitySets** v Azure `2018-04-01` , zatímco dostupná verze API je společná pro Azure a Azure Stack `2016-03-30` . Společná verze rozhraní API pro **Microsoft. Storage/storageAccounts** sdílená mezi všemi lokalitami Azure a Azure Stack je `2016-01-01` , zatímco nejnovější verze rozhraní API v Azure je `2018-02-01` .

Z tohoto důvodu Správce prostředků představil koncept profilů rozhraní API do šablon. Bez profilů rozhraní API jsou jednotlivé prostředky v šabloně nakonfigurované s `apiVersion` prvkem, který popisuje verzi rozhraní API pro daný konkrétní prostředek.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Verze profilu rozhraní API funguje jako alias pro jednu verzi rozhraní API pro každý typ prostředku, který je společný pro Azure a Azure Stack. Namísto určení verze rozhraní API pro každý prostředek v šabloně zadáte pouze verzi profilu rozhraní API v novém kořenovém elementu s názvem `apiProfile` a vynecháte `apiVersion` element pro jednotlivé prostředky.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Profil rozhraní API zajišťuje, aby verze rozhraní API byly dostupné napříč umístěními, takže nemusíte ručně ověřovat apiVersions, které jsou k dispozici v určitém umístění. Aby se zajistilo, že verze rozhraní API, na které se odkazuje váš profil rozhraní API, se nachází v Azure Stack prostředí, musí Azure Stack operátoři uchovávat řešení v aktuálním stavu na základě zásad pro podporu. Pokud je systém starší než šest měsíců, je považován za nedodržení předpisů a prostředí musí být aktualizováno.

Profil rozhraní API není požadovaným prvkem v šabloně. I v případě, že prvek přidáte, bude použit pouze pro prostředky, pro které `apiVersion` není zadána žádná. Tento prvek umožňuje postupné změny, ale nevyžaduje žádné změny stávajících šablon.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

## <a name="check-endpoint-references"></a>Kontrolovat odkazy na koncový bod

Prostředky můžou mít odkazy na jiné služby na platformě. Veřejné IP adresy můžou mít například přiřazen veřejný název DNS. Veřejný cloud, cloudy svrchovaného prostředí a Azure Stack řešení mají své vlastní jedinečné obory názvů koncových bodů. Ve většině případů prostředek vyžaduje jako vstup v šabloně pouze předponu. Během běhu Azure Resource Manager připojí hodnotu koncového bodu k tomuto poli. Některé hodnoty koncového bodu je potřeba explicitně zadat v šabloně.

> [!NOTE]
> Pro vývoj šablon pro cloudovou konzistenci nekódujte pevně obory názvů koncových bodů.

Následující dva příklady jsou společné obory názvů koncových bodů, které je potřeba explicitně zadat při vytváření prostředku:

* Účty úložiště (objekt blob, fronta, tabulka a soubor)
* Připojovací řetězce pro databáze a Azure cache pro Redis

Obory názvů koncových bodů můžete také použít ve výstupu šablony jako informace pro uživatele po dokončení nasazení. Následují běžné příklady:

* Účty úložiště (objekt blob, fronta, tabulka a soubor)
* Připojovací řetězce (MySql, SQLServer, SQLAzure, Custom, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Traffic Manager
* domainNameLabel veřejné IP adresy
* Cloud Services

Obecně se vyhněte koncovým bodům pevně zakódované v šabloně. Osvědčeným postupem je použití referenční funkce šablony k dynamickému načítání koncových bodů. Například koncový bod nejčastěji pevně zakódované je obor názvů koncového bodu pro účty úložiště. Každý účet úložiště má jedinečný plně kvalifikovaný název domény, který je vytvořený zřetězením názvu účtu úložiště s oborem názvů koncového bodu. Účet Blob Storage s názvem mystorageaccount1 má za následek různé plně kvalifikované názvy domény v závislosti na cloudu:

* `mystorageaccount1.blob.core.windows.net` Při vytvoření v globálním cloudu Azure.
* `mystorageaccount1.blob.core.chinacloudapi.cn` Při vytvoření v cloudu Azure Čína 21Vianet.

Následující referenční funkce šablony načte obor názvů koncového bodu z poskytovatele prostředků úložiště:

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Nahrazením hodnoty pevně zakódované koncového bodu účtu úložiště pomocí `reference` funkce šablony můžete použít stejnou šablonu pro nasazení do různých prostředí úspěšně, aniž byste museli provádět změny v referenci koncového bodu.

### <a name="refer-to-existing-resources-by-unique-id"></a>Podívejte se na stávající prostředky podle jedinečného ID.

Můžete také odkazovat na existující prostředek ze stejné nebo jiné skupiny prostředků a v rámci stejného předplatného nebo jiného předplatného v rámci stejného tenanta ve stejném cloudu. Chcete-li načíst vlastnosti prostředku, je nutné použít jedinečný identifikátor samotného prostředku. `resourceId`Funkce šablony načte jedinečné ID prostředku, například SQL Server, jak ukazuje následující kód:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Potom můžete `resourceId` funkci použít uvnitř `reference` šablony funkce k načtení vlastností databáze. Návratový objekt obsahuje `fullyQualifiedDomainName` vlastnost, která obsahuje hodnotu úplného koncového bodu. Tato hodnota je načtena za běhu a poskytuje obor názvů koncového bodu specifického pro cloudové prostředí. Chcete-li definovat připojovací řetězec bez zakódujeme oboru názvů koncového bodu, můžete odkazovat na vlastnost návratového objektu přímo v připojovacím řetězci, jak je znázorněno níže:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Zvážit vlastnosti prostředku

Konkrétní prostředky v prostředí Azure Stack mají jedinečné vlastnosti, které musíte zvážit v šabloně.

### <a name="ensure-vm-images-are-available"></a>Zajištění dostupnosti imagí virtuálních počítačů

Azure poskytuje bohatou volbu imagí virtuálních počítačů. Tyto image se vytvářejí a připravují pro nasazení od Microsoftu a partnerů. Obrázky tvoří základ pro virtuální počítače na platformě. Šablona konzistentní vzhledem k cloudu by ale měla odkazovat jenom na dostupné parametry – konkrétně vydavatele, nabídku a SKU imagí virtuálních počítačů, které jsou dostupné pro globální Azure, cloudy Azure svrchované nebo řešení Azure Stack.

Pokud chcete načíst seznam dostupných imagí virtuálních počítačů v nějakém umístění, spusťte následující příkaz rozhraní příkazového řádku Azure:

```azurecli-interactive
az vm image list -all
```

Stejný seznam můžete načíst pomocí rutiny Azure PowerShell [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) a zadat požadované umístění s `-Location` parametrem. Například:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Tento příkaz trvá několik minut, než se vrátí všechny dostupné image v Západní Evropa oblasti globálního cloudu Azure.

Pokud jste provedli tyto image virtuálních počítačů k dispozici pro Azure Stack, budou využita všechna dostupná úložiště. Aby se vešla i nejmenší jednotka škálování, Azure Stack vám umožní vybrat image, které chcete přidat do prostředí.

Následující ukázka kódu ukazuje konzistentní přístup k odkazům na parametry vydavatele, nabídky a SKU v šablonách ARM:

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

### <a name="check-local-vm-sizes"></a>Kontrolovat velikosti místních virtuálních počítačů

Pokud chcete vytvořit šablonu pro cloudovou konzistenci, musíte se ujistit, že požadovaná velikost virtuálního počítače je dostupná ve všech cílových prostředích. Velikosti virtuálních počítačů jsou seskupením charakteristik výkonu a možností. Některé velikosti virtuálních počítačů závisí na hardwaru, na kterém je virtuální počítač spuštěný. Například pokud chcete nasadit virtuální počítač optimalizovaný pomocí GPU, hardware, který spouští hypervisor, musí mít hardware GPU.

Když Microsoft zavádí novou velikost virtuálního počítače, který má určité závislosti hardwaru, je velikost virtuálního počítače většinou dostupná jako první v malé podmnožině oblastí v cloudu Azure. Později je zpřístupní ostatním oblastem a cloudům. Abyste se ujistili, že velikost virtuálního počítače existuje v každém cloudu, na který nasazujete, můžete dostupné velikosti získat pomocí následujícího příkazu Azure CLI:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Pro Azure PowerShell použijte:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Úplný seznam dostupných služeb najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Kontrolovat použití Azure Managed Disks v Azure Stack

Služba Managed disks zpracovává úložiště pro tenanta Azure. Místo explicitního vytvoření účtu úložiště a zadání identifikátoru URI virtuálního pevného disku (VHD) můžete použít spravované disky k implicitnímu provedení těchto akcí při nasazení virtuálního počítače. Spravované disky zlepšují dostupnost tím, že se do různých jednotek úložiště umísťují všechny disky z virtuálních počítačů ve stejné skupině dostupnosti. Existující virtuální pevné disky se navíc dají převést z úrovně Standard na Premium Storage s výrazně kratším prostojem.

I když jsou spravované disky v plánu pro Azure Stack, nejsou aktuálně podporované. Dokud neproběhne, můžete vyvíjet šablony konzistentní vzhledem k cloudu pro Azure Stack explicitním zadáním virtuálních pevných disků pomocí `vhd` elementu v šabloně pro prostředek virtuálního počítače, jak je znázorněno níže:

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

Naopak pro určení konfigurace spravovaného disku v šabloně odeberte `vhd` prvek z konfigurace disku.

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

Stejné změny také platí pro [datové disky](../../virtual-machines/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Ověřte, že jsou rozšíření virtuálních počítačů k dispozici v Azure Stack

Dalším aspektem konzistence cloudu je použití [rozšíření virtuálních počítačů](../../virtual-machines/extensions/features-windows.md) ke konfiguraci prostředků v rámci virtuálního počítače. V Azure Stack nejsou k dispozici všechna rozšíření virtuálních počítačů. Šablona může určovat prostředky vyhrazené pro rozšíření virtuálního počítače a vytvářet závislosti a podmínky v rámci šablony.

Pokud třeba chcete nakonfigurovat virtuální počítač s Microsoft SQL Server, může rozšíření virtuálního počítače nakonfigurovat SQL Server jako součást nasazení šablony. Zvažte, co se stane, když šablona nasazení obsahuje taky aplikační server nakonfigurovaný k vytvoření databáze na virtuálním počítači se systémem SQL Server. Kromě toho, že používá rozšíření virtuálního počítače pro aplikační servery, můžete nakonfigurovat závislost aplikačního serveru na úspěšném návratu prostředku rozšíření SQL Server virtuálního počítače. Tento přístup zajišťuje, aby byl virtuální počítač se spuštěným SQL Server nakonfigurovaný a dostupný, když je aplikační server pokyn k vytvoření databáze.

Deklarativní přístup k šabloně umožňuje definovat koncový stav prostředků a jejich vzájemných závislostí, zatímco platforma postará o logiku požadovanou pro závislosti.

#### <a name="check-that-vm-extensions-are-available"></a>Ověřte, že jsou dostupná rozšíření virtuálních počítačů.

Existuje mnoho typů rozšíření virtuálních počítačů. Při vývoji šablony pro konzistenci cloudu se ujistěte, že používáte pouze rozšíření, která jsou k dispozici ve všech oblastech, které šablona cílí.

Pokud chcete načíst seznam rozšíření virtuálních počítačů, které jsou k dispozici pro konkrétní oblast (v tomto příkladu `myLocation` ), spusťte následující příkaz rozhraní příkazového řádku Azure:

```azurecli-interactive
az vm extension image list --location myLocation
```

Můžete také spustit rutinu Azure PowerShell [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) a použít `-Location` ji k určení umístění bitové kopie virtuálního počítače. Například:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Zajistěte, aby byly dostupné verze

Vzhledem k tomu, že rozšíření virtuálních počítačů jsou Správce prostředků prostředky jako první, mají vlastní verze rozhraní API. Jak ukazuje následující kód, typ rozšíření virtuálního počítače je vnořeným prostředkem ve zprostředkovateli prostředků Microsoft. Compute.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

Verze rozhraní API prostředku rozšíření virtuálních počítačů musí být přítomná ve všech umístěních, která plánujete s vaší šablonou cílit. Závislost umístění funguje jako verze rozhraní API poskytovatele prostředků, kterou popisuje výše v části "ověření verze všech typů prostředků".

Pokud chcete načíst seznam dostupných verzí rozhraní API pro prostředek rozšíření virtuálních počítačů, použijte rutinu [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) u poskytovatele prostředků **Microsoft. COMPUTE** , jak je znázorněno na následujícím obrázku:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Rozšíření virtuálních počítačů můžete použít taky v sadě Virtual Machine Scale Sets. Platí stejné podmínky umístění. Pokud chcete vytvořit šablonu pro cloudovou konzistenci, ujistěte se, že verze rozhraní API jsou k dispozici ve všech umístěních, na která plánujete nasazení. Pokud chcete načíst verze API prostředku rozšíření virtuálních počítačů pro sady škálování, použijte stejnou rutinu jako předtím, ale zadejte typ prostředku Virtual Machine Scale Sets, jak je znázorněno níže:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Každé konkrétní rozšíření je také ve verzi. Tato verze je zobrazená ve `typeHandlerVersion` vlastnosti rozšíření virtuálního počítače. Zajistěte, aby byla verze zadaná v `typeHandlerVersion` elementu rozšíření virtuálních počítačů vaší šablony dostupná v umístěních, kde plánujete šablonu nasadit. Například následující kód určuje verzi 1,7:

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

Pokud chcete načíst seznam dostupných verzí pro konkrétní rozšíření virtuálního počítače, použijte rutinu [Get-AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage) . Následující příklad načte dostupné verze rozšíření VM DSC (Konfigurace požadovaného stavu) z **myLocation**:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Chcete-li získat seznam vydavatelů, použijte příkaz [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) . Chcete-li požádat o typ, použijte [příkaz Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) commend.

## <a name="tips-for-testing-and-automation"></a>Tipy pro testování a automatizaci

Při vytváření šablony se jedná o výzvu k udržení přehledu o všech souvisejících nastaveních, schopnostech a omezeních. Běžným přístupem je vývoj a testování šablon proti jednomu cloudu, než se cílí na jiná umístění. Nicméně předchozí testy jsou prováděny v procesu vytváření, což bude mít méně řešení potíží a přepis kódu, který váš vývojový tým provede. Nasazení, která selžou kvůli selhání závislostí umístění, mohou být časově náročná k řešení potíží. To je důvod, proč doporučujeme v cyklu vytváření obsahu co nejdříve automatizovat testování. Nakonec budete potřebovat méně času vývoje a méně prostředků a artefakty konzistentní vzhledem k vašemu cloudu budou ještě důležitější.

Následující obrázek znázorňuje typický příklad procesu vývoje pro tým s použitím integrovaného vývojového prostředí (IDE). V různých fázích časové osy se spustí různé typy testů. Tady jsou na stejném řešení funkční dva vývojáři, ale tento scénář platí stejně pro jednoho vývojáře nebo velký tým. Každý vývojář obvykle vytváří místní kopii centrálního úložiště, takže každý z nich může pracovat na místní kopii, aniž by to mělo vliv na ostatní, kteří mohou pracovat se stejnými soubory.

![Diagram znázorňuje dvě sady jednotkových testů a integračních testů paralelně na místních I D E, které se sloučí do vývojového toku C I/C do testů jednotek, potom Integration Tests, test Deployment a pak nasazení.](./media/templates-cloud-consistency/workflow.png)

Vezměte v úvahu následující tipy pro testování a automatizaci:

* Využijte testovacích nástrojů. Například Visual Studio Code a Visual Studio obsahují IntelliSense a další funkce, které vám pomůžou ověřit vaše šablony.
* Pro zlepšení kvality kódu během vývoje na místním rozhraní IDE proveďte analýzu statického kódu pomocí testů jednotek a testů integrace.
* Pro lepší zkušenosti při počátečním vývoji by testy jednotek a testy integrace měly upozorňovat jenom na nalezení problému a pokračování testů. Tímto způsobem můžete identifikovat problémy, které budou řešeny, a určit prioritu pořadí změn, označovanou také jako nasazení řízené testy (TDD).
* Mějte na paměti, že některé testy je možné provést bez připojení k Azure Resource Manager. Jiní uživatelé, jako je testování nasazení šablony, vyžadují Správce prostředků k provedení určitých akcí, které nelze provést offline.
* Testování šablony nasazení proti rozhraní API pro ověřování se nerovná skutečnému nasazení. I když nasadíte šablonu z místního souboru, všechny odkazy na vnořené šablony v šabloně jsou načteny přímo Správce prostředků a artefakty, na které odkazuje rozšíření virtuálních počítačů, jsou načteny agentem virtuálního počítače spuštěným v nasazeném virtuálním počítači.

## <a name="next-steps"></a>Další kroky

* [Pokyny pro šablonu Azure Resource Manager](/azure-stack/user/azure-stack-develop-templates)
* [Osvědčené postupy pro šablony ARM](template-syntax.md)
