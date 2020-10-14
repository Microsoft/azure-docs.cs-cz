---
title: 'Rychlý Start: vytvoření podrobného plánu pomocí Azure CLI'
description: V tomto rychlém startu použijete Azure modrotisky k vytváření, definování a nasazování artefaktů pomocí Azure CLI.
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: 790e23897a24ea06565572163b8226af88e0bb7c
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057956"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-azure-cli"></a>Rychlý Start: definování a přiřazení Azure Blueprint pomocí Azure CLI

Naučíte se vytvářet a přiřazovat podrobné plány a umožňují definici běžných vzorů pro vývoj opakovaně použitelných a rychlých nasazení konfigurací na základě Azure Resource Manager šablon (šablon ARM), zásad, zabezpečení a dalších. V tomto kurzu se naučíte používat podrobné plány Azure Blueprint k provádění nejčastějších úloh spojených s vytvářením, publikováním a přiřazením podrobného plánu v organizaci, jako je například:

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-blueprint-extension"></a>Přidat rozšíření podrobného plánu

Aby bylo možné povolit Azure CLI ke správě definic a přiřazení podrobného plánu, je nutné rozšíření přidat.
Toto rozšíření funguje bez ohledu na to rozhraní příkazového řádku Azure je možné, včetně [bash ve Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (samostatné a uvnitř portálu), [image Dockeru rozhraní příkazového řádku Azure](https://hub.docker.com/_/microsoft-azure-cli), nebo lokálně nainstalované.

1. Ověřte, že je nainstalované nejnovější rozhraní příkazového řádku Azure (aspoň **2.0.76**). Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/cli/azure/install-azure-cli-windows).

1. V prostředí Azure CLI podle vašeho výběru ho importujete pomocí následujícího příkazu:

   ```azurecli-interactive
   # Add the Blueprint extension to the Azure CLI environment
   az extension add --name blueprint
   ```

1. Ověřte, že rozšíření je nainstalované a má očekávanou verzi (minimálně **0.1.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for extension options
   az blueprint -h
   ```

## <a name="create-a-blueprint"></a>Vytvoření podrobného plánu

Jako první krok při definování standardního vzoru pro dodržování předpisů je sestavení podrobného plánu z dostupných prostředků. Vytvoříme podrobný plán MyBlueprint, který pro předplatné nakonfiguruje přiřazení rolí a zásad. Potom do skupiny prostředků přidáme skupinu prostředků, šablonu ARM a přiřazení role.

> [!NOTE]
> Při _použití rozhraní_ příkazového řádku Azure je nejprve vytvořen objekt podrobného plánu. Pro každý přidávaný _artefakt_, který má parametry, je potřeba předem definovat parametry v počátečním _podrobném plánu_.

1. Vytvořte počáteční objekt _blueprint_. Parametr **Parameters** přebírá soubor JSON, který obsahuje všechny parametry úrovně podrobného plánu. Tyto parametry se nastaví při přiřazení a používají je artefakty přidané v dalších krocích.

   - Soubor JSON – blueprintparms.jsna

     ```json
     {
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
                "displayName": "storage account type.",
                "description": null
            }
        },
        "tagName": {
            "type": "string",
            "metadata": {
                "displayName": "The name of the tag to provide the policy assignment.",
                "description": null
            }
        },
        "tagValue": {
            "type": "string",
            "metadata": {
                "displayName": "The value of the tag to provide the policy assignment.",
                "description": null
            }
        },
        "contributors": {
            "type": "array",
            "metadata": {
                "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                "strongType": "PrincipalId"
            }
        },
        "owners": {
            "type": "array",
            "metadata": {
                "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                "strongType": "PrincipalId"
            }
        }
     }
     ```

   - Příkaz Azure CLI

     ```azurecli-interactive
     # Login first with az login if not using Cloud Shell

     # Create the blueprint object
     az blueprint create \
        --name 'MyBlueprint' \
        --description 'This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.' \
        --parameters blueprintparms.json
     ```

     > [!NOTE]
     > Při importu definic podrobných plánů použijte název souboru _blueprint.js_ .
     > Tento název souboru se používá při volání metody [AZ detail import](/cli/azure/ext/blueprint/blueprint#ext-blueprint-az-blueprint-import).

     Ve výchozím nastavení je ve výchozím předplatném vytvořen objekt podrobného plánu. Chcete-li určit skupinu pro správu, použijte skupinu **pro správu**parametrů. K zadání předplatného použijte parametr **Subscription**.

1. Přidejte do definice skupinu prostředků pro artefakty úložiště.

   ```azurecli-interactive
   az blueprint resource-group add \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'storageRG' \
      --description 'Contains the resource template deployment and a role assignment.'
   ```

1. Přidejte do předplatného přiřazení role. V následujícím příkladu jsou identity instančního objektu s přiřazenou určenou rolí nakonfigurované na parametr nastavený při přiřazení podrobného plánu. V tomto příkladu se používá předdefinovaná role _Přispěvatel_ s identifikátorem GUID `b24988ac-6180-42a0-ab88-20f7382dd24c` .

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleContributor' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c' \
      --principal-ids "[parameters('contributors')]"
   ```

