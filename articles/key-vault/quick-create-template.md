---
title: Rychlý Start Azure – vytvoření trezoru klíčů Azure a tajného klíče pomocí šablony Azure Resource Manager | Microsoft Docs
description: Rychlý Start ukazující vytvoření trezorů klíčů Azure a přidání tajných kódů do trezorů pomocí šablony Azure Resource Manager.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/17/2019
ms.author: jgao
ms.openlocfilehash: 5a1e0329f8d0227c05924137d5eaddd9658d5b3c
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076931"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Rychlý start: Nastavení a načtení tajného klíče z Azure Key Vault pomocí šablony Správce prostředků

[Azure Key Vault](./key-vault-overview.md) je cloudová služba, která poskytuje zabezpečené úložiště tajných kódů, jako jsou klíče, hesla, certifikáty a další tajné kódy. Tento rychlý Start se zaměřuje na proces nasazení šablony Správce prostředků pro vytvoření trezoru klíčů a tajného klíče.

[Šablona správce prostředků](../azure-resource-manager/template-deployment-overview.md) je soubor JavaScript Object Notation (JSON), který definuje infrastrukturu a konfiguraci projektu. Šablona používá deklarativní syntaxi, která umožňuje určit, co máte v úmyslu nasadit bez nutnosti napsat sekvenci programovacích příkazů k jeho vytvoření. Pokud chcete získat další informace o vývoji šablon Správce prostředků, přečtěte si téma [dokumentace správce prostředků](/azure/azure-resource-manager/) a [odkaz na šablonu](/azure/templates/microsoft.keyvault/allversions).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Šablona potřebuje ke konfiguraci oprávnění vaše ID objektu uživatele Azure AD. Následující procedura získá ID objektu (GUID).

    1. Spusťte následující Azure PowerShell nebo příkaz rozhraní příkazového řádku Azure CLI tak, že vyberete možnost **vyzkoušet**a potom tento skript vložíte do podokna prostředí. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**.

        # <a name="clitabcli"></a>[Rozhraní příkazového řádku](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Zapište ID objektu. Budete ho potřebovat v další části tohoto rychlého startu.

## <a name="create-a-vault-and-a-secret"></a>Vytvoření trezoru a tajného klíče

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

[!code-json[<Azure Resource Manager template create key vault>](~/quickstart-templates/101-key-vault-create/azuredeploy.json)]

Další ukázky šablon Azure Key Vault najdete [tady](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří Trezor klíčů a tajný klíč.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Vyberte nebo zadejte následující hodnoty.

    ![Správce prostředků šablona, integrace Key Vault, nasazení portálu](./media/quick-create-template/create-key-vault-using-template-portal.png)

    Pokud není zadaný, použijte k vytvoření trezoru klíčů a tajného klíče výchozí hodnotu.

    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků**: vyberte **vytvořit novou**, zadejte jedinečný název skupiny prostředků a pak klikněte na **OK**.
    * **Umístění:** Vyberte prosím umístění.  Například **USA (střed)** .
    * **Key Vault název**: zadejte název trezoru klíčů, který musí být globálně jedinečný v rámci oboru názvů. Vault.Azure.NET. Při ověřování nasazení budete potřebovat název v další části.
    * **ID tenanta**: funkce šablony automaticky načte vaše ID tenanta.  Neměňte výchozí hodnotu.
    * **ID uživatele AD**: Zadejte ID vašeho uživatelského objektu Azure AD, které jste získali z [požadovaných součástí](#prerequisites).
    * **Název tajného**kódu: zadejte název tajného klíče, který ukládáte do trezoru klíčů.  Například **AdminPassword**.
    * **Hodnota tajného klíče**: zadejte tajnou hodnotu.  Pokud ukládáte heslo, doporučuje se použít vygenerované heslo, které jste vytvořili v části požadavky.
    * **Souhlasím s výše uvedenými podmínkami a ujednáními**: Vybrali.
3. Vyberte **Koupit**. Po úspěšném nasazení trezoru klíčů obdržíte oznámení:

    ![Správce prostředků šablona, integrace Key Vault, nasazení oznámení portálu](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

## <a name="validate-the-deployment"></a>Ověření nasazení

Můžete buď použít Azure Portal ke kontrole trezoru klíčů a tajného klíče, nebo k vytvoření seznamu vytvořeného tajného klíče použít následující skript Azure CLI nebo Azure PowerShell.

# <a name="clitabcli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

Výstup bude vypadat nějak takto:

# <a name="clitabcli"></a>[Rozhraní příkazového řádku](#tab/CLI)

![Správce prostředků šablona, integrace Key Vault, nasazení výstupu ověření portálu](./media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

![Správce prostředků šablona, integrace Key Vault, nasazení výstupu ověření portálu](./media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---
## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy týkající se služby Key Vault vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Až nebudete prostředky potřebovat, odstraňte jejich skupinu. Tím odstraníte Key Vault i související prostředky. Odstranění skupiny prostředků pomocí Azure CLI nebo Azure PowerShellu:

# <a name="clitabcli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili Trezor klíčů a tajný kód pomocí šablony Azure Resource Manager a ověření nasazení bylo ověřeno. Další informace o Key Vault a Azure Resource Manager najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](key-vault-overview.md)
- Další informace o [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
- Získat další informace o [klíčích, tajných klíčích a certifikátech](about-keys-secrets-and-certificates.md)
- Kontrola [Azure Key Vault osvědčených postupů](key-vault-best-practices.md)