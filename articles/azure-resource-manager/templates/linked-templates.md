---
title: Propojení šablon pro nasazení
description: Popisuje způsob použití propojených šablon v šabloně Azure Resource Manager k vytvoření modulárního řešení šablon. Ukazuje, jak předat hodnoty parametrů, určit soubor parametrů a dynamicky vytvořené adresy URL.
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 1e2ccc57b42f8072c9aa28612d534507b9a674ed
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852094"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Použití propojené a vnořené šablony při nasazování prostředků Azure

K nasazení složitých řešení můžete rozdělit šablonu na mnoho souvisejících šablon a pak je nasadit společně prostřednictvím hlavní šablony. Související šablony mohou být samostatné soubory nebo syntaxe šablony, která je vložena do hlavní šablony. V tomto článku se používá pojem **propojená šablona** pro odkazování na samostatný soubor šablony, na který se odkazuje pomocí odkazu z hlavní šablony. Používá termín **vnořená šablona** pro odkazování na vloženou syntaxi šablony v rámci hlavní šablony.

U malých až středních řešení je jednodušší pochopit a spravovat jedinou šablonu. Všechny prostředky a hodnoty vidíte v jednom souboru. Ve složitějších scénářích umožňují propojené šablony rozdělit řešení do specializovaných komponent. Tyto šablony můžete jednoduše opakovaně použít v jiných scénářích.

Kurz najdete v tématu [kurz: Vytvoření propojených Azure Resource Manager šablon](./deployment-tutorial-linked-template.md).

> [!NOTE]
> U propojených nebo vnořených šablon můžete nastavit režim nasazení pouze na [přírůstkové](deployment-modes.md). Hlavní šablonu je však možné nasadit v úplném režimu. Pokud nasadíte hlavní šablonu v režimu úplného nasazení a propojená nebo vnořená šablona cílí na stejnou skupinu prostředků, budou se prostředky nasazené v propojené nebo vnořené šabloně zahrnout do vyhodnocování nasazení kompletního režimu. Kombinovaná kolekce prostředků nasazených v hlavní šabloně a propojených nebo vnořených šablon je porovnána s existujícími prostředky ve skupině prostředků. Všechny prostředky, které nejsou součástí této kombinované kolekce, se odstraní.
>
> Pokud je propojená nebo vnořená šablona cílí na jinou skupinu prostředků, používá toto nasazení přírůstkový režim.
>

## <a name="nested-template"></a>Vnořená šablona

Chcete-li vnořit šablonu, přidejte do hlavní šablony [prostředek nasazení](/azure/templates/microsoft.resources/deployments) . Do vlastnosti **Template (šablona** ) zadejte syntaxi šablony.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
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
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

### <a name="expression-evaluation-scope-in-nested-templates"></a>Rozsah vyhodnocení výrazu ve vnořených šablonách

