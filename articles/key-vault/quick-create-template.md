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
ms.date: 05/22/2019
ms.author: jgao
ms.openlocfilehash: b27caa3d91d67ad63bfbf5e7c549d690980cdd7b
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934443"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Rychlý start: Nastavení a načtení tajného klíče z Azure Key Vault pomocí šablony Správce prostředků

[Azure Key Vault](./key-vault-overview.md) je cloudová služba, která poskytuje zabezpečené úložiště tajných kódů, jako jsou klíče, hesla, certifikáty a další tajné kódy. Tento rychlý Start se zaměřuje na proces nasazení šablony Správce prostředků pro vytvoření trezoru klíčů a tajného klíče. Další informace o vývoji šablon Správce prostředků naleznete v [dokumentaci správce prostředků](/azure/azure-resource-manager/) a odkazu na [šablonu](/azure/templates/microsoft.keyvault/allversions).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Šablona potřebuje ke konfiguraci oprávnění vaše ID objektu uživatele Azure AD. Následující procedura získá ID objektu (GUID).

    1. Spusťte následující Azure PowerShell nebo příkaz rozhraní příkazového řádku Azure CLI tak, že vyberete možnost **vyzkoušet**a potom tento skript vložíte do podokna prostředí. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**. 

        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --upn-or-object-id $upn --query "objectId" 
        ```

        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        ```

    2. Zapište ID objektu. Budete ho potřebovat v další části tohoto rychlého startu.

## <a name="create-a-vault-and-a-secret"></a>Vytvoření trezoru a tajného klíče

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Další ukázky šablon Azure Key Vault najdete [tady](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří Trezor klíčů a tajný klíč.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Vyberte nebo zadejte následující hodnoty.  

    ![Integrace služby Key Vault v šabloně Resource Manageru – portál pro nasazení](./media/quick-create-template/create-key-vault-using-template-portal.png)

    Pokud není zadaný, použijte k vytvoření trezoru klíčů a tajného klíče výchozí hodnotu.

    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků**: vyberte **vytvořit novou**, zadejte jedinečný název skupiny prostředků a pak klikněte na **OK**. 
    * **Umístění:** Vyberte prosím umístění.  Například **USA (střed)** .
    * **Key Vault název**: zadejte název trezoru klíčů, který musí být globálně jedinečný v rámci oboru názvů. Vault.Azure.NET.  
    * **ID tenanta:** Funkce šablony automaticky načte ID vašeho tenanta.  Neměňte výchozí hodnotu.
    * **ID uživatele AD**: Zadejte ID vašeho uživatelského objektu Azure AD, které jste získali z [požadovaných součástí](#prerequisites).
    * **Název tajného**kódu: zadejte název tajného klíče, který ukládáte do trezoru klíčů.  Například **AdminPassword**.
    * **Hodnota tajného klíče**: zadejte tajnou hodnotu.  Pokud ukládáte heslo, doporučuje se použít vygenerované heslo, které jste vytvořili v části požadavky.
    * **Souhlasím s výše uvedenými podmínkami a ujednáními**: Vybrat
3. Vyberte **Koupit**.

## <a name="validate-the-deployment"></a>Ověření nasazení

Můžete buď použít Azure Portal ke kontrole trezoru klíčů a tajného klíče, nebo k vytvoření seznamu vytvořeného tajného klíče použít následující skript Azure CLI nebo Azure PowerShell.

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName
```

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy týkající se služby Key Vault vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Až nebudete prostředky potřebovat, odstraňte jejich skupinu. Tím odstraníte Key Vault i související prostředky. Odstranění skupiny prostředků pomocí Azure CLI nebo Azure PowerShellu:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```
```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>Další postup

* [Domovská stránka služby Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Dokumentace ke službě Azure Key Vault](index.yml)
* [Azure SDK pro Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Reference k rozhraní Azure REST API](https://docs.microsoft.com/rest/api/keyvault/)
