---
title: Testovací případy pro sadu nástrojů test Toolkit
description: Popisuje testy, které jsou spuštěny pomocí sady nástrojů pro test šablon ARM.
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 451323058ad743d6e26fc8bcea27d1b44c76f543
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97674038"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>Výchozí testovací případy pro sadu nástrojů pro test šablon ARM

Tento článek popisuje výchozí testy, které jsou spouštěny pomocí [šablony test Toolkit](test-toolkit.md). Poskytuje příklady, které jsou testem úspěšné nebo neúspěšné. Obsahuje název každého testu.

## <a name="use-correct-schema"></a>Použít správné schéma

Název testu: **schéma DeploymentTemplate je správné** .

V šabloně je nutné zadat platnou hodnotu schématu.

Následující příklad **projde** tímto testem.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

Vlastnost Schema v šabloně musí být nastavená na jedno z následujících schémat:

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>Musí existovat parametry.

Název testu: **vlastnost Parameters musí existovat** .

Vaše šablona by měla mít element Parameters. Parametry jsou nezbytné pro to, aby se šablony znovu nališily v různých prostředích. Přidejte parametry do šablony pro hodnoty, které se mění při nasazování do různých prostředí.

Následující příklad **projde** tímto testem:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>Musí se použít deklarované parametry.

Název testu: **musí se odkazovat na parametry** .

Pokud chcete v šabloně omezit záměnu, odstraňte všechny parametry, které jsou definované, ale nepoužívají se. Tento test vyhledá všechny parametry, které se nepoužívají kdekoli v šabloně. Vyloučení nepoužívaných parametrů také usnadňuje nasazení šablony, protože nemusíte zadávat zbytečné hodnoty.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>Zabezpečené parametry nemůžou mít výchozí pevně zakódované.

Název testu: **parametry zabezpečeného řetězce nemůžou mít výchozí hodnotu** .

Nezadávejte pevně zakódované výchozí hodnoty pro zabezpečený parametr ve vaší šabloně. Prázdný řetězec je pro výchozí hodnotu přesnější.

Použijete typy **SecureString** nebo **SecureObject** u parametrů, které obsahují citlivé hodnoty, jako jsou hesla. Pokud parametr používá zabezpečený typ, hodnota parametru není zaznamenána do protokolu ani ukládána v historii nasazení. Tato akce zabrání uživateli se zlými úmysly zjistit citlivou hodnotu.

Pokud však zadáte výchozí hodnotu, je tato hodnota zjistitelná kýmkoli, kdo má přístup k šabloně nebo k historii nasazení.

Následující příklad **neprojde** tímto testem:

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

Následující příklad **projde** tímto testem:

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>Adresy URL prostředí nemůžou být pevně zakódované.

Název testu: **DeploymentTemplate nesmí obsahovat identifikátor URI pevně zakódované**

