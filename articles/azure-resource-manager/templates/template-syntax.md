---
title: Struktura šablony a syntaxe
description: Popisuje strukturu a vlastnosti šablon Azure Resource Manager pomocí deklarativní syntaxe JSON.
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 60d800eb5251fb3454ba60a67bd109261c6ff9d4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687867"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>Principy struktury a syntaxe šablon ARM

Tento článek popisuje strukturu šablony Správce prostředků Azure (ARM). Představuje různé části šablony a vlastnosti, které jsou k dispozici v těchto částech.

Tento článek je určen pro uživatele, kteří mají nějaké znalosti s ARM šablony. Poskytuje podrobné informace o struktuře šablony. Podrobný kurz, který vás provede procesem vytváření šablony, najdete v [tématu Kurz: Vytvoření a nasazení první šablony Azure Resource Manageru](template-tutorial-create-first-template.md).

## <a name="template-format"></a>Formát šablon

Ve své nejjednodušší struktuře má šablona následující prvky:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
| $schema |Ano |Umístění souboru schématu JSON, který popisuje verzi jazyka šablony. Číslo verze, které používáte, závisí na rozsahu nasazení a editoru JSON.<br><br>Pokud používáte [Kód VS s rozšířením nástrojů Azure Resource Manager](use-vs-code-to-create-template.md), použijte nejnovější verzi pro nasazení skupin prostředků:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Ostatní editory (včetně sady Visual Studio) nemusí být schopen zpracovat toto schéma. Pro tyto editory použijte:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Pro nasazení předplatného použijte:<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>Pro nasazení skupiny pro správu použijte:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>Pro nasazení klienta použijte:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| contentVersion |Ano |Verze šablony (například 1.0.0.0). Pro tento prvek můžete zadat libovolnou hodnotu. Tato hodnota slouží k dokumentaci významných změn v šabloně. Při nasazování prostředků pomocí šablony lze tuto hodnotu použít k ujistěte se, že je použita správná šablona. |
| apiProfil |Ne | Verze rozhraní API, která slouží jako kolekce verzí rozhraní API pro typy prostředků. Pomocí této hodnoty se vyhnete nutnosti zadávat verze rozhraní API pro každý prostředek v šabloně. Když zadáte verzi profilu rozhraní API a nezadáte verzi rozhraní API pro typ prostředku, Resource Manager používá verzi rozhraní API pro tento typ prostředku, který je definován v profilu.<br><br>Vlastnost profilu rozhraní API je užitečná zejména při nasazování šablony do různých prostředí, jako je Azure Stack a globální Azure. Pomocí verze profilu rozhraní API můžete zajistit, aby vaše šablona automaticky používala verze, které jsou podporované v obou prostředích. Seznam aktuálních verzí profilu rozhraní API a verzí rozhraní API prostředků definovaných v profilu naleznete v [tématu Profil rozhraní API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Další informace naleznete v [tématu Sledování verzí pomocí profilů rozhraní API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [Parametry](#parameters) |Ne |Hodnoty, které jsou k dispozici při spuštění nasazení k přizpůsobení nasazení prostředků. |
| [Proměnné](#variables) |Ne |Hodnoty, které se používají jako fragmenty JSON v šabloně pro zjednodušení výrazů jazyka šablony. |
| [Funkce](#functions) |Ne |Uživatelem definované funkce, které jsou k dispozici v šabloně. |
| [Zdroje](#resources) |Ano |Typy prostředků, které jsou nasazeny nebo aktualizovány ve skupině prostředků nebo předplatném. |
| [Výstupy](#outputs) |Ne |Hodnoty, které jsou vráceny po nasazení. |

Každý prvek má vlastnosti, které můžete nastavit. Tento článek popisuje části šablony podrobněji.

## <a name="parameters"></a>Parametry

V části parametry šablony určíte, které hodnoty můžete zadat při nasazování prostředků. V šabloně máte limit 256 parametrů. Počet parametrů můžete snížit pomocí objektů, které obsahují více vlastností.

Dostupné vlastnosti parametru jsou:

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
| název parametru |Ano |Název parametru Musí se jednat o platný identifikátor JavaScriptu. |
| type |Ano |Typ hodnoty parametru. Povolené typy a hodnoty jsou **řetězec**, **securestring**, **int**, **bool**, **object**, **secureObject**a **array**. Viz [Datové typy](#data-types). |
| Defaultvalue |Ne |Výchozí hodnota parametru, pokud pro parametr není k dispozici žádná hodnota. |
| Allowedvalues |Ne |Pole povolených hodnot pro parametr a ujistěte se, že je k dispozici správná hodnota. |
| Minvalue |Ne |Minimální hodnota pro parametry typu int, tato hodnota je včetně. |
| Maxvalue |Ne |Maximální hodnota parametrů typu int, tato hodnota je včetně. |
| Minlength |Ne |Minimální délka pro parametry řetězce, zabezpečeného řetězce a typu pole, tato hodnota je včetně. |
| Maxlength |Ne |Maximální délka parametrů řetězce, zabezpečeného řetězce a typu pole, tato hodnota je včetně. |
| description |Ne |Popis parametru, který se zobrazí uživatelům prostřednictvím portálu. Další informace naleznete [v tématu Komentáře v šablonách](#comments). |

Příklady použití parametrů najdete [v tématu Parametry v šablonách Azure Resource Manageru](template-parameters.md).

### <a name="data-types"></a>Typy dat

U celá čísla předávaná jako včleněné parametry může být rozsah hodnot omezen sadou SDK nebo nástrojem příkazového řádku, který používáte pro nasazení. Například při použití Prostředí PowerShell k nasazení šablony, celočíselné typy může být v rozsahu od -2147483648 do 2147483647. Chcete-li se tomuto omezení vyhnout, zadejte velké celé číslo hodnoty v [souboru parametrů](parameter-files.md). Typy prostředků používají vlastní limity pro celé vlastnosti.

Při zadávání logických a čtyřčíselných hodnot v šabloně neobklopujte hodnotu uvozovkami. Počáteční a koncové řetězcové hodnoty s dvojitými uvozovkami.

Objekty začínají levou složenou závorkou a končí pravou složenou závorkou. Pole začínají levou závorkou a končí pravou závorkou.

Zabezpečené řetězce a zabezpečené objekty nelze číst po nasazení prostředků.

Ukázky formátovacích datových typů naleznete [v tématu Formáty typů parametrů](parameter-files.md#parameter-type-formats).

## <a name="variables"></a>Proměnné

V části proměnné vytvoříte hodnoty, které lze použít v celé šabloně. Proměnné není nutné definovat, ale často zjednodušují šablonu snížením složitých výrazů.

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

Informace o `copy` použití k vytvoření několika hodnot pro proměnnou naleznete v tématu [Variable iteration](copy-variables.md).

Příklady použití proměnných najdete [v tématu Proměnné v šabloně Správce prostředků Azure](template-variables.md).

## <a name="functions"></a>Functions

V šabloně můžete vytvářet vlastní funkce. Tyto funkce jsou k dispozici pro použití v šabloně. Obvykle definujete složité výrazy, které nechcete opakovat v celé šabloně. Uživatelem definované funkce vytvoříte z výrazů a [funkcí,](template-functions.md) které jsou podporovány v šablonách.

Při definování uživatelské funkce existují určitá omezení:

* Funkce nemá přístup k proměnným.
* Funkce může používat pouze parametry, které jsou definovány ve funkci. Při použití [funkce parametry](template-functions-deployment.md#parameters) v rámci uživatelem definované funkce, jste omezeni na parametry pro tuto funkci.
* Funkce nemůže volat jiné uživatelem definované funkce.
* Funkce nemůže použít [referenční funkci](template-functions-resource.md#reference).
* Parametry funkce nemohou mít výchozí hodnoty.

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
| namespace |Ano |Obor názvů pro vlastní funkce. Slouží k zabránění konfliktům názvů s funkcemi šablony. |
| název funkce |Ano |Název vlastní funkce. Při volání funkce zkombinujte název funkce s oborem názvů. Chcete-li například volat funkci s názvem uniqueName v `"[contoso.uniqueName()]"`oboru názvů contoso, použijte . |
| název parametru |Ne |Název parametru, který má být použit v rámci vlastní funkce. |
| hodnota parametru |Ne |Typ hodnoty parametru. Povolené typy a hodnoty jsou **řetězec**, **securestring**, **int**, **bool**, **object**, **secureObject**a **array**. |
| výstupní typ |Ano |Typ výstupní hodnoty. Výstupní hodnoty podporují stejné typy jako vstupní parametry funkce. |
| výstupní hodnota |Ano |Výraz jazyka šablony, který je vyhodnocen a vrácen z funkce. |

Příklady použití vlastních funkcí najdete [v tématu Uživatelem definované funkce v šabloně Správce prostředků Azure](template-user-defined-functions.md).

## <a name="resources"></a>Prostředky

V části prostředky definujete prostředky, které jsou nasazeny nebo aktualizovány.

Prostředky definujete s následující strukturou:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
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
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Název elementu | Požaduje se | Popis |
|:--- |:--- |:--- |
| Podmínka | Ne | Logická hodnota, která označuje, zda bude prostředek zřízen během tohoto nasazení. Když `true`je prostředek vytvořen během nasazení. Když `false`je prostředek pro toto nasazení přeskočen. Viz [podmínka](conditional-resource-deployment.md). |
| type |Ano |Typ zdroje. Tato hodnota je kombinací oboru názvů poskytovatele prostředků a typu prostředku (například **Microsoft.Storage/storageAccounts**). Chcete-li zjistit dostupné hodnoty, viz [odkaz na šablonu](/azure/templates/). Pro podřízený prostředek, formát typu závisí na tom, zda je vnořený v rámci nadřazeného prostředku nebo definované mimo nadřazený prostředek. Viz [Nastavit název a typ pro podřízené prostředky](child-resource-name-type.md). |
| apiVersion |Ano |Verze rozhraní REST API pro vytvoření prostředku. Chcete-li zjistit dostupné hodnoty, viz [odkaz na šablonu](/azure/templates/). |
| jméno |Ano |Název prostředku. Název musí dodržovat omezení komponent URI definovaná v rfc3986. Služby Azure, které zveřejňují název prostředku externím stranám, ověřují název a ujistěte se, že se nejedná o pokus o falšení jiné identity. U podřízeného prostředku závisí formát názvu na tom, zda je vnořený v nadřazeném prostředku nebo definován mimo nadřazený prostředek. Viz [Nastavit název a typ pro podřízené prostředky](child-resource-name-type.md). |
| Komentáře |Ne |Poznámky pro dokumentaci zdrojů v šabloně. Další informace naleznete [v tématu Komentáře v šablonách](template-syntax.md#comments). |
| location |Různé |Podporovaná geografická umístění poskytnutého prostředku. Můžete vybrat libovolné dostupné umístění, ale obvykle má smysl vybrat jedno, které je blízko vašim uživatelům. Obvykle má také smysl umístit prostředky, které vzájemně komunikují ve stejné oblasti. Většina typů prostředků vyžaduje umístění, ale některé typy (například přiřazení role) umístění nevyžadují. Viz [Nastavení umístění prostředků](resource-location.md). |
| dependsOn |Ne |Prostředky, které musí být nasazeny před nasazením tohoto prostředku. Správce prostředků vyhodnocuje závislosti mezi prostředky a nasazuje je ve správném pořadí. Když prostředky nejsou závislé na sobě navzájem, jsou nasazeny paralelně. Hodnotou může být seznam názvů prostředků nebo jedinečných identifikátorů prostředků oddělený čárkami. Seznam pouze prostředky, které jsou nasazeny v této šabloně. Prostředky, které nejsou definovány v této šabloně, již musí existovat. Vyhněte se přidávání zbytečných závislostí, protože mohou zpomalit nasazení a vytvořit cyklické závislosti. Pokyny k nastavení závislostí najdete [v tématu Definování závislostí v šablonách Azure Resource Manageru](define-resource-dependency.md). |
| tags |Ne |Značky, které jsou přidruženy k prostředku. Použijte značky logicky uspořádat prostředky v rámci předplatného. |
| Sku | Ne | Některé prostředky umožňují hodnoty, které definují skladovou položku k nasazení. Můžete například zadat typ redundance pro účet úložiště. |
| Druhu | Ne | Některé prostředky umožňují hodnotu, která definuje typ prostředku, který nasazujete. Můžete například zadat typ Cosmos DB, který chcete vytvořit. |
| copy |Ne |Pokud je potřeba více než jednu instanci, počet prostředků k vytvoření. Výchozí režim je paralelní. Určete sériový režim, pokud nechcete, aby všechny nebo prostředky nasadit současně. Další informace najdete [v tématu Vytvoření několika instancí prostředků ve Správci prostředků Azure](copy-resources.md). |
| Plán | Ne | Některé prostředky umožňují hodnoty, které definují plán nasadit. Můžete například zadat image tržiště pro virtuální počítač. |
| properties |Ne |Nastavení konfigurace specifické pro prostředky. Hodnoty vlastností jsou stejné jako hodnoty, které zadáte v těle požadavku pro operaci rozhraní REST API (PUT metoda) k vytvoření prostředku. Můžete také zadat pole kopírování pro vytvoření několika instancí vlastnosti. Chcete-li zjistit dostupné hodnoty, viz [odkaz na šablonu](/azure/templates/). |
| resources |Ne |Podřízené prostředky, které závisí na definovaném prostředku. Zadejte pouze typy prostředků, které jsou povoleny schématem nadřazeného prostředku. Závislost na nadřazeném prostředku není implicitní. Tuto závislost je nutné explicitně definovat. Viz [Nastavit název a typ pro podřízené prostředky](child-resource-name-type.md). |

## <a name="outputs"></a>Výstupy

V části Výstupy zadáte hodnoty, které jsou vráceny z nasazení. Obvykle vrátíte hodnoty z prostředků, které byly nasazeny.

Následující příklad ukazuje strukturu definice výstupu:

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| Název elementu | Požaduje se | Popis |
|:--- |:--- |:--- |
| název výstupu |Ano |Název výstupní hodnoty. Musí se jednat o platný identifikátor JavaScriptu. |
| Podmínka |Ne | Logická hodnota, která označuje, zda je tato výstupní hodnota vrácena. Když `true`je hodnota zahrnuta do výstupu pro nasazení. Když `false`je výstupní hodnota pro toto nasazení přeskočena. Pokud není zadán, výchozí `true`hodnota je . |
| type |Ano |Typ výstupní hodnoty. Výstupní hodnoty podporují stejné typy jako vstupní parametry šablony. Pokud zadáte **securestring** pro typ výstupu, hodnota se nezobrazí v historii nasazení a nelze načíst z jiné šablony. Chcete-li použít tajnou hodnotu ve více než jedné šabloně, uložte tajný klíč do trezoru klíčů a odkazujte na tajný klíč v souboru parametrů. Další informace naleznete [v tématu Azure Key Vault předat hodnotu zabezpečeného parametru během nasazení](key-vault-parameter.md). |
| value |Ne |Výraz jazyka šablony, který je vyhodnocen a vrácen jako výstupní hodnota. Zadejte **hodnotu** nebo **kopii**. |
| copy |Ne | Slouží k vrácení více než jednu hodnotu pro výstup. Zadejte **hodnotu** nebo **kopii**. Další informace najdete [v tématu Výstupní iterace v šablonách Azure Resource Manager .](copy-outputs.md) |

Příklady použití výstupů najdete [v tématu Výstupy v šabloně Azure Resource Manager](template-outputs.md).

<a id="comments" />

## <a name="comments-and-metadata"></a>Komentáře a metadata

Máte několik možností pro přidání komentářů a metadat do šablony.

### <a name="comments"></a>Komentáře

Pro vložkové komentáře `//` můžete `/* ... */` použít buď nebo ale tato syntaxe nefunguje se všemi nástroji. Editor šablon portálu nelze použít k práci na šablonách s vložkovými komentáři. Pokud přidáte tento styl komentáře, ujistěte se, že nástroje, které používáte podporu vsazené komentáře JSON.

> [!NOTE]
> Chcete-li nasadit šablony s komentáři pomocí azure cli s verzí `--handle-extended-json-format` 2.3.0 nebo starší, musíte použít přepínač.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

V kódu Visual Studia může [rozšíření Nástroje Správce prostředků Azure](use-vs-code-to-create-template.md#install-resource-manager-tools-extension) automaticky rozpoznat šablonu Správce prostředků a odpovídajícím způsobem změnit jazykový režim. Pokud se v pravém dolním rohu kódu VS zobrazí **šablona Azure Resource Manager,** můžete použít včleněné komentáře. Vsazené komentáře již nejsou označeny jako neplatné.

![Režim šablony Správce prostředků Azure kódu Visual Studia](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metadata

`metadata` Objekt můžete přidat téměř kdekoli v šabloně. Správce prostředků objekt ignoruje, ale editor JSON vás může upozornit, že vlastnost není platná. V objektu definujte vlastnosti, které potřebujete.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Pro **parametry**přidejte `metadata` objekt `description` s vlastností.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Při nasazování šablony prostřednictvím portálu se text, který zadáte v popisu, automaticky použije jako tip pro tento parametr.

![Zobrazit tip na parametry](./media/template-syntax/show-parameter-tip.png)

Pro **prostředky**přidejte `comments` element nebo objekt metadat. Následující příklad ukazuje prvek komentáře a objekt metadat.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
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

Do uživatelem definovaných funkcí nelze přidat objekt metadat.

## <a name="multi-line-strings"></a>Víceřádkové řetězce

Řetězec můžete rozdělit na více řádků. Například viz vlastnost umístění a jeden z komentářů v následujícím příkladu JSON.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Chcete-li nasadit šablony s víceřádkovými řetězci pomocí azure cli s verzí `--handle-extended-json-format` 2.3.0 nebo starší, musíte použít přepínač.

## <a name="next-steps"></a>Další kroky

* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
* Podrobnosti o funkcích, které můžete použít v rámci šablony, najdete v [tématu Funkce šablony Správce prostředků Azure](template-functions.md).
* Pokud chcete během nasazení zkombinovat několik šablon, přečtěte si informace [o použití propojených šablon ve Správci prostředků Azure](linked-templates.md).
* Doporučení týkající se vytváření šablon najdete v [tématu Doporučené postupy šablony Správce prostředků Azure](template-best-practices.md).
* Doporučení k vytváření šablon Správce prostředků, které můžete použít ve všech prostředích Azure a Azure Stack, najdete [v tématu Vývoj šablon Azure Resource Manager pro konzistenci cloudu](templates-cloud-consistency.md).
