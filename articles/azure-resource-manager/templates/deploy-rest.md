---
title: Nasazení prostředků pomocí rozhraní REST API a šablony
description: K nasazení prostředků do Azure použijte Azure Resource Manager a rozhraní REST API Správce prostředků. Prostředky jsou definovány v šabloně Resource Manageru.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 9cdb7b668e5170917b41ef49639bd9a17e538766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153229"
---
# <a name="deploy-resources-with-arm-templates-and-resource-manager-rest-api"></a>Nasazení prostředků pomocí šablon ARM a rozhraní REST API správce prostředků

Tento článek vysvětluje, jak používat rozhraní REST API správce prostředků s azure resource manager (ARM) šablony k nasazení prostředků do Azure.

Šablonu můžete zahrnout do těla požadavku nebo vytvořit odkaz na soubor. Při použití souboru může být místní soubor nebo externí soubor, který je k dispozici prostřednictvím identifikátoru URI. Když je vaše šablona v účtu úložiště, můžete omezit přístup k šabloně a poskytnout token sdíleného přístupového podpisu (SAS) během nasazení.

## <a name="deployment-scope"></a>Rozsah nasazení

Nasazení můžete cílit na skupinu pro správu, předplatné Azure nebo skupinu prostředků. Ve většině případů budete cílit nasazení do skupiny prostředků. Pomocí nasazení skupiny pro správu nebo předplatného můžete použít zásady a přiřazení rolí v zadaném oboru. Nasazení předplatného můžete také použít k vytvoření skupiny prostředků a nasazení prostředků do ní. V závislosti na rozsahu nasazení můžete použít různé příkazy.

Chcete-li nasadit do **skupiny prostředků**, použijte [nasazení - vytvořit](/rest/api/resources/deployments/createorupdate). Žádost je odeslána na:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Chcete-li nasadit do **předplatného**, použijte [nasazení - vytvořit na odběr oboru](/rest/api/resources/deployments/createorupdateatsubscriptionscope). Žádost je odeslána na:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Další informace o nasazení na úrovni předplatného naleznete v [tématu Vytvoření skupin prostředků a prostředků na úrovni předplatného](deploy-to-subscription.md).

Chcete-li nasadit do **skupiny pro správu**, použijte [nasazení - vytvořit v oboru skupiny pro správu](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). Žádost je odeslána na:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Další informace o nasazení na úrovni skupiny pro správu naleznete [v tématu Vytvoření prostředků na úrovni skupiny pro správu](deploy-to-management-group.md).

Příklady v tomto článku používají nasazení skupiny prostředků.

## <a name="deploy-with-the-rest-api"></a>Nasazení pomocí rozhraní REST API

1. Nastavte [běžné parametry a záhlaví](/rest/api/azure/), včetně ověřovacích tokenů.

1. Pokud nemáte existující skupinu prostředků, vytvořte skupinu prostředků. Zadejte ID předplatného, název nové skupiny prostředků a umístění, které potřebujete pro vaše řešení. Další informace naleznete [v tématu Vytvoření skupiny prostředků](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   S tělo žádosti, jako je:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Před spuštěním ověřte své nasazení spuštěním operace [ověření nasazení šablony.](/rest/api/resources/deployments/validate) Při testování nasazení zadejte parametry přesně tak, jak byste při provádění nasazení (znázorněno v dalším kroku).

1. Chcete-li nasadit šablonu, zadejte ID předplatného, název skupiny prostředků, název nasazení v uri požadavku.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   V textu požadavku zadejte odkaz na šablonu a soubor parametrů. Další informace o souboru parametrů naleznete v tématu [Vytvoření souboru parametrů Správce prostředků](parameter-files.md).

   Všimněte si, že **režim** je nastaven na **přírůstkové**. Chcete-li spustit úplné nasazení, nastavte **režim** na **Dokončit**. Při použití režimu dokončení buďte opatrní, protože můžete neúmyslně odstranit prostředky, které v šabloně nejsou.

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

    Pokud chcete protokolovat obsah odpovědi, obsah požadavku nebo obojí, zahrňte do požadavku **ladění.**

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

    Účet úložiště můžete nastavit tak, aby používal token sdíleného přístupového podpisu (SAS). Další informace naleznete v [tématu Delegování přístupu pomocí sdíleného přístupového podpisu](/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

    Pokud potřebujete zadat citlivou hodnotu parametru (například hesla), přidejte tuto hodnotu do trezoru klíčů. Načíst trezor klíčů během nasazení, jak je znázorněno v předchozím příkladu. Další informace naleznete v [tématu Předání zabezpečených hodnot během nasazení](key-vault-parameter.md).

1. Místo propojení se soubory pro šablonu a parametry je můžete zahrnout do těla požadavku. Následující příklad ukazuje tělo požadavku s vložkou šablony a parametru:

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
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
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

1. Chcete-li získat stav nasazení šablony, použijte [nasazení - získat](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="next-steps"></a>Další kroky

- Pokud chcete vrátit se k úspěšnému nasazení, když se zobrazí chyba, přečtěte si informace [o chybě vrácení zpět k úspěšnému nasazení](rollback-on-error.md).
- Pokud chcete určit, jak zpracovat prostředky, které existují ve skupině prostředků, ale nejsou definovány v šabloně, najdete v [tématu režimy nasazení Správce prostředků Azure](deployment-modes.md).
- Další informace o zpracování asynchronních operací REST najdete v tématu [Sledování asynchronních operací Azure](../management/async-operations.md).
- Další informace o šablonách najdete [v tématu Principy struktury a syntaxe šablon ARM](template-syntax.md).

