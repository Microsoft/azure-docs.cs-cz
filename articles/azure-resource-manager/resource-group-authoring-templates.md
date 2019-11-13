---
title: Azure Resource Manager struktura a syntaxe šablony | Microsoft Docs
description: Popisuje strukturu a vlastnosti šablon Azure Resource Manager pomocí deklarativní syntaxe JSON.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: tomfitz
ms.openlocfilehash: 065f392f3089f4d6872cd39c6aaab2098afc6c90
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012160"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Pochopení struktury a syntaxe šablon Azure Resource Manager

Tento článek popisuje strukturu Azure Resource Manager šablony. Zobrazuje různé oddíly šablony a vlastnosti, které jsou k dispozici v těchto oddílech.

Tento článek je určený pro uživatele, kteří mají určitou znalost Správce prostředků šablon. Poskytuje podrobné informace o struktuře šablony. Pokud chcete seznámení s vytvářením šablony, přečtěte si téma [Azure Resource Manager šablony](template-deployment-overview.md).

## <a name="template-format"></a>Formát šablony

V nejjednodušší struktuře má šablona následující prvky:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Název elementu | Požaduje se | Popis |
|:--- |:--- |:--- |
| $schema |Ano |Umístění souboru schématu JSON, který popisuje verzi jazyka šablony.<br><br> Pro nasazení skupin prostředků použijte: `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Pro nasazení předplatných použijte: `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Ano |Verze šablony (například 1.0.0.0). Pro tento prvek můžete zadat libovolnou hodnotu. Tuto hodnotu použijte k dokumentování významných změn v šabloně. Při nasazování prostředků pomocí šablony můžete tuto hodnotu použít k tomu, abyste se ujistili, že je používána pravá šablona. |
| apiProfile |Ne | Verze rozhraní API, která slouží jako kolekce verzí rozhraní API pro typy prostředků. Tuto hodnotu použijte, chcete-li se vyhnout nutnosti zadávat verze rozhraní API pro každý prostředek v šabloně. Když zadáte verzi profilu rozhraní API a nezadáte verzi rozhraní API pro typ prostředku, Správce prostředků používá verzi rozhraní API pro tento typ prostředku, který je definovaný v profilu.<br><br>Vlastnost profil rozhraní API je užitečná hlavně při nasazení šablony do různých prostředí, jako je Azure Stack a globální Azure. Pomocí verze profilu rozhraní API se ujistěte, že vaše šablona automaticky používá verze, které jsou v obou prostředích podporované. Seznam aktuálních verzí profilů rozhraní API a verzí rozhraní API prostředků definovaných v profilu najdete v tématu [profil rozhraní API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Další informace najdete v tématu [sledování verzí pomocí profilů rozhraní API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Ne |Hodnoty, které jsou k dispozici při spuštění nasazení za účelem přizpůsobení nasazení prostředků. |
| [variables](#variables) |Ne |Hodnoty, které se používají jako fragmenty JSON v šabloně pro zjednodušení výrazů jazyka šablony. |
| [functions](#functions) |Ne |Uživatelsky definované funkce, které jsou k dispozici v rámci šablony. |
| [resources](#resources) |Ano |Typy prostředků, které se nasazují nebo aktualizují v rámci skupiny prostředků nebo předplatného. |
| [outputs](#outputs) |Ne |Hodnoty, které se vrátí po nasazení. |

Každý prvek má vlastnosti, které lze nastavit. V tomto článku jsou podrobněji popsány části šablony.

## <a name="parameters"></a>Parametry

V části Parameters (parametry) v šabloně určíte, které hodnoty můžete zadat při nasazování prostředků. V šabloně budete omezeni na 256 parametrů. Počet parametrů můžete snížit pomocí objektů, které obsahují více vlastností.

Dostupné vlastnosti pro parametr:

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>"
    }
  }
}
```

| Název elementu | Požaduje se | Popis |
|:--- |:--- |:--- |
| název parametru |Ano |Název parametru Musí být platný identifikátor jazyka JavaScript. |
| type |Ano |Typ hodnoty parametru Povolené typy a hodnoty jsou **String**, **SecureString**, **int**, **bool**, **Object**, **secureObject**a **Array**. Podívejte se na [datové typy](#data-types). |
| defaultValue |Ne |Výchozí hodnota parametru, pokud není k dispozici žádná hodnota pro parametr. |
| allowedValues |Ne |Pole povolených hodnot pro parametr, aby bylo zajištěno, že je zadána pravá hodnota. |
| minValue |Ne |Minimální hodnota pro parametry typu int je tato hodnota včetně. |
| maxValue |Ne |Maximální hodnota pro parametry typu int je tato hodnota včetně. |
| minLength |Ne |Minimální délka parametrů pro řetězec, zabezpečený řetězec a typ pole je hodnota včetně. |
| maxLength |Ne |Maximální délka parametrů pro řetězec, zabezpečený řetězec a typ pole je hodnota včetně. |
| description |Ne |Popis parametru, který se uživatelům zobrazí prostřednictvím portálu. Další informace najdete v tématu [komentáře v šablonách](#comments). |

Příklady použití parametrů naleznete [v tématu Parameters in Azure Resource Manager Templates](template-parameters.md).

### <a name="data-types"></a>Typy dat

Pro celá čísla předaná jako vložené parametry může být rozsah hodnot omezen sadou SDK nebo nástrojem příkazového řádku, který používáte pro nasazení. Například při použití prostředí PowerShell k nasazení šablony mohou být typy celého čísla v rozsahu od-2147483648 do 2147483647. Chcete-li se tomuto omezení vyhnout, zadejte v [souboru parametrů](resource-manager-parameter-files.md)velké celočíselné hodnoty. Typy prostředků použijí vlastní omezení pro celočíselné vlastnosti.

Při zadávání logických a celočíselných hodnot v šabloně se hodnota neobklopuje pomocí uvozovek. Počáteční a koncové řetězcové hodnoty pomocí dvojitých uvozovek.

Objekty začínají levou závorkou a končí pravou závorkou. Pole začínají levou hranatou závorkou a končí pravou závorkou.

Zabezpečené řetězce a zabezpečené objekty nelze přečíst po nasazení prostředků.

Ukázky formátování datových typů naleznete v tématu [formáty typu parametru](resource-manager-parameter-files.md#parameter-type-formats).

## <a name="variables"></a>Proměnné

V části proměnné můžete vytvářet hodnoty, které lze použít v rámci šablony. Nemusíte definovat proměnné, ale často zjednodušují vaši šablonu tím, že snižují složité výrazy.

Následující příklad ukazuje dostupné možnosti pro definování proměnné:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": {
    <variable-complex-type-value>
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

Informace o použití `copy` k vytvoření několika hodnot proměnné naleznete v tématu [Variable iterace](resource-group-create-multiple.md#variable-iteration).

Příklady použití proměnných naleznete [v tématu proměnné v šabloně Azure Resource Manager](template-variables.md).

## <a name="functions"></a>Funkce

V rámci šablony můžete vytvořit vlastní funkce. Tyto funkce jsou k dispozici pro použití ve vaší šabloně. Obvykle definujete složité výrazy, které nechcete opakovat v celé šabloně. Můžete vytvořit uživatelsky definované funkce z výrazů a [funkcí](resource-group-template-functions.md) , které jsou podporovány v šablonách.

Při definování uživatelské funkce existují určitá omezení:

* Funkce nemá přístup k proměnným.
* Funkce může používat pouze parametry, které jsou definovány ve funkci. Použijete-li [funkci Parameters](resource-group-template-functions-deployment.md#parameters) v rámci uživatelsky definované funkce, budete omezeni na parametry této funkce.
* Funkce nemůže volat jiné uživatelsky definované funkce.
* Funkce nemůže používat [odkazovou funkci](resource-group-template-functions-resource.md#reference).
* Parametry pro funkci nemohou mít výchozí hodnoty.

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| Název elementu | Požaduje se | Popis |
|:--- |:--- |:--- |
| – obor názvů |Ano |Obor názvů pro vlastní funkce Použijte k zamezení konfliktu názvů s funkcemi šablon. |
| název funkce |Ano |Název vlastní funkce Při volání funkce kombinovat název funkce s oborem názvů. Například pro volání funkce s názvem uniqueName v oboru názvů contoso, použijte `"[contoso.uniqueName()]"`. |
| název parametru |Ne |Název parametru, který se má použít v rámci vlastní funkce |
| hodnota parametru |Ne |Typ hodnoty parametru Povolené typy a hodnoty jsou **String**, **SecureString**, **int**, **bool**, **Object**, **secureObject**a **Array**. |
| výstupní typ |Ano |Typ výstupní hodnoty. Výstupní hodnoty podporují stejné typy jako vstupní parametry funkce. |
| výstupní hodnota |Ano |Výraz jazyka šablony, který je vyhodnocen a vrácen z funkce. |

Příklady použití vlastních funkcí naleznete [v tématu uživatelsky definované funkce v šabloně Azure Resource Manager](template-user-defined-functions.md).

## <a name="resources"></a>Prostředky

V části Resources (prostředky) definujete prostředky, které jsou nasazené nebo aktualizované.

Provedete definování prostředků s následující strukturou:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Název elementu | Požaduje se | Popis |
|:--- |:--- |:--- |
| condition | Ne | Logická hodnota, která označuje, jestli se prostředek zřídí během tohoto nasazení. Při `true`se prostředek vytvoří během nasazování. Při `false`se prostředek pro toto nasazení přeskočí. Zobrazit [podmínku](conditional-resource-deployment.md). |
| apiVersion |Ano |Verze REST API, která se má použít k vytvoření prostředku Chcete-li zjistit dostupné hodnoty, přečtěte si téma [Reference k šabloně](/azure/templates/). |
| type |Ano |Typ prostředku. Tato hodnota je kombinací oboru názvů poskytovatele prostředků a typu prostředku (například **Microsoft. Storage/storageAccounts**). Chcete-li zjistit dostupné hodnoty, přečtěte si téma [Reference k šabloně](/azure/templates/). U podřízených prostředků závisí formát typu na tom, jestli je vnořený v nadřazeném prostředku nebo definovaný mimo nadřazený prostředek. Viz [Nastavení názvu a typu pro podřízené prostředky](child-resource-name-type.md). |
| jméno |Ano |Název prostředku. Název musí splňovat omezení součásti identifikátoru URI definovaná v RFC3986. Služby Azure, které zveřejňují název prostředku mimo jiné, ověřují název, aby se ujistil, že se nejedná o pokus o falšování jiné identity. U podřízeného prostředku formát názvu závisí na tom, jestli je vnořený v nadřazeném prostředku nebo definovaný mimo nadřazený prostředek. Viz [Nastavení názvu a typu pro podřízené prostředky](child-resource-name-type.md). |
| location |Různé |Podporovaná geografická umístění poskytnutého prostředku Můžete vybrat kterékoli z dostupných umístění, ale obvykle dává smysl vybrat, která je blízko vašim uživatelům. Obvykle má smysl umístit prostředky, které vzájemně spolupracují ve stejné oblasti. Většina typů prostředků vyžaduje umístění, ale některé typy (například přiřazení role) nevyžadují umístění. Viz [Nastavení umístění prostředku](resource-location.md). |
| značek |Ne |Značky, které jsou přidruženy k prostředku. Použijte značky pro logickou organizaci prostředků v rámci vašeho předplatného. |
| vyjádření |Ne |Poznámky k dokumentaci prostředků ve vaší šabloně. Další informace najdete v tématu [komentáře v šablonách](resource-group-authoring-templates.md#comments). |
| kopií |Ne |Pokud je potřeba více než jedna instance, počet prostředků, které se mají vytvořit. Výchozí režim je paralelní. Zadejte sériový režim, pokud nechcete, aby se nasadily všechny nebo prostředky. Další informace najdete v tématu [vytvoření několika instancí prostředků v Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Ne |Prostředky, které musí být nasazeny před nasazením tohoto prostředku. Správce prostředků vyhodnocuje závislosti mezi prostředky a nasadí je ve správném pořadí. Pokud nejsou prostředky vzájemně závislé, nasadí se paralelně. Hodnota může být čárkami oddělený seznam názvů prostředků nebo jedinečných identifikátorů prostředků. Pouze seznam prostředků, které jsou nasazeny v této šabloně. Prostředky, které nejsou definované v této šabloně, už musí existovat. Vyhněte se přidávání zbytečných závislostí, protože mohou zpomalit nasazení a vytvářet cyklické závislosti. Pokyny k nastavení závislostí najdete v tématu [Definování závislostí v šablonách Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |Ne |Nastavení konfigurace specifické pro prostředky. Hodnoty vlastností jsou stejné jako hodnoty, které zadáte v textu žádosti pro operaci REST API (metoda PUT) pro vytvoření prostředku. Můžete také zadat pole pro kopírování a vytvořit několik instancí vlastnosti. Chcete-li zjistit dostupné hodnoty, přečtěte si téma [Reference k šabloně](/azure/templates/). |
| skladové | Ne | Některé prostředky umožňují hodnoty definující SKU, které se mají nasadit. Můžete například zadat typ redundance pro účet úložiště. |
| plnění | Ne | Některé prostředky umožňují hodnotu definující typ prostředku, který nasadíte. Můžete například zadat typ Cosmos DB, který se má vytvořit. |
| rozhraní | Ne | Některé prostředky umožňují hodnoty definující plán, který se má nasadit. Můžete například zadat image Marketplace pro virtuální počítač. |
| prostředky |Ne |Podřízené prostředky závislé na definovaném prostředku. Poskytněte jenom typy prostředků, které jsou povolené schématem nadřazeného prostředku. Nepředpokládá se závislost na nadřazeném prostředku. Tuto závislost musíte explicitně definovat. Viz [Nastavení názvu a typu pro podřízené prostředky](child-resource-name-type.md). |

## <a name="outputs"></a>Výstupy

V části výstupů zadáte hodnoty, které se vracejí z nasazení. Obvykle vracíte hodnoty z nasazených prostředků.

Následující příklad ukazuje strukturu definici výstupu:

```json
"outputs": {
  "<output-name>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Název elementu | Požaduje se | Popis |
|:--- |:--- |:--- |
| Název výstupu |Ano |Název výstupní hodnoty. Musí být platný identifikátor jazyka JavaScript. |
| condition |Ne | Logická hodnota, která označuje, zda je vrácena tato výstupní hodnota. Když `true`, hodnota je obsažena ve výstupu pro nasazení. Při `false`je výstupní hodnota pro toto nasazení vynechána. Pokud není zadaný, použije se výchozí hodnota `true`. |
| type |Ano |Typ výstupní hodnoty. Výstupní hodnoty podporují stejné typy jako vstupní parametry šablony. Pokud zadáte **SecureString** pro typ výstupu, hodnota se nezobrazí v historii nasazení a nelze ji načíst z jiné šablony. Chcete-li použít tajnou hodnotu ve více než jedné šabloně, uložte tajný klíč do Key Vault a odkazujte na tajný kód v souboru parametrů. Další informace najdete v tématu [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](resource-manager-keyvault-parameter.md). |
| hodnota |Ano |Výraz jazyka šablony, která je vyhodnocena a vrátila jako výstupní hodnota. |

Příklady použití výstupů najdete [v tématu výstupy v šabloně Azure Resource Manager](template-outputs.md).

<a id="comments" />

## <a name="comments-and-metadata"></a>Komentáře a metadata

Máte několik možností, jak přidat komentáře a metadata do šablony.

### <a name="comments"></a>Komentáře

Pro vložené komentáře můžete použít buď `//`, nebo `/* ... */`, ale tato syntaxe nefunguje u všech nástrojů. Editor šablon portálu nemůžete použít pro práci se šablonami s vloženými komentáři. Pokud přidáte tento styl komentáře, ujistěte se, že nástroje, které používáte, podporují vložené komentáře JSON.

> [!NOTE]
> K nasazení šablon s komentáři pomocí rozhraní příkazového řádku Azure je nutné použít přepínač `--handle-extended-json-format`.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

V Visual Studio Code může [rozšíření Azure Resource Manager nástrojů](./resource-manager-tools-vs-code.md#install-resource-manager-tools-extension) automaticky detekovat šablonu správce prostředků a odpovídajícím způsobem změnit jazykový režim. Pokud se v pravém dolním rohu VS Code zobrazí **Azure Resource Manager šablona** , můžete použít vložené komentáře. Vložené komentáře již nejsou označeny jako neplatné.

![Režim šablony Visual Studio Code Azure Resource Manager](./media/resource-group-authoring-templates/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metadata

Objekt `metadata` můžete přidat skoro kdekoli v šabloně. Správce prostředků objekt ignoruje, ale editor JSON vám může zobrazit upozornění, že vlastnost není platná. V objektu definujte vlastnosti, které potřebujete.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Pro **parametry**přidejte objekt `metadata` s vlastností `description`.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Při nasazování šablony prostřednictvím portálu se text, který zadáte v popisu, automaticky použije jako Tip pro tento parametr.

![Zobrazit Tip parametru](./media/resource-group-authoring-templates/show-parameter-tip.png)

Pro **prostředky**přidejte `comments` element nebo objekt metadat. Následující příklad ukazuje jak element Comments, tak objekt metadat.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

Pro **výstupy**přidejte objekt metadat do výstupní hodnoty.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

Do uživatelsky definovaných funkcí nemůžete přidat objekt metadat.

## <a name="multi-line-strings"></a>Víceřádkové řetězce

Řetězec můžete rozdělit na více řádků. Například vlastnost Location a jeden z komentářů v následujícím příkladu JSON.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Chcete-li nasadit šablony s víceřádkovými řetězci pomocí rozhraní příkazového řádku Azure, je nutné použít přepínač `--handle-extended-json-format`.

## <a name="next-steps"></a>Další kroky

* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
* Podrobnosti o funkce, které můžete použít z v rámci šablony najdete v tématu [funkce šablon Azure Resource Manageru](resource-group-template-functions.md).
* Pokud chcete zkombinovat několik šablon během nasazování, přečtěte si téma [použití propojených šablon s Azure Resource Manager](resource-group-linked-templates.md).
* Doporučení k vytváření šablon najdete v tématu [osvědčené postupy pro šablonu Azure Resource Manager](template-best-practices.md).
* Doporučení k vytváření Správce prostředků šablon, které můžete použít ve všech prostředích a Azure Stack Azure, najdete v tématu [vývoj šablon Azure Resource Manager pro zajištění konzistence cloudu](templates-cloud-consistency.md).
