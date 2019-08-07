---
title: Azure Resource Manager struktura a syntaxe šablony | Microsoft Docs
description: Popisuje strukturu a vlastnosti šablon Azure Resource Manager pomocí deklarativní syntaxe JSON.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: tomfitz
ms.openlocfilehash: 9858e8a52888304edd48893db02faa992b356b3b
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774907"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Pochopení struktury a syntaxe šablon Azure Resource Manager

Tento článek popisuje strukturu Azure Resource Manager šablony. Zobrazuje různé oddíly šablony a vlastnosti, které jsou k dispozici v těchto oddílech. Šablona se skládá z formátu JSON a výrazů, které můžete použít k sestavení hodnot pro vaše nasazení.

Tento článek je určený pro uživatele, kteří mají určitou znalost Správce prostředků šablon. Poskytuje podrobné informace o struktuře a syntaxi šablony. Pokud chcete vytvořit úvodní šablonu, přečtěte si téma [Vytvoření první šablony Azure Resource Manager](resource-manager-create-first-template.md).

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
| $schema |Ano |Umístění souboru schématu JSON, který popisuje verzi jazyka šablony.<br><br> Pro nasazení skupin prostředků použijte:`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Pro nasazení předplatných použijte:`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Ano |Verze šablony (například 1.0.0.0). Pro tento prvek můžete zadat libovolnou hodnotu. Tuto hodnotu použijte k dokumentování významných změn v šabloně. Při nasazování prostředků pomocí šablony můžete tuto hodnotu použít k tomu, abyste se ujistili, že je používána pravá šablona. |
| apiProfile |Ne | Verze rozhraní API, která slouží jako kolekce verzí rozhraní API pro typy prostředků. Tuto hodnotu použijte, chcete-li se vyhnout nutnosti zadávat verze rozhraní API pro každý prostředek v šabloně. Když zadáte verzi profilu rozhraní API a nezadáte verzi rozhraní API pro typ prostředku, Správce prostředků používá verzi rozhraní API pro tento typ prostředku, který je definovaný v profilu.<br><br>Vlastnost profil rozhraní API je užitečná hlavně při nasazení šablony do různých prostředí, jako je Azure Stack a globální Azure. Pomocí verze profilu rozhraní API se ujistěte, že vaše šablona automaticky používá verze, které jsou v obou prostředích podporované. Seznam aktuálních verzí profilů rozhraní API a verzí rozhraní API prostředků definovaných v profilu najdete v tématu [profil rozhraní API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Další informace najdete v tématu [sledování verzí pomocí profilů rozhraní API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Ne |Hodnoty, které jsou k dispozici při spuštění nasazení za účelem přizpůsobení nasazení prostředků. |
| [variables](#variables) |Ne |Hodnoty, které se používají jako fragmenty JSON v šabloně pro zjednodušení výrazů jazyka šablony. |
| [functions](#functions) |Ne |Uživatelsky definované funkce, které jsou k dispozici v rámci šablony. |
| [resources](#resources) |Ano |Typy prostředků, které se nasazují nebo aktualizují v rámci skupiny prostředků nebo předplatného. |
| [outputs](#outputs) |Ne |Hodnoty, které se vrátí po nasazení. |

Každý prvek má vlastnosti, které lze nastavit. V tomto článku jsou podrobněji popsány části šablony.

## <a name="syntax"></a>Syntaxe

Základní syntaxí šablony je JSON. Můžete však použít výrazy k rozšiřování hodnot JSON dostupných v rámci šablony.  Výrazy začínají a končí závorkami: `[` a `]`v uvedeném pořadí. Hodnota výrazu je vyhodnocena při nasazení šablony. Výraz může vracet řetězec, celé číslo, logickou hodnotu, pole nebo objekt. Následující příklad ukazuje výraz ve výchozí hodnotě parametru:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Syntaxe `resourceGroup()` v rámci výrazu volá jednu z funkcí, které správce prostředků poskytují pro použití v rámci šablony. Stejně jako v jazyce JavaScript jsou volání funkcí formátována `functionName(arg1,arg2,arg3)`jako. Syntaxe `.location` načte jednu vlastnost z objektu vráceného touto funkcí.

Funkce šablon a jejich parametry rozlišují velká a malá písmena. Například Správce prostředků vyřeší **proměnné (' var1 ')** a **proměnné (' var1 ')** jako stejné. Je-li tato funkce vyhodnocena, pokud funkce Express nemění velká a malá písmena (například toUpper nebo toLower), funkce zachovává případ. Některé typy prostředků mohou mít požadavky na případy bez ohledu na to, jak jsou funkce vyhodnoceny.

Chcete-li, aby byl řetězcový literál začínat levou hranatou `[` závorkou `]`a končit pravou závorkou, ale nebyl interpretován jako výraz, přidejte další hranatou závorku, `[[`která zahájí řetězec s. Například proměnná:

```json
"demoVar1": "[[test value]"
```

Přeloží `[test value]`na.

Nicméně pokud literální řetězec nekončí závorkou, nezařídí první vymezovač. Například proměnná:

```json
"demoVar2": "[test] value"
```

Přeloží `[test] value`na.

Chcete-li předat řetězcovou hodnotu jako parametr funkci, použijte jednoduché uvozovky.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

Chcete-li ve výrazu, jako je například přidání objektu JSON do šablony, řídicí dvojité uvozovky, použijte zpětné lomítko.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Výraz šablony nemůže být delší než 24 576 znaků.

Úplný seznam funkcí šablon naleznete v tématu [Azure Resource Manager Functions Template](resource-group-template-functions.md). 

## <a name="parameters"></a>Parametry

V části Parameters (parametry) v šabloně určíte, které hodnoty můžete zadat při nasazování prostředků. Tyto hodnoty parametrů umožňují přizpůsobit nasazení poskytnutím hodnot, které jsou upraveny pro konkrétní prostředí (například vývoj, testování a produkce). V šabloně není nutné zadávat parametry, ale bez parametrů vaše šablona by vždy nasadila stejné prostředky se stejnými názvy, umístěními a vlastnostmi.

V šabloně budete omezeni na 256 parametrů. Počet parametrů můžete snížit pomocí objektů, které obsahují více vlastností, jak je znázorněno v tomto článku.

### <a name="available-properties"></a>Dostupné vlastnosti

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
| parameterName |Ano |Název parametru Musí být platný identifikátor jazyka JavaScript. |
| type |Ano |Typ hodnoty parametru Povolené typy a hodnoty jsou **String**, **SecureString**, **int**, **bool**, **Object**, **secureObject**a **Array**. |
| defaultValue |Ne |Výchozí hodnota parametru, pokud není k dispozici žádná hodnota pro parametr. |
| allowedValues |Ne |Pole povolených hodnot pro parametr, aby bylo zajištěno, že je zadána pravá hodnota. |
| minValue |Ne |Minimální hodnota pro parametry typu int je tato hodnota včetně. |
| maxValue |Ne |Maximální hodnota pro parametry typu int je tato hodnota včetně. |
| minLength |Ne |Minimální délka parametrů pro řetězec, zabezpečený řetězec a typ pole je hodnota včetně. |
| maxLength |Ne |Maximální délka parametrů pro řetězec, zabezpečený řetězec a typ pole je hodnota včetně. |
| description |Ne |Popis parametru, který se uživatelům zobrazí prostřednictvím portálu. Další informace najdete v tématu [komentáře v šablonách](#comments). |

### <a name="define-and-use-a-parameter"></a>Definování a použití parametru

V následujícím příkladu je uvedena definice jednoduchého parametru. Definuje název parametru a určuje, že přebírá řetězcovou hodnotu. Parametr přijímá pouze hodnoty, které mají smysl pro zamýšlené použití. Určuje výchozí hodnotu, pokud není během nasazení zadána žádná hodnota. Nakonec parametr obsahuje popis jeho použití.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

V šabloně odkazujete na hodnotu parametru s následující syntaxí:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

### <a name="template-functions-with-parameters"></a>Funkce šablon s parametry

Když zadáte výchozí hodnotu pro parametr, můžete použít většinu funkcí šablon. K vytvoření výchozí hodnoty můžete použít jinou hodnotu parametru. Následující šablona ukazuje použití funkcí ve výchozí hodnotě:

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

`reference` Funkci nelze použít v oddílu Parameters. Parametry jsou vyhodnocovány před nasazením `reference` , takže funkce nemůže získat běhový stav prostředku. 

### <a name="objects-as-parameters"></a>Objekty jako parametry

Můžete snadněji organizovat související hodnoty jejich předáním v podobě objektu. Tento přístup také snižuje počet parametrů v šabloně.

Definujte parametr v šabloně a místo jedné hodnoty během nasazení zadejte objekt JSON. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Potom odkazujte na podvlastnosti parametru pomocí operátoru tečka.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

### <a name="parameter-example-templates"></a>Příklady šablon parametrů

Tyto příklady šablon předvádějí některé scénáře použití parametrů. Nasaďte je pro testování způsobu zpracování parametrů v různých scénářích.

|Šablona  |Popis  |
|---------|---------|
|[parametry s funkcemi pro výchozí hodnoty](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Ukazuje, jak používat funkce šablon při definování výchozích hodnot pro parametry. Šablona neimplementuje žádné prostředky. Vytvoří hodnoty parametrů a vrátí tyto hodnoty. |
|[objekt parametru](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Ukazuje použití objektu pro parametr. Šablona neimplementuje žádné prostředky. Vytvoří hodnoty parametrů a vrátí tyto hodnoty. |

## <a name="variables"></a>Proměnné

V části proměnné můžete vytvářet hodnoty, které lze použít v rámci šablony. Nemusíte definovat proměnné, ale často zjednodušují vaši šablonu tím, že snižují složité výrazy.

### <a name="available-definitions"></a>Dostupné definice

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

Informace o použití `copy` pro vytvoření několika hodnot proměnné naleznete v tématu [Variable iterace](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Definování a použití proměnné

Následující příklad ukazuje definici proměnné. Vytvoří hodnotu řetězce pro název účtu úložiště. Používá několik funkcí šablon k získání hodnoty parametru a zřetězuje je do jedinečného řetězce.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Proměnnou použijete při definování prostředku.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>Konfigurační proměnné

Pomocí komplexních typů JSON můžete definovat související hodnoty pro prostředí.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

V parametrech vytvoříte hodnotu, která určuje, které hodnoty konfigurace se mají použít.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Aktuální nastavení načtete pomocí:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>Příklady šablon proměnných

Tyto příklady šablon předvádějí některé scénáře použití proměnných. Nasaďte je pro testování, jak jsou proměnné zpracovávány v různých scénářích. 

|Šablona  |Popis  |
|---------|---------|
| [Definice proměnných](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Ukazuje různé typy proměnných. Šablona neimplementuje žádné prostředky. Vytvoří proměnné hodnoty a vrátí tyto hodnoty. |
| [konfigurační proměnná](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Ukazuje použití proměnné definující konfigurační hodnoty. Šablona neimplementuje žádné prostředky. Vytvoří proměnné hodnoty a vrátí tyto hodnoty. |
| [pravidla zabezpečení sítě](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) a [soubor parametrů](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Sestaví pole ve správném formátu pro přiřazení pravidel zabezpečení do skupiny zabezpečení sítě. |


## <a name="functions"></a>Funkce

V rámci šablony můžete vytvořit vlastní funkce. Tyto funkce jsou k dispozici pro použití ve vaší šabloně. Obvykle definujete složitý výraz, který nechcete opakovat v rámci šablony. Můžete vytvořit uživatelsky definované funkce z výrazů a [funkcí](resource-group-template-functions.md) , které jsou podporovány v šablonách.

Při definování uživatelské funkce existují určitá omezení:

* Funkce nemá přístup k proměnným.
* Funkce může používat pouze parametry, které jsou definovány ve funkci. Použijete-li [funkci Parameters](resource-group-template-functions-deployment.md#parameters) v rámci uživatelsky definované funkce, budete omezeni na parametry této funkce.
* Funkce nemůže volat jiné uživatelsky definované funkce.
* Funkce nemůže používat odkazovou [funkci](resource-group-template-functions-resource.md#reference).
* Parametry pro funkci nemohou mít výchozí hodnoty.

Vaše funkce vyžadují hodnotu oboru názvů, aby se zabránilo konfliktům názvů s funkcemi šablon. Následující příklad ukazuje funkci, která vrací název účtu úložiště:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

Funkci zavoláte pomocí:

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>Zdroje a prostředky
V části Resources (prostředky) definujete prostředky, které jsou nasazené nebo aktualizované.

### <a name="available-properties"></a>Dostupné vlastnosti

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
| condition | Ne | Logická hodnota, která označuje, jestli se prostředek zřídí během tohoto nasazení. Kdy `true`se prostředek vytvoří během nasazování. Kdy `false`se prostředek pro toto nasazení přeskočí. Zobrazit [podmínku](#condition). |
| apiVersion |Ano |Verze REST API, která se má použít k vytvoření prostředku Chcete-li zjistit dostupné hodnoty, přečtěte si téma [Reference k šabloně](/azure/templates/). |
| type |Ano |Typ prostředku. Tato hodnota je kombinací oboru názvů poskytovatele prostředků a typu prostředku (například **Microsoft. Storage/storageAccounts**). Chcete-li zjistit dostupné hodnoty, přečtěte si téma [Reference k šabloně](/azure/templates/). U podřízených prostředků závisí formát typu na tom, jestli je vnořený v nadřazeném prostředku nebo definovaný mimo nadřazený prostředek. Viz [Nastavení názvu a typu pro podřízené prostředky](child-resource-name-type.md). |
| name |Ano |Název prostředku. Název musí splňovat omezení součásti identifikátoru URI definovaná v RFC3986. Kromě toho služby Azure, které zveřejňují název prostředku mimo jiné, ověřují název, abyste se ujistili, že se nejedná o pokus o falšování jiné identity. U podřízeného prostředku formát názvu závisí na tom, jestli je vnořený v nadřazeném prostředku nebo definovaný mimo nadřazený prostředek. Viz [Nastavení názvu a typu pro podřízené prostředky](child-resource-name-type.md). |
| location |Různé |Podporovaná geografická umístění poskytnutého prostředku Můžete vybrat kterékoli z dostupných umístění, ale obvykle dává smysl vybrat, která je blízko vašim uživatelům. Obvykle má smysl umístit prostředky, které vzájemně spolupracují ve stejné oblasti. Většina typů prostředků vyžaduje umístění, ale některé typy (například přiřazení role) nevyžadují umístění. |
| značky |Ne |Značky, které jsou přidruženy k prostředku. Použijte značky pro logickou organizaci prostředků v rámci vašeho předplatného. |
| vyjádření |Ne |Poznámky k dokumentaci prostředků ve vaší šabloně. Další informace najdete v tématu [komentáře v šablonách](resource-group-authoring-templates.md#comments). |
| Kopírovat |Ne |Pokud je potřeba více než jedna instance, počet prostředků, které se mají vytvořit. Výchozí režim je paralelní. Zadejte sériový režim, pokud nechcete, aby se nasadily všechny nebo prostředky. Další informace najdete v tématu [vytvoření několika instancí prostředků v Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Ne |Prostředky, které musí být nasazeny před nasazením tohoto prostředku. Správce prostředků vyhodnocuje závislosti mezi prostředky a nasadí je ve správném pořadí. Pokud nejsou prostředky vzájemně závislé, nasadí se paralelně. Hodnota může být čárkami oddělený seznam názvů prostředků nebo jedinečných identifikátorů prostředků. Pouze seznam prostředků, které jsou nasazeny v této šabloně. Prostředky, které nejsou definované v této šabloně, už musí existovat. Vyhněte se přidávání zbytečných závislostí, protože mohou zpomalit nasazení a vytvářet cyklické závislosti. Pokyny k nastavení závislostí najdete v tématu [Definování závislostí v šablonách Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |Ne |Nastavení konfigurace specifické pro prostředky. Hodnoty vlastností jsou stejné jako hodnoty, které zadáte v textu žádosti pro operaci REST API (metoda PUT) pro vytvoření prostředku. Můžete také zadat pole pro kopírování a vytvořit několik instancí vlastnosti. Chcete-li zjistit dostupné hodnoty, přečtěte si téma [Reference k šabloně](/azure/templates/). |
| SKU | Ne | Některé prostředky umožňují hodnoty definující SKU, které se mají nasadit. Můžete například zadat typ redundance pro účet úložiště. |
| plnění | Ne | Některé prostředky umožňují hodnotu definující typ prostředku, který nasadíte. Můžete například zadat typ Cosmos DB, který se má vytvořit. |
| rozhraní | Ne | Některé prostředky umožňují hodnoty definující plán, který se má nasadit. Můžete například zadat image Marketplace pro virtuální počítač. | 
| prostředky |Ne |Podřízené prostředky závislé na definovaném prostředku. Poskytněte jenom typy prostředků, které jsou povolené schématem nadřazeného prostředku. Nepředpokládá se závislost na nadřazeném prostředku. Tuto závislost musíte explicitně definovat. Viz [Nastavení názvu a typu pro podřízené prostředky](child-resource-name-type.md). |

### <a name="condition"></a>Podmínka

V `condition` případě, že je nutné se při nasazení rozhodnout, zda chcete vytvořit prostředek, použijte element. Hodnota pro tento prvek je přeložena na hodnotu true nebo false. Pokud je hodnota true, je prostředek vytvořen. Pokud je hodnota false, prostředek se nevytvoří. Hodnota se dá použít jenom u celého prostředku.

Obvykle tuto hodnotu použijete, pokud chcete vytvořit nový prostředek nebo použít existující. Pokud třeba chcete určit, jestli se má nový účet úložiště nasadit nebo použít existující účet úložiště, použijte:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Kompletní příklad šablony, která používá `condition` element, najdete v tématu [virtuální počítač s novým nebo existujícím Virtual Network, úložištěm a veřejnou IP adresou](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

Použijete-li funkci [reference](resource-group-template-functions-resource.md#reference) nebo [list](resource-group-template-functions-resource.md#list) s prostředkem, který je podmíněně nasazen, je funkce vyhodnocena i v případě, že prostředek není nasazen. Pokud funkce odkazuje na prostředek, který neexistuje, zobrazí se chyba. Použijte funkci [if](resource-group-template-functions-logical.md#if) a ujistěte se, že je funkce vyhodnocena pouze pro podmínky při nasazení prostředku. Podívejte se na [funkci IF](resource-group-template-functions-logical.md#if) pro ukázkovou šablonu, která používá if a odkaz s podmíněně nasazeným prostředkem.

### <a name="resource-names"></a>Názvy prostředků

Obecně pracujete se třemi typy názvů prostředků v Správce prostředků:

* Názvy prostředků, které musí být jedinečné.
* Názvy prostředků, které nejsou nutné, aby byly jedinečné, ale je třeba zadat název, který vám může poznat prostředek identifikovat.
* Názvy prostředků, které mohou být obecné.

Zadejte **jedinečný název prostředku** pro libovolný typ prostředku, který má koncový bod pro přístup k datům. Mezi běžné typy prostředků, které vyžadují jedinečný název, patří:

* Azure Storage<sup>1</sup> 
* Funkce Web Apps ve službě Azure App Service
* SQL Server
* Azure Key Vault
* Azure Cache for Redis
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> názvy účtů úložiště musí mít také malá a velká písmena, méně než 24 znaků a nesmí obsahovat spojovníky.

Při nastavování názvu můžete buď ručně vytvořit jedinečný název, nebo použít funkci [uniqueString ()](resource-group-template-functions-string.md#uniquestring) pro vygenerování názvu. Také můžete chtít přidat předponu nebo příponu k **uniqueString** výsledku. Změna jedinečného názvu vám umožní snadněji identifikovat typ prostředku z názvu. Můžete například vygenerovat jedinečný název pro účet úložiště pomocí následující proměnné:

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

U některých typů prostředků je vhodné zadat **název pro identifikaci**, ale název nemusí být jedinečný. Pro tyto typy prostředků zadejte název, který popisuje použití nebo charakteristiky.

```json
"parameters": {
  "vmName": { 
    "type": "string",
    "defaultValue": "demoLinuxVM",
    "metadata": {
      "description": "The name of the VM to create."
    }
  }
}
```

U typů prostředků, ke kterým většinou přistupujete pomocí jiného prostředku, můžete použít **obecný název** , který je pevně zakódovaný v šabloně. Můžete například nastavit standardní, obecný název pro pravidla brány firewall na serveru SQL Server:

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

### <a name="resource-location"></a>Umístění prostředku

Při nasazování šablony je nutné zadat umístění každého prostředku. Různé typy prostředků jsou podporovány v různých umístěních. Pokud chcete získat podporovaná umístění pro typ prostředku, přečtěte si téma [poskytovatelé a typy prostředků Azure](resource-manager-supported-services.md).

Pomocí parametru zadejte umístění pro prostředky a nastavte výchozí hodnotu na `resourceGroup().location`.

Následující příklad ukazuje účet úložiště, který je nasazený do umístění zadaného jako parametr:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="outputs"></a>Výstupy

V části výstupů zadáte hodnoty, které se vracejí z nasazení. Obvykle vracíte hodnoty z nasazených prostředků.

### <a name="available-properties"></a>Dostupné vlastnosti

Následující příklad ukazuje strukturu definici výstupu:

```json
"outputs": {
  "<outputName>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Název elementu | Požaduje se | Popis |
|:--- |:--- |:--- |
| outputName |Ano |Název výstupní hodnoty. Musí být platný identifikátor jazyka JavaScript. |
| condition |Ne | Logická hodnota, která označuje, zda je vrácena tato výstupní hodnota. Když `true`je hodnota obsažena ve výstupu pro nasazení. V `false`případě je výstupní hodnota pro toto nasazení vynechána. Není-li zadána, je `true`použita výchozí hodnota. |
| type |Ano |Typ výstupní hodnoty. Výstupní hodnoty podporují stejné typy jako vstupní parametry šablony. Pokud zadáte **SecureString** pro typ výstupu, hodnota se nezobrazí v historii nasazení a nelze ji načíst z jiné šablony. Chcete-li použít tajnou hodnotu ve více než jedné šabloně, uložte tajný klíč do Key Vault a odkazujte na tajný kód v souboru parametrů. Další informace najdete v tématu [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](resource-manager-keyvault-parameter.md). |
| value |Ano |Výraz jazyka šablony, která je vyhodnocena a vrátila jako výstupní hodnota. |

### <a name="define-and-use-output-values"></a>Definice a používání výstupní hodnoty

Následující příklad ukazuje, jak vrátit ID prostředku pro veřejnou IP adresu:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Další příklad ukazuje, jak podmíněně vracet ID prostředku pro veřejnou IP adresu na základě toho, zda byla nasazena nová:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Jednoduchý příklad podmíněného výstupu naleznete v tématu [podmíněná výstupní šablona](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

Po nasazení můžete načíst hodnotu pomocí skriptu. Pokud používáte PowerShell, použijte:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Výstupní hodnota z propojené šablony můžete načíst pomocí [odkaz](resource-group-template-functions-resource.md#reference) funkce. Výstupní hodnota z propojené šablony získáte načtení hodnoty vlastností se syntaxí, jako jsou: `"[reference('deploymentName').outputs.propertyName.value]"`.

Při získávání výstupu vlastnost z propojené šablony, název vlastnosti nemůže obsahovat čárku.

Následující příklad ukazuje, jak nastavit IP adresu v nástroji pro vyrovnávání zatížení načtením hodnoty z propojené šablony.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Nelze použít `reference` funkce v části výstupů [vnořené šablony](resource-group-linked-templates.md#link-or-nest-a-template). Na návratové hodnoty pro nasazený prostředek ve vnořené šablony, převeďte vnořené šablony na propojenou šablonu.

### <a name="output-example-templates"></a>Příklady výstupních šablon

|Šablona  |Popis  |
|---------|---------|
|[Zkopírujte proměnné](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Vytvoří proměnné komplexní a vrací tyto hodnoty. Nenasadí žádné prostředky. |
|[Veřejná IP adresa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Vytvoří veřejnou IP adresu a vypíše ID prostředku. |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Obsahuje odkazy na předchozí šablonu postupem. Při vytváření nástroje pro vyrovnávání zatížení, používá ID prostředku ve výstupu. |


<a id="comments" />

## <a name="comments-and-metadata"></a>Komentáře a metadata

Máte několik možností, jak přidat komentáře a metadata do šablony.

Můžete přidat `metadata` objekt skoro kdekoli v šabloně. Správce prostředků objekt ignoruje, ale editor JSON vám může zobrazit upozornění, že vlastnost není platná. V objektu definujte vlastnosti, které potřebujete.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

![Zobrazit Tip parametru](./media/resource-group-authoring-templates/show-parameter-tip.png)

Pro **prostředky**přidejte `comments` prvek nebo objekt metadat. Následující příklad ukazuje jak element Comments, tak objekt metadat.

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

Pro vložené komentáře můžete použít `//` , ale tato syntaxe nefunguje u všech nástrojů. K nasazení šablony s vloženými komentáři nelze použít rozhraní příkazového řádku Azure CLI. A nemůžete použít editor šablon portálu pro práci se šablonami s vloženými komentáři. Pokud přidáte tento styl komentáře, ujistěte se, že nástroje, které používáte, podporují vložené komentáře JSON.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

V VS Code můžete nastavit režim jazyka na JSON s komentáři. Vložené komentáře již nejsou označeny jako neplatné. Postup změny režimu:

1. Otevřít výběr jazykového režimu (CTRL + K M)

1. Vyberte **JSON s komentáři**.

   ![Vybrat režim jazyka](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Další postup
* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
* Podrobnosti o funkce, které můžete použít z v rámci šablony najdete v tématu [funkce šablon Azure Resource Manageru](resource-group-template-functions.md).
* Pokud chcete zkombinovat několik šablon během nasazování, přečtěte si téma [použití propojených šablon s Azure Resource Manager](resource-group-linked-templates.md).
* Doporučení k vytváření šablon najdete v tématu [osvědčené postupy pro šablonu Azure Resource Manager](template-best-practices.md).
* Doporučení k vytváření Správce prostředků šablon, které můžete použít ve všech prostředích a Azure Stack Azure, najdete v tématu [vývoj šablon Azure Resource Manager pro zajištění konzistence cloudu](templates-cloud-consistency.md).
