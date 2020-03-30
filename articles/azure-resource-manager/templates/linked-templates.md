---
title: Šablony odkazů pro nasazení
description: Popisuje, jak používat propojené šablony v šabloně Azure Resource Manager k vytvoření modulárního řešení šablon. Ukazuje, jak předat hodnoty parametrů, zadat soubor parametrů a dynamicky vytvořené adresy URL.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 322797383ee865ceb66c44793387da827aeb8879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131928"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Použití propojené a vnořené šablony při nasazování prostředků Azure

Chcete-li nasadit komplexní řešení, můžete šablonu rozdělit na mnoho souvisejících šablon a pak je nasadit společně prostřednictvím hlavní šablony. Související šablony mohou být samostatné soubory nebo syntaxe šablony, která je vložena do hlavní šablony. Tento článek používá termín **propojené šablony** odkazovat na samostatný soubor šablony, který je odkazován pomocí odkazu z hlavní šablony. Používá termín **vnořené šablony** odkazovat na vložené šablony syntaxe v rámci hlavní šablony.

U malých a středních řešení je jedna šablona srozumitelnější a snadněji pochopitelná a udržovatelná. Všechny prostředky a hodnoty můžete zobrazit v jednom souboru. U pokročilých scénářů umožňují propojené šablony rozdělit řešení na cílové součásti. Tyto šablony můžete snadno znovu použít pro jiné scénáře.

Kurz najdete v [tématu Kurz: vytvoření propojených šablon Azure Resource Manageru](template-tutorial-create-linked-templates.md).

> [!NOTE]
> Pro propojené nebo vnořené šablony můžete použít pouze režim [přírůstkového](deployment-modes.md) nasazení.
>

## <a name="nested-template"></a>Vnořená šablona

Chcete-li šablonu vnořit, přidejte do hlavní šablony [prostředek nasazení.](/azure/templates/microsoft.resources/deployments) Ve vlastnosti **šablony** zadejte syntaxi šablony.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "name": "nestedTemplate1",
      "apiVersion": "2017-05-10",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Následující příklad nasazuje účet úložiště prostřednictvím vnořené šablony.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "nestedTemplate1",
      "apiVersion": "2017-05-10",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="expression-evaluation-scope-in-nested-templates"></a>Obor vyhodnocení výrazu v vnořených šablonách

