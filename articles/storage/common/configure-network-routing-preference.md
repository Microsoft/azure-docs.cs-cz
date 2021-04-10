---
title: Konfigurace předvolby směrování sítě
titleSuffix: Azure Storage
description: Nakonfigurujte předvolby směrování sítě pro váš účet úložiště Azure, abyste určili, jak se bude směrovat síťový provoz na váš účet od klientů přes Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 0738f7e427c2ff094c9b6df7539ba67dff80d095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589850"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Nakonfigurujte předvolby síťového směrování pro Azure Storage

Tento článek popisuje, jak můžete nakonfigurovat pro svůj účet úložiště předvolby směrování sítě a koncové body specifické pro směrování. 

Priorita síťového směrování určuje, jak se síťový provoz směruje na váš účet od klientů přes Internet. Koncové body specifické pro trasu jsou nové koncové body, které Azure Storage vytvoří pro váš účet úložiště. Tyto koncové body směrují provoz přes požadovanou cestu beze změny výchozí předvolby směrování. Další informace najdete v tématu [Předvolby síťového směrování pro Azure Storage](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Konfigurace předvolby směrování pro výchozí veřejný koncový bod

Ve výchozím nastavení je předvolby směrování pro veřejný koncový bod účtu úložiště nastavená na globální síť Microsoft. Jako výchozí předvolby směrování pro veřejný koncový bod vašeho účtu úložiště si můžete vybrat mezi globálním a internetovým směrováním od Microsoftu. Další informace o rozdílu mezi těmito dvěma typy směrování najdete v tématu [Předvolby síťového směrování pro Azure Storage](network-routing-preference.md). 

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Změna předvolby směrování na Internet:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. Na portálu přejděte na svůj účet úložiště.

3. V části **Nastavení** vyberte **sítě**.

    > [!div class="mx-imgBorder"]
    > ![Možnost nabídky síť](./media/configure-network-routing-preference/networking-option.png)

4.  Na kartě **brány firewall a virtuální sítě** v části **síťové směrování** změňte nastavení **Předvolby směrování** na **Internet směrování**.

5.  Klikněte na **Uložit**.

    > [!div class="mx-imgBorder"]
    > ![možnost směrování v Internetu](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Přihlaste se k předplatnému Azure pomocí `Connect-AzAccount` příkazu a podle pokynů na obrazovce proveďte ověření.

   ```powershell
   Connect-AzAccount
   ```

2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na předplatné účtu úložiště, který bude hostovat váš statický Web.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Nahraďte `<subscription-id>` hodnotu zástupného symbolu číslem ID vašeho předplatného.

3. Chcete-li změnit předvolby směrování na směrování sítě Internet, použijte příkaz [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) a nastavte `--routing-choice` parametr na `InternetRouting` .

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   Nahraďte `<resource-group-name>` hodnotu zástupného symbolu názvem skupiny prostředků, která obsahuje účet úložiště.

   Nahraďte `<storage-account-name>` hodnotu zástupného symbolu názvem účtu úložiště.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Přihlaste se ke svému předplatnému Azure.

   - Pokud chcete spustit Azure Cloud Shell, přihlaste se k [Azure Portal](https://portal.azure.com).

   - K místní instalaci rozhraní příkazového řádku se přihlaste spuštěním příkazu [AZ Login](/cli/azure/reference-index#az-login) :

     ```azurecli
     az login
     ```
2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na předplatné účtu úložiště, který bude hostovat váš statický Web.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Nahraďte `<subscription-id>` hodnotu zástupného symbolu číslem ID vašeho předplatného.

3. Chcete-li změnit předvolby směrování na směrování sítě Internet, použijte příkaz [AZ Storage Account Update](/cli/azure/storage/account#az_storage_account_update) a nastavte `--routing-choice` parametr na `InternetRouting` .

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   Nahraďte `<storage-account-name>` hodnotu zástupného symbolu názvem vašeho účtu úložiště.

---

## <a name="configure-a-route-specific-endpoint"></a>Konfigurace koncového bodu specifického pro trasu

Můžete také nakonfigurovat koncový bod specifický pro trasu. Můžete třeba nastavit předvolby směrování pro výchozí koncový bod na *Směrování v Internetu* a pak publikovat koncový bod specifický pro trasu, který umožňuje směrování provozu mezi klienty na internetu a vaším účtem úložiště prostřednictvím globální sítě Microsoftu.

Tato preference má vliv jenom na koncový bod konkrétní trasy. Tato předvolba nemá vliv na výchozí předvolby směrování.  

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1.  Na portálu přejděte na svůj účet úložiště.

2.  V části **Nastavení** vyberte **sítě**.

3.  Na kartě **brány firewall a virtuální sítě** v části **publikovat koncové body specifické pro trasu** zvolte Předvolby směrování pro koncový bod specifický pro trasu a potom klikněte na **Uložit**.

    Na následujícím obrázku vidíte výběr možnosti **Směrování sítě Microsoft** .

    > [!div class="mx-imgBorder"]
    > ![Možnost směrování sítě Microsoft](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Ke konfiguraci koncového bodu specifického pro trasu použijte příkaz [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) . 

   - Pokud chcete vytvořit koncový bod specifický pro trasu, který používá předvolby síťového směrování Microsoftu, nastavte `-PublishMicrosoftEndpoint` parametr na `true` . 

   - Pokud chcete vytvořit koncový bod specifický pro trasu, který používá předvolby směrování v Internetu, nastavte `-PublishInternetEndpointTo` parametr na `true` .  

   Následující příklad vytvoří koncový bod specifický pro trasu, který používá předvolby síťového směrování Microsoftu.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   Nahraďte `<resource-group-name>` hodnotu zástupného symbolu názvem skupiny prostředků, která obsahuje účet úložiště.

   Nahraďte `<storage-account-name>` hodnotu zástupného symbolu názvem účtu úložiště.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Ke konfiguraci koncového bodu specifického pro trasu použijte příkaz [AZ Storage Account Update](/azure/storage/account#az-storage-account-update) . 

   - Pokud chcete vytvořit koncový bod specifický pro trasu, který používá předvolby síťového směrování Microsoftu, nastavte `--publish-microsoft-endpoints` parametr na `true` . 

   - Pokud chcete vytvořit koncový bod specifický pro trasu, který používá předvolby směrování v Internetu, nastavte `--publish-internet-endpoints` parametr na `true` .  

   Následující příklad vytvoří koncový bod specifický pro trasu, který používá předvolby síťového směrování Microsoftu.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   Nahraďte `<storage-account-name>` hodnotu zástupného symbolu názvem účtu úložiště.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Najít název koncového bodu pro koncový bod specifický pro trasu

Pokud jste nakonfigurovali koncový bod specifický pro trasu, můžete koncový bod najít ve vlastnostech účtu úložiště.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1.  V části **Nastavení** vyberte **vlastnosti**.

    > [!div class="mx-imgBorder"]
    > ![možnost nabídky vlastnosti](./media/configure-network-routing-preference/properties.png)

2.  Pro každou službu, která podporuje předvolby směrování, se zobrazí koncový bod **Směrování sítě Microsoft** . Tento obrázek ukazuje koncový bod pro objekty BLOB a souborové služby.

    > [!div class="mx-imgBorder"]
    > ![Možnost směrování sítě Microsoft pro koncové body specifické pro trasu](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. K vytištění koncových bodů do konzoly použijte `PrimaryEndpoints` vlastnost objektu účtu úložiště.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   Nahraďte `<resource-group-name>` hodnotu zástupného symbolu názvem skupiny prostředků, která obsahuje účet úložiště.

   Nahraďte `<storage-account-name>` hodnotu zástupného symbolu názvem účtu úložiště.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. K tisku koncových bodů do konzoly použijte vlastnost [AZ Storage Account show](/cli/azure/storage/account#az_storage_account_show) objektu Account účtu úložiště.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   Nahraďte `<resource-group-name>` hodnotu zástupného symbolu názvem skupiny prostředků, která obsahuje účet úložiště.

   Nahraďte `<storage-account-name>` hodnotu zástupného symbolu názvem účtu úložiště.

---

## <a name="see-also"></a>Viz také

- [Priorita síťového směrování](network-routing-preference.md)
- [Konfigurace bran firewall Azure Storage a virtuálních sítí](storage-network-security.md)
- [Doporučení zabezpečení pro úložiště objektů BLOB](../blobs/security-recommendations.md)
