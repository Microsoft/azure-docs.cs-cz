---
title: Rychlý start Azure – vytvoření služby Azure key vault a tajného kódu s použitím šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Rychlý start ukazuje, jak vytvořit trezory klíčů Azure a přidávání tajných kódů do trezorech pomocí šablony Azure Resource Manageru.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/04/2019
ms.author: jgao
ms.openlocfilehash: 4b774e5b0a5c64a9af9a5a54ba264c6226558a24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60777720"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí šablony Resource Manageru

[Služba Azure Key Vault](./key-vault-overview.md) je Cloudová služba, která poskytuje zabezpečené úložiště pro tajné kódy, jako jsou klíče, hesla, certifikáty a dalších tajných kódů. V tomto rychlém startu se zaměřuje na postup nasazení šablony Resource Manageru k vytvoření služby key vault a tajný klíč. Další informace o vývoji šablon Resource Manageru najdete v tématu [dokumentace ke službě Správce prostředků](/azure/azure-resource-manager/) a [referenčními informacemi k šablonám](/azure/templates/microsoft.keyvault/allversions).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Šablona potřebuje ke konfiguraci oprávnění vaše ID objektu uživatele Azure AD. Následující postup získá ID (GUID) objektu.

    1. Spuštěním následujícího příkazu prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure vyberete **vyzkoušet**a potom vložte skript do okna prostředí. Vložte skript, klikněte pravým tlačítkem na prostředí a pak vyberte **vložte**. 

        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --upn-or-object-id $upn --query "objectId" 
        ```

        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        ```

    2. Poznamenejte si ID objektu. Budete potřebovat v další části tohoto rychlého startu.

## <a name="create-a-vault-and-a-secret"></a>Vytvoření trezoru a tajný klíč

Šablona použitá v tomto rychlém startu je z [šablon rychlého startu Azure](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Další ukázkové šablony Azure Key Vault najdete [tady](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří trezor klíčů a tajný klíč.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Vyberte nebo zadejte následující hodnoty.  

    ![Integrace služby Key Vault v šabloně Resource Manageru – portál pro nasazení](./media/quick-create-template/create-key-vault-using-template-portal.png)

    Pokud není zadána, použijte výchozí hodnotu k vytvoření služby key vault a tajný klíč.

    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků**: vyberte **vytvořit nový**, zadejte jedinečný název pro skupinu prostředků a pak klikněte na tlačítko **OK**. 
    * **Umístění:** Vyberte prosím umístění.  Například **USA (střed)**.
    * **Název trezoru klíčů**: Zadejte jedinečný název pro trezor klíčů.  
    * **ID tenanta:** Funkce šablony automaticky načte ID vašeho tenanta.  Výchozí hodnotu neměňte.
    * **Id uživatele AD**: Zadejte své ID objektu uživatele Azure AD, který jste získali z [požadavky](#prerequisites).
    * **Název tajného kódu**: Zadejte název, ve kterém je uložený ve službě key vault tajný klíč.  Například **adminpassword**
    * **Tajná hodnota**: Zadejte hodnotu tajného kódu.  Pokud ukládáte heslo, se doporučuje použít vygenerované heslo, které jste vytvořili v části požadavky.
    * **Souhlasím s podmínkami a ujednáními stavu výše**: Vybrat
3. Vyberte **Koupit**.

## <a name="validate-the-deployment"></a>Ověření nasazení

Můžete buď pomocí webu Azure portal ke kontrole služby key vault a tajný kód, nebo pomocí následujícího skriptu Azure Powershellu nebo rozhraní příkazového řádku Azure můžete zobrazit seznam vytvořit tajný kód.

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
Až nebudete prostředky potřebovat, odstraňte jejich skupinu. Tím odstraníte Key Vault i související prostředky. Odstranit skupinu prostředků pomocí Azure Powershellu nebo rozhraní příkazového řádku Azure:

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
* [Dokumentace ke službě Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK pro Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Reference k rozhraní Azure REST API](https://docs.microsoft.com/rest/api/keyvault/)