1. Přidejte do předplatného přiřazení zásad. V tomto příkladu se používá _značka Apply a její výchozí hodnota pro předdefinované zásady skupin prostředků_ s identifikátorem GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - Soubor JSON – artifacts\policyTags.jsna

     ```json
     {
        "tagName": {
           "value": "[parameters('tagName')]"
        },
        "tagValue": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Příkaz Azure CLI

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply tag and its default value to resource groups' \
        --description 'Apply tag and its default value to resource groups' \
        --parameters artifacts\policyTags.json
     ```

1. Přidejte do předplatného další přiřazenou zásadu pro značku úložiště (znovu použijte parametr _storageAccountType_). Tento další artefakt přiřazené zásady ukazuje, že parametr definovaný v podrobném plánu může používat více artefaktů. V tomto příklad se parametr **storageAccountType** používá k nastavení značky u skupiny prostředků. Tato hodnota poskytuje informace o účtu úložiště, který se vytvoří v dalším kroku. V tomto příkladu se používá _značka Apply a její výchozí hodnota pro předdefinované zásady skupin prostředků_ s identifikátorem GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - Soubor JSON – artifacts\policyStorageTags.jsna

     ```json
     {
        "tagName": {
           "value": "StorageType"
        },
        "tagValue": {
           "value": "[parameters('storageAccountType')]"
        }
     }
     ```

   - Příkaz Azure CLI

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyStorageTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply storage tag to resource group' \
        --description 'Apply storage tag and the parameter also used by the template to resource groups' \
        --parameters artifacts\policyStorageTags.json
     ```

1. Přidejte pod skupinu prostředků šablonu. Parametr **šablony** pro šablonu ARM obsahuje normální komponenty JSON šablony. Šablona také znovu používá parametry podrobného plánu **storageAccountType**, **tagName** a **tagValue**, které se šabloně předávají. Parametry podrobného plánu jsou k dispozici pro šablonu pomocí **parametrů** parametrů a uvnitř šablony JSON, kterou používá pár klíč-hodnota k vložení hodnoty. Název parametru podrobného plánu a šablony může být stejný.

   - Soubor šablony ARM formátu JSON – artifacts\templateStorage.jsna

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - Soubor parametrů šablony ARM formátu JSON – artifacts\templateStorageParams.js

     ```json
     {
        "storageAccountTypeFromBP": {
           "value": "[parameters('storageAccountType')]"
        },
        "tagNameFromBP": {
           "value": "[parameters('tagName')]"
        },
        "tagValueFromBP": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Příkaz Azure CLI

     ```azurecli-interactive
     az blueprint artifact template create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'templateStorage' \
        --template artifacts\templateStorage.json \
        --parameters artifacts\templateStorageParams.json \
        --resource-group-art 'storageRG'
     ```

1. Přidejte pod skupinu prostředků přiřazení role. Jde o podobné přiřazení jako při předchozím přiřazení role. V následujícím příkladu se identifikátor definice použije pro roli **Owner** (Vlastník), který jí z podrobného plánu nabídne jiný parametr. V tomto příkladu se používá předdefinovaná role _Owner_ s identifikátorem GUID `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` .

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleOwner' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635' \
      --principal-ids "[parameters('owners')]" \
      --resource-group-art 'storageRG'
   ```

## <a name="publish-a-blueprint"></a>Publikování podrobného plánu

Po přidání artefaktů do podrobného plánu ho můžeme publikovat. Publikováním se zpřístupní, aby bylo možné ho přiřadit k předplatnému.

```azurecli-interactive
az blueprint publish --blueprint-name 'MyBlueprint' --version '{BlueprintVersion}'
```

Hodnota `{BlueprintVersion}` představuje řetězec složený z písmen, číslic a spojovníků (bez mezer a speciálních znaků) o maximální délce 20 znaků. Použijte něco jedinečného a informativní, jako je například **v20200605-135541**.

## <a name="assign-a-blueprint"></a>Přiřazení podrobného plánu

Po publikování podrobného plánu pomocí Azure CLI ho můžete přiřadit k předplatnému. Přiřaďte vytvořený podrobný plán některému z předplatných v hierarchii skupiny pro správu. Pokud je podrobný plán uložen v předplatném, může být přiřazen pouze k tomuto předplatnému. Parametr **Details-Name** určuje plán, který se má přiřadit. Pokud chcete zadat parametry názvu, umístění, identity, zámku a podrobného plánu, použijte v příkazu párové parametry rozhraní příkazového řádku Azure CLI `az blueprint assignment create` nebo je poskytněte v souboru JSON **parametrů** .

1. Spusťte nasazení podrobného plánu tím, že ho přiřadíte k předplatnému. Jelikož parametry **přispěvatelé** a **vlastníci** vyžadují, aby bylo přiřazení role uděleno pole objectID objektů zabezpečení, použijte [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) pro shromáždění identifikátorů objectID pro použití v **parametrech** pro vlastní uživatele, skupiny nebo instanční objekty.

   - Soubor JSON – blueprintAssignment.jsna

     ```json
     {
        "storageAccountType": {
            "value": "Standard_GRS"
        },
        "tagName": {
            "value": "CostCenter"
        },
        "tagValue": {
            "value": "ContosoIT"
        },
        "contributors": {
            "value": [
                "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                "38833b56-194d-420b-90ce-cff578296714"
            ]
        },
        "owners": {
            "value": [
                "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                "316deb5f-7187-4512-9dd4-21e7798b0ef9"
            ]
        }
     }
     ```

   - Příkaz Azure CLI

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

   - Spravovaná identita přiřazená uživatelem

     Přiřazení podrobného plánu může také používat [uživatelsky přiřazenou spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md).
     V tomto případě je parametr **typu identity** nastavený na _UserAssigned_ a parametr **User-Assigned-identity** Určuje identitu. Nahraďte `{userIdentity}` názvem vaší uživatelsky přiřazené spravované identity.

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --identity-type UserAssigned \
        --user-assigned-identities {userIdentity} \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

     **Spravovaná identita přiřazená uživatelem** může být v jakémkoli předplatném a skupině prostředků, ke které má uživatel přihlášený podrobný plán oprávnění.

     > [!IMPORTANT]
     > Azure modrotisky nespravuje spravovanou identitu přiřazenou uživatelem. Uživatelé zodpovídají za přiřazení dostatečných rolí a oprávnění nebo přiřazení podrobného plánu se nezdaří.

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="unassign-a-blueprint"></a>Zrušení přiřazení plánu

Podrobný plán můžete odebrat z předplatného. Odebrání se často provádí v případě, že už nepotřebujete prostředky artefaktů. Po odebrání podrobného plánu zůstanou přiřazené artefakty, které byly jeho součástí. Chcete-li odebrat přiřazení podrobného plánu, použijte `az blueprint assignment delete` příkaz:

```azurecli-interactive
az blueprint assignment delete --name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili, přiřadili a odebrali podrobný plán pomocí Azure CLI. Další informace o plánech Azure najdete v článku o životním cyklu podrobného plánu.

> [!div class="nextstepaction"]
> [Další informace o životním cyklu podrobného plánu](./concepts/lifecycle.md)