Při použití vnořené šablony můžete určit, zda budou výrazy šablony vyhodnocovány v rámci oboru nadřazené šablony nebo vnořené šablony. Obor určuje, jak jsou vyřešeny parametry, proměnné a funkce, jako [je resourceGroup](template-functions-resource.md#resourcegroup) a [subscription.](template-functions-resource.md#subscription)

Můžete nastavit obor `expressionEvaluationOptions` prostřednictvím vlastnosti. Ve výchozím `expressionEvaluationOptions` nastavení je `outer`vlastnost nastavena na , což znamená, že používá obor nadřazené šablony. Nastavte hodnotu `inner` tak, aby výrazy byly vyhodnoceny v rámci oboru vnořené šablony.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2017-05-10",
  "name": "nestedTemplate1",
  "properties": {
  "expressionEvaluationOptions": {
    "scope": "inner"
  },
  ...
```

Následující šablona ukazuje, jak jsou výrazy šablony vyřešeny podle oboru. Obsahuje proměnnou `exampleVar` s názvem, která je definována v nadřazené šabloně i vnořené šabloně. Vrátí hodnotu proměnné.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

Hodnota `exampleVar` se změní v závislosti `scope` na `expressionEvaluationOptions`hodnotě vlastnosti v . V následující tabulce jsou uvedeny výsledky pro oba obory.

| `expressionEvaluationOptions` `scope` | Výstup |
| ----- | ------ |
| Vnitřní | z vnořené šablony |
| vnější (nebo výchozí) | z nadřazené šablony |

Následující příklad nasadí server SQL a načte tajný klíč trezoru klíčů, který se použije pro heslo. Obor je nastaven `inner` tak, že dynamicky vytvoří ID trezoru klíčů (viz `adminPassword.reference.keyVault` vnější `parameters`šablony) a předá jej jako parametr vnořené šabloně.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

> [!NOTE]
>
> Pokud je obor `outer`nastaven na , `reference` nelze použít funkci v části výstupy vnořené šablony pro prostředek, který jste nasadili v vnořené šabloně. Chcete-li vrátit hodnoty pro nasazený prostředek ve vnořené šabloně, použijte `inner` obor nebo převeďte vnořenou šablonu na propojenou šablonu.

## <a name="linked-template"></a>Propojená šablona

Chcete-li vytvořit propojení šablony, přidejte do hlavní šablony [prostředek nasazení.](/azure/templates/microsoft.resources/deployments) Ve vlastnosti **templateLink** zadejte identifikátor URI šablony, který má být zahrnut. Následující příklad odkazuje na šablonu, která nasazuje nový účet úložiště.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Při odkazování na propojenou `uri` šablonu nesmí být hodnota místního souboru nebo souboru, který je k dispozici pouze v místní síti. Je nutné zadat hodnotu URI, kterou lze stáhnout jako **http** nebo **https**. 

> [!NOTE]
>
> Můžete odkazovat šablony pomocí parametrů, které nakonec vyřešit na něco, co `_artifactsLocation` používá **http** nebo **https**, například pomocí parametru, jako je takto:`"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]",`



Správce prostředků musí mít přístup k šabloně. Jednou z možností je umístit propojenou šablonu do účtu úložiště a použít identifikátor URI pro tuto položku.

### <a name="parameters-for-linked-template"></a>Parametry pro propojenou šablonu

Parametry propojené šablony můžete zadat buď v externím souboru, nebo v roce. Při poskytování souboru externích parametrů použijte vlastnost **parametersLink:**

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2018-05-01",
  "name": "linkedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
      "contentVersion":"1.0.0.0"
    },
    "parametersLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
      "contentVersion":"1.0.0.0"
    }
  }
  }
]
```

Chcete-li předat hodnoty parametrů v řádku, použijte **vlastnost parameters.**

```json
"resources": [
  {
   "type": "Microsoft.Resources/deployments",
   "apiVersion": "2018-05-01",
   "name": "linkedTemplate",
   "properties": {
     "mode": "Incremental",
     "templateLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
      "contentVersion":"1.0.0.0"
     },
     "parameters": {
      "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
    }
   }
  }
]
```

Nelze použít jak vložky parametry a odkaz na soubor parametrů. Nasazení se nezdaří s `parametersLink` `parameters` chybou, pokud jsou zadány oba a jsou zadány.

## `contentVersion`

Nemusíte poskytovat `contentVersion` vlastnost pro nebo `templateLink` `parametersLink` majetek. Pokud nezadáte `contentVersion`, aktuální verze šablony se nasadí. Pokud zadáte hodnotu pro verzi obsahu, musí odpovídat verzi v propojené šabloně; v opačném případě se nasazení nezdaří s chybou.

## <a name="using-variables-to-link-templates"></a>Použití proměnných k propojení šablon

V předchozích příkladech byly zobrazeny pevně zakódované hodnoty adres URL pro odkazy na šablony. Tento přístup může fungovat pro jednoduchou šablonu, ale nefunguje dobře pro velkou sadu modulárních šablon. Místo toho můžete vytvořit statickou proměnnou, která ukládá základní adresu URL pro hlavní šablonu, a pak dynamicky vytvářet adresy URL pro propojené šablony z této základní adresy URL. Výhodou tohoto přístupu je, že můžete snadno přesunout nebo rozvrstvit šablonu, protože je třeba změnit pouze statickou proměnnou v hlavní šabloně. Hlavní šablona předává správné identifikátory URI v rozložené šabloně.

Následující příklad ukazuje, jak použít základní adresu URL k vytvoření dvou adres URL pro propojené šablony **(sharedTemplateUrl** a **vmTemplate).**

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Můžete také použít [deployment()](template-functions-deployment.md#deployment) získat základní adresu URL pro aktuální šablonu a použít ji k získání adresy URL pro jiné šablony ve stejném umístění. Tento přístup je užitečný, pokud se změní umístění šablony nebo se chcete vyhnout pevným kódovacím adresám URL v souboru šablony. Vlastnost templateLink je vrácena pouze při propojení se vzdálenou šablonou s adresou URL. Pokud používáte místní šablonu, tato vlastnost není k dispozici.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Nakonec byste použít proměnnou ve `uri` vlastnosti. `templateLink`

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>Použití kopie

Chcete-li vytvořit více instancí prostředku s vnořenou šablonou, přidejte prvek kopírování na úrovni prostředku **Microsoft.Resources/deployments.** Nebo pokud je obor vnitřní, můžete přidat kopii do vnořené šablony.

Následující ukázková šablona ukazuje, jak používat kopii s vnořenou šablonou.

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2018-05-01",
  "name": "[concat('nestedTemplate', copyIndex())]",
  // yes, copy works here
  "copy":{
    "name": "storagecopy",
    "count": 2
  },
  "properties": {
    "mode": "Incremental",
    "expressionEvaluationOptions": {
    "scope": "inner"
    },
    "template": {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(variables('storageName'), copyIndex())]",
      "location": "West US",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2"
      // Copy works here when scope is inner
      // But, when scope is default or outer, you get an error
      //"copy":{
      //  "name": "storagecopy",
      //  "count": 2
      //}
      }
    ]
    }
  }
  }
]
```

## <a name="get-values-from-linked-template"></a>Získání hodnot z propojené šablony

Chcete-li získat výstupní hodnotu z propojené šablony, `"[reference('deploymentName').outputs.propertyName.value]"`načtěte hodnotu vlastnosti syntaxí jako: .

Při získávání výstupní vlastnosti z propojené šablony nesmí název vlastnosti obsahovat pomlčku.

Následující příklady ukazují, jak odkazovat na propojenou šablonu a načíst výstupní hodnotu. Propojená šablona vrátí jednoduchou zprávu.  Za prvé, propojená šablona:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "greetingMessage": {
      "value": "Hello World",
      "type" : "string"
    }
  }
}
```

