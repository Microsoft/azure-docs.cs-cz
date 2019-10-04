---
title: Propojení šablon pro nasazení Azure | Microsoft Docs
description: Popisuje způsob použití propojených šablon v šabloně Azure Resource Manager k vytvoření modulárního řešení šablon. Ukazuje, jak předat hodnoty parametrů, určit soubor parametrů a dynamicky vytvořené adresy URL.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: tomfitz
ms.openlocfilehash: 59af553f4080ca86e964b75234e4d812297d8541
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827334"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Použití propojených a vnořených šablon při nasazování prostředků Azure

K nasazení vašeho řešení můžete použít jednu šablonu nebo hlavní šablonu s mnoha souvisejícími šablonami. Související šablony mohou být buď samostatné soubory, které jsou propojeny s hlavní šablonou, nebo šablony, které jsou vnořené v rámci hlavní šablony.

Pro malá až středně střední řešení je jedinou šablonou snazší pochopení a udržování. Všechny prostředky a hodnoty můžete zobrazit v jednom souboru. V případě pokročilých scénářů umožňují propojené šablony rozdělit řešení na cílené součásti. Tyto šablony můžete snadno opakovaně použít pro jiné scénáře.

Při použití propojených šablon vytvoříte hlavní šablonu, která přijímá hodnoty parametrů během nasazování. Hlavní šablona obsahuje všechny propojené šablony a v případě potřeby předává hodnoty do těchto šablon.

Kurz najdete v tématu [kurz: Vytvoření propojených Azure Resource Manager šablon](./resource-manager-tutorial-create-linked-templates.md).

> [!NOTE]
> U propojených nebo vnořených šablon můžete použít pouze režim [přírůstkového](deployment-modes.md) nasazení.
>

## <a name="deployments-resource"></a>Prostředek nasazení

