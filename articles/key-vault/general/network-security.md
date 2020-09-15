---
title: Konfigurace Azure Key Vault bran firewall a virtuálních sítí – Azure Key Vault
description: Podrobné pokyny pro konfiguraci Key Vault bran firewall a virtuálních sítí
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: sudbalas
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1c8d1af9bcea6b5e0e43a8928cbcdd907f2fb534
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069029"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurace Azure Key Vault bran firewall a virtuálních sítí

Tento článek poskytuje podrobné pokyny ke konfiguraci Azure Key Vault bran firewall a virtuálních sítí pro omezení přístupu k trezoru klíčů. [Koncové body služby virtuální sítě pro Key Vault](overview-vnet-service-endpoints.md) umožňují omezit přístup k zadané virtuální síti a sadě rozsahů adres IPv4 (Internet Protocol verze 4).

> [!IMPORTANT]
> Po uplatnění pravidel brány firewall můžou uživatelé provádět jenom Key Vault operace [roviny dat](secure-your-key-vault.md#data-plane-access-control) , když jejich požadavky pocházejí z povolených virtuálních sítí nebo rozsahů IPv4 adres. To platí také pro přístup k Key Vault z Azure Portal. I když uživatelé můžou přejít k trezoru klíčů z Azure Portal, nemusí být schopni zobrazit seznam klíčů, tajných kódů ani certifikátů, pokud jejich klientský počítač není v seznamu povolených. To má vliv také na Key Vault pro výběr jinými službami Azure. Uživatelé můžou zobrazit seznam trezorů klíčů, ale ne seznam klíčů, pokud pravidla brány firewall brání jejich klientskému počítači.

> [!NOTE]
> Mějte na paměti následující omezení konfigurace:
> * Povoluje se maximálně 127 pravidel virtuální sítě a 127 pravidel IPv4. 
> * Rozsahy malých adres, které používají předponu "/31", nejsou podporovány. Místo toho nakonfigurujte tyto rozsahy pomocí jednotlivých pravidel IP adres.
> * Pravidla sítě IP jsou povolena pouze pro veřejné IP adresy. Rozsahy IP adres rezervované pro privátní sítě (definované v dokumentu RFC 1918) nejsou v pravidlech protokolu IP povoleny. Soukromé sítě obsahují adresy, které začínají na **10.**, **172.16-31**a **192,168.**. 
> * V tuto chvíli se podporují jenom IPv4 adresy.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Tady je postup konfigurace Key Vault bran firewall a virtuálních sítí pomocí Azure Portal:

1. Přejděte do trezoru klíčů, který chcete zabezpečit.
2. Vyberte **sítě**a pak vyberte kartu **brány firewall a virtuální sítě** .
3. V části **Povolení přístupu z**vyberte **vybrané sítě**.
4. Pokud chcete přidat existující virtuální sítě do bran firewall a pravidel virtuální sítě, vyberte **+ Přidat existující virtuální sítě**.
5. V novém okně, které se otevře, vyberte předplatné, virtuální sítě a podsítě, pro které chcete přístup k tomuto trezoru klíčů zpřístupnit. Pokud virtuální sítě a podsítě, které vyberete, nemají povolený koncový bod služby, potvrďte, že chcete povolit koncové body služby, a vyberte **Povolit**. Platnost může trvat až 15 minut.
6. V části sítě s protokolem **IP**přidejte rozsahy adres IPv4 zadáním rozsahů IPv4 adres v [CIDR (směrování mezi doménami) a zápisem](https://tools.ietf.org/html/rfc4632) nebo jednotlivými IP adresami.
7. Pokud chcete, aby důvěryhodné služby společnosti Microsoft používaly bránu Key Vault firewall, vyberte Ano. Úplný seznam aktuálních Key Vault důvěryhodných služeb najdete na následujícím odkazu. [Azure Key Vault důvěryhodné služby](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
7. Vyberte **Uložit**.

Můžete taky přidat nové virtuální sítě a podsítě a potom pro nově vytvořené virtuální sítě a podsítě povolit koncové body služby tak, že vyberete **+ Přidat novou virtuální síť**. Pak postupujte podle pokynů.

## <a name="use-the-azure-cli"></a>Použití Azure CLI 

Tady je postup konfigurace Key Vault bran firewall a virtuálních sítí pomocí Azure CLI.

1. Nainstalujte rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) a [Přihlaste se](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Vypíše dostupná pravidla virtuální sítě. Pokud jste pro tento trezor klíčů nenastavili žádná pravidla, seznam bude prázdný.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Povolte koncový bod služby pro Key Vault v existující virtuální síti a podsíti.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Přidejte síťové pravidlo pro virtuální síť a podsíť.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Přidejte rozsah IP adres, ze kterého chcete povolený provoz.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Pokud má být tento trezor klíčů přístupný pro jakékoli důvěryhodné služby, nastavte `bypass` na `AzureServices` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Zapněte pravidla sítě nastavením výchozí akce na `Deny` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Použití Azure Powershell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tady je postup konfigurace Key Vault bran firewall a virtuálních sítí pomocí prostředí PowerShell:

1. Nainstalujte nejnovější [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)a [přihlaste](https://docs.microsoft.com/powershell/azure/authenticate-azureps)se.

2. Vypíše dostupná pravidla virtuální sítě. Pokud jste pro tento trezor klíčů nenastavili žádná pravidla, seznam bude prázdný.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Povolte koncový bod služby pro Key Vault v existující virtuální síti a podsíti.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Přidejte síťové pravidlo pro virtuální síť a podsíť.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Přidejte rozsah IP adres, ze kterého chcete povolený provoz.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Pokud má být tento trezor klíčů přístupný pro jakékoli důvěryhodné služby, nastavte `bypass` na `AzureServices` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Zapněte pravidla sítě nastavením výchozí akce na `Deny` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Odkazy
* Odkaz na šablonu ARM: [odkaz na šablonu Azure Key Vault ARM](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/vaults)
* Příkazy rozhraní příkazového řádku Azure: [AZ pro Trezor klíčů Network-Rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Rutiny Azure PowerShell: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Další kroky

* [Koncové body služby virtuální sítě pro Key Vault](overview-vnet-service-endpoints.md)
* [Zabezpečení trezoru klíčů](secure-your-key-vault.md)
