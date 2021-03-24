---
title: Nasazení prostředků pomocí REST API a šablony
description: K nasazení prostředků do Azure použijte Azure Resource Manager a Správce prostředků REST API. Prostředky jsou definovány v šabloně Resource Manageru.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: e688d7abfaca442c3de395d25961b4e81e6c7b24
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889191"
---
# <a name="deploy-resources-with-arm-templates-and-azure-resource-manager-rest-api"></a>Nasazení prostředků pomocí šablon ARM a Azure Resource Manager REST API

Tento článek vysvětluje, jak používat Azure Resource Manager REST API s Azure Resource Manager šablonami (šablony ARM) k nasazení vašich prostředků do Azure.

Můžete buď zahrnout šablonu do textu žádosti nebo odkaz na soubor. Při použití souboru může být místním souborem nebo externím souborem, který je k dispozici prostřednictvím identifikátoru URI. Když je šablona v účtu úložiště, můžete omezit přístup k šabloně a poskytnout token sdíleného přístupového podpisu (SAS) během nasazování.

## <a name="deployment-scope"></a>Rozsah nasazení

Nasazení můžete cílit na skupinu prostředků, předplatné Azure, skupinu pro správu nebo tenanta. V závislosti na rozsahu nasazení použijete jiné příkazy.

- K nasazení do **skupiny prostředků** použijte [nasazení – vytvořit](/rest/api/resources/deployments/createorupdate). Požadavek se pošle na:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

- K nasazení do **předplatného** použijte [nasazení – vytvořit v oboru předplatného](/rest/api/resources/deployments/createorupdateatsubscriptionscope). Požadavek se pošle na:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

  Další informace o nasazeních na úrovni předplatného najdete v tématu [Vytvoření skupin prostředků a prostředků na úrovni předplatného](deploy-to-subscription.md).

- Pro nasazení do **skupiny pro správu** použijte [nasazení – vytvořte v oboru skupiny pro správu](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). Požadavek se pošle na:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

  Další informace o nasazení na úrovni skupiny pro správu najdete v tématu věnovaném [vytvoření prostředků na úrovni skupiny pro správu](deploy-to-management-group.md).

- K nasazení do **tenanta** použijte [nasazení – vytvořit nebo aktualizovat v oboru tenanta](/rest/api/resources/deployments/createorupdateattenantscope). Požadavek se pošle na:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

  Další informace o nasazeních na úrovni tenanta najdete v tématu [vytvoření prostředků na úrovni tenanta](deploy-to-tenant.md).

Příklady v tomto článku používají nasazení skupin prostředků.

## <a name="deploy-with-the-rest-api"></a>Nasazení pomocí rozhraní REST API

1. Nastavte [společné parametry a hlavičky](/rest/api/azure/), včetně ověřovacích tokenů.

