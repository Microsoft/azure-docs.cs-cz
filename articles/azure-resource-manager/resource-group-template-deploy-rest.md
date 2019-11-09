---
title: Nasazení prostředků pomocí REST API a šablony | Microsoft Docs
description: K nasazení prostředků do Azure použijte Azure Resource Manager a Správce prostředků REST API. Prostředky jsou definovány v šabloně Resource Manageru.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: tomfitz
ms.openlocfilehash: 0472510801f5827327ac06a3927ca597b6caa612
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73834360"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Nasazení prostředků pomocí šablon Resource Manageru a jeho rozhraní REST API

Tento článek vysvětluje, jak používat Správce prostředků REST API se šablonami Správce prostředků k nasazení prostředků do Azure.  

Můžete buď zahrnout šablonu do textu žádosti nebo odkaz na soubor. Při použití souboru může být místním souborem nebo externím souborem, který je k dispozici prostřednictvím identifikátoru URI. Když je šablona v účtu úložiště, můžete omezit přístup k šabloně a poskytnout token sdíleného přístupového podpisu (SAS) během nasazování.

## <a name="deployment-scope"></a>Rozsah nasazení

Nasazení můžete cílit do skupiny pro správu, předplatného Azure nebo skupiny prostředků. Ve většině případů budete cílit na nasazení do skupiny prostředků. Pomocí skupiny pro správu nebo nasazení předplatných můžete použít zásady a přiřazení rolí v rámci zadaného rozsahu. K vytvoření skupiny prostředků a nasazení prostředků do ní taky použijete nasazení předplatného. V závislosti na rozsahu nasazení použijete jiné příkazy.

K nasazení do **skupiny prostředků**použijte [nasazení – vytvořit](/rest/api/resources/deployments/createorupdate). Požadavek se pošle na:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

K nasazení do **předplatného**použijte [nasazení – vytvořit v oboru předplatného](/rest/api/resources/deployments/createorupdateatsubscriptionscope). Požadavek se pošle na:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Další informace o nasazeních na úrovni předplatného najdete v tématu [Vytvoření skupin prostředků a prostředků na úrovni předplatného](deploy-to-subscription.md).

Pro nasazení do **skupiny pro správu**použijte [nasazení – vytvořte v oboru skupiny pro správu](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). Požadavek se pošle na:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Další informace o nasazení na úrovni skupiny pro správu najdete v tématu věnovaném [vytvoření prostředků na úrovni skupiny pro správu](deploy-to-management-group.md).

Příklady v tomto článku používají nasazení skupin prostředků.

## <a name="deploy-with-the-rest-api"></a>Nasazení pomocí REST API

1. Nastavte [společné parametry a hlavičky](/rest/api/azure/), včetně ověřovacích tokenů.

1. Pokud nemáte existující skupinu prostředků, vytvořte skupinu prostředků. Zadejte ID předplatného, název nové skupiny prostředků a umístění, které budete potřebovat pro vaše řešení. Další informace najdete v tématu [Vytvoření skupiny prostředků](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   S textem žádosti, jako je:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Před spuštěním ověřování ověřte jeho nasazení spuštěním operace [Ověření nasazení šablony](/rest/api/resources/deployments/validate) . Při testování nasazení zadejte parametry přesně stejně jako při spuštění nasazení (viz v dalším kroku).

1. Pokud chcete nasadit šablonu, zadejte ID předplatného, název skupiny prostředků, název nasazení v identifikátoru URI požadavku. 

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   V textu žádosti zadejte odkaz na šablonu a soubor parametrů. Další informace o souboru parametrů naleznete v tématu [Create správce prostředků Parameter File](resource-manager-parameter-files.md).

   Všimněte si, že **režim** je nastaven na **přírůstkové**. Chcete-li spustit kompletní nasazení, nastavte **režim** na **dokončeno**. Buďte opatrní při použití kompletního režimu, protože můžete nechtěně odstranit prostředky, které nejsou ve vaší šabloně.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Pokud chcete protokolovat obsah odpovědi, požádat o obsah nebo obojí, přidejte do žádosti **debugSetting** .

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    Účet úložiště můžete nastavit tak, aby používal token sdíleného přístupového podpisu (SAS). Další informace najdete v tématu [delegování přístupu pomocí sdíleného přístupového podpisu](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

    Pokud potřebujete zadat citlivou hodnotu pro parametr (například heslo), přidejte tuto hodnotu do trezoru klíčů. Načtěte Trezor klíčů během nasazení, jak je znázorněno v předchozím příkladu. Další informace najdete v tématu [Předání zabezpečených hodnot během nasazování](resource-manager-keyvault-parameter.md). 

1. Místo propojení se soubory pro šablonu a parametry je můžete zahrnout do textu žádosti. Následující příklad ukazuje tělo žádosti s vloženou šablonou a parametrem:

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

1. Chcete-li získat stav nasazení šablony, použijte [nasazení – získat](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="next-steps"></a>Další kroky

- Chcete-li se vrátit k úspěšnému nasazení, když se zobrazí chyba, přečtěte si téma [vrácení chyby při úspěšném nasazení](rollback-on-error.md).
- Pokud chcete určit, jak se mají zpracovávat prostředky, které existují ve skupině prostředků, ale nejsou definované v šabloně, přečtěte si téma [režimy nasazení Azure Resource Manager](deployment-modes.md).
- Další informace o zpracování asynchronních operací REST najdete v tématu [sledování asynchronních operací Azure](resource-manager-async-operations.md).
- Další informace o šablonách naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).