Chcete-li vytvořit odkaz na jinou šablonu, přidejte do hlavní šablony prostředek **nasazení** .

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
        "mode": "Incremental",
        <nested-template-or-external-template>
    }
  }
]
```

Vlastnosti, které zadáte pro prostředek nasazení, se liší v závislosti na tom, jestli odkazujete na externí šablonu nebo vnoření vložené šablony do hlavní šablony.

## <a name="nested-template"></a>Vnořená šablona

Chcete-li vnořit šablonu do hlavní šablony, použijte vlastnost **template** a zadejte syntaxi šablony.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "nestedTemplate",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[variables('storageName')]",
            "location": "West US",
            "kind": "StorageV2",
            "sku": {
                "name": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> U vnořených šablon nelze použít parametry nebo proměnné, které jsou definovány v rámci vnořené šablony. Z hlavní šablony můžete použít parametry a proměnné. V předchozím příkladu `[variables('storageName')]` načte hodnotu z hlavní šablony, nikoli vnořenou šablonu. Toto omezení se nevztahuje na externí šablony.
>
> Pro dva prostředky definované uvnitř vnořené šablony a jeden prostředek závisí na druhém, hodnota závislosti je jednoduše názvem závislého prostředku:
> ```json
> "dependsOn": [
>   "[variables('storageAccountName')]"
> ],
> ```
>
> Nemůžete použít funkci `reference` v části výstupy vnořené šablony pro prostředek, který jste nasadili ve vnořené šabloně. Chcete-li vrátit hodnoty nasazeného prostředku ve vnořené šabloně, převeďte vnořenou šablonu na propojenou šablonu.

Vnořená šablona vyžaduje [stejné vlastnosti](resource-group-authoring-templates.md) jako standardní šablona.

## <a name="external-template"></a>Externí šablona

Chcete-li vytvořit odkaz na externí šablonu, použijte vlastnost **templateLink** . Nemůžete zadat místní soubor nebo soubor, který je k dispozici pouze v místní síti. Můžete zadat jenom hodnotu identifikátoru URI, která zahrnuje buď **http** , nebo **https**. Správce prostředků musí být schopné získat přístup k šabloně.

Jednou z možností je umístit propojenou šablonu do účtu úložiště a použít identifikátor URI pro tuto položku.

Parametry pro externí šablonu můžete zadat buď v externím souboru, nebo do vloženého.

### <a name="external-parameters"></a>Externí parametry

Při poskytování externího souboru parametrů použijte vlastnost **parametersLink** :

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

Pro šablonu nebo parametry není nutné zadávat vlastnost `contentVersion`. Pokud nezadáte hodnotu verze obsahu, bude nasazena aktuální verze šablony. Pokud zadáte hodnotu verze obsahu, musí odpovídat verzi v propojené šabloně. v opačném případě se nasazení nepovede a dojde k chybě.

### <a name="inline-parameters"></a>Vložené parametry

Nebo můžete zadat vložený parametr. Nemůžete použít vložené parametry ani odkaz na soubor parametrů. Nasazení se nezdařila s chybou, pokud je zadána hodnota `parametersLink` i `parameters`.

Chcete-li předat hodnotu z hlavní šablony do propojené šablony, použijte vlastnost **Parameters** .

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

## <a name="using-copy"></a>Pomocí kopírování

Chcete-li vytvořit více instancí prostředku s vnořenou šablonou, přidejte element Copy na úrovni prostředku **Microsoft. Resources/Deployments** .

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
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "kind": "StorageV2",
            "sku": {
              "name": "Standard_LRS"
            }
            // no, copy doesn't work here
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

## <a name="using-variables-to-link-templates"></a>Použití proměnných k propojení šablon

Předchozí příklady ukázaly pevně kódované hodnoty adresy URL pro odkazy šablony. Tento přístup může fungovat pro jednoduchou šablonu, ale nefunguje dobře při práci s velkou sadou modulárních šablon. Místo toho můžete vytvořit statickou proměnnou, která bude ukládat základní adresu URL pro hlavní šablonu a pak dynamicky vytvořit adresy URL pro propojené šablony z této základní adresy URL. Výhodou tohoto přístupu je, že šablonu můžete snadno přesunout nebo rozvětvit, protože stačí změnit pouze statickou proměnnou v hlavní šabloně. Hlavní šablona projde správné identifikátory URI v rámci rozložené šablony.

Následující příklad ukazuje, jak použít základní adresu URL pro vytvoření dvou adres URL pro propojené šablony (**sharedTemplateUrl** a **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Můžete také použít [Deployment ()](resource-group-template-functions-deployment.md#deployment) pro získání základní adresy URL pro aktuální šablonu a použít ji k získání adresy URL pro jiné šablony ve stejném umístění. Tento přístup je užitečný v případě, že se změní umístění šablony nebo chcete v souboru šablony vyhnout adresám URL pevného kódování. Vlastnost templateLink je vrácena pouze při odkazování na vzdálenou šablonu s adresou URL. Pokud používáte místní šablonu, tato vlastnost není k dispozici.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Získat hodnoty z propojené šablony

Chcete-li získat výstupní hodnotu z propojené šablony, načtěte hodnotu vlastnosti s syntaxí jako: `"[reference('deploymentName').outputs.propertyName.value]"`.

Při získávání výstupní vlastnosti z propojené šablony název vlastnosti nemůže obsahovat pomlčku.

Následující příklady ukazují, jak odkazovat na propojenou šablonu a načíst výstupní hodnotu. Odkazovaná šablona vrátí jednoduchou zprávu.

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

Hlavní šablona nasadí propojenou šablonu a získá vrácenou hodnotu. Všimněte si, že odkazuje na prostředek nasazení podle názvu a používá název vlastnosti vrácenou propojenou šablonou.

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

Podobně jako u jiných typů prostředků můžete nastavit závislosti mezi propojenou šablonou a dalšími prostředky. Pokud jiné zdroje vyžadují výstupní hodnotu z propojené šablony, zajistěte, aby byla propojená šablona nasazena před nimi. Nebo, pokud propojená šablona spoléhá na jiné prostředky, zajistěte, aby byly nasazeny další prostředky před propojenou šablonou.

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

Chcete-li při nasazování nástroje pro vyrovnávání zatížení použít veřejnou IP adresu z předchozí šablony, připojte se k šabloně a přidejte závislost na prostředek nasazení. Veřejná IP adresa v nástroji pro vyrovnávání zatížení je nastavená na výstupní hodnotu z propojené šablony.

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

## <a name="linked-and-nested-templates-in-deployment-history"></a>Propojené a vnořené šablony v historii nasazení

Správce prostředků zpracuje každou šablonu jako samostatné nasazení v historii nasazení. Proto se v historii nasazení zobrazí hlavní šablona se třemi propojenými nebo vnořenými šablonami jako:

![Historie nasazení](./media/resource-group-linked-templates/deployment-history.png)

Tyto samostatné položky v historii můžete použít k načtení výstupních hodnot po nasazení. Následující šablona vytvoří veřejnou IP adresu a vytvoří výstup IP adresy:

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

Nebo skript Azure CLI v prostředí bash:

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

## <a name="securing-an-external-template"></a>Zabezpečení externí šablony

I když je propojená šablona musí být externě dostupná, nemusí být všeobecně dostupná veřejnosti. Šablonu můžete přidat do účtu privátního úložiště, který je přístupný jenom pro vlastníka účtu úložiště. Pak vytvoříte token sdíleného přístupového podpisu (SAS) pro povolení přístupu během nasazení. Tento token SAS přidáte k identifikátoru URI pro propojenou šablonu. I když je token předán jako zabezpečený řetězec, do operací nasazení se zaprotokoluje identifikátor URI propojené šablony, včetně tokenu SAS. Chcete-li omezit vystavení, nastavte vypršení platnosti tokenu.

Soubor parametrů může být také omezený pro přístup prostřednictvím tokenu SAS.

V současné době nemůžete propojit šablonu v účtu úložiště, který je za [Azure Storage bránou firewall](../storage/common/storage-network-security.md).

Následující příklad ukazuje, jak předat token SAS při odkazování na šablonu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
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

V PowerShellu získáte token pro kontejner a nasadíte šablony pomocí následujících příkazů. Všimněte si, že parametr **containerSasToken** je definován v šabloně. Nejedná se o parametr v příkazu **New-AzResourceGroupDeployment** .

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Pro Azure CLI v prostředí bash získáte token pro kontejner a nasadíte šablony s následujícím kódem:

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

## <a name="example-templates"></a>Příklady šablon

Následující příklady znázorňují běžné použití propojených šablon.

|Hlavní šablona  |Odkazovaná šablona |Popis  |
|---------|---------| ---------|
|[Ahoj světe](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[odkazovaná šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Vrátí řetězec z propojené šablony. |
|[Load Balancer s veřejnou IP adresou](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[odkazovaná šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Vrátí veřejnou IP adresu z propojené šablony a nastaví tuto hodnotu v nástroji pro vyrovnávání zatížení. |
|[Několik IP adres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [odkazovaná šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Vytvoří v propojené šabloně několik veřejných IP adres.  |

## <a name="next-steps"></a>Další kroky

* Kurz najdete v tématu [kurz: Vytvoření propojených Azure Resource Manager šablon](./resource-manager-tutorial-create-linked-templates.md).
* Další informace o definování pořadí nasazení pro vaše prostředky najdete v tématu [Definování závislostí v šablonách Azure Resource Manager](resource-group-define-dependencies.md).
* Informace o tom, jak definovat jeden prostředek, ale vytvořit mnoho instancí, najdete [v tématu Vytvoření více instancí prostředků v Azure Resource Manager](resource-group-create-multiple.md).
* Postup při nastavení šablony v účtu úložiště a vygenerování tokenu SAS najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](resource-group-template-deploy.md) nebo [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](resource-group-template-deploy-cli.md).
