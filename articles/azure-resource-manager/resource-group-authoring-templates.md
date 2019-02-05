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
ms.date: 02/04/2019
ms.author: tomfitz
ms.openlocfilehash: 77dda85c920fda90b8379445a79569413b2dd463
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55691501"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Princip struktury a syntaxe šablon Azure Resource Manageru

Tento článek popisuje strukturu šablony Azure Resource Manageru. Představuje různé části šablony a vlastnosti, které jsou k dispozici v těchto oddílech. Šablona se skládá z JSON a z výrazů, které můžete použít k vytvoření hodnot pro vaše nasazení. Podrobný kurz k vytvoření šablony najdete v tématu [vytvoření první šablony Azure Resource Manageru](resource-manager-create-first-template.md).

## <a name="template-format"></a>Formát šablon

Ve své nejjednodušší struktury šablony obsahuje následující prvky:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
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
| parameters |Ne |Hodnoty, které jsou k dispozici při spuštění nasazení přizpůsobení nasazení prostředků. |
| Proměnné |Ne |Hodnoty, které se používají jako fragmentů JSON v šabloně pro zjednodušení výrazy jazyka šablony. |
| functions |Ne |Uživatelem definované funkce, které jsou k dispozici v rámci šablony. |
| zdroje |Ano |Typy prostředků, které jsou nasazené nebo aktualizují ve skupině prostředků. |
| výstupy |Ne |Hodnoty, které se vrátí po nasazení. |

Každý prvek má vlastnosti, které můžete nastavit. Následující příklad ukazuje úplnou syntaxi šablony:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
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
    },
    "variables": {
        "<variable-name>": "<variable-value>",
        "<variable-object-name>": {
            <variable-complex-type-value>
        },
        "<variable-object-name>": {
            "copy": [
                {
                    "name": "<name-of-array-property>",
                    "count": <number-of-iterations>,
                    "input": {
                        <properties-to-repeat>
                    }
                }
            ]
        },
        "copy": [
            {
                "name": "<variable-array-name>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "functions": [
      {
        "namespace": "<namespace-for-your-function>",
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
              "value": "<function-expression>"
            }
          }
        }
      }
    ],
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
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
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
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
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

Tento článek popisuje části šablony podrobněji.

## <a name="syntax"></a>Syntaxe

Základní syntaxe šablony je JSON. Výrazy a funkce však vztahují i k dispozici v rámci šablony hodnoty JSON.  Výrazy se zapisují v rámci JSON řetězcové literály, jehož první a poslední znaky jsou závorky: `[` a `]`v uvedeném pořadí. Hodnota tohoto výrazu je vyhodnocen při nasazení šablony. Zatímco zapisují jako řetězcový literál, výsledek vyhodnocení výrazu může být jiného typu JSON, jako je například pole nebo celé číslo, v závislosti na skutečné výrazu.  Aby řetězcový literál začínat se hranatá závorka `[`, ale ne bylo interpretováno jako výraz, přidejte další závorku spustit řetězec s `[[`.

Obvykle použijete výrazy s využitím functions k provádění operací pro konfiguraci nasazení. Stejně jako v jazyce JavaScript, volání funkce jsou formátovány jako `functionName(arg1,arg2,arg3)`. Vlastnosti odkazovat pomocí operátorů tečkou a [index].

Následující příklad ukazuje, jak použít několik funkcí při vytváření hodnotu:

```json
"variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
}
```

Úplný seznam funkcí šablon najdete v tématu [funkce šablon Azure Resource Manageru](resource-group-template-functions.md). 

## <a name="parameters"></a>Parametry

V sekci parametrů šablony zadejte hodnoty, které můžete zadat při nasazování prostředků. Tyto hodnoty parametrů umožňují vlastní nastavení nasazení tím, že poskytuje hodnoty, které jsou přizpůsobené pro konkrétní prostředí (jako je vývoj, testování a produkce). Není nutné zadat parametry v šabloně, ale bez parametrů by vždy šablony nasadit stejným prostředkům se stejnými názvy, umístění a vlastnosti.

Následující příklad ukazuje definicí jednoduchého parametru:

```json
"parameters": {
  "siteNamePrefix": {
    "type": "string",
    "metadata": {
      "description": "The name prefix of the web app that you wish to create."
    }
  },
},
```

Informace o definování parametrů najdete v tématu [oddílu parametry šablon Azure Resource Manageru](resource-manager-templates-parameters.md).

## <a name="variables"></a>Proměnné

