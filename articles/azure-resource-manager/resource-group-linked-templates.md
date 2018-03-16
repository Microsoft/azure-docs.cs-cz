---
title: "Odkaz šablony pro nasazení Azure | Microsoft Docs"
description: "Popisuje způsob použití propojených šablon v šablonu Azure Resource Manageru k vytvoření řešení modulární šablony. Ukazuje, jak chcete předat hodnoty parametrů, zadejte soubor parametrů a dynamicky vytvořené adresy URL."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2018
ms.author: tomfitz
ms.openlocfilehash: c9a7fc0025e6f4f2b793f0616b4bc41c22c2a498
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Pomocí propojená a vnořené šablony při nasazování prostředků Azure

K nasazení řešení, můžete použít jednu šablonu nebo hlavní šablonu s více souvisejících šablon. Související šablonou může být buď samostatný soubor, který je propojený s z hlavní šablony, nebo šablonu, která je vnořená v rámci hlavní šablony.

Pro malé a střední řešení je jednou šablonou snadněji pochopit a spravovat. Budete moci zobrazit všechny prostředky a hodnot v jednom souboru. Pro pokročilé scénáře propojených šablon umožňují rozdělení řešení do cílové součásti a opakovaně používat šablony.

Při použití propojených šablon, vytvoříte hlavní šablonu, která přijímá hodnot parametrů během nasazování. Hlavní šablona obsahuje všechny propojené šablony a předá tyto šablony podle potřeby hodnoty.

## <a name="link-or-nest-a-template"></a>Propojení nebo vnořit šablonu

Propojit s jinou šablonu, přidejte **nasazení** prostředků do hlavní šablony.

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

Vlastnosti, které zadáte pro prostředek nasazení lišit v závislosti na tom, jestli jsou propojení na externí šablonu nebo vnoření šablonu vložené v šabloně hlavní.

### <a name="nested-template"></a>Vnořené šablony

Pokud chcete vnořit šablony v rámci hlavní šablony, pomocí **šablony** vlastnost a zadejte se syntaxí šablony.

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
> Pro vnořené šablony nemůžete použít parametry nebo proměnné, které jsou definovány v rámci vnořené šablony. Můžete použít parametry a proměnné z hlavní šablony. V předchozím příkladu `[variables('storageName')]` načte hodnotu z hlavní šablony není vnořené šablony. Toto omezení se nevztahuje na externí šablony.
>
> Nelze použít `reference` funkce v části výstupy vnořené šablony. K návratu hodnot pro prostředek nasazené v šabloně vnořené, převeďte na šablonu propojené vnořené šablony.

### <a name="external-template-and-external-parameters"></a>Externí šablony a externí parametry

Odkaz na externí šablonu a soubor parametrů, použijte **templateLink** a **parametersLink**. Při propojení s šablonu, musí být služby Správce prostředků k němu přístup. Nelze zadat místní soubor nebo soubor, který je k dispozici ve vaší místní síti. Můžete pouze zadat hodnotu identifikátoru URI, která zahrnuje buď **http** nebo **https**. Jednou z možností je umístit propojené šablony v účtu úložiště, a použijte identifikátor URI pro tuto položku.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
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

### <a name="external-template-and-inline-parameters"></a>Externí parametry šablony a vložené

Nebo můžete zadat vložený parametr. Chcete-li předat hodnotu z hlavní šablony do propojené šablony, použijte **parametry**.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
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

## <a name="using-variables-to-link-templates"></a>Použití proměnných propojení šablony

Předchozí příklady nám ukázaly pevně definovaných hodnot adresu URL pro odkazy. šablony. Tento přístup může fungovat pro jednoduchou šablonu, ale nebude fungovat správně při práci s velké sady modulární šablony. Místo toho můžete vytvořit statickou proměnné, která ukládá základní adresu URL pro hlavní šablonu a dynamicky vytvářet adresy URL pro propojené šablony z této základní adresu URL. Výhodou tohoto přístupu je snadno přesunout nebo rozvětvit šablony, protože potřebujete změnit statické proměnné v šabloně hlavní. Hlavní šablonu předá správné identifikátory URI v rámci rozložená šablony.

