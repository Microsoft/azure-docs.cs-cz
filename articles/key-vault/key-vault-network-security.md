---
ms.assetid: ''
title: Konfigurace bran firewall služby Azure Key Vault a virtuální sítě – Azure Key Vault
description: Podrobné pokyny ke konfiguraci virtuálních sítí a bran firewall služby Key Vault
services: key-vault
author: amitbapat
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 01/02/2019
ms.author: ambapat
ms.openlocfilehash: caf649c51346f63aa05d8f2d460e2870493b1587
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991612"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurace bran firewall služby Azure Key Vault a virtuální sítě

Tento článek obsahuje podrobné pokyny ke konfiguraci brány firewall služby Azure Key Vault a virtuální sítě k omezení přístupu k trezoru klíčů. [Koncové body služeb virtuální sítě pro službu Key Vault](key-vault-overview-vnet-service-endpoints.md) umožňují omezit přístup k zadané virtuální sítě a sadu rozsahů adres IPv4 (internet protocol verze 4).

> [!IMPORTANT]
> Po pravidla brány firewall jsou aktivní, uživatelé mohou provádět pouze služby Key Vault [rovina dat](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) povolené operace při jejich požadavky pocházejí z virtuální sítě nebo rozsahy adres IPv4. To platí i pro přístup k trezoru klíčů z portálu Azure portal. I když můžou uživatelé procházet k trezoru klíčů z portálu Azure portal, se nebudou moct vypsat klíče, tajné kódy nebo certifikáty Pokud klientském počítači se nenachází v seznamu povolených aplikací. To ovlivňuje také výběr Key Vault pomocí dalších služeb Azure. Uživatelé možná půjde zobrazit seznam trezorů klíčů, ale v seznamu klíčů, je-li zabránit pravidla brány firewall na klientském počítači.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Tady je postup konfigurace brány firewall služby Key Vault a virtuální sítě pomocí webu Azure portal:

1. Přejděte do služby key vault, kterou chcete zabezpečit.
2. Vyberte **virtuální sítí a bran firewall**.
3. V části **povolit přístup z**vyberte **vybrané sítě**.
4. Chcete-li přidat existující virtuální sítě do brány firewall a pravidla virtuální sítě, vyberte **+ přidat existující virtuální sítě**.
5. V novém okně, které se otevře vyberte předplatné, virtuální sítě a podsítě, které chcete povolit přístup k tomuto trezoru klíčů. Pokud virtuální sítě a podsítě vyberete nemají povolené koncové body služby, ověřte, že chcete povolit koncové body služby a vyberte **povolit**. Může trvat až 15 minut, než se projeví.
6. V části **IP sítě**, přidejte rozsahy adres protokolu IPv4 tak, že zadáte rozsahy adres IPv4 v [zápisu CIDR (Classless Inter-Domain se směrováním)](https://tools.ietf.org/html/rfc4632) nebo jednotlivé IP adresy.
7. Vyberte **Uložit**.

Můžete také přidat nové virtuální sítě a podsítě a pak povolit koncové body služby pro nově vytvořený virtuální sítě a podsítě, tak, že vyberete **+ přidat novou virtuální síť**. Postupujte podle pokynů.

## <a name="use-the-azure-cli"></a>Použití Azure CLI 

Tady je postup konfigurace brány firewall služby Key Vault a virtuální sítě pomocí rozhraní příkazového řádku Azure

1. [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a [přihlášení](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Seznam pravidel virtuální sítě k dispozici. Pokud jste nenastavili všechna pravidla pro tento trezor klíčů, bude seznam prázdný.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Povolte koncový bod služby Key Vault na existující virtuální síť a podsíť.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Přidáte pravidlo pro sítě pro virtuální síť a podsíť.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Přidáte rozsah IP adres, ze kterého chcete povolit přenosy.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Pokud tento trezor klíčů by měly být přístupné pro všechny důvěryhodné služby, nastavte `bypass` k `AzureServices`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Zapnout pravidel sítě tak, že nastavíte výchozí akce `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Použití Azure Powershell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tady je postup konfigurace brány firewall služby Key Vault a virtuální sítě pomocí prostředí PowerShell:

1. Nainstalujte nejnovější [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps), a [přihlášení](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Seznam pravidel virtuální sítě k dispozici. Pokud jste nenastavili všechna pravidla pro tento trezor klíčů, bude seznam prázdný.
   ```PowerShell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Povolte koncový bod služby Key Vault na existující virtuální síť a podsíť.
   ```PowerShell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Přidáte pravidlo pro sítě pro virtuální síť a podsíť.
   ```PowerShell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Přidáte rozsah IP adres, ze kterého chcete povolit přenosy.
   ```PowerShell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Pokud tento trezor klíčů by měly být přístupné pro všechny důvěryhodné služby, nastavte `bypass` k `AzureServices`.
   ```PowerShell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Zapnout pravidel sítě tak, že nastavíte výchozí akce `Deny`.
   ```PowerShell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Odkazy

* Příkazy Azure CLI: [az keyvault sítě rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Rutiny Powershellu pro Azure: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Další postup

* [Koncové body služeb virtuální sítě pro službu Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Zabezpečení trezoru klíčů](key-vault-secure-your-key-vault.md)
