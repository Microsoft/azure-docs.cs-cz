---
title: 'Rychlý Start: vytvoření účtu Azure dosah pomocí Azure PowerShell/Azure CLI (Preview)'
description: V tomto rychlém startu se dozvíte, jak vytvořit účet Azure dosah pomocí Azure PowerShell/Azure CLI.
author: hophanms
ms.author: hophan
ms.date: 11/23/2020
ms.topic: quickstart
ms.service: purview
ms.subservice: purview-data-catalog
ms.custom:
- mode-api
ms.openlocfilehash: 6266aedaec8f171a1a6ff3e0d15abdad0263767a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530874"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Rychlý Start: vytvoření účtu Azure dosah pomocí Azure PowerShell/Azure CLI

> [!IMPORTANT]
> Služba Azure dosah je aktuálně ve verzi PREVIEW. [Doplňkové podmínky použití pro](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview Microsoft Azure zahrnují další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.

V tomto rychlém startu vytvoříte účet Azure dosah pomocí Azure PowerShell/Azure CLI.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Uživatelský účet, který použijete pro přihlášení k Azure, musí být členem role přispěvatel nebo vlastník nebo správcem předplatného Azure.

* Vlastní [klient Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Nainstalujte do klientského počítače Azure PowerShell nebo rozhraní příkazového řádku Azure CLI a nasaďte šablonu: [nasazení z příkazového řádku](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment) .

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="configure-your-subscription"></a>Konfigurace předplatného

V případě potřeby pomocí těchto kroků nakonfigurujte předplatné, aby bylo možné Azure dosah spustit ve vašem předplatném:

   1. V Azure Portal vyhledejte a vyberte **předplatná**.

   1. V seznamu předplatných vyberte předplatné, které chcete použít. Vyžaduje se oprávnění k přístupu pro správu předplatného.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Snímek obrazovky ukazující, jak vybrat odběr v Azure Portal.":::

   1. U svého předplatného vyberte **poskytovatelé prostředků**. V podokně **poskytovatelé prostředků** vyhledejte a zaregistrujte všechny tři poskytovatele prostředků: 
       1. **Microsoft. dosah**
       1. **Microsoft.Storage**
       1. **Microsoft. EventHub** 
      
      Pokud nejsou registrovány, zaregistrujte je výběrem možnosti **Registrovat**.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Snímek obrazovky, který ukazuje, jak zaregistrovat poskytovatele prostředků Microsoft dosahe Azure v Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Vytvoření instance účtu Azure dosah

1. Přihlaste se pomocí přihlašovacích údajů Azure

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. Pokud máte více předplatných Azure, vyberte předplatné, které chcete použít:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Vytvořte skupinu prostředků pro účet dosah. Tento krok můžete přeskočit, pokud ho už máte:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. Vytvořte soubor šablony dosah, například `purviewtemplate.json` . Můžete aktualizovat `name` , `location` a `capacity` ( `4` nebo `16` ):

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. Nasadit šablonu dosah

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Jestliže chcete spustit tento příkaz nasazení, musíte mít k dispozici [nejnovější verzi](/cli/azure/install-azure-cli) Azure CLI.
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. Příkaz pro nasazení vrátí výsledky. Vyhledejte, pokud `ProvisioningState` chcete zjistit, zda nasazení proběhlo úspěšně.
    
## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit účet Azure dosah.

V dalším článku se dozvíte, jak uživatelům dovolit přístup k vašemu účtu Azure dosah. 

> [!div class="nextstepaction"]
> [Přidání uživatelů k účtu Azure dosah](catalog-permissions.md)
