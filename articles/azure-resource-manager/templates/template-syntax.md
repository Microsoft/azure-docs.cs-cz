---
title: Struktura a syntaxe šablon
description: Popisuje strukturu a vlastnosti šablon Azure Resource Manager pomocí deklarativní syntaxe JSON.
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: ae2c5a5fe1440c3adbae475cd4c7652a3b01c285
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86116535"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>Vysvětlení struktury a syntaxe šablon ARM

Tento článek popisuje strukturu šablony Azure Resource Manager (ARM). Zobrazuje různé oddíly šablony a vlastnosti, které jsou k dispozici v těchto oddílech.

Tento článek je určený pro uživatele, kteří mají zkušenosti se šablonami ARM. Poskytuje podrobné informace o struktuře šablony. Podrobný kurz, který vás provede procesem vytvoření šablony, najdete v tématu [kurz: vytvoření a nasazení první šablony Azure Resource Manager](template-tutorial-create-first-template.md).

## <a name="template-format"></a>Formát šablon

V nejjednodušší struktuře má šablona následující prvky:

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

| Název elementu | Povinné | Popis |
|:--- |:--- |:--- |
| $schema |Yes |Umístění souboru schématu JSON, který popisuje verzi jazyka šablony. Použité číslo verze závisí na rozsahu nasazení a editoru JSON.<br><br>Pokud používáte [vs Code s rozšířením Azure Resource Manager Tools](quickstart-create-templates-use-visual-studio-code.md), použijte nejnovější verzi pro nasazení skupin prostředků:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Jiné editory (včetně sady Visual Studio) nemusí být schopné zpracovat toto schéma. Pro tyto editory použijte:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Pro nasazení předplatných použijte:<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>Pro nasazení skupin pro správu použijte:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>Pro nasazení klientů použijte:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| Contentversion – |Yes |Verze šablony (například 1.0.0.0). Pro tento prvek můžete zadat libovolnou hodnotu. Tuto hodnotu použijte k dokumentování významných změn v šabloně. Při nasazování prostředků pomocí šablony můžete tuto hodnotu použít k tomu, abyste se ujistili, že je používána pravá šablona. |
| apiProfile |No | Verze rozhraní API, která slouží jako kolekce verzí rozhraní API pro typy prostředků. Tuto hodnotu použijte, chcete-li se vyhnout nutnosti zadávat verze rozhraní API pro každý prostředek v šabloně. Když zadáte verzi profilu rozhraní API a nezadáte verzi rozhraní API pro typ prostředku, Správce prostředků používá verzi rozhraní API pro tento typ prostředku, který je definovaný v profilu.<br><br>Vlastnost profil rozhraní API je užitečná hlavně při nasazení šablony do různých prostředí, jako je Azure Stack a globální Azure. Pomocí verze profilu rozhraní API se ujistěte, že vaše šablona automaticky používá verze, které jsou v obou prostředích podporované. Seznam aktuálních verzí profilů rozhraní API a verzí rozhraní API prostředků definovaných v profilu najdete v tématu [profil rozhraní API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Další informace najdete v tématu [sledování verzí pomocí profilů rozhraní API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [ukazatelů](#parameters) |No |Hodnoty, které jsou k dispozici při spuštění nasazení za účelem přizpůsobení nasazení prostředků. |
| [proměnné](#variables) |No |Hodnoty, které se používají jako fragmenty JSON v šabloně pro zjednodušení výrazů jazyka šablony. |
| [POZVYHLEDAT](#functions) |No |Uživatelsky definované funkce, které jsou k dispozici v rámci šablony. |
| [prostředky](#resources) |Yes |Typy prostředků, které se nasazují nebo aktualizují v rámci skupiny prostředků nebo předplatného. |
| [činnosti](#outputs) |No |Hodnoty, které se vrátí po nasazení. |

Každý prvek má vlastnosti, které lze nastavit. V tomto článku jsou podrobněji popsány části šablony.

## <a name="parameters"></a>Parametry

V oddílu parametrs dané šablony určíte, které hodnoty můžete při nasazování prostředků zadat. V šabloně můžete zadat jen 256 parametrů. Počet parametrů můžete snížit pomocí objektů, které obsahují více vlastností.

Následují dostupné vlastnosti pro parametr:

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

| Název elementu | Povinné | Popis |
|:--- |:--- |:--- |
| název parametru |Yes |Název parametru Musí být platný identifikátor JavaScriptu. |
| typ |Yes |Typ hodnoty parametru Povolené typy a hodnoty jsou **String**, **SecureString**, **int**, **bool**, **Object**, **secureObject**a **Array**. Podívejte se na [datové typy](#data-types). |
| Hodnot |No |Výchozí hodnota parametru, pokud není k dispozici žádná hodnota pro parametr. |
| allowedValues |No |Pole povolených hodnot pro parametr, aby bylo zajištěno, že je zadána pravá hodnota. |
| minValue |No |Minimální hodnota pro parametry typu int je tato hodnota včetně. |
| maxValue |No |Maximální hodnota pro parametry typu int je tato hodnota včetně. |
| minLength |No |Minimální délka parametrů pro řetězec, zabezpečený řetězec a typ pole je hodnota včetně. |
| maxLength |No |Maximální délka parametrů pro řetězec, zabezpečený řetězec a typ pole je hodnota včetně. |
| Popis |No |Popis parametru, který se uživatelům zobrazí prostřednictvím portálu. Další informace najdete v tématu [komentáře v šablonách](#comments). |

Příklady použití parametrů naleznete [v tématu Parameters in Azure Resource Manager Templates](template-parameters.md).

### <a name="data-types"></a>Typy dat

Pro celá čísla předaná jako vložené parametry může být rozsah hodnot omezen sadou SDK nebo nástrojem příkazového řádku, který používáte pro nasazení. Například při použití prostředí PowerShell k nasazení šablony mohou být typy celého čísla v rozsahu od-2147483648 do 2147483647. Chcete-li se tomuto omezení vyhnout, zadejte v [souboru parametrů](parameter-files.md)velké celočíselné hodnoty. Typy prostředků použijí vlastní omezení pro celočíselné vlastnosti.

Při zadávání logických a celočíselných hodnot v šabloně se hodnota neobklopuje pomocí uvozovek. Počáteční a koncové řetězcové hodnoty pomocí dvojitých uvozovek.

Objekty začínají levou závorkou a končí pravou závorkou. Pole začínají levou hranatou závorkou a končí pravou závorkou.

Když nastavíte parametr na zabezpečený řetězec nebo zabezpečený objekt, hodnota parametru se neuloží do historie nasazení a nezaprotokoluje se. Nicméně pokud nastavíte tuto zabezpečenou hodnotu na vlastnost, která neočekává zabezpečenou hodnotu, hodnota není chráněná. Například pokud nastavíte zabezpečený řetězec na značku, tato hodnota je uložena jako prostý text. Používejte zabezpečené řetězce pro hesla a tajné kódy.

Ukázky formátování datových typů naleznete v tématu [formáty typu parametru](parameter-files.md#parameter-type-formats).

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

Informace o použití `copy` pro vytvoření několika hodnot proměnné naleznete v tématu [Variable iterace](copy-variables.md).

Příklady použití proměnných naleznete [v tématu proměnné v šabloně Azure Resource Manager](template-variables.md).

## <a name="functions"></a>Funkce

V rámci šablony můžete vytvořit vlastní funkce. Tyto funkce jsou k dispozici pro použití ve vaší šabloně. Obvykle definujete složité výrazy, které nechcete opakovat v celé šabloně. Můžete vytvořit uživatelsky definované funkce z výrazů a [funkcí](template-functions.md) , které jsou podporovány v šablonách.

Při definování uživatelské funkce existují určitá omezení:

* Funkce nemá přístup k proměnným.
* Funkce může používat pouze parametry, které jsou definovány ve funkci. Použijete-li [funkci Parameters](template-functions-deployment.md#parameters) v rámci uživatelsky definované funkce, budete omezeni na parametry této funkce.
* Funkce nemůže volat jiné uživatelsky definované funkce.
* Funkce nemůže používat [odkazovou funkci](template-functions-resource.md#reference).
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

| Název elementu | Povinné | Popis |
|:--- |:--- |:--- |
| namespace |Yes |Obor názvů pro vlastní funkce Použijte k zamezení konfliktu názvů s funkcemi šablon. |
| název funkce |Yes |Název vlastní funkce Při volání funkce kombinovat název funkce s oborem názvů. Například pro volání funkce s názvem uniqueName v oboru názvů contoso, použijte `"[contoso.uniqueName()]"` . |
| název parametru |No |Název parametru, který se má použít v rámci vlastní funkce |
| hodnota parametru |No |Typ hodnoty parametru Povolené typy a hodnoty jsou **String**, **SecureString**, **int**, **bool**, **Object**, **secureObject**a **Array**. |
| výstupní typ |Yes |Typ výstupní hodnoty. Výstupní hodnoty podporují stejné typy jako vstupní parametry funkce. |
| výstupní hodnota |Yes |Výraz jazyka šablony, který je vyhodnocen a vrácen z funkce. |

Příklady použití vlastních funkcí naleznete [v tématu uživatelsky definované funkce v šabloně Azure Resource Manager](template-user-defined-functions.md).

## <a name="resources"></a>Zdroje a prostředky

V části Resources (prostředky) definujete prostředky, které jsou nasazené nebo aktualizované.

Provedete definování prostředků s následující strukturou:

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

| Název elementu | Povinné | Popis |
|:--- |:--- |:--- |
| pomocné | No | Logická hodnota, která označuje, jestli se prostředek zřídí během tohoto nasazení. Kdy se `true` prostředek vytvoří během nasazování. Kdy se `false` prostředek pro toto nasazení přeskočí. Zobrazit [podmínku](conditional-resource-deployment.md). |
| typ |Yes |Typ prostředku. Tato hodnota je kombinací oboru názvů poskytovatele prostředků a typu prostředku (například **Microsoft. Storage/storageAccounts**). Chcete-li zjistit dostupné hodnoty, přečtěte si téma [Reference k šabloně](/azure/templates/). U podřízených prostředků závisí formát typu na tom, jestli je vnořený v nadřazeném prostředku nebo definovaný mimo nadřazený prostředek. Viz [Nastavení názvu a typu pro podřízené prostředky](child-resource-name-type.md). |
| apiVersion |Yes |Verze REST API, která se má použít k vytvoření prostředku Chcete-li zjistit dostupné hodnoty, přečtěte si téma [Reference k šabloně](/azure/templates/). |
| name |Yes |Název prostředku. Název musí splňovat omezení součásti identifikátoru URI definovaná v RFC3986. Služby Azure, které zveřejňují název prostředku mimo jiné, ověřují název, aby se ujistil, že se nejedná o pokus o falšování jiné identity. U podřízeného prostředku formát názvu závisí na tom, jestli je vnořený v nadřazeném prostředku nebo definovaný mimo nadřazený prostředek. Viz [Nastavení názvu a typu pro podřízené prostředky](child-resource-name-type.md). |
| vyjádření |No |Poznámky k dokumentaci prostředků ve vaší šabloně. Další informace najdete v tématu [komentáře v šablonách](template-syntax.md#comments). |
| location |Různé |Podporovaná geografická umístění poskytnutého prostředku Můžete vybrat kterékoli z dostupných umístění, ale obvykle dává smysl vybrat, která je blízko vašim uživatelům. Obvykle má smysl umístit prostředky, které vzájemně spolupracují ve stejné oblasti. Většina typů prostředků vyžaduje umístění, ale některé typy (například přiřazení role) nevyžadují umístění. Viz [Nastavení umístění prostředku](resource-location.md). |
| dependsOn |No |Prostředky, které musí být nasazeny před nasazením tohoto prostředku. Správce prostředků vyhodnocuje závislosti mezi prostředky a nasadí je ve správném pořadí. Pokud nejsou prostředky vzájemně závislé, nasadí se paralelně. Hodnota může být čárkami oddělený seznam názvů prostředků nebo jedinečných identifikátorů prostředků. Pouze seznam prostředků, které jsou nasazeny v této šabloně. Prostředky, které nejsou definované v této šabloně, už musí existovat. Vyhněte se přidávání zbytečných závislostí, protože mohou zpomalit nasazení a vytvářet cyklické závislosti. Pokyny k nastavení závislostí najdete v tématu [Definování závislostí v šablonách Azure Resource Manager](define-resource-dependency.md). |
| tags |No |Značky, které jsou přidruženy k prostředku. Použijte značky pro logickou organizaci prostředků v rámci vašeho předplatného. |
| skladové | No | Některé prostředky umožňují hodnoty definující SKU, které se mají nasadit. Můžete například zadat typ redundance pro účet úložiště. |
| plnění | No | Některé prostředky umožňují hodnotu definující typ prostředku, který nasadíte. Můžete například zadat typ Cosmos DB, který se má vytvořit. |
| kopírování |No |Pokud je potřeba více než jedna instance, počet prostředků, které se mají vytvořit. Výchozí režim je paralelní. Zadejte sériový režim, pokud nechcete, aby se nasadily všechny nebo prostředky. Další informace najdete v tématu [vytvoření několika instancí prostředků v Azure Resource Manager](copy-resources.md). |
| rozhraní | No | Některé prostředky umožňují hodnoty definující plán, který se má nasadit. Můžete například zadat image Marketplace pro virtuální počítač. |
| properties |No |Nastavení konfigurace specifické pro prostředky. Hodnoty vlastností jsou stejné jako hodnoty, které zadáte v textu žádosti pro operaci REST API (metoda PUT) pro vytvoření prostředku. Můžete také zadat pole pro kopírování a vytvořit několik instancí vlastnosti. Chcete-li zjistit dostupné hodnoty, přečtěte si téma [Reference k šabloně](/azure/templates/). |
| resources |No |Podřízené prostředky závislé na definovaném prostředku. Poskytněte jenom typy prostředků, které jsou povolené schématem nadřazeného prostředku. Nepředpokládá se závislost na nadřazeném prostředku. Tuto závislost musíte explicitně definovat. Viz [Nastavení názvu a typu pro podřízené prostředky](child-resource-name-type.md). |

## <a name="outputs"></a>Výstupy

V části výstupy určíte hodnoty, které se vrátí z nasazení. Obvykle vracíte hodnoty z nasazených prostředků.

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

| Název elementu | Povinné | Popis |
|:--- |:--- |:--- |
| Název výstupu |Yes |Název výstupní hodnoty. Musí být platný identifikátor JavaScriptu. |
| pomocné |No | Logická hodnota, která označuje, zda je vrácena tato výstupní hodnota. Když `true` je hodnota obsažena ve výstupu pro nasazení. `false`V případě je výstupní hodnota pro toto nasazení vynechána. Není-li zadána, je použita výchozí hodnota `true` . |
| typ |Yes |Typ výstupní hodnoty. Výstupní hodnoty podporují stejné typy jako vstupní parametry šablony. Pokud zadáte **SecureString** pro typ výstupu, hodnota se nezobrazí v historii nasazení a nelze ji načíst z jiné šablony. Chcete-li použít tajnou hodnotu ve více než jedné šabloně, uložte tajný klíč do Key Vault a odkazujte na tajný kód v souboru parametrů. Další informace najdete v tématu [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](key-vault-parameter.md). |
| value |No |Výraz jazyka šablony, který je vyhodnocen a vrácen jako výstupní hodnota. Zadejte buď **hodnotu** , nebo **kopii**. |
| kopírování |No | Slouží k vrácení více než jedné hodnoty pro výstup. Zadejte **hodnotu** nebo **zkopírujte**. Další informace naleznete v tématu [výstupní iterace v šablonách Azure Resource Manager](copy-outputs.md). |

Příklady použití výstupů najdete [v tématu výstupy v šabloně Azure Resource Manager](template-outputs.md).

<a id="comments"></a>

## <a name="comments-and-metadata"></a>Komentáře a metadata

Máte několik možností, jak přidat komentáře a metadata do šablony.

### <a name="comments"></a>Komentáře

Pro vložené komentáře můžete použít buď nebo, `//` `/* ... */` ale tato syntaxe nefunguje u všech nástrojů. Pokud přidáte tento styl komentáře, ujistěte se, že nástroje, které používáte, podporují vložené komentáře JSON.

> [!NOTE]
> Chcete-li nasadit šablony s komentáři pomocí Azure CLI s verzí 2.3.0 nebo starší, je nutné použít `--handle-extended-json-format` přepínač.

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

V Visual Studio Code může [rozšíření Azure Resource Manager nástrojů](quickstart-create-templates-use-visual-studio-code.md) automaticky detekovat šablonu správce prostředků a odpovídajícím způsobem změnit jazykový režim. Pokud vidíte **Azure Resource Manager šablonu** v pravém dolním rohu vs Code, můžete použít vložené komentáře. Vložené komentáře již nejsou označeny jako neplatné.

![Režim šablony Visual Studio Code Azure Resource Manager](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metadata

Můžete přidat `metadata` objekt skoro kdekoli v šabloně. Správce prostředků objekt ignoruje, ale editor JSON vám může zobrazit upozornění, že vlastnost není platná. V objektu definujte vlastnosti, které potřebujete.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Pro **parametry**přidejte `metadata` objekt s `description` vlastností.

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

![Zobrazit Tip parametru](./media/template-syntax/show-parameter-tip.png)

Pro **prostředky**přidejte `comments` prvek nebo objekt metadat. Následující příklad ukazuje jak element Comments, tak objekt metadat.

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

Do uživatelsky definovaných funkcí nemůžete přidat objekt metadat.

## <a name="multi-line-strings"></a>Víceřádkové řetězce

Řetězec můžete rozdělit na více řádků. Podívejte se například na vlastnost Location a jeden z komentářů v následujícím příkladu JSON.

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

Chcete-li nasadit šablony s víceřádkovými řetězci pomocí rozhraní příkazového řádku Azure s verzí 2.3.0 nebo starší, je nutné použít `--handle-extended-json-format` přepínač.

## <a name="next-steps"></a>Další kroky

* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
* Podrobnosti o funkcích, které můžete použít v rámci šablony, naleznete v tématu [Azure Resource Manager Functions Template](template-functions.md).
* Pokud chcete zkombinovat několik šablon během nasazování, přečtěte si téma [použití propojených šablon s Azure Resource Manager](linked-templates.md).
* Doporučení k vytváření šablon najdete v tématu [osvědčené postupy pro šablonu Azure Resource Manager](template-best-practices.md).
* Odpovědi na běžné otázky najdete v tématu [Nejčastější dotazy k šablonám ARM](frequently-asked-questions.md).
