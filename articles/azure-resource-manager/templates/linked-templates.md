---
title: Propojení šablon pro nasazení
description: Popisuje způsob použití propojených šablon v šabloně Azure Resource Manageru k vytvoření řešení modulární šablony. Ukazuje, jak předat hodnoty, parametry, zadejte soubor parametrů a dynamicky generovaný adresy URL.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: e26b795a645ab9128dd738ba6a54b66ac0b7da2a
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78272577"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Použití propojené a vnořené šablony při nasazování prostředků Azure.

K nasazení složitých řešení můžete rozdělit šablonu na mnoho souvisejících šablon a pak je nasadit společně prostřednictvím hlavní šablony. Související šablony mohou být samostatné soubory nebo syntaxe šablony, která je vložena do hlavní šablony. V tomto článku se používá pojem **propojená šablona** pro odkazování na samostatný soubor šablony, který je propojený s hlavní šablonou. Používá termín **vnořená šablona** pro odkazování na vloženou syntaxi šablony v rámci hlavní šablony.

Pro malé až střední řešení s jednou šablonou je snáze pochopit a udržovat. Můžete zobrazit všechny prostředky a hodnoty v jednom souboru. V případě pokročilých scénářů umožňují propojené šablony rozdělit řešení na cílené součásti. Tyto šablony můžete snadno opakovaně použít pro jiné scénáře.

Kurz najdete v tématu [kurz: Vytvoření propojených Azure Resource Manager šablon](template-tutorial-create-linked-templates.md).

> [!NOTE]
> U propojených nebo vnořených šablon můžete použít pouze režim [přírůstkového](deployment-modes.md) nasazení.
>

## <a name="nested-template"></a>Vnořené šablony

Chcete-li vnořit šablonu, přidejte do hlavní šablony [prostředek nasazení](/azure/templates/microsoft.resources/deployments) . Do vlastnosti **Template (šablona** ) zadejte syntaxi šablony.

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

Následující příklad nasadí účet úložiště prostřednictvím vnořené šablony.

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

### <a name="scope-for-expressions-in-nested-templates"></a>Obor pro výrazy ve vnořených šablonách