Při použití vnořené šablony můžete zadat, jestli se výrazy šablony vyhodnotí v rozsahu nadřazené nebo vnořené šablony. Rozsah určuje, jak jsou řešeny parametry, proměnné a funkce jako [zdrojová](template-functions-resource.md#resourcegroup) a [odběrová](template-functions-resource.md#subscription) pole.

Rozsah můžete nastavit pomocí `expressionEvaluationOptions` Vlastnosti. Ve výchozím nastavení `expressionEvaluationOptions` je vlastnost nastavena na hodnotu `outer` , což znamená, že používá nadřazený obor šablony. Nastavte hodnotu na, aby `inner` byly výrazy vyhodnoceny v rámci rozsahu vnořené šablony.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2019-10-01",
  "name": "nestedTemplate1",
  "properties": {
  "expressionEvaluationOptions": {
    "scope": "inner"
  },
  ...
```

Následující šablona ukazuje, jak jsou řešeny výrazy šablony podle oboru. Obsahuje proměnnou s názvem `exampleVar` , která je definována v nadřazené šabloně i ve vnořené šabloně. Vrátí hodnotu proměnné.

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
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Hodnota `exampleVar` změny závisí na hodnotě `scope` vlastnosti v `expressionEvaluationOptions` . V následující tabulce jsou uvedeny výsledky pro oba obory.

| `expressionEvaluationOptions` oboru | Výstup |
| ----- | ------ |
| vnořen | z vnořené šablony |
| vnější (nebo výchozí) | z nadřazené šablony |

Následující příklad nasadí SQL Server a načte tajný klíč trezoru klíčů, který se použije pro heslo. Rozsah je nastaven na, `inner` protože dynamicky vytváří ID trezoru klíčů (viz `adminPassword.reference.keyVault` v vnějších šablonách `parameters` ) a předává je do vnořené šablony jako parametr.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "apiVersion": "2019-10-01",
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
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
> Pokud je obor nastaven na hodnotu `outer` , nemůžete použít `reference` funkci v části výstupy vnořené šablony pro prostředek, který jste nasadili ve vnořené šabloně. Chcete-li vrátit hodnoty nasazeného prostředku ve vnořené šabloně, buď použijte `inner` rozsah, nebo převeďte vnořenou šablonu na propojenou šablonu.

## <a name="linked-template"></a>Propojená šablona

Pokud chcete propojit šablonu, přidejte do hlavní šablony [prostředek nasazení](/azure/templates/microsoft.resources/deployments) . Do vlastnosti **templateLink** zadejte identifikátor URI šablony, která se má zahrnout. Následující příklad odkazuje na šablonu v účtu úložiště.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
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

Když odkazujete na propojenou šablonu, hodnota `uri` nemůže být místní soubor nebo soubor, který je k dispozici pouze v místní síti. Azure Resource Manager musí být schopné získat přístup k šabloně. Zadejte hodnotu identifikátoru URI, která se bude stahovat jako **http** nebo **https**. 

Můžete odkazovat na šablony pomocí parametrů, které zahrnují **protokol HTTP** nebo **https**. Například běžným vzorem je použití `_artifactsLocation` parametru. Můžete nastavit propojenou šablonu pomocí výrazu jako:

```json
"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]"
```

Pokud vytváříte odkaz na šablonu v GitHubu, použijte neupravenou adresu URL. Odkaz má formát: `https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-with-templates/quickstart-template/azuredeploy.json` . Pokud chcete získat nezpracovaný odkaz, vyberte **nezpracované**.

:::image type="content" source="./media/linked-templates/select-raw.png" alt-text="Vybrat hrubou adresu URL":::

### <a name="parameters-for-linked-template"></a>Parametry pro propojenou šablonu

Parametry pro propojenou šablonu můžete zadat buď v externím souboru, nebo do vloženého. Při poskytování externího souboru parametrů použijte vlastnost **parametersLink** :

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2019-10-01",
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
   "apiVersion": "2019-10-01",
   "name": "linkedTemplate",
   "properties": {
     "mode": "Incremental",
     "templateLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
      "contentVersion":"1.0.0.0"
     },
     "parameters": {
      "storageAccountName":{"value": "[parameters('storageAccountName')]"}
    }
   }
  }
]
```

Nemůžete použít vložené parametry ani odkaz na soubor parametrů. Nasazení se nepovede s chybou `parametersLink` , když `parameters` je zadán parametr a.

## <a name="template-specs"></a>Specifikace šablon

Místo udržování vašich propojených šablon v přístupném koncovém bodu můžete vytvořit [specifikaci šablony](template-specs.md) , která zabalí hlavní šablonu a její propojené šablony do jedné entity, kterou můžete nasadit. Specifikace šablony je prostředek ve vašem předplatném Azure. Usnadňuje bezpečné sdílení šablony s uživateli ve vaší organizaci. K udělení přístupu ke specifikaci šablony použijte řízení přístupu na základě role Azure (Azure RBAC). Tato funkce je aktuálně ve verzi Preview.

Další informace najdete tady:

- [Kurz: Vytvoření specifikace šablony s propojenými šablonami](./template-specs-create-linked.md).
- [Kurz: nasazení specifikace šablony jako propojené šablony](./template-specs-deploy-linked-template.md).

## <a name="dependencies"></a>Závislosti

Stejně jako u jiných typů prostředků můžete nastavit závislosti mezi propojenými šablonami. Pokud musí být prostředky v jedné propojené šabloně nasazené před prostředky ve druhé propojené šabloně, nastavte druhou šablonu závislou na prvním.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/linked-dependency.json" highlight="10,22,24":::

## <a name="contentversion"></a>Contentversion –

Nemusíte zadávat `contentVersion` vlastnost pro `templateLink` `parametersLink` vlastnost nebo. Pokud neposkytnete `contentVersion` , je nasazena aktuální verze šablony. Pokud zadáte hodnotu verze obsahu, musí odpovídat verzi v propojené šabloně. v opačném případě se nasazení nepovede a dojde k chybě.

## <a name="using-variables-to-link-templates"></a>Použití proměnných k propojení šablon

Předchozí příklady ukázaly pevně kódované hodnoty adresy URL pro odkazy šablony. Tento přístup může fungovat pro jednoduchou šablonu, ale nefunguje dobře pro velkou sadu modulárních šablon. Místo toho můžete vytvořit statickou proměnnou, která bude ukládat základní adresu URL pro hlavní šablonu a pak dynamicky vytvořit adresy URL pro propojené šablony z této základní adresy URL. Výhodou tohoto přístupu je, že šablonu můžete snadno přesunout nebo rozvětvit, protože v hlavní šabloně potřebujete změnit jenom statickou proměnnou. Hlavní šablona projde správné identifikátory URI v rámci rozložené šablony.

Následující příklad ukazuje, jak použít základní adresu URL pro vytvoření dvou adres URL pro propojené šablony (**sharedTemplateUrl** a **vmTemplate**).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Můžete také použít [Deployment ()](template-functions-deployment.md#deployment) pro získání základní adresy URL pro aktuální šablonu a použít ji k získání adresy URL pro jiné šablony ve stejném umístění. Tento přístup je užitečný v případě, že se změní umístění šablony nebo chcete v souboru šablony vyhnout adresám URL pevného kódování. Vlastnost templateLink je vrácena pouze při odkazování na vzdálenou šablonu s adresou URL. Pokud používáte místní šablonu, tato vlastnost není k dispozici.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Nakonec byste použili proměnnou ve `uri` vlastnosti `templateLink` Vlastnosti.

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>Pomocí kopírování

Chcete-li vytvořit více instancí prostředku s vnořenou šablonou, přidejte element Copy na úrovni prostředku **Microsoft. Resources/Deployments** . Nebo, pokud je oborem vnitřní, můžete přidat kopii v rámci vnořené šablony.

Následující příklad šablony ukazuje, jak použít kopírování s vnořenou šablonou.

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2019-10-01",
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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Chcete-li získat výstupní hodnotu z propojené šablony, načtěte hodnotu vlastnosti s syntaxí, jako je: `"[reference('deploymentName').outputs.propertyName.value]"` .

Při získávání výstupní vlastnosti z propojené šablony nesmí název vlastnosti obsahovat pomlčku.

Následující příklady ukazují, jak odkazovat na propojenou šablonu a načíst výstupní hodnotu. Odkazovaná šablona vrátí jednoduchou zprávu.  Nejprve odkazovaná Šablona:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworld.json":::

Hlavní šablona nasadí propojenou šablonu a získá vrácenou hodnotu. Všimněte si, že odkazuje na prostředek nasazení podle názvu a používá název vlastnosti vrácenou propojenou šablonou.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworldparent.json" highlight="10,23":::

Následující příklad ukazuje šablonu, která nasadí veřejnou IP adresu a vrátí ID prostředku prostředku Azure pro tuto veřejnou IP adresu:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip.json" highlight="27":::

Chcete-li při nasazování nástroje pro vyrovnávání zatížení použít veřejnou IP adresu z předchozí šablony, připojte se k šabloně a deklarujte závislost na `Microsoft.Resources/deployments` prostředku. Veřejná IP adresa v nástroji pro vyrovnávání zatížení je nastavená na výstupní hodnotu z propojené šablony.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json" highlight="28,41":::

## <a name="deployment-history"></a>Historie nasazení

Správce prostředků zpracuje každou šablonu jako samostatné nasazení v historii nasazení. V historii nasazení se zobrazí hlavní šablona se třemi propojenými nebo vnořenými šablonami jako:

![Historie nasazení](./media/linked-templates/deployment-history.png)

Tyto samostatné položky v historii můžete použít k načtení výstupních hodnot po nasazení. Následující šablona vytvoří veřejnou IP adresu a vytvoří výstup IP adresy:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
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
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Zabezpečení externí šablony

I když je propojená šablona musí být externě dostupná, nemusí být všeobecně dostupná veřejnosti. Šablonu můžete přidat do účtu privátního úložiště, který je přístupný jenom pro vlastníka účtu úložiště. Pak vytvoříte token sdíleného přístupového podpisu (SAS) pro povolení přístupu během nasazení. Tento token SAS přidáte k identifikátoru URI pro propojenou šablonu. I když je token předán jako zabezpečený řetězec, do operací nasazení se zaprotokoluje identifikátor URI propojené šablony, včetně tokenu SAS. Chcete-li omezit vystavení, nastavte vypršení platnosti tokenu.

Soubor parametrů může být také omezený pro přístup prostřednictvím tokenu SAS.

V současné době nemůžete propojit šablonu v účtu úložiště, který je za [Azure Storage bránou firewall](../../storage/common/storage-network-security.md).

> [!IMPORTANT]
> Namísto zabezpečení propojené šablony s tokenem SAS zvažte vytvoření [specifikace šablony](template-specs.md). Specifikace šablony bezpečně ukládá hlavní šablonu a její propojené šablony jako prostředek ve vašem předplatném Azure. Pomocí Azure RBAC udělíte přístup uživatelům, kteří potřebují šablonu nasadit.

Následující příklad ukazuje, jak předat token SAS při odkazování na šablonu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "containerSasToken": { "type": "securestring" }
  },
  "resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
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
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Příklady šablon

Následující příklady znázorňují běžné použití propojených šablon.

|Hlavní šablona  |Propojená šablona |Popis  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[odkazovaná šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Vrátí řetězec z propojené šablony. |
|[Load Balancer s veřejnou IP adresou](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[odkazovaná šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Vrátí veřejnou IP adresu z propojené šablony a nastaví tuto hodnotu v nástroji pro vyrovnávání zatížení. |
|[Několik IP adres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [odkazovaná šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Vytvoří v propojené šabloně několik veřejných IP adres.  |

## <a name="next-steps"></a>Další kroky

* Kurz najdete v tématu [kurz: Vytvoření propojených Azure Resource Manager šablon](./deployment-tutorial-linked-template.md).
* Další informace o definování pořadí nasazení pro vaše prostředky najdete v tématu [Definování závislostí v šablonách Azure Resource Manager](define-resource-dependency.md).
* Informace o tom, jak definovat jeden prostředek, ale vytvořit mnoho instancí, najdete [v tématu Vytvoření více instancí prostředků v Azure Resource Manager](copy-resources.md).
* Postup při nastavení šablony v účtu úložiště a vygenerování tokenu SAS najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](deploy-powershell.md) nebo [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](deploy-cli.md).
