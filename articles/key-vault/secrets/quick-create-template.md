---
title: Azure Quickstart – vytvoření trezoru klíčů Azure a tajného klíče pomocí šablony Azure Resource Manager | Dokumenty společnosti Microsoft
description: Úvodní příručka ukazuje, jak vytvořit trezory klíčů Azure a přidat tajné klíče do trezorů pomocí šablony Azure Resource Manager.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: 273a467f5db2201015352aaf4a232f5a42e29673
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618079"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Úvodní příručka: Nastavení a načtení tajného klíče z trezoru klíčů Azure pomocí šablony Správce prostředků

[Azure Key Vault](../general/overview.md) je cloudová služba, která poskytuje zabezpečené úložiště tajných kódů, jako jsou klíče, hesla, certifikáty a další tajné klíče. Tento rychlý start se zaměřuje na proces nasazení šablony Správce prostředků k vytvoření trezoru klíčů a tajného klíče.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Šablona potřebuje ke konfiguraci oprávnění vaše ID objektu uživatele Azure AD. Následující postup získá ID objektu (GUID).

    1. Spusťte následující příkaz Azure PowerShell nebo Azure CLI tak, že **vyberete Try it**a pak vložte skript do podokna prostředí. Chcete-li skript vložit, klepněte pravým tlačítkem myši na prostředí a vyberte **příkaz Vložit**.

        # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Poznamenejte si ID objektu. Potřebujete ji v další části tohoto rychlého startu.

## <a name="create-a-vault-and-a-secret"></a>Vytvoření trezoru a tajného klíče

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json" range="1-150" highlight="107-148":::

V šabloně jsou definovány dva prostředky Azure:

* [**Microsoft.KeyVault/trezory:**](/azure/templates/microsoft.keyvault/vaults)vytvořte trezor klíčů Azure.
* [**Microsoft.KeyVault/vaults/secrets**](/azure/templates/microsoft.keyvault/vaults/secrets): vytvořte tajný klíč trezoru klíčů.

Další ukázky šablon Azure Key Vault najdete [zde](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

### <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří trezor klíčů a tajný klíč.

    [![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty.

    ![Šablona Správce prostředků, integrace trezoru klíčů, nasazení portálu](../media/quick-create-template/create-key-vault-using-template-portal.png)

    Pokud není zadán, použijte výchozí hodnotu k vytvoření trezoru klíčů a tajného klíče.

    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků**: Vyberte **Vytvořit nový**, zadejte jedinečný název skupiny prostředků a klepněte na tlačítko **OK**.
    * **Umístění:** Vyberte prosím umístění.  Například **střední USA**.
    * **Název trezoru klíčů**: Zadejte název trezoru klíčů, který musí být v oboru názvů .vault.azure.net globálně jedinečný. Při ověřování nasazení potřebujete název v další části.
    * **Id klienta**: funkce šablony automaticky načte ID klienta.  Neměňte výchozí hodnotu.
    * **Id uživatele reklamy:** zadejte ID objektu uživatele Azure AD, které jste načetli z [požadavků](#prerequisites).
    * **Tajný název**: zadejte název tajného klíče, který uložíte do trezoru klíčů.  Například **adminpassword**.
    * **Tajná hodnota:** zadejte tajnou hodnotu.  Pokud ukládáte heslo, doporučujeme použít generované heslo, které jste vytvořili v části Požadavky.
    * **Souhlasím s podmínkami a ujednáními uvedenými nahoře:** Toto políčko zaškrtněte.
3. Vyberte **Koupit**. Po úspěšném nasazení trezoru klíčů se zobrazí oznámení:

    ![Šablona Správce prostředků, integrace trezoru klíčů, oznámení o nasazení portálu](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Portál Azure se používá k nasazení šablony. Kromě portálu Azure můžete taky použít Azure PowerShell, Azure CLI a REST API. Další metody nasazení najdete v [tématu Nasazení šablon](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Můžete buď použít portál Azure ke kontrole trezoru klíčů a tajného klíče, nebo použít následující Azure CLI nebo Skript Azure PowerShell uvést tajný klíč vytvořený.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

Výstup vypadá podobně jako:

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

![Šablona Správce prostředků, integrace trezoru klíčů, nasazení výstupu ověření portálu](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![Šablona Správce prostředků, integrace trezoru klíčů, nasazení výstupu ověření portálu](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---
## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy týkající se služby Key Vault vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Až nebudete prostředky potřebovat, odstraňte jejich skupinu. Tím odstraníte Key Vault i související prostředky. Odstranění skupiny prostředků pomocí Azure CLI nebo Azure PowerShellu:

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili trezor klíčů a tajný klíč pomocí šablony Azure Resource Manager a ověřili nasazení. Další informace o trezoru klíčů a Správci prostředků Azure najdete v následujících článcích.

- Přečtěte si [přehled trezoru klíčů Azure](../general/overview.md)
- Další informace o [Azure Resource Manageru](../../azure-resource-manager/management/overview.md)
- Kontrola [doporučených postupů azure key vaultu](../general/best-practices.md)
