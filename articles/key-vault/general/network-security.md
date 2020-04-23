---
title: Konfigurace bran azure key vault a virtuálních sítí – Azure Key Vault
description: Podrobné pokyny ke konfiguraci bran firewall úložiště klíčů a virtuálních sítí
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: sudbalas
ms.openlocfilehash: 0438f573c33c71e0f30b7db1909e3649b21010a7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086585"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurace bran azure key vault a virtuálních sítí

Tento článek obsahuje podrobné pokyny ke konfiguraci bran firewall azure key vault a virtuálních sítí pro omezení přístupu k trezoru klíčů. [Koncové body služby virtuální sítě pro úložiště klíčů](overview-vnet-service-endpoints.md)) umožňují omezit přístup k zadané virtuální síti a sadě rozsahů adres IPv4 (internet protocol version 4).

> [!IMPORTANT]
> Po použití pravidel brány firewall mohou uživatelé provádět operace [s rovinou dat úložiště](secure-your-key-vault.md#data-plane-access-control) klíčů pouze v případě, že jejich požadavky pocházejí z povolených virtuálních sítí nebo rozsahů adres IPv4. To platí také pro přístup k trezoru klíčů z webu Azure Portal. Přestože uživatelé mohou přejít do trezoru klíčů z portálu Azure, nemusí být schopni seznam klíčů, tajných kódů nebo certifikátů, pokud jejich klientský počítač není v seznamu povolených. To má také vliv na výběr trezoru klíčů jinými službami Azure. Uživatelé mohou zobrazit seznam trezorů klíčů, ale ne klíče seznamu, pokud pravidla brány firewall brání jejich klientském počítači.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Tady je postup konfigurace bran firewall a virtuálních sítí trezoru klíčů pomocí portálu Azure:

1. Přejděte do trezoru klíčů, který chcete zabezpečit.
2. Vyberte **Síť**a pak vyberte kartu **Brány firewall a virtuální sítě.**
3. V části **Povolit přístup z**vyberte **Vybrané sítě**.
4. Pokud chcete přidat existující virtuální sítě do bran firewall a pravidel virtuálních sítí, vyberte **+ Přidat existující virtuální sítě**.
5. V novém okně, které se otevře, vyberte předplatné, virtuální sítě a podsítě, které chcete povolit přístup k tomuto trezoru klíčů. Pokud vybrané virtuální sítě a podsítě nemají povolené koncové body služby, zkontrolujte, zda chcete povolit koncové body služby, a vyberte **Povolit**. Může trvat až 15 minut, než se projeví.
6. V části **IP Networks**přidejte rozsahy adres IPv4 zadáním rozsahů adres IPv4 do [zápisu CIDR (Classless Inter-domain Routing)](https://tools.ietf.org/html/rfc4632) nebo do jednotlivých ADRES IP.
7. Vyberte **Uložit**.

Můžete také přidat nové virtuální sítě a podsítě a potom povolit koncové body služby pro nově vytvořené virtuální sítě a podsítě výběrem **+ Přidat novou virtuální síť**. Pak postupujte podle pokynů.

## <a name="use-the-azure-cli"></a>Použití Azure CLI 

Tady je postup konfigurace bran firewall a virtuálních sítí trezoru klíčů pomocí rozhraní příkazového příkazu Azure

1. [Nainstalujte azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) a [přihlaste se](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Seznam dostupných pravidel virtuální sítě. Pokud jste pro tento trezor klíčů nenastavili žádná pravidla, bude seznam prázdný.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Povolte koncový bod služby pro trezor klíčů v existující virtuální síti a podsíti.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Přidejte síťové pravidlo pro virtuální síť a podsíť.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Přidejte rozsah IP adres, ze kterého chcete povolit provoz.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Pokud by tento trezor klíčů měl být `bypass` `AzureServices`přístupný všem důvěryhodným službám, nastavte na .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Zapněte pravidla sítě nastavením výchozí `Deny`akce na .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Použití Azure Powershell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tady je postup konfigurace bran firewall a virtuálních sítí trezoru klíčů pomocí Prostředí PowerShell:

1. Nainstalujte nejnovější [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)a [přihlaste se](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Seznam dostupných pravidel virtuální sítě. Pokud jste pro tento trezor klíčů nenastavili žádná pravidla, bude seznam prázdný.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Povolte koncový bod služby pro trezor klíčů v existující virtuální síti a podsíti.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Přidejte síťové pravidlo pro virtuální síť a podsíť.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Přidejte rozsah IP adres, ze kterého chcete povolit provoz.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Pokud by tento trezor klíčů měl být `bypass` `AzureServices`přístupný všem důvěryhodným službám, nastavte na .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Zapněte pravidla sítě nastavením výchozí `Deny`akce na .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Odkazy

* Příkazy Azure CLI: [pravidlo sítě az keyvault](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Rutiny prostředí Azure PowerShell: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Další kroky

* [Koncové body služby virtuální sítě pro trezor klíčů](overview-vnet-service-endpoints.md))
* [Zabezpečte trezor klíčů](secure-your-key-vault.md))
