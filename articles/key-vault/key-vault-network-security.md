---
ms.assetid: ''
title: Konfigurace bran firewall služby Azure Key Vault a virtuální sítě
description: Podrobné pokyny ke konfiguraci virtuálních sítí a bran firewall služby Key Vault
services: key-vault
author: amitbapat
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/31/2018
ms.author: ambapat
ms.openlocfilehash: c58fc56742c0a11771bdd84e4195e6f476622a3b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345232"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurace bran firewall služby Azure Key Vault a virtuální sítě

Tato příručka popisuje podrobný postup konfigurace brány firewall služby Key Vault a virtuální sítě k omezení přístupu k trezoru klíčů. [Virtuální koncové body služeb sítě pro službu Key Vault](key-vault-overview-vnet-service-endpoints.md) umožňují omezit přístup k trezoru klíčů, aby zadané virtuální sítě a/nebo sadu rozsahů adres IPv4 (Internet Protocol verze 4).

> [!IMPORTANT]
> Jakmile se brána firewall a pravidla virtuální sítě jsou v podstatě všechny služby Key Vault [rovina dat](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) operace lze provádět pouze, když volající požadavky pocházejí z povolených virtuálních sítí nebo rozsahy adres IPV4. To platí i pro přístup k trezoru klíčů z webu Azure portal. Když uživatel může prohlížeče do trezoru klíčů z webu Azure portal, se nebudou moct seznam klíčů/tajných kódů a certifikátů Pokud klientském počítači se nenachází v seznamu povolených aplikací. To ovlivňuje také výběr trezoru klíč pomocí dalších služeb Azure. Uživatelé moci zobrazit seznam trezorů klíčů, ale v seznamu klíčů, je-li zabránit pravidla brány firewall na klientském počítači.

## <a name="azure-portal"></a>portál Azure

1. Přejděte do služby key vault, kterou chcete zabezpečit.
2. Klikněte na **virtuální sítí a bran firewall**.
3. Klikněte na **vybrané sítě** pod **povolit přístup z**.
4. Chcete-li přidat existující virtuální sítě do brány firewall a pravidla virtuální sítě, klikněte na tlačítko **+ přidat existující virtuální sítě**.
5. V novém okně, která se otevře vyberte předplatné, virtuální sítě a podsítě, které chcete povolit přístup k tomuto trezoru klíčů. Pokud virtuální sítě a podsítě, kterou jste vybrali, nemají povolené koncové body služby se zobrazí zpráva s oznámením, "následující sítě nemají... enavled koncové body služby". Klikněte na tlačítko **povolit** po potvrzení, že chcete povolit koncové body služby pro uvedených virtuálních sítí a tento počet podsítí:. Může trvat až 15 minut, než se projeví.
6. Můžete také přidat nové virtuální sítě a podsítě a pak kliknutím na povolit koncové body služby pro nově vytvořený virtuální sítě a podsítě, **+ přidat novou virtuální síť** a postupovat podle pokynů.
7. V části **IP sítě**, můžete přidat rozsahy adres protokolu IPv4 tak, že zadáte rozsahy adres IPv4 v [zápisu CIDR (Classless Inter-Domain se směrováním)](https://tools.ietf.org/html/rfc4632) nebo jednotlivé IP adresy.
8. Klikněte na **Uložit**.

## <a name="azure-cli-20"></a>Azure CLI 2.0

1. [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) a [přihlášení](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Pravidla virtuální sítě k dispozici seznam, pokud jste nenastavili všechna pravidla pro tento trezor klíčů, seznam bude prázdný.
```azurecli
az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
```

3. Povolit koncový bod služby Key Vault na existující virtuální sítě a podsítě
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
```

4. Přidat pravidlo pro sítě pro virtuální síť a podsíť
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
```

5. Přidat rozsah IP adres, aby přijímaly provoz z
```azurecli
az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
```

6. Pokud tento trezor klíčů musí být přístupné pro všechny důvěryhodné služby, nastavte "obejít" AzureServices
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
```

7. Nyní konečný a důležité krok, zapněte síti pravidla dále tím, že nastavíte výchozí akci "Odepřít.
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
```

## <a name="azure-powershell"></a>Azure PowerShell

1. Nainstalujte nejnovější [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) a [přihlášení](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Pravidla virtuální sítě k dispozici seznam, pokud jste nenastavili všechna pravidla pro tento trezor klíčů, seznam bude prázdný.
```PowerShell
(Get-AzureRmKeyVault -VaultName "mykeyvault").NetworkAcls
```

3. Povolit koncový bod služby Key Vault na existující virtuální sítě a podsítě
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzureRmVirtualNetwork
```

4. Přidat pravidlo pro sítě pro virtuální síť a podsíť
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
```

5. Přidat rozsah IP adres, aby přijímaly provoz z
```PowerShell
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
```

6. Pokud tento trezor klíčů musí být přístupné pro všechny důvěryhodné služby, nastavte "obejít" AzureServices
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
```

7. Nyní konečný a důležité krok, zapněte síti pravidla dále tím, že nastavíte výchozí akci "Odepřít.
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
```

## <a name="references"></a>Odkazy

* Příkazy Azure CLI 2.0 – [az keyvault sítě rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Rutiny Azure Powershellu – [Get-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurermkeyvault), [přidat AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureRmKeyVaultNetworkRule), [odebrat AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureRmKeyVaultNetworkRule), [ Aktualizace AzureRmKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureRmKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Další postup

* [Koncové body služeb virtuální sítě pro službu Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Zabezpečení trezoru klíčů](key-vault-secure-your-key-vault.md)