V sekci proměnných vytvořit hodnoty, které lze použít v celé vaší šablony. Není nutné definovat proměnné, ale často zjednodušení šablony snížením složité výrazy.

Následující příklad ukazuje definicí jednoduchého proměnné:

```json
"variables": {
  "webSiteName": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
},
```

Informace o definování proměnných najdete v tématu [části proměnných šablon Azure Resource Manageru](resource-manager-templates-variables.md).

## <a name="functions"></a>Functions

V rámci šablony můžete vytvořit vaše vlastní funkce. Tyto funkce jsou k dispozici pro použití ve vaší šabloně. Obvykle definujete složitý výraz, který nechcete opakovat v rámci šablony. Vytvoření uživatelem definovaných funkcí z výrazů a [funkce](resource-group-template-functions.md) v rámci šablon, které jsou podporovány.

Při definování funkce user, platí určitá omezení:

* Funkce nemá přístup k proměnné.
* Funkce nemá přístup k parametry šablony. To znamená [parametry funkce](resource-group-template-functions-deployment.md#parameters) je omezen na parametry funkce.
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
V části prostředky definovat prostředky, které jsou nasazené a aktualizovat. V této části můžete získat složité, protože musíte porozumět typům, které nasazení provádíte do zadejte správné hodnoty.

```json
"resources": [
  {
    "apiVersion": "2016-08-01",
    "name": "[variables('webSiteName')]",
    "type": "Microsoft.Web/sites",
    "location": "[resourceGroup().location]",
    "properties": {
      "serverFarmId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Web/serverFarms/<plan-name>"
    }
  }
],
```

Chcete-li podmíněně zahrnout nebo vyloučit prostředku během nasazení, použijte [podmínky](resource-manager-templates-resources.md#condition). Další informace o oddílu prostředků najdete v tématu [oddíl prostředků šablon Azure Resource Manageru](resource-manager-templates-resources.md).

## <a name="outputs"></a>Výstupy
V části výstupů zadáte hodnoty, které se vracejí z nasazení. Například může vrátit identifikátor URI pro přístup k nasazených prostředků.

```json
"outputs": {
  "newHostName": {
    "type": "string",
    "value": "[reference(variables('webSiteName')).defaultHostName]"
  }
}
```

Další informace najdete v tématu [výstupy část šablon Azure Resource Manageru](resource-manager-templates-outputs.md).

## <a name="comments"></a>Komentáře

Máte několik možností pro přidání komentářů do šablony.

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

Pro **prostředky**, přidejte `comments` elementu.

```json
"resources": [
    {
      "comments": "Storage account used to store VM disks",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-07-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {}
    },
```

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

Obecné komentáře můžete použít `//` však tato syntaxe způsobí chybu při nasazení šablony pomocí rozhraní příkazového řádku Azure.

```json
"variables": {
    // Create unique name for the storage account
    "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
},
```

## <a name="template-limits"></a>Omezení šablony

Omezení velikosti šablony pro 1 MB a každý soubor parametrů na 64 KB. Po rozšířila s definic iterativní prostředků a hodnoty pro proměnné a parametry, platí omezení 1 MB na konečný stav šablony. 

Také jste omezeni na:

* 256 parametry
* 256 proměnné
* 800 prostředky (včetně počet kopií)
* 64 výstupní hodnoty
* 24,576 znaků ve výrazu šablony

Některá omezení šablony mohou překročit pomocí vnořené šablony. Další informace najdete v tématu [použití propojených šablon při nasazování prostředků Azure](resource-group-linked-templates.md). Pokud chcete snížit počet parametrů, proměnných nebo výstupů, můžete kombinovat několik hodnot do objektu. Další informace najdete v tématu [objektů jako parametry](resource-manager-objects-as-parameters.md).

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Další postup
* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
* Podrobnosti o funkce, které můžete použít z v rámci šablony najdete v tématu [funkce šablon Azure Resource Manageru](resource-group-template-functions.md).
* Pokud chcete sloučit několik šablon během nasazení, přečtěte si téma [použití propojených šablon s Azure Resource Managerem](resource-group-linked-templates.md).
* Doporučení o vytváření šablon naleznete v tématu [osvědčené postupy pro šablony Azure Resource Manageru](template-best-practices.md).
* Doporučení týkající se vytváření šablon Resource Manageru, které můžete používat v globálních oblastech Azure, suverénních cloudech Azure a ve službě Azure Stack, najdete v článku o [vývoji šablon Azure Resource Manageru pro konzistenci cloudu](templates-cloud-consistency.md).
