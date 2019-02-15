---
title: Nasazení prostředků pomocí rozhraní REST API a šablony | Dokumentace Microsoftu
description: Nasazení prostředků do Azure pomocí Azure Resource Manageru a rozhraní REST API Resource Manageru. Prostředky jsou definovány v šabloně Resource Manageru.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2019
ms.author: tomfitz
ms.openlocfilehash: f49b8ed592422927288e24b164a04645e2e37744
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301379"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Nasazení prostředků pomocí šablon Resource Manageru a jeho rozhraní REST API

Tento článek vysvětluje, jak nasadit prostředky do Azure pomocí rozhraní REST API Resource Manageru se šablonami Resource Manageru.  

> [!TIP]
> Chybě během nasazování, vám pomůžou s laděním:
> 
> * [Zobrazení operací nasazení](resource-manager-deployment-operations.md) Další informace o tom, jak informace, které pomůžou vyřešit chyby
> * [Řešení běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md) se dozvíte, jak řešit běžné chyby nasazení
> 
> 

Šablony můžete zahrnout buď v textu žádosti nebo odkaz na soubor. Při použití souboru, může to být z místního souboru nebo externí soubor, který je k dispozici prostřednictvím identifikátoru URI. Když je šablona v účtu úložiště, můžete omezit přístup k šabloně a během nasazení zadat token sdíleného přístupového podpisu (SAS).

## <a name="deploy-with-the-rest-api"></a>Nasazení pomocí rozhraní REST API
1. Nastavte [společných parametrů a záhlaví](/rest/api/azure/), včetně ověřování tokenů.

1. Pokud nemáte existující skupinu prostředků, vytvořte skupinu prostředků. Zadejte ID svého předplatného, název nové skupiny prostředků a umístění, které potřebujete pro vaše řešení. Další informace najdete v tématu [vytvořte skupinu prostředků](/rest/api/resources/resourcegroups/createorupdate).

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2018-05-01
  ```

  Pomocí textu žádosti podobně jako:
  ```json
  {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
  }
  ```

1. Ověření nasazení před spuštěním spuštěním [ověření šablony nasazení](/rest/api/resources/deployments/validate) operace. Při testování nasazení, zadejte parametry stejným způsobem jako při provádění nasazení (viz dál).

1. Vytvoření nasazení. Zadejte ID svého předplatného, název skupiny prostředků, název nasazení a odkaz na šablonu. Informace o souboru šablony najdete v tématu [soubor parametrů](#parameter-file). Další informace o rozhraní REST API k vytvoření skupiny prostředků najdete v tématu [vytvoření šablony nasazení](/rest/api/resources/deployments/createorupdate). Všimněte si, že **režimu** je nastavena na **přírůstkové**. Spustit úplné nasazení, nastavte **režimu** k **Complete**. Buďte opatrní při použití úplný režim jako nechtěně odstraníte prostředky, které nejsou v šabloně.

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
  ```

  Pomocí textu žádosti podobně jako:

   ```json
  {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
  ```

    Pokud chcete protokolovat obsah odpovědi nebo obsah požadavku, zahrňte **debugSetting** v požadavku.

  ```json
  {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
  }
  ```

    Použít token sdíleného přístupového podpisu (SAS) můžete nastavit při vytváření svého účtu úložiště. Další informace najdete v tématu [delegování přístupu pomocí sdíleného přístupového podpisu](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

1. Namísto odkazování na soubory pro šablonu a parametry, můžete zahrnout v textu požadavku.

  ```json
  {
      "properties": {
      "mode": "Incremental",
      "template": {
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
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2018-02-01",
            "location": "[parameters('location')]",
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
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
  }
  ```

5. Získání stavu nasazení šablony. Další informace najdete v tématu [získat informace o nasazování šablon](/rest/api/resources/deployments/get).

  ```HTTP
  GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
  ```

## <a name="redeploy-when-deployment-fails"></a>Opětovné nasazení při nasazení se nezdaří

Pokud se nasazení nezdaří, můžete automaticky znovu nasadit starší a úspěšné nasazení z historie nasazení. Pokud chcete nastavit opětovné nasazení, použijte `onErrorDeployment` vlastnost v textu požadavku.

Tato možnost dala použít, vaše nasazení musí mít jedinečné názvy, tak je možné identifikovat v historii. Pokud nemáte jedinečné názvy, aktuální selhání nasazení může přepsat předchozí úspěšné nasazení v historii. Tuto možnost můžete použít pouze u kořenové úrovně nasazení. Nasazení z vnořené šablony nejsou k dispozici pro nové nasazení.

K opětovnému nasazení poslední úspěšné nasazení, pokud se aktuální nasazení nezdaří, použijte:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Pokud chcete znovu nasadit konkrétní nasazení, pokud se aktuální nasazení nezdaří, použijte:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

Zadané nasazení musí mít bylo úspěšné.

## <a name="parameter-file"></a>Soubor s parametry

Pokud používáte soubor s parametry k předání hodnot parametru během nasazení, musíte vytvořit soubor JSON s formátem podobně jako v následujícím příkladu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

Velikost souboru parametrů nemůže být delší než 64 KB.

Pokud je potřeba zadat hodnotu citlivé parametru (například hesla), přidejte tuto hodnotu do trezoru klíčů. Načtení služby key vault během nasazení, jak je znázorněno v předchozím příkladu. Další informace najdete v tématu [předání zabezpečených hodnot během nasazení](resource-manager-keyvault-parameter.md). 

## <a name="next-steps"></a>Další postup
* Chcete-li určit způsob zpracování prostředky, které existují ve skupině prostředků, ale nejsou definovány v šabloně, přečtěte si téma [režimy nasazení Azure Resource Manageru](deployment-modes.md).
* Další informace o zpracování asynchronních operací REST, naleznete v tématu [sledování asynchronních operací Azure](resource-manager-async-operations.md).
* Příklad nasazení prostředků pomocí klientské knihovny .NET najdete v tématu [nasazení prostředků pomocí šablony a knihoven .NET](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Chcete-li definovat parametry v šabloně, přečtěte si téma [vytváření šablon](resource-group-authoring-templates.md#parameters).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).

