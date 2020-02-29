---
title: Rychlý Start Azure – vytvoření trezoru klíčů Azure a tajného klíče pomocí šablony Azure Resource Manager | Microsoft Docs
description: Rychlý Start ukazující vytvoření trezorů klíčů Azure a přidání tajných kódů do trezorů pomocí šablony Azure Resource Manager.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: 2e4e812ec13c089508fea890df2af1f8372bbe37
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190411"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí šablony Správce prostředků

[Azure Key Vault](./key-vault-overview.md) je cloudová služba, která poskytuje zabezpečené úložiště tajných kódů, jako jsou klíče, hesla, certifikáty a další tajné kódy. Tento rychlý Start se zaměřuje na proces nasazení šablony Správce prostředků pro vytvoření trezoru klíčů a tajného klíče.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto článku potřebujete:

* Šablona potřebuje ke konfiguraci oprávnění vaše ID objektu uživatele Azure AD. Následující procedura získá ID objektu (GUID).

    1. Spusťte následující Azure PowerShell nebo příkaz rozhraní příkazového řádku Azure CLI tak, že vyberete možnost **vyzkoušet**a potom tento skript vložíte do podokna prostředí. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**.

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

    2. Zapište ID objektu. Budete ho potřebovat v další části tohoto rychlého startu.

## <a name="create-a-vault-and-a-secret"></a>Vytvoření trezoru a tajného klíče

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json" range="1-150" highlight="107-148":::

V šabloně jsou definované dva prostředky Azure:

* Trezory [**a trezory Microsoft.** ](/azure/templates/microsoft.keyvault/vaults)webkey: vytvoření trezoru klíčů Azure.
* [**Trezor a tajné kódy Microsoft.** ](/azure/templates/microsoft.keyvault/vaults/secrets)webkey: Vytvořte tajný klíč trezoru klíčů.

Další ukázky šablon Azure Key Vault najdete [tady](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

### <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří Trezor klíčů a tajný klíč.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Vyberte nebo zadejte následující hodnoty.

    ![Správce prostředků šablona, integrace Key Vault, nasazení portálu](./media/quick-create-template/create-key-vault-using-template-portal.png)

    Pokud není zadaný, použijte k vytvoření trezoru klíčů a tajného klíče výchozí hodnotu.

    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků**: vyberte **vytvořit novou**, zadejte jedinečný název skupiny prostředků a pak klikněte na **OK**.
    * **Umístění:** Vyberte prosím umístění.  Například **střed USA**.
    * **Key Vault název**: zadejte název trezoru klíčů, který musí být globálně jedinečný v rámci oboru názvů. Vault.Azure.NET. Při ověřování nasazení budete potřebovat název v další části.
    * **ID tenanta**: funkce šablony automaticky NAČTE vaše ID tenanta.  Neměňte výchozí hodnotu.
    * **ID uživatele AD**: Zadejte ID vašeho uživatelského objektu Azure AD, které jste získali z [požadovaných součástí](#prerequisites).
    * **Název tajného**kódu: zadejte název tajného klíče, který ukládáte do trezoru klíčů.  Například **AdminPassword**.
    * **Hodnota tajného klíče**: zadejte tajnou hodnotu.  Pokud ukládáte heslo, doporučuje se použít vygenerované heslo, které jste vytvořili v části požadavky.
    * **Souhlasím s podmínkami a ujednáními uvedenými nahoře:** Toto políčko zaškrtněte.
3. Vyberte **Koupit**. Po úspěšném nasazení trezoru klíčů obdržíte oznámení:

    ![Správce prostředků šablona, integrace Key Vault, nasazení oznámení portálu](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Azure Portal slouží k nasazení šablony. Kromě Azure Portal můžete použít také Azure PowerShell, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Můžete buď použít Azure Portal ke kontrole trezoru klíčů a tajného klíče, nebo k vytvoření seznamu vytvořeného tajného klíče použít následující skript Azure CLI nebo Azure PowerShell.

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

Výstup bude vypadat nějak takto:

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

![Správce prostředků šablona, integrace Key Vault, nasazení výstupu ověření portálu](./media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![Správce prostředků šablona, integrace Key Vault, nasazení výstupu ověření portálu](./media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---
## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy týkající se služby Key Vault vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Až nebudete prostředky potřebovat, odstraňte jejich skupinu. Tím odstraníte Key Vault i související prostředky. Odstranění skupiny prostředků pomocí rozhraní příkazového řádku Azure nebo Azure PowerShell:

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

V tomto rychlém startu jste vytvořili Trezor klíčů a tajný kód pomocí šablony Azure Resource Manager a ověření nasazení bylo ověřeno. Další informace o Key Vault a Azure Resource Manager najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](key-vault-overview.md)
- Další informace o [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Získat další informace o [klíčích, tajných klíčích a certifikátech](about-keys-secrets-and-certificates.md)
- Kontrola [Azure Key Vault osvědčených postupů](key-vault-best-practices.md)