V šabloně nekódujte pevně adresy URL prostředí. Místo toho použijte [funkci prostředí](template-functions-deployment.md#environment) k dynamickému získání těchto adres URL během nasazování. Seznam hostitelských adres URL, které jsou blokovány, naleznete v [testovacím případu](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1).

Následující příklad tento test **neprojde** , protože adresa URL je pevně zakódované.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

Test se také **nezdařil** při použití s [identifikátorem](template-functions-string.md#uri) [Concat](template-functions-string.md#concat) nebo URI.

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

Následující příklad **projde** tímto testem.

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>Umístění používá parametr

Název testu: **umístění by nemělo být pevně zakódované**

Vaše šablony by měly mít parametr pojmenovaný Location. Tento parametr slouží k nastavení umístění prostředků ve vaší šabloně. V hlavní šabloně (s názvem azuredeploy.json nebo mainTemplate.json) se tento parametr může ve výchozím nastavení namístit pro skupinu prostředků. V propojených nebo vnořených šablonách by parametr umístění neměl mít výchozí umístění.

Uživatelé šablony mohou mít k dispozici omezené oblasti. Když zadáte pevný kód pro umístění prostředku, můžou být uživatelé zablokovaný v vytváření prostředků v této oblasti. Uživatele můžete zablokovat i v případě, že nastavíte umístění prostředku na `"[resourceGroup().location]"` . Skupina prostředků mohla být vytvořena v oblasti, ke které nemají přístup jiní uživatelé. Uživatelům se zablokuje použití šablony.

Když zadáte parametr umístění, který je ve výchozím nastavení pro umístění skupiny prostředků, můžou uživatelé použít výchozí hodnotu, pokud je to vhodné, ale taky zadat jiné umístění.

Následující příklad tento test **neprojde** , protože umístění v prostředku je nastaveno na `resourceGroup().location` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

V dalším příkladu se používá parametr Location, ale tento test se **nezdařil** , protože parametr Location má výchozí hodnotu pevně zakódované umístění.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Místo toho vytvořte parametr, který je ve výchozím nastavení pro umístění skupiny prostředků, ale umožní uživatelům zadat jinou hodnotu. Následující příklad **projde** tento test, pokud je šablona použita jako hlavní šablona.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Pokud je však předchozí příklad použit jako propojená šablona, test se **nezdařil**. Pokud se používá jako propojená šablona, odeberte výchozí hodnotu.

## <a name="resources-should-have-location"></a>Prostředky by měly mít umístění

Název testu: **prostředky by měly mít umístění**

Umístění prostředku musí být nastavené na [výraz šablony](template-expressions.md) nebo `global` . Výraz šablony obvykle používá parametr umístění popsaný v předchozím testu.

Následující příklad tento test **neprojde** , protože umístění není výraz nebo `global` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Následující příklad **projde** tímto testem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

Další příklad také **projde** tímto testem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>Velikost virtuálního počítače používá parametr

Název testu: **Velikost virtuálního počítače by měla být parametr**

Nekódujte pevně velikost virtuálního počítače. Zadejte parametr, aby mohli uživatelé šablony upravovat velikost nasazeného virtuálního počítače.

Následující příklad tento test **neprojde** .

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

Místo toho zadejte parametr.

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

Pak nastavte velikost virtuálního počítače na tento parametr.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>Minimální a maximální hodnoty jsou čísla.

Název testu: **minimální a maximální hodnota je číslo** .

Pokud pro parametr definujete minimální a maximální hodnoty, zadejte je jako čísla.

Následující příklad **neprojde** tímto testem:

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Místo toho zadejte hodnoty jako čísla. Následující příklad **projde** tímto testem:

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

Toto upozornění se zobrazí také v případě, že zadáte minimální nebo maximální hodnotu, ale ne druhou.

## <a name="artifacts-parameter-defined-correctly"></a>Správně definovaný parametr artefaktů

Název testu: **parametr artefakty**

Při zahrnutí parametrů pro `_artifactsLocation` a `_artifactsLocationSasToken` použijte správné výchozí hodnoty a typy. Aby bylo možné tento test předat, musí být splněny následující podmínky:

* Pokud zadáte jeden parametr, je nutné zadat druhý.
* `_artifactsLocation` musí být **řetězec**
* `_artifactsLocation` musí mít výchozí hodnotu v hlavní šabloně.
* `_artifactsLocation` ve vnořené šabloně nemůže být výchozí hodnota. 
* `_artifactsLocation` musí mít buď `"[deployment().properties.templateLink.uri]"` nebo nezpracovaná adresa URL úložiště pro výchozí hodnotu.
* `_artifactsLocationSasToken` musí se jednat o **secureString**
* `_artifactsLocationSasToken` pro výchozí hodnotu může být jenom prázdný řetězec.
* `_artifactsLocationSasToken` ve vnořené šabloně nemůže být výchozí hodnota. 

## <a name="declared-variables-must-be-used"></a>Musí se použít deklarované proměnné.

Název testu: **musí se odkazovat na proměnné** .

Chcete-li v šabloně omezit záměnu, odstraňte všechny proměnné, které jsou definovány, ale nejsou použity. Tento test vyhledá všechny proměnné, které se nepoužívají kdekoli v šabloně.

## <a name="dynamic-variable-should-not-use-concat"></a>Dynamická proměnná by neměla používat Concat

Název testu: **odkazy dynamických proměnných by neměly používat Concat**

Někdy je potřeba dynamicky vytvořit proměnnou na základě hodnoty jiné proměnné nebo parametru. Při nastavování hodnoty nepoužívejte funkci [Concat](template-functions-string.md#concat) . Místo toho použijte objekt, který obsahuje dostupné možnosti a dynamicky získá jednu z vlastností objektu během nasazování.

Následující příklad **projde** tímto testem. Proměnná **currentImage** se dynamicky nastavuje během nasazování.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>Použít nejnovější verzi rozhraní API

Název testu: **ApiVersions by měl být poslední**

Verze rozhraní API pro každý prostředek by měla používat nejnovější verzi. Test vyhodnocuje verzi, kterou používáte, pro verze, které jsou k dispozici pro daný typ prostředku.

## <a name="use-hardcoded-api-version"></a>Použít verzi rozhraní API pevně zakódované

Název testu: **Zprostředkovatelé ApiVersions nejsou povolené** .

Verze rozhraní API pro typ prostředku určuje, které vlastnosti jsou k dispozici. Poskytněte pevně kódovanou verzi rozhraní API ve vaší šabloně. Neobnovujte verzi rozhraní API určenou během nasazování. Nevíte, které vlastnosti jsou k dispozici.

Následující příklad tento test **neprojde** .

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

Následující příklad **projde** tímto testem.

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>Vlastnosti nemůžou být prázdné.

Název testu: **šablona nesmí obsahovat prázdné znaky** .

Nenekódujte pevně vlastnosti na prázdnou hodnotu. Prázdné hodnoty zahrnují hodnoty null a prázdné řetězce, objekty nebo pole. Pokud jste vlastnost nastavili na prázdnou hodnotu, odeberte tuto vlastnost ze šablony. Je však v pořádku nastavit vlastnost na prázdnou hodnotu během nasazení, jako například prostřednictvím parametru.

## <a name="use-resource-id-functions"></a>Použití funkcí ID prostředků

Název testu: **identifikátory by měly být odvozeny od identifikátorů ResourceID**

Při zadávání ID prostředku použijte jednu z funkcí ID prostředku. Povolené funkce jsou:

* [Prostředku](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

Nepoužívejte funkci Concat k vytvoření ID prostředku. Následující příklad tento test **neprojde** .

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

Následující příklad **projde** tento test.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>Funkce ResourceId má správné parametry.

Název testu: **identifikátory ResourceID nesmí obsahovat**

Při generování ID prostředků nepoužívejte nepotřebné funkce pro volitelné parametry. Ve výchozím nastavení funkce [ResourceID](template-functions-resource.md#resourceid) používá aktuální předplatné a skupinu prostředků. Tyto hodnoty nemusíte zadávat.  

Následující příklad tento test **neprojde** , protože nemusíte ZADÁVAT aktuální ID předplatného a název skupiny prostředků.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Následující příklad **projde** tento test.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Tento test platí pro:

* [Prostředku](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [odkaz](template-functions-resource.md#reference)
* [seznamu](template-functions-resource.md#list)

`reference`V případě a se `list*` Test **nezdařil** , pokud použijete `concat` k vytvoření ID prostředku.

## <a name="dependson-best-practices"></a>osvědčené postupy pro dependsOn

Název testu: **osvědčené postupy DependsOn**

Při nastavování závislostí nasazení nepoužívejte funkci [if](template-functions-logical.md#if) k otestování podmínky. Pokud jeden prostředek závisí na prostředku, který je [podmíněně nasazený](conditional-resource-deployment.md), nastavte závislost jako u libovolného prostředku. Pokud není podmíněný prostředek nasazený, Azure Resource Manager ho automaticky odebere z požadovaných závislostí.

Následující příklad tento test **neprojde** .

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

Následující příklad **projde** tento test.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>Vnořená nebo propojená nasazení nemůžou použít ladění.

Název testu: **prostředky nasazení se nesmí ladit** .

Pokud definujete [vnořenou nebo propojenou šablonu](linked-templates.md) s typem prostředku **Microsoft. Resources/Deployments** , můžete povolit ladění pro tuto šablonu. Ladění je přesné, pokud potřebujete tuto šablonu otestovat, ale je nutné ji zapnout, až budete připraveni použít šablonu v produkčním prostředí.

## <a name="admin-user-names-cant-be-literal-value"></a>Uživatelská jména správce nemůžou být literální hodnota.

Název testu: **AdminUsername by neměl být literál**

Při nastavování uživatelského jména správce nepoužívejte hodnotu literálu.

Následující příklad **neprojde** tímto testem:

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Místo toho použijte parametr. Následující příklad **projde** tímto testem:

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Použít nejnovější bitovou kopii virtuálního počítače

Název testu: **image virtuálních počítačů by měly používat nejnovější verzi**

Pokud vaše šablona obsahuje virtuální počítač s imagí, ujistěte se, že používá nejnovější verzi image.

## <a name="use-stable-vm-images"></a>Použití stabilních imagí virtuálních počítačů

Název testu: **Virtual Machines by neměl být ve verzi Preview**

Virtuální počítače by neměly používat náhled imagí.

Následující příklad tento test **neprojde** .

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

Následující příklad **projde** tímto testem.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Nepoužívat rozšíření ManagedIdentity

Název testu: **ManagedIdentityExtension se nesmí použít** .

Nepoužívejte rozšíření ManagedIdentity pro virtuální počítač. Další informace najdete v tématu [jak ukončit používání rozšíření spravované identity virtuálních počítačů a začít používat Azure instance metadata Service](../../active-directory/managed-identities-azure-resources/howto-migrate-vm-extension.md).

## <a name="outputs-cant-include-secrets"></a>Výstupy nemůžou zahrnovat tajné klíče.

Název testu: **výstupy nesmí obsahovat tajné** kódy.

V části výstupy nezahrnujte žádné hodnoty, které potenciálně zveřejňují tajné kódy. Výstup z šablony je uložený v historii nasazení, takže uživatel se zlými úmysly může tyto informace najít.

Následující příklad **neprojde** test, protože zahrnuje zabezpečený parametr ve výstupní hodnotě.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

Následující příklad se **nezdařil** , protože používá funkci [list *](template-functions-resource.md#list) ve výstupech.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="next-steps"></a>Další kroky

- Další informace o spuštění sady nástrojů test Toolkit najdete v tématu [použití sady nástrojů pro test šablon ARM](test-toolkit.md).
- Microsoft Learn modul, který se zabývá používáním sady nástrojů test Toolkit, najdete v tématu [Náhled změn a ověření prostředků Azure pomocí nástrojů co-if a šablony ARM test Toolkit](/learn/modules/arm-template-test/).