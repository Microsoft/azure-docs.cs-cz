---
title: Rychlý Start k vytvoření trezoru služby Azure Recovery Services pomocí šablony Azure Resource Manager
description: V tomto rychlém startu se dozvíte, jak vytvořit trezor služby Azure Recovery Services pomocí šablony Azure Resource Manager (šablona ARM).
ms.date: 04/29/2020
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 11a88b5485ad970802a65af31daccdb30a1c86df
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533246"
---
# <a name="quickstart-create-a-recovery-services-vault-using-an-arm-template"></a>Rychlý Start: vytvoření trezoru Recovery Services pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak nastavit trezor Recovery Services pomocí šablony Azure Resource Manager (šablona ARM). Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii provozní kontinuity a zotavení po havárii (BCDR), takže vaše podnikové aplikace budou zůstat online během plánovaných a neplánovaných výpadků. Site Recovery spravuje zotavení po havárii místních počítačů a virtuálních počítačů Azure (VM), včetně replikace, převzetí služeb při selhání a obnovení.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud nemáte aktivní předplatné Azure, můžete si před tím, než začnete, vytvořit [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/).

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json":::

V šabloně jsou definované dva prostředky Azure:

- [Trezory Microsoft. RecoveryServices](/azure/templates/microsoft.recoveryservices/vaults): vytvoří trezor.
- [Microsoft. RecoveryServices/trezory/backupstorageconfig](/rest/api/backup/backupresourcestorageconfigs): konfiguruje nastavení redundance zálohy trezoru.

Šablona obsahuje volitelné parametry pro konfiguraci zálohování trezoru. Nastavení redundance úložiště jsou místně redundantní úložiště (LRS) nebo geograficky redundantní úložiště (GRS). Další informace najdete v tématu [Nastavení redundance úložiště](../backup/backup-create-rs-vault.md#set-storage-redundancy).

Další šablony Recovery Services pro Azure najdete v tématu [šablony rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

Aby bylo možné šablonu nasadit, je nutné zadat **předplatné**, **skupinu prostředků** a **název trezoru** .

1. Pokud se chcete přihlásit k Azure a otevřít šablonu, vyberte Image **nasadit do Azure** .

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. Vyberte nebo zadejte následující hodnoty:

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Šablona pro vytvoření trezoru Recovery Services.":::

   - **Předplatné**: vyberte své předplatné Azure.
   - **Skupina prostředků**: Vyberte existující skupinu nebo vyberte **vytvořit novou** a přidejte skupinu.
   - **Umístění**: ve výchozím nastavení je umístění skupiny prostředků a po výběru skupiny prostředků nebude k dispozici.
   - **Název trezoru**: zadejte název trezoru.
   - **Změnit typ úložiště**: výchozí hodnota je **false**. Vyberte **true** jenom v případě, že potřebujete změnit typ úložiště trezoru.
   - **Typ úložiště trezoru**: výchozí hodnota je **GloballyRedundant**. Pokud je typ úložiště nastavený na **true**, vyberte **LocallyRedundant**.
   - **Umístění**: ve `[resourceGroup().location]` výchozím nastavení funkce je umístění skupiny prostředků. Chcete-li změnit umístění, zadejte hodnotu, například **westus**.
   - Zaškrtněte políčko Souhlasím **s podmínkami a ujednáními uvedenými nahoře**.

1. Nasazení trezoru spustíte tak, že vyberete tlačítko **koupit** . Po úspěšném nasazení se zobrazí oznámení.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="Nasazení trezoru bylo úspěšné.":::

## <a name="validate-the-deployment"></a>Ověření nasazení

K potvrzení, že byl trezor vytvořen, použijte rozhraní příkazového řádku Azure nebo Azure PowerShell.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the vault name:" &&
read vaultName &&
az backup vault show --name $vaultName --resource-group $resourceGroupName &&
az backup vault backup-properties show --name $vaultName --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
$vaultName = Read-Host -Prompt "Enter the vault name"
$vaultBackupConfig = Get-AzRecoveryServicesVault -Name $vaultName
Get-AzRecoveryServicesVault -ResourceGroupName $resouceGroupName -Name $vaultName
Get-AzRecoveryServicesBackupProperty -Vault $vaultBackupConfig
Write-Host "Press [ENTER] to continue..."
```

---

Následující výstup je výpis informací o trezoru:

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```Output
"id": "/subscriptions/<Subscription Id>/resourceGroups/myResourceGroup
         /providers/Microsoft.RecoveryServices/vaults/myVault"
"location": "eastus"
"name": "myVault"
"resourceGroup": "myResourceGroup"

"storageModelType": "GeoRedundant"
"storageType": "GeoRedundant"
"type": "Microsoft.RecoveryServices/vaults/backupstorageconfig"
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : myVault
Type              : Microsoft.RecoveryServices/vaults
Location          : eastus
ResourceGroupName : myResourceGroup
SubscriptionId    : <Subscription Id>

BackupStorageRedundancy
-----------------------
GeoRedundant
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud plánujete používat nové prostředky, není nutné provádět žádnou akci. V opačném případě můžete odebrat skupinu prostředků a trezor, které byly vytvořené v rámci tohoto rychlého startu. Pokud chcete odstranit skupinu prostředků a její prostředky, použijte rozhraní příkazového řádku Azure CLI nebo Azure PowerShell.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resouceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Recovery Services trezor. Pokud se chcete dozvědět víc o zotavení po havárii, přejděte k dalšímu článku rychlý Start.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii](azure-to-azure-quickstart.md)
