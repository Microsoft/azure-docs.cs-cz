---
title: Propojení šablony pro nasazení v Azure | Dokumentace Microsoftu
description: Popisuje způsob použití propojených šablon v šabloně Azure Resource Manageru k vytvoření řešení modulární šablony. Ukazuje, jak předat hodnoty, parametry, zadejte soubor parametrů a dynamicky generovaný adresy URL.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2018
ms.author: tomfitz
ms.openlocfilehash: 724b1a2562e4723bd02c97cdecb0ef7dbd8ed177
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139057"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Použití propojené a vnořené šablony při nasazování prostředků Azure.

K nasazení svého řešení, můžete buď s jednou šablonou, nebo hlavní šablony s mnoha související šablony. Související šablony může být buď samostatný soubor, který je propojený s z hlavní šablony, nebo šablonu, která je vnořená v rámci hlavní šablony.

Pro malé až střední řešení s jednou šablonou je snáze pochopit a udržovat. Můžete zobrazit všechny prostředky a hodnoty v jednom souboru. Pro pokročilé scénáře propojené šablony vám umožní rozdělit řešení do cílové komponenty a opakovaně používat šablony.

Při použití propojených šablon, vytvoříte hlavní šablonu, která přijímá hodnot parametrů během nasazování. Hlavní šablony obsahuje propojených šablon a předá tyto šablony podle potřeby hodnoty.

Podívejte se kurz [kurz: vytvoření propojenými šablonami Azure Resource Manageru](./resource-manager-tutorial-create-linked-templates.md).

## <a name="link-or-nest-a-template"></a>Odkaz nebo vnořené šablony

Chcete-li propojit s jinou šablonu, přidejte **nasazení** prostředků do hlavní šablony.

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

Vlastnosti, které zadáte pro zdroj nasazení se liší podle toho, jestli jste odkazování na externí šablony nebo vnoření vložená šablona v šabloně hlavní.

Pro obě propojené a vnořené šablony, můžete použít pouze [přírůstkové](deployment-modes.md) režimu nasazení.

### <a name="nested-template"></a>Vnořené šablony

Chcete-li vnořené šablony v rámci hlavní šablony, použijte **šablony** vlastnost a zadáním syntaxe šablony.

