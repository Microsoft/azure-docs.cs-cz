---
title: Struktura šablony Azure Resource Manageru a syntaxe | Dokumentace Microsoftu
description: Popisuje strukturu a vlastnosti šablony Azure Resource Manageru pomocí deklarativní syntaxe JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2019
ms.author: tomfitz
ms.openlocfilehash: 264db79f5c934603004eb595930b44abc622efd5
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492189"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Princip struktury a syntaxe šablon Azure Resource Manageru

Tento článek popisuje strukturu šablony Azure Resource Manageru. Představuje různé části šablony a vlastnosti, které jsou k dispozici v těchto oddílech. Šablona se skládá z JSON a z výrazů, které můžete použít k vytvoření hodnot pro vaše nasazení.

Tento článek je určený pro uživatele, kteří mají některé znalosti s šablonami Resource Manageru. Poskytuje podrobné informace o struktuře a syntaxe šablony. Pokud chcete, úvod do vytváření šablony, přečtěte si téma [vytvoření první šablony Azure Resource Manageru](resource-manager-create-first-template.md).

## <a name="template-format"></a>Formát šablon

Ve své nejjednodušší struktury šablony obsahuje následující prvky:

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
| $schema |Ano |Umístění souboru schématu JSON, který popisuje verzi jazyka šablony.<br><br> Pro nasazení skupiny prostředků použijte: `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Pro nasazení předplatného použijte: `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Ano |Verze šablony (jako je například 1.0.0.0). Tento prvek můžete zadat libovolnou hodnotu. Tato hodnota zdokumentovat významné změny v šabloně používejte. Při nasazování prostředků pomocí šablony, tato hodnota je možné, aby se zajistilo, že používá správnou šablonu. |
| apiProfile |Ne | Verze rozhraní API, která slouží jako kolekce verze rozhraní API pro typy prostředků. Tuto hodnotu použijte, abyste ho nemuseli znovu k určení verze rozhraní API pro každý prostředek v šabloně. Když zadáte profilu verze rozhraní API a nezadávejte verze rozhraní API pro typ prostředku, používá Resource Manageru verze rozhraní API pro příslušný typ prostředku, který je definován v profilu.<br><br>Vlastnost profilu rozhraní API je zvláště užitečné při nasazování šablony do různých prostředí, jako je Azure Stack a globální Azure. Ujistěte se, že vaše šablona automaticky používá verze, které jsou podporovány v obou prostředích pomocí profilu verze rozhraní API. Seznam aktuální profilu verze rozhraní API a prostředků verze rozhraní API, které jsou definovány v profilu najdete v tématu [profil API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Další informace najdete v tématu [sledování verzí pomocí profilů rozhraní API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Ne |Hodnoty, které jsou k dispozici při spuštění nasazení přizpůsobení nasazení prostředků. |
| [Proměnné](#variables) |Ne |Hodnoty, které se používají jako fragmentů JSON v šabloně pro zjednodušení výrazy jazyka šablony. |
| [functions](#functions) |Ne |Uživatelem definované funkce, které jsou k dispozici v rámci šablony. |
| [prostředky](#resources) |Ano |Typy prostředků, které jsou nasazené nebo aktualizovat skupinu prostředků nebo předplatného. |
| [výstupy](#outputs) |Ne |Hodnoty, které se vrátí po nasazení. |

Každý prvek má vlastnosti, které můžete nastavit. Tento článek popisuje části šablony podrobněji.

## <a name="syntax"></a>Syntaxe

Základní syntaxe šablony je JSON. Však můžete použít výrazy k rozšíření k dispozici v rámci šablony hodnoty JSON.  Výrazy začínat a končit závorky: `[` a `]`v uvedeném pořadí. Hodnota tohoto výrazu je vyhodnocen při nasazení šablony. Výraz může vrátit řetězec, celé číslo, logickou hodnotu, pole nebo objekt. Následující příklad ukazuje výraz výchozí hodnoty parametru:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

V rámci výrazu, syntaxe `resourceGroup()` volání jedné z funkcí, které poskytuje správce prostředků pro použití v rámci šablony. Stejně jako v jazyce JavaScript, volání funkce jsou formátovány jako `functionName(arg1,arg2,arg3)`. Syntaxe `.location` načte jednu vlastnost z objekt vrácený rutinou tuto funkci.

Šablony funkcí a jejich parametrů rozlišují velikost písmen. Například Resource Manageru překládá **variables('var1')** a **VARIABLES('VAR1')** za stejné. Při vyhodnocování, pokud funkci výslovně upraví případu (například toUpper nebo toLower), funkce zachová případu. Některé typy prostředků mohou mít případu požadavky bez ohledu na to, jak se vyhodnocují funkce.

Aby řetězcový literál začínat se hranatá závorka `[`, ale ne bylo interpretováno jako výraz, přidejte další závorku spustit řetězec s `[[`.

Předat hodnotu řetězce jako parametr funkce, použijte jednoduché uvozovky.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

K návratu dvojité uvozovky ve výrazu, jako je například přidávání objektu JSON v šabloně použijte zpětné lomítko.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Výraz šablony nemůže být delší než 24,576 znaků.

Úplný seznam funkcí šablon najdete v tématu [funkce šablon Azure Resource Manageru](resource-group-template-functions.md). 

## <a name="parameters"></a>Parametry

V sekci parametrů šablony zadejte hodnoty, které můžete zadat při nasazování prostředků. Tyto hodnoty parametrů umožňují vlastní nastavení nasazení tím, že poskytuje hodnoty, které jsou přizpůsobené pro konkrétní prostředí (jako je vývoj, testování a produkce). Není nutné zadat parametry v šabloně, ale bez parametrů by vždy šablony nasadit stejným prostředkům se stejnými názvy, umístění a vlastnosti.

Jste omezeni na 256 parametrů v šabloně. Počet parametrů můžete omezit použitím objektů, které obsahují více vlastností, jak je znázorněno v tomto článku.

### <a name="available-properties"></a>Dostupné vlastnosti

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
| Název parametru |Ano |Název parametru. Musí být platný identifikátor jazyka JavaScript. |
| type |Ano |Typ hodnoty parametru. Povolené typy a hodnoty jsou **řetězec**, **securestring**, **int**, **bool**, **objekt**, **secureObject**, a **pole**. |
| Výchozí hodnota |Ne |Výchozí hodnota pro parametr, pokud se nezadá žádná hodnota pro parametr. |
| allowedValues |Ne |Povolené hodnoty pro parametr, abyste měli jistotu, že se zadal správný hodnotu pole. |
| minValue |Ne |Minimální hodnota pro parametry typu int, tato hodnota je také zahrnuto. |
| maxValue |Ne |Maximální hodnoty pro parametry typu int, tato hodnota je také zahrnuto. |
| minLength |Ne |Minimální délku řetězce, zabezpečený řetězec a parametry typu pole, tato hodnota je také zahrnuto. |
| maxLength |Ne |Maximální délka řetězce, zabezpečený řetězec a parametry typu pole, tato hodnota je také zahrnuto. |
| description |Ne |Popis parametru, který se zobrazí uživatelům na portálu. Další informace najdete v tématu [komentáře v šablonách](#comments). |

### <a name="define-and-use-a-parameter"></a>Definujte a použijte parametr.

Následující příklad ukazuje definicí jednoduchého parametru. Definuje název parametru a určuje, že přijímá řetězcovou hodnotu. Parametr přijímá pouze hodnoty, které dávají smysl pro zamýšlený účel. Pokud během nasazení se nezadá žádná hodnota určuje výchozí hodnotu. Nakonec tento parametr obsahuje popis jeho použití.

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

V šabloně referenční hodnota parametru s následující syntaxí:

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

### <a name="template-functions-with-parameters"></a>Šablony funkce s parametry

Při zadávání výchozí hodnota pro parametr, můžete použít většina funkcí šablony. Pro vytvoření výchozí hodnoty můžete použít jinou hodnotu parametru. Následující šablona ukazuje použití funkcí v výchozí hodnota:

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

Nelze použít `reference` funkce v sekci parametrů. Jsou parametry vyhodnoceny před nasazením proto `reference` funkce nelze získat běhový stav prostředku. 

### <a name="objects-as-parameters"></a>Objekty, které jako parametry

Může být jednodušší k uspořádání souvisejících hodnot v předáním jako objekt. Tento přístup také snižuje počet parametrů v šabloně.

Definujte parametr v šabloně a během nasazení, zadejte objekt JSON místo jednu hodnotu. 

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

Potom odkazujete objektu třídy subproperties parametru pomocí operátoru tečka.

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

### <a name="parameter-example-templates"></a>Parametr ukázkových šablon

Tyto šablony příklad ukazují některé scénáře použití parametrů. Nasazení, je otestovat zpracování parametrů v různých scénářích.

|Šablona  |Popis  |
|---------|---------|
|[parametry s využitím functions pro výchozí hodnoty](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Popisuje způsob použití funkce šablon, při definování výchozí hodnoty pro parametry. Šablona nenasadí žádné prostředky. Konstrukce hodnoty parametrů a vrátí tyto hodnoty. |
|[Parametr objektu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Ukazuje použití objektu pro parametr. Šablona nenasadí žádné prostředky. Konstrukce hodnoty parametrů a vrátí tyto hodnoty. |

## <a name="variables"></a>Proměnné

V sekci proměnných vytvořit hodnoty, které lze použít v celé vaší šablony. Není nutné definovat proměnné, ale často zjednodušení šablony snížením složité výrazy.

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

Informace o používání `copy` vytvořit několik hodnot proměnné, přečtěte si článek [proměnné iterace](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Definice a používání proměnné

Následující příklad ukazuje definicí proměnné. Vytvoří hodnotu řetězce pro název účtu úložiště. Používá několik funkcí šablony pro získání hodnoty parametrů a zřetězí do jedinečného řetězce.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Při definování prostředku, použijte proměnnou.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>Konfigurační proměnné

Komplexní typy JSON můžete použít k definování souvisejících hodnot pro prostředí.

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

V parametrech vytvořit hodnotu určující, která konfigurace bude hodnoty použití.

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

Aktuální nastavení s načíst:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>Příklad proměnné šablony

Tyto šablony příklad ukazují některé scénáře pro používání proměnných. Je nasadíte na testování, jak se zpracovává proměnné v různých scénářích. 

|Šablona  |Popis  |
|---------|---------|
| [Definice proměnných](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Ukazuje různé druhy proměnných. Šablona nenasadí žádné prostředky. Vytvoří proměnné hodnoty a vrátí tyto hodnoty. |
| [Konfigurační proměnná](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Ukazuje použití proměnné, která definuje hodnoty konfigurace. Šablona nenasadí žádné prostředky. Vytvoří proměnné hodnoty a vrátí tyto hodnoty. |
| [pravidel zabezpečení sítě](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) a [soubor s parametry](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Vytvoří pole ve správném formátu pro přiřazení pravidla zabezpečení pro skupinu zabezpečení sítě. |


## <a name="functions"></a>Functions

V rámci šablony můžete vytvořit vaše vlastní funkce. Tyto funkce jsou k dispozici pro použití ve vaší šabloně. Obvykle definujete složitý výraz, který nechcete opakovat v rámci šablony. Vytvoření uživatelem definovaných funkcí z výrazů a [funkce](resource-group-template-functions.md) v rámci šablon, které jsou podporovány.

Při definování funkce user, platí určitá omezení:

* Funkce nemá přístup k proměnné.
* Funkci lze použít pouze parametry, které jsou definovány ve funkci. Při použití [parametry funkce](resource-group-template-functions-deployment.md#parameters) v rámci uživatelem definované funkce, je omezený na parametry pro tuto funkci.
* Funkci nelze volat jiné uživatelem definované funkce.
* Funkci nelze použít [odkazu funkci](resource-group-template-functions-resource.md#reference).
* Parametry pro tuto funkci nemůže mít výchozí hodnoty.

Vaše funkce vyžadují hodnotu oboru názvů, aby předešel konfliktům s funkcí šablony. Následující příklad ukazuje funkci, která vrátí název účtu úložiště:

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

Volání funkce:

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
V části prostředky definovat prostředky, které jsou nasazené a aktualizovat.

### <a name="available-properties"></a>Dostupné vlastnosti

Můžete definovat prostředky s následující strukturou:

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
| condition | Ne | Logická hodnota, která určuje, zda prostředek se zřídí během tohoto nasazení. Když `true`, je prostředek vytvořený během nasazení. Když `false`, prostředek se přeskočí pro toto nasazení. Zobrazit [podmínku](#condition). |
| apiVersion |Ano |Verze rozhraní REST API pro použití při vytváření prostředku. Zjistíte dostupné hodnoty, najdete v článku [referenčními informacemi k šablonám](/azure/templates/). |
| type |Ano |Typ prostředku. Tato hodnota je kombinací obor názvů zprostředkovatele prostředků a typ prostředku (například **Microsoft.Storage/storageAccounts**). Zjistíte dostupné hodnoty, najdete v článku [referenčními informacemi k šablonám](/azure/templates/). |
| jméno |Ano |Název prostředku Název musí následovat identifikátor URI součásti omezení RFC3986. Kromě toho služby Azure, které zpřístupňují název prostředku se třetími stranami ověřit název, který má ujistit, že není pokus zfalšovat jiné identity. |
| location |Různé |Podporované geografické umístění zadaného prostředku. Můžete vybrat některý z dostupných umístění, ale obvykle je vhodné vybrat ten, který je blízko vašim uživatelům. Obvykle je také vhodné umístit prostředky, které spolu interagují ve stejné oblasti. Většina typů prostředků vyžaduje umístění, ale některé typy (jako je například přiřazení role) nevyžadují umístění. |
| tags |Ne |Značky, které jsou spojeny s prostředkem. Použití značek logicky tak uspořádat prostředky napříč vašeho předplatného. |
| Komentáře |Ne |Poznámky pro dokumentaci prostředků ve vaší šabloně. Další informace najdete v tématu [komentáře v šablonách](resource-group-authoring-templates.md#comments). |
| Kopírovat |Ne |V případě potřeby je více než jednu instanci, kolik prostředků k vytvoření. Paralelní je výchozí režim. Zadejte sériové režim, když nechcete, aby všechny nebo prostředky k nasazení ve stejnou dobu. Další informace najdete v tématu [vytvořit několik instancí prostředků v Azure Resource Manageru](resource-group-create-multiple.md). |
| dependsOn |Ne |Prostředky, které musí být nasazený před nasazením tento prostředek. Resource Manager vyhodnotí závislosti mezi prostředky a nasadí ve správném pořadí. Pokud nejsou na sobě navzájem závislé prostředky, kde jsou nasazeny současně. Hodnota může být čárkou oddělený seznam prostředek názvy nebo jedinečné identifikátory prostředků. Pouze výpis prostředků, které jsou nasazené v této šabloně. Prostředky, které nejsou definovány v této šabloně už musí existovat. Vyhněte se přidává zbytečné závislostí může zpomalit vaše nasazení a vytvoření cyklické závislosti. Pokyny k nastavení závislostí v tématu [definování závislostí v šablonách Azure Resource Manageru](resource-group-define-dependencies.md). |
| properties |Ne |Nastavení konfigurace specifických pro prostředky. Hodnoty pro tyto vlastnosti jsou stejné jako hodnoty, které zadáte v textu požadavku pro operaci rozhraní REST API (metodu PUT) a vytvoří prostředek. Můžete také zadat pole kopie vytvořit několik instancí vlastnosti. Zjistíte dostupné hodnoty, najdete v článku [referenčními informacemi k šablonám](/azure/templates/). |
| SKU | Ne | Některé prostředky povolit hodnoty, které definují skladová položka pro nasazení. Můžete například zadat typ redundance účtu úložiště. |
| Typ | Ne | Některé prostředky povolit hodnotu, která definuje typ prostředku, který nasadíte. Můžete například zadat typ služby Cosmos DB k vytvoření. |
| plán | Ne | Některé prostředky povolit hodnoty, které definují plán pro nasazení. Můžete například zadat image marketplace pro virtuální počítač. | 
| prostředky |Ne |Podřízené prostředky, které jsou závislé na prostředku definuje. Zadejte pouze typy prostředků, které jsou povoleny ve schématu nadřazený prostředek. Plně kvalifikovaný typ podřízený prostředek obsahuje nadřazený typ prostředku, jako například **Microsoft.Web/sites/extensions**. Závislost na nadřazený prostředek není zahrnuta. Je nutné explicitně definovat dané závislosti. |

### <a name="condition"></a>Podmínka

Pokud během nasazení musíte rozhodnout, jestli se mají vytvořit prostředek, použijte `condition` elementu. Hodnota pro tento element se přeloží na hodnotu true nebo false. Pokud je hodnota true, je prostředek vytvořený. Pokud je hodnota false, nevytvoří se prostředek. Hodnota dá používat jedině pro celý prostředek.

Obvykle tuto hodnotu použijete, pokud chcete vytvořit nový prostředek, nebo použijte již existující. Například, chcete-li určit, jestli je nasazená nový účet úložiště nebo existující účet úložiště se používá, použijte:

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

Kompletní příklad šablony, která se používá `condition` prvku, naleznete v tématu [virtuálního počítače s novou nebo existující virtuální sítě, úložiště a veřejnou IP adresu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

Pokud používáte [odkaz](resource-group-template-functions-resource.md#reference) nebo [seznamu](resource-group-template-functions-resource.md#list) funkce, s prostředkem, který je nasazený podmíněně, funkce vyhodnocena i v případě, že není nasazený prostředek. Pokud funkci odkazuje na prostředek, který neexistuje, dojde k chybě. Použití [Pokud](resource-group-template-functions-logical.md#if) funkce, která se ujistěte se, že funkce se vyhodnocuje jenom pro podmínky, při nasazení prostředku. Najdete v článku [Pokud funkce](resource-group-template-functions-logical.md#if) pro ukázkovou šablonu, která využívá Pokud a odkaz se podmíněně nasazených prostředků.

### <a name="resource-names"></a>Názvy prostředků

Obecně platí práci s tři typy názvů prostředků v Resource Manageru:

* Názvy prostředků, které musí být jedinečný.
* Názvy prostředků, které nemusí být jedinečný, ale můžete rozhodnout pro poskytnutí název, který vám pomůže identifikovat prostředek.
* Názvy prostředků, které mohou být obecný.

Zadejte **název jedinečný prostředek** pro libovolný typ prostředku, který má koncový bod data access. Některé běžné typy prostředků, které vyžadují jedinečný název patří:

* Azure Storage<sup>1</sup> 
* Funkce Web Apps ve službě Azure App Service
* SQL Server
* Azure Key Vault
* Azure Cache for Redis
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> názvy účtů úložiště také musí obsahovat malá písmena, 24 znaků nebo méně, a není nutné žádné pomlčky.

Při nastavování názvu, můžete ručně vytvořit jedinečný název nebo použít [uniqueString()](resource-group-template-functions-string.md#uniquestring) funkci generování názvu. Můžete také chtít přidat předponu nebo příponu k **uniqueString** výsledek. Úprava jedinečný název můžete vám umožní snadno identifikovat typ prostředku z názvu. Můžete například vygenerovat jedinečný název pro účet úložiště pomocí následující proměnnou:

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

Pro některé typy prostředků, můžete chtít poskytnout **název pro identifikaci**, ale název nemusí být jedinečný. Pro tyto typy prostředků zadejte název s popisem použití nebo vlastnosti.

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

Pro typy prostředků, které většinou přístup přes jiný prostředek, můžete použít **obecný název** , který je pevně zakódované v šabloně. Můžete třeba nastavit standardní, obecný název pravidla brány firewall na serveru SQL server:

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

### <a name="resource-location"></a>Umístění prostředku

Při nasazování šablony, je nutné zadat umístění každého prostředku. Různé typy prostředků jsou podporovány v různých umístěních. Podporovaná umístění pro typ prostředku, získáte [poskytovatelé a typy prostředků Azure](resource-manager-supported-services.md).

Parametr použít k určení umístění pro prostředky a nastavení výchozí hodnoty `resourceGroup().location`.

Následující příklad ukazuje účet úložiště, která je nasazena do umístění zadaného jako parametr:

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

### <a name="child-resources"></a>Podřízené prostředky

V rámci některé typy prostředků můžete také definovat pole podřízené prostředky. Podřízené prostředky jsou prostředky, které existují pouze v rámci kontextu jiný prostředek. Například databáze SQL nemůže existovat bez serveru SQL server, databázi je podřízený server. Můžete definovat databáze v rámci definice pro server.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Pokud vnořený, typ je nastavený na `databases` , ale jeho typ úplné prostředku je `Microsoft.Sql/servers/databases`. Nezadáte `Microsoft.Sql/servers/` se předpokládá z nadřazeného typu prostředku. Název prostředku podřízené nastavený na `exampledatabase` ale úplný název obsahuje název nadřazené. Nezadáte `exampleserver` se předpokládá z nadřazeného zdroje.

Formát typu podřízeného prostředku je: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

Formát názvu podřízených prostředků je: `{parent-resource-name}/{child-resource-name}`

Ale není nutné definovat databáze v rámci serveru. Můžete definovat podřízený prostředek na nejvyšší úrovni. Tento přístup může použít, pokud se nadřazený prostředek není nasazený ve stejné šabloně, nebo pokud chcete použít `copy` vytvořit více než jeden podřízený prostředek. S tímto přístupem poskytují úplnou prostředků a zahrnout název nadřazeného prostředku v názvu prostředku podřízeného.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

Při vytváření plně kvalifikovaný odkaz na prostředek, není pořadí zkombinovat segmenty z typu a název jednoduše zřetězení těchto dvou. Místo toho za názvovým prostorem, použijte sekvenci *typ nebo název* dvojice z nejméně specifická, které budou nejvíce specifické:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Příklad:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` správnost `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` není správná

## <a name="outputs"></a>Výstupy

V části výstupů zadáte hodnoty, které se vracejí z nasazení. Obvykle návratové hodnoty z nasazených prostředků.

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
| condition |Ne | Logická hodnota označující, zda tento výstupní hodnota je vrácena. Když `true`, hodnota je součástí výstupu pro nasazení. Když `false`, výstupní hodnota je vynecháno pro toto nasazení. Pokud není zadán, výchozí hodnota je `true`. |
| type |Ano |Typ výstupní hodnoty. Výstupní hodnoty podporují stejné typy jako vstupní parametry šablony. |
| hodnota |Ano |Výraz jazyka šablony, která je vyhodnocena a vrátila jako výstupní hodnota. |

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

Následující příklad ukazuje, jak podmíněně vrátit ID prostředku pro veřejnou IP adresu na základě, jestli je nový, jeden byla nasazena:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Jednoduchý příklad podmíněného výstupu naleznete v tématu [podmíněného výstupu šablony](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

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

Následující příklad ukazuje, jak nastavit IP adresu nástroje pro vyrovnávání zatížení načtením hodnotu z propojenou šablonu.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Nelze použít `reference` funkce v části výstupů [vnořené šablony](resource-group-linked-templates.md#link-or-nest-a-template). Na návratové hodnoty pro nasazený prostředek ve vnořené šablony, převeďte vnořené šablony na propojenou šablonu.

### <a name="output-example-templates"></a>Výstup příkladu šablony

|Šablona  |Popis  |
|---------|---------|
|[Zkopírujte proměnné](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Vytvoří proměnné komplexní a vrací tyto hodnoty. Nenasadí žádné prostředky. |
|[Veřejná IP adresa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Vytvoří veřejnou IP adresu a vypíše ID prostředku. |
|[Nástroj pro vyrovnávání zatížení](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Obsahuje odkazy na předchozí šablonu postupem. Při vytváření nástroje pro vyrovnávání zatížení, používá ID prostředku ve výstupu. |


<a id="comments" />

## <a name="comments-and-metadata"></a>Poznámky a metadata

Máte několik možností pro přidání poznámky a metadata do šablony.

Můžete přidat `metadata` objekt skoro kdekoli ve vaší šabloně. Objekt ignoruje Resource Manageru, ale JSON editor možná by vás varovala, že vlastnost není platný. V objektu definujte vlastnosti, které potřebujete.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Pro **parametry**, přidejte `metadata` objektu `description` vlastnost.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Při nasazování šablony prostřednictvím portálu, text, který zadáte v popisu automaticky slouží jako komentář pro tento parametr.

![Zobrazit tip parametru](./media/resource-group-authoring-templates/show-parameter-tip.png)

Pro **prostředky**, přidejte `comments` element nebo objekt metadat. Následující příklad ukazuje element komentáře a objekt metadat.

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

Pro **výstupy**, přidejte objekt metadat výstupní hodnotu.

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

Objekt metadat nelze přidat do uživatelem definované funkce.

Vložené komentáře, můžete použít `//` , ale tato syntaxe nefunguje s všechny nástroje. Pokud chcete nasadit šablonu pomocí vložené komentáře nelze použít rozhraní příkazového řádku Azure. A editoru portálu šablony nelze použít pro práci na šablonách s vložené komentáře. Pokud chcete přidat tento styl komentář, ujistěte se, nástrojů, které používáte podporu vložené JSON komentáře.

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

V nástroji VS Code můžete nastavit režim jazyka do formátu JSON s komentáři. Vložené komentáře se už nebude označena jako neplatná. Chcete-li změnit režim:

1. Otevřete výběr jazyka režimu (Ctrl + K M)

1. Vyberte **JSON s komentáři**.

   ![Vybrat režim jazyka](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Další postup
* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
* Podrobnosti o funkce, které můžete použít z v rámci šablony najdete v tématu [funkce šablon Azure Resource Manageru](resource-group-template-functions.md).
* Pokud chcete sloučit několik šablon během nasazení, přečtěte si téma [použití propojených šablon s Azure Resource Managerem](resource-group-linked-templates.md).
* Doporučení o vytváření šablon naleznete v tématu [osvědčené postupy pro šablony Azure Resource Manageru](template-best-practices.md).
* Doporučení týkající se vytvoření šablony Resource Manageru, které můžete použít ve všech prostředích Azure a Azure Stack, najdete v tématu [šablon vývoj Azure Resource Manageru pro cloud konzistence](templates-cloud-consistency.md).