Následující příklad ukazuje, jak použít základní adresu URL k vytvoření dvou adres URL pro propojených šablon (**sharedTemplateUrl** a **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Můžete také použít [deployment()](resource-group-template-functions-deployment.md#deployment) získat základní adresu URL pro aktuální šablony a použít k získání adresy URL pro další šablony ve stejném umístění. Tento přístup je užitečný, pokud se změní vaši polohu šablony (možná z důvodu Správa verzí) nebo chcete vyhnout pevného kódování adresy URL v souboru šablony.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Získání hodnoty z propojené šablony

Výstupní hodnotu z propojené šablony získáte načíst hodnotu vlastnosti se syntaxí, jako je: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Následující příklady ukazují, jak odkazovat na šablonu propojené a načíst výstupní hodnotu. Propojené šablony vrátí zprávu jednoduché.

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

Hlavní šablona nasadí propojené šablony a získá vrácené hodnoty. Všimněte si, že odkazuje na prostředek nasazení podle názvu a používá název vlastnosti vrácených šablonou propojené.

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
                "mode": "incremental",
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

Podobně jako ostatní typy prostředků můžete nastavit závislosti mezi propojené šablony a dalším prostředkům. Proto pokud další prostředky vyžadují hodnotu výstup z propojené šablony, jste měli jistotu, že propojené šablony nasazení před sebou. Nebo, pokud propojené šablony závisí na jiné prostředky, můžete zajistit, že jiné prostředky, které jsou nasazeny před propojené šablony.

Následující příklad ukazuje šablonu, která nasadí veřejnou IP adresu a vrátí ID prostředku:

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

Chcete-li použít veřejnou IP adresu z předchozí šablony při nasazení služby Vyrovnávání zatížení, propojení do šablony a přidat závislost na prostředku nasazení. Veřejnou IP adresu na Vyrovnávání zatížení nastavena na hodnotu výstup z propojené šablony.

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

Správce prostředků zpracovává každé šabloně jako samostatné nasazení v historii nasazení. Proto se třemi šablonami propojenou nebo vnořené hlavní šablonu se zobrazí v historii nasazení jako:

![Historie nasazení](./media/resource-group-linked-templates/deployment-history.png)

Tyto samostatné položky v historii slouží k načtení hodnoty výstup po nasazení. Následující šablony vytvoří veřejnou IP adresu a výstupy IP adresu:

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

Následující odkazy šablony do předchozí šablony. Vytvoří tři veřejné IP adresy.

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

Po dokončení nasazení můžete načíst výstup hodnot pomocí následujícího skriptu prostředí PowerShell:

```powershell
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Nebo skriptu rozhraní příkazového řádku Azure:

```azurecli
for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Externí šablony zabezpečení

I když propojené šablony musí být externě k dispozici, nemusí být obecně dostupné pro veřejnost. Šablony můžete přidat na účet privátní úložiště, které je přístupné pouze majiteli účtu úložiště. Pak vytvořte token sdílený přístupový podpis (SAS) pro povolení přístupu během nasazení. Přidejte tento token SAS URI propojené šablony. I když token, je předaná jako zabezpečený řetězec, je identifikátor URI propojené šablony, včetně tokenu SAS, přihlášení operace nasazení. K omezení rizika, nastavte vypršení platnosti pro daný token.

Soubor parametrů také možné omezit přístup pomocí tokenu SAS.

Následující příklad ukazuje, jak předat SAS token při propojení do šablony:

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
        "mode": "incremental",
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

V prostředí PowerShell můžete získat token pro kontejner a nasazení šablon s:

```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

V Azure CLI získat token pro kontejner a nasazení šablon s následujícím kódem:

```azurecli
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

Následující příklady ukazují běžná použití propojených šablon.

|Hlavní šablony  |Propojené šablony |Popis  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[Propojené šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Vrátí řetězec z propojené šablony. |
|[Nástroj pro vyrovnávání zatížení s veřejnou IP adresu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[Propojené šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Vrátí veřejnou IP adresu z propojené šablony a nastavuje tuto hodnotu nástroji pro vyrovnávání zatížení. |
|[Několik IP adres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [Propojené šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Vytvoří víc veřejných IP adres v propojené šablony.  |

## <a name="next-steps"></a>Další postup

* Další informace o definování pořadí nasazení pro vaše prostředky najdete v tématu [definování závislostí v šablonách Azure Resource Manager](resource-group-define-dependencies.md).
* Zjistěte, jak definovat jeden prostředek ale vytvořit mnoho instancí, najdete v tématu [vytvořit více instancí prostředků ve službě Správce prostředků Azure](resource-group-create-multiple.md).
* Postup nastavení šablony v účtu úložiště a generování tokenu SAS naleznete v tématu [nasazení prostředků pomocí šablony Resource Manageru a prostředí Azure PowerShell](resource-group-template-deploy.md) nebo [nasazení prostředků pomocí šablony Resource Manageru a rozhraní příkazového řádku Azure](resource-group-template-deploy-cli.md).