Hlavní šablona nasadí propojenou šablonu a získá vrácenou hodnotu. Všimněte si, že odkazuje na prostředek nasazení podle názvu a používá název vlastnosti vrácené propojené šablony.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
    }
  }
}
```

Stejně jako u jiných typů prostředků můžete nastavit závislosti mezi propojenou šablonou a dalšími prostředky. Pokud jiné prostředky vyžadují výstupní hodnotu z propojené šablony, ujistěte se, že propojená šablona je nasazena před nimi. Nebo když propojená šablona závisí na jiných prostředcích, ujistěte se, že ostatní prostředky jsou nasazeny před propojenou šablonou.

Následující příklad ukazuje šablonu, která nasazuje veřejnou IP adresu a vrací ID prostředku Azure pro tuto veřejnou IP adresu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "eastus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 4
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "resourceID": {
      "type": "string",
      "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
    }
  }
}
```

Chcete-li použít veřejnou IP adresu z předchozí šablony při nasazování správce zatížení, `Microsoft.Resources/deployments` propojte ji se šablonou a deklarujte závislost na prostředku. Veřejná IP adresa v zařízení pro vyrovnávání zatížení je nastavena na výstupní hodnotu z propojené šablony.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "loadBalancers_name": {
      "defaultValue": "mylb",
      "type": "string"
    },
    "publicIPAddresses_name": {
      "defaultValue": "myip",
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/loadBalancers",
      "apiVersion": "2018-11-01",
      "name": "[parameters('loadBalancers_name')]",
      "location": "eastus",
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "LoadBalancerFrontEnd",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                // this is where the output value from linkedTemplate is used
                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
              }
            }
          }
        ],
        "backendAddressPools": [],
        "loadBalancingRules": [],
        "probes": [],
        "inboundNatRules": [],
        "outboundNatRules": [],
        "inboundNatPools": []
      },
      // This is where the dependency is declared
      "dependsOn": [
        "linkedTemplate"
      ]
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
          "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
        }
      }
    }
  ]
}
```

## <a name="deployment-history"></a>Historie nasazení

Správce prostředků zpracovává každou šablonu jako samostatné nasazení v historii nasazení. Hlavní šablona se třemi propojenými nebo vnořenými šablonami se v historii nasazení zobrazí takto:

![Historie nasazení](./media/linked-templates/deployment-history.png)

Tyto samostatné položky v historii můžete použít k načtení výstupních hodnot po nasazení. Následující šablona vytvoří veřejnou IP adresu a vyvede ip adresu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "southcentralus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Static",
        "idleTimeoutInMinutes": 4,
        "dnsSettings": {
          "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
        }
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "returnedIPAddress": {
      "type": "string",
      "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
    }
  }
}
```