```json
"resources": [
  {
    "apiVersion": "2017-05-10",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
              "accountType": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> Vnořené šablony nemůžete použít parametry a proměnné, které jsou definovány v rámci vnořené šablony. Můžete použít parametry a proměnné z hlavní šablony. V předchozím příkladu `[variables('storageName')]` načte hodnotu z hlavní šablony, ne vnořené šablony. Toto omezení se nevztahuje na externí šablony.
>
> Nelze použít `reference` funkce v část Outputs následujícím vnořené šablony. Na návratové hodnoty pro nasazený prostředek ve vnořené šablony, převeďte vnořené šablony na propojenou šablonu.

Vnořené šablony vyžaduje [stejné vlastnosti](resource-group-authoring-templates.md) jako standardní šablony.

### <a name="external-template-and-external-parameters"></a>Externí šablony a externí parametrů

Odkaz na externí šablony a soubor parametrů, použijte **templateLink** a **parametersLink**. Při propojování na šablonu, musí být ve službě Správce prostředků k němu mít přístup. Nelze zadat místní soubor nebo soubor, který je dostupný jenom u vaší místní síti. Můžete zadat pouze hodnotu identifikátoru URI, která obsahuje buď **http** nebo **https**. Jednou z možností je umístíte propojené šablony v účtu úložiště, a použijte identifikátor URI pro danou položku.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
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

Není nutné poskytovat `contentVersion` vlastnost pro šablony nebo parametry. Pokud nezadáte hodnotu verze obsahu, je aktuální verze šablony nasazení. Pokud zadáte hodnotu pro verze obsahu, se musí shodovat s verzí v propojené šablony; v opačném případě se nasazení nezdaří s chybou.

### <a name="external-template-and-inline-parameters"></a>Externí šablony a vložené parametry

Nebo můžete zadat parametr vložené. Do souboru parametrů nelze použít vložený parametry a odkaz. Nasazení se nezdaří s chybou při obě `parametersLink` a `parameters` jsou uvedeny.

Chcete-li předat hodnotu z hlavní šablony propojené šablony, použijte **parametry**.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
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

## <a name="using-variables-to-link-templates"></a>Použití proměnných k propojení šablony

Předchozí příklady nám ukázaly pevně definovaných hodnot adresy URL pro odkazy šablony. Tento přístup může fungovat pro jednoduchou šablonou, ale nebude fungovat dobře, při práci s rozsáhlou sadou modulární šablony. Místo toho můžete vytvořit statickou proměnnou, která ukládá základní adresu URL pro hlavní šablony a dynamicky vytvářet adresy URL pro propojené šablony z této základní adresu URL. Výhodou tohoto přístupu je můžete snadno přesunout nebo vytvořit fork šablony, protože je potřeba jenom změnit statická proměnná v šabloně hlavní. Hlavní šablony předá správné identifikátory URI v šabloně rozložená.

Následující příklad ukazuje, jak můžete vytvořit dvě adresy URL pro propojenými šablonami základní adresu URL (**sharedTemplateUrl** a **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Můžete také použít [deployment()](resource-group-template-functions-deployment.md#deployment) k získání základní adresu URL pro aktuální šablony a použijte ho k získání adresy URL pro další šablony ve stejném umístění. Tento přístup je užitečný, pokud se změny umístění šablony nebo pokud chcete se vyhnout pevnému kódování adresy URL v souboru šablony. Vlastnost templateLink dochází pouze při připojování ke vzdálené šablony s adresou URL. Pokud používáte místní šablonu, tato vlastnost není k dispozici.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Získání hodnot z propojené šablony

Výstupní hodnota z propojené šablony získáte načtení hodnoty vlastností se syntaxí, jako jsou: `"[reference('deploymentName').outputs.propertyName.value]"`.

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
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
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

Podobně jako ostatní typy prostředků můžete nastavit závislosti mezi propojené šablony a dalších prostředků. Proto když jiné prostředky vyžadují výstupní hodnota z propojené šablony, ujistěte se, že nasazení propojené šablony před sebou. Nebo, pokud propojené šablony závisí na jiné prostředky, ujistěte se, že před propojené šablony jsou nasazené jiné prostředky.

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
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
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
            "name": "[parameters('loadBalancers_name')]",
            "apiVersion": "2017-06-01",
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
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
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

## <a name="linked-and-nested-templates-in-deployment-history"></a>Propojené a vnořené šablony v historii nasazení

Každou šablonu Resource Manageru zpracovává jako samostatného nasazení v historii nasazení. Proto hlavní šablony s tři propojené nebo vnořené šablony se zobrazí v historii nasazení jako:

![Historie nasazení](./media/resource-group-linked-templates/deployment-history.png)

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
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
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
            "apiVersion": "2017-05-10",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            },
            "copy": {
                "count": 3,
                "name": "ip-loop"
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
    $deployment = Get-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
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

Následující příklad ukazuje, jak předat SAS token při propojování do šablony:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
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

V prostředí PowerShell získání tokenu pro kontejneru a nasazení šablon pomocí následujících příkazů. Všimněte si, **containerSasToken** parametr je definován v šabloně. Není parametrem **New-AzureRmResourceGroupDeployment** příkazu.

```azurepowershell-interactive
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
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
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[Propojené šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Vrátí řetězec z propojené šablony. |
|[Nástroj pro vyrovnávání zatížení s veřejnou IP adresu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[Propojené šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Vrátí veřejnou IP adresu z propojené šablony a nastaví tuto hodnotu v nástroji pro vyrovnávání zatížení. |
|[Několik IP adres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [Propojené šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Vytvoří několik veřejných IP adres v propojené šablony.  |

## <a name="next-steps"></a>Další postup

* Absolvovat kurz, naleznete v tématu [kurz: vytvoření propojenými šablonami Azure Resource Manageru](./resource-manager-tutorial-create-linked-templates.md).
* Další informace o definování pořadí nasazení pro vaše prostředky, najdete v článku [definování závislostí v šablonách Azure Resource Manageru](resource-group-define-dependencies.md).
* Zjistěte, jak definovat jeden prostředek, ale vytvořit velký počet instancí, najdete v článku [vytvořit více instancí prostředku v Azure Resource Manageru](resource-group-create-multiple.md).
* Pokyny k nastavení šablony v účtu úložiště a generování tokenu SAS naleznete v tématu [nasazení prostředků pomocí šablon Resource Manageru a prostředí Azure PowerShell](resource-group-template-deploy.md) nebo [nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](resource-group-template-deploy-cli.md).