1. Pokud provádíte nasazení do skupiny prostředků, která neexistuje, vytvořte skupinu prostředků. Zadejte ID předplatného, název nové skupiny prostředků a umístění, které budete potřebovat pro vaše řešení. Další informace najdete v tématu [Vytvoření skupiny prostředků](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2020-06-01
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

1. Před nasazením šablony můžete zobrazit náhled změn, které šablona provede pro vaše prostředí. Pomocí [operace citlivosti](template-deploy-what-if.md) ověřte, že šablona provádí očekávané změny. Co když zároveň ověří chyby v šabloně.

1. Pokud chcete nasadit šablonu, zadejte ID předplatného, název skupiny prostředků, název nasazení v identifikátoru URI požadavku.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2020-10-01
   ```

   V textu žádosti zadejte odkaz na šablonu a soubor parametrů. Další informace o souboru parametrů najdete v tématu [Vytvoření souboru parametrů Resource Manageru](parameter-files.md).

   Všimněte si, že `mode` je nastavená na **přírůstkové**. Pokud chcete spustit kompletní nasazení, nastavte `mode` na **dokončeno**. Buďte opatrní při použití kompletního režimu, protože můžete nechtěně odstranit prostředky, které nejsou ve vaší šabloně.

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

    Pokud chcete protokolovat obsah odpovědi, požádat o obsah nebo obojí, zahrňte `debugSetting` do žádosti.

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

    Účet úložiště můžete nastavit tak, aby používal token sdíleného přístupového podpisu (SAS). Další informace najdete v tématu [delegování přístupu pomocí sdíleného přístupového podpisu](/rest/api/storageservices/delegate-access-with-shared-access-signature).

    Pokud potřebujete zadat citlivou hodnotu pro parametr (například heslo), přidejte tuto hodnotu do trezoru klíčů. Načtěte Trezor klíčů během nasazení, jak je znázorněno v předchozím příkladu. Další informace najdete v tématu [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](key-vault-parameter.md).

1. Místo propojení se soubory pro šablonu a parametry je můžete zahrnout do textu žádosti. Následující příklad ukazuje tělo žádosti s vloženou šablonou a parametrem:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

1. Chcete-li získat stav nasazení šablony, použijte [nasazení – získat](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
   ```

## <a name="deployment-name"></a>Název nasazení

Vašemu nasazení můžete dát název, jako je například `ExampleDeployment` .

Pokaždé, když spustíte nasazení, do historie nasazení skupiny prostředků se přidá záznam s názvem nasazení. Pokud spustíte jiné nasazení a přiřadíte mu stejný název, bude předchozí položka nahrazena aktuálním nasazením. Pokud chcete zachovat jedinečné položky v historii nasazení, udělte každému nasazení jedinečný název.

Chcete-li vytvořit jedinečný název, můžete přiřadit náhodné číslo. Nebo přidejte hodnotu data.

Pokud spustíte souběžná nasazení do stejné skupiny prostředků se stejným názvem nasazení, bude dokončeno pouze poslední nasazení. Všechna nasazení se stejným názvem, která nebyla dokončena, budou nahrazena posledním nasazením. Pokud například spustíte nasazení s názvem `newStorage` , které nasadí účet úložiště s názvem `storage1` a zároveň spustíte jiné nasazení s názvem `newStorage` , které nasadí účet úložiště s názvem `storage2` , nasadíte jenom jeden účet úložiště. Výsledný účet úložiště je pojmenován `storage2` .

Pokud ale spustíte nasazení s názvem `newStorage` , které nasadí účet úložiště s názvem `storage1` , a hned po jeho dokončení spustíte jiné nasazení s názvem `newStorage` , které nasadí účet úložiště s názvem `storage2` , budete mít dva účty úložiště. Jedna má název `storage1` a druhá má název `storage2` . Ale v historii nasazení máte jenom jednu položku.

Pokud pro každé nasazení zadáte jedinečný název, můžete je spustit souběžně bez konfliktu. Pokud spustíte nasazení s názvem, `newStorage1` které nasadí účet úložiště s názvem `storage1` a zároveň spustíte jiné nasazení s názvem, `newStorage2` které nasadí účet úložiště s názvem `storage2` , budete mít dva účty úložiště a dvě položky v historii nasazení.

Aby nedocházelo ke konfliktům s souběžnými nasazeními a zajistili v historii nasazení jedinečné položky, udělte každé nasazení jedinečný název.

## <a name="next-steps"></a>Další kroky

- Chcete-li se vrátit k úspěšnému nasazení, když se zobrazí chyba, přečtěte si téma [vrácení chyby při úspěšném nasazení](rollback-on-error.md).
- Pokud chcete určit, jak se mají zpracovávat prostředky, které existují ve skupině prostředků, ale nejsou definované v šabloně, přečtěte si téma [režimy nasazení Azure Resource Manager](deployment-modes.md).
- Další informace o zpracování asynchronních operací REST najdete v tématu [sledování asynchronních operací Azure](../management/async-operations.md).
- Další informace o šablonách najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