Následující šablona odkazuje na předchozí šablonu. Vytvoří tři veřejné IP adresy.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "[concat('linkedTemplate', copyIndex())]",
      "copy": {
        "count": 3,
        "name": "ip-loop"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
        "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
        "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
        }
      }
    }
  ]
}
```

Po nasazení můžete načíst výstupní hodnoty pomocí následujícího skriptu prostředí PowerShell:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Nebo skript Azure CLI v prostředí Bash:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Zabezpečení externí šablony

Přestože propojená šablona musí být externě dostupná, nemusí být obecně dostupná veřejnosti. Šablonu můžete přidat do účtu soukromého úložiště, který je přístupný jenom pro vlastníka účtu úložiště. Potom vytvoříte token sdíleného přístupového podpisu (SAS), který umožní přístup během nasazení. Přidáte token SAS do identifikátoru URI pro propojenou šablonu. I když je token předán jako zabezpečený řetězec, identifikátor URI propojené šablony, včetně tokenu SAS, je zaznamenán v operacích nasazení. Chcete-li omezit expozici, nastavte vypršení platnosti tokenu.

Soubor parametrů může být také omezen na přístup prostřednictvím tokenu SAS.

V současné době nelze propojit šablonu v účtu úložiště, který je za [bránou firewall služby Azure Storage](../../storage/common/storage-network-security.md).

Následující příklad ukazuje, jak předat token SAS při propojení se šablonou:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "containerSasToken": { "type": "securestring" }
  },
  "resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
      "contentVersion": "1.0.0.0"
    }
    }
  }
  ],
  "outputs": {
  }
}
```

V PowerShellu získáte token pro kontejner a nasadit šablony s následujícími příkazy. Všimněte si, že **parametr containerSasToken** je definován v šabloně. Není parametr v **new-azResourceGroupDeployment příkazu.**

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Pro Azure CLI v prostředí Bash získáte token pro kontejner a nasadíte šablony s následujícím kódem:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
  --resource-group ManageGroup \
  --name storagecontosotemplates \
  --query connectionString)
token=$(az storage container generate-sas \
  --name templates \
  --expiry $expiretime \
  --permissions r \
  --output tsv \
  --connection-string $connection)
url=$(az storage blob url \
  --container-name templates \
  --name parent.json \
  --output tsv \
  --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Ukázkové šablony

Následující příklady ukazují běžné použití propojených šablon.

|Hlavní šablona  |Propojená šablona |Popis  |
|---------|---------| ---------|
|[Ahoj světe](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[propojená šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Vrátí řetězec z propojené šablony. |
|[Vyrovnávání zatížení s veřejnou IP adresou](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[propojená šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Vrátí veřejnou IP adresu z propojené šablony a nastaví tuto hodnotu v zátěžovém záložně. |
|[Několik IP adres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [propojená šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Vytvoří několik veřejných IP adres v propojené šabloně.  |

## <a name="next-steps"></a>Další kroky

* Pokud jde o kurz, přečtěte si [informace o tématu: vytvoření propojených šablon Azure Resource Manageru](template-tutorial-create-linked-templates.md).
* Další informace o definování pořadí nasazení pro vaše prostředky, najdete [v tématu Definování závislostí v šablonách Azure Resource Manager .](define-resource-dependency.md)
* Informace o tom, jak definovat jeden prostředek, ale vytvořit mnoho instancí, najdete v tématu [vytvoření více instancí prostředků ve Správci prostředků Azure](copy-resources.md).
* Postup nastavení šablony v účtu úložiště a generování tokenu SAS najdete v tématu [Nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShellu](deploy-powershell.md) nebo Nasazení prostředků pomocí šablon Správce prostředků a Azure [CLI](deploy-cli.md).