Při použití vnořené šablony můžete určit, zda jsou výrazy šablony vyhodnocovány v rámci rozsahu nadřazené šablony nebo vnořené šablony. Rozsah určuje, jak jsou řešeny parametry, proměnné a funkce jako [zdrojová](template-functions-resource.md#resourcegroup) a [odběrová](template-functions-resource.md#subscription) pole.

Rozsah můžete nastavit pomocí vlastnosti `expressionEvaluationOptions`. Ve výchozím nastavení je vlastnost `expressionEvaluationOptions` nastavena na hodnotu `outer`, což znamená, že používá nadřazený obor šablony. Nastavte hodnotu na `inner` pro oborové výrazy na vnořenou šablonu.

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

Následující šablona ukazuje, jak jsou řešeny výrazy šablony podle oboru. Obsahuje proměnnou s názvem `exampleVar`, která je definována v nadřazené šabloně i ve vnořené šabloně. Vrátí hodnotu proměnné.

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

Hodnota proměnné se změní v závislosti na rozsahu. V následující tabulce jsou uvedeny výsledky pro oba obory.

| Rozsah | Výstup |
| ----- | ------ |
| vnitřní | z vnořené šablony |
| vnější (nebo výchozí) | z nadřazené šablony |

Následující příklad nasadí SQL Server a načte tajný klíč trezoru klíčů, který se použije pro heslo. Rozsah je nastaven na `inner`, protože dynamicky vytvoří ID trezoru klíčů a předá ho jako parametr vnořené šabloně.

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
> Pokud je obor nastaven na `outer`, nemůžete použít funkci `reference` v části výstupy vnořené šablony pro prostředek, který jste nasadili ve vnořené šabloně. Chcete-li vrátit hodnoty nasazeného prostředku ve vnořené šabloně, buď použijte vnitřní rozsah, nebo převeďte vnořenou šablonu na propojenou šablonu.

## <a name="linked-template"></a>Propojené šablony

Pokud chcete propojit šablonu, přidejte do hlavní šablony [prostředek nasazení](/azure/templates/microsoft.resources/deployments) . Do vlastnosti **templateLink** zadejte identifikátor URI šablony, která se má zahrnout. Následující příklad odkazuje na šablonu, která nasadí nový účet úložiště.

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

Nelze zadat místní soubor nebo soubor, který je dostupný jenom u vaší místní síti. Můžete zadat jenom hodnotu identifikátoru URI, která zahrnuje buď **http** , nebo **https**. Správce prostředků musí být schopné získat přístup k šabloně. Jednou z možností je umístíte propojené šablony v účtu úložiště, a použijte identifikátor URI pro danou položku.

Pro šablonu nebo parametry není nutné zadávat vlastnost `contentVersion`. Pokud nezadáte hodnotu verze obsahu, je aktuální verze šablony nasazení. Pokud zadáte hodnotu pro verze obsahu, se musí shodovat s verzí v propojené šablony; v opačném případě se nasazení nezdaří s chybou.

### <a name="parameters-for-linked-template"></a>Parametry pro propojenou šablonu

Parametry pro propojenou šablonu můžete zadat buď v externím souboru, nebo do vloženého. Při poskytování externího souboru parametrů použijte vlastnost **parametersLink** :

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

K předání hodnot parametrů do inline použijte vlastnost **Parameters** .

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

Do souboru parametrů nelze použít vložený parametry a odkaz. Nasazení se nezdařila s chybou, pokud jsou zadány `parametersLink` i `parameters`.

## <a name="using-copy"></a>Pomocí kopírování

Chcete-li vytvořit více instancí prostředku s vnořenou šablonou, přidejte element Copy na úrovni prostředku **Microsoft. Resources/Deployments** . Nebo, pokud je oborem vnitřní, můžete přidat kopii v rámci vnořené šablony.

Následující příklad šablony ukazuje, jak použít kopírování s vnořenou šablonou.

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

## <a name="using-variables-to-link-templates"></a>Použití proměnných k propojení šablony

Předchozí příklady nám ukázaly pevně definovaných hodnot adresy URL pro odkazy šablony. Tento přístup může fungovat pro jednoduchou šablonou, ale nebude fungovat dobře, při práci s rozsáhlou sadou modulární šablony. Místo toho můžete vytvořit statickou proměnnou, která ukládá základní adresu URL pro hlavní šablony a dynamicky vytvářet adresy URL pro propojené šablony z této základní adresu URL. Výhodou tohoto přístupu je můžete snadno přesunout nebo vytvořit fork šablony, protože je potřeba jenom změnit statická proměnná v šabloně hlavní. Hlavní šablony předá správné identifikátory URI v šabloně rozložená.

Následující příklad ukazuje, jak použít základní adresu URL pro vytvoření dvou adres URL pro propojené šablony (**sharedTemplateUrl** a **vmTemplate**).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Můžete také použít [Deployment ()](template-functions-deployment.md#deployment) pro získání základní adresy URL pro aktuální šablonu a použít ji k získání adresy URL pro jiné šablony ve stejném umístění. Tento přístup je užitečný, pokud se změny umístění šablony nebo pokud chcete se vyhnout pevnému kódování adresy URL v souboru šablony. Vlastnost templateLink dochází pouze při připojování ke vzdálené šablony s adresou URL. Pokud používáte místní šablonu, tato vlastnost není k dispozici.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Získání hodnot z propojené šablony

Chcete-li získat výstupní hodnotu z propojené šablony, načtěte hodnotu vlastnosti s syntaxí jako: `"[reference('deploymentName').outputs.propertyName.value]"`.

Při získávání výstupu vlastnost z propojené šablony, název vlastnosti nemůže obsahovat čárku.

Následující příklady ukazují, jak odkazovat na propojenou šablonu a načíst výstupní hodnoty. Propojené šablony vrátí zprávu jednoduché.

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

Hlavní šablona nasadí propojenou šablonu a získá vrácené hodnoty. Všimněte si, že odkazuje na prostředek nasazení podle názvu a použije název vlastnosti vrácené propojené šablony.

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

Podobně jako ostatní typy prostředků můžete nastavit závislosti mezi propojené šablony a dalších prostředků. Pokud jiné zdroje vyžadují výstupní hodnotu z propojené šablony, zajistěte, aby byla propojená šablona nasazena před nimi. Nebo, pokud propojené šablony závisí na jiné prostředky, ujistěte se, že před propojené šablony jsou nasazené jiné prostředky.

Následující příklad ukazuje šablonu, která nasadí veřejné IP adresy a vrátí ID prostředku:

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

Použít veřejnou IP adresu z předchozí šablonu postupem při nasazování nástroje pro vyrovnávání zatížení, odkaz na šablonu a přidání závislosti na nasazení prostředku. Veřejné IP adresy na nástroj pro vyrovnávání zatížení nastavena na výstupní hodnota z propojené šablony.

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

Každou šablonu Resource Manageru zpracovává jako samostatného nasazení v historii nasazení. V historii nasazení se zobrazí hlavní šablona se třemi propojenými nebo vnořenými šablonami jako:

![Historie nasazení](./media/linked-templates/deployment-history.png)

Tyto samostatné položky v historii můžete použít k načtení hodnoty výstup po dokončení nasazení. Následující šablony vytvoří veřejnou IP adresu a vypíše IP adresu:

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

Následující šablony odkazuje na předchozí šablonu postupem. Vytvoří tři veřejné IP adresy.

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

Po nasazení můžete načíst výstup hodnot pomocí následujícího skriptu prostředí PowerShell:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Nebo skript rozhraní příkazového řádku Azure v prostředí Bash:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az group deployment show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Externí šablony zabezpečení

I když propojené šablony musí být externě k dispozici, nemusí být obecně dostupná. Šablony můžete přidat do privátního úložiště účtu, který je přístupný pouze majiteli účtu úložiště. Pak vytvoříte token sdíleného přístupového podpisu (SAS) umožňuje přístup během nasazení. Přidejte tento token SAS k identifikátoru URI pro propojenou šablonu. I v případě, že token, který je předaný jako zabezpečený řetězec, identifikátor URI propojené šablony, včetně SAS token je přihlášen operací nasazení. K omezení rizika ohrožení, nastavte vypršení platnosti tokenu.

Soubor parametrů lze také omezit na přístupu prostřednictvím tokenu SAS.

V současné době nemůžete propojit šablonu v účtu úložiště, který je za [Azure Storage bránou firewall](../../storage/common/storage-network-security.md).

Následující příklad ukazuje, jak předat SAS token při propojování do šablony:

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

V prostředí PowerShell získání tokenu pro kontejneru a nasazení šablon pomocí následujících příkazů. Všimněte si, že parametr **containerSasToken** je definován v šabloně. Nejedná se o parametr v příkazu **New-AzResourceGroupDeployment** .

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Azure CLI v prostředí Bash získání tokenu pro kontejneru a nasazení šablon pomocí následujícího kódu:

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
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Příklad šablony

Následující příklady ukazují běžné způsoby použití propojených šablon.

|Hlavní šablony  |Propojené šablony |Popis  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[odkazovaná šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Vrátí řetězec z propojené šablony. |
|[Load Balancer s veřejnou IP adresou](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[odkazovaná šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Vrátí veřejnou IP adresu z propojené šablony a nastaví tuto hodnotu v nástroji pro vyrovnávání zatížení. |
|[Několik IP adres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [odkazovaná šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Vytvoří několik veřejných IP adres v propojené šablony.  |

## <a name="next-steps"></a>Další kroky

* Kurz najdete v tématu [kurz: Vytvoření propojených Azure Resource Manager šablon](template-tutorial-create-linked-templates.md).
* Další informace o definování pořadí nasazení pro vaše prostředky najdete v tématu [Definování závislostí v šablonách Azure Resource Manager](define-resource-dependency.md).
* Informace o tom, jak definovat jeden prostředek, ale vytvořit mnoho instancí, najdete [v tématu Vytvoření více instancí prostředků v Azure Resource Manager](copy-resources.md).
* Postup při nastavení šablony v účtu úložiště a vygenerování tokenu SAS najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](deploy-powershell.md) nebo [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](deploy-cli.md).
