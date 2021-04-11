---
title: Konfigurace Azure Key Vault bran firewall a virtuálních sítí – Azure Key Vault
description: Podrobné pokyny pro konfiguraci Key Vault bran firewall a virtuálních sítí
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 40094c00a4b896756c5c0e51116e0ae33ae2a096
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580725"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurace Azure Key Vault bran firewall a virtuálních sítí

Tento článek vám poskytne pokyny k tomu, jak nakonfigurovat bránu Azure Key Vault firewall. Tento dokument podrobně popisuje různé konfigurace pro bránu Key Vault firewall a poskytuje podrobné pokyny ke konfiguraci Azure Key Vault pro práci s jinými aplikacemi a službami Azure.

Další informace najdete v tématu [koncové body služby virtuální sítě pro Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="firewall-settings"></a>Nastavení brány firewall

Tato část se zabývá různými způsoby, jak lze nakonfigurovat bránu Azure Key Vault firewall.

### <a name="key-vault-firewall-disabled-default"></a>Zakázaná brána firewall Key Vault (výchozí)

Ve výchozím nastavení je při vytváření nového trezoru klíčů zakázaná brána firewall Azure Key Vault. Všechny aplikace a služby Azure mají přístup k trezoru klíčů a odesílají požadavky do trezoru klíčů. Upozorňujeme, že tato konfigurace neznamená, že každý uživatel bude moct provádět operace s vaším trezorem klíčů. Trezor klíčů stále omezuje tajné klíče, klíče a certifikáty uložené v trezoru klíčů tím, že vyžaduje oprávnění Azure Active Directory ověřování a zásady přístupu. Další informace o ověřování trezoru klíčů najdete v dokumentu [tady](./authentication-fundamentals.md)popisuje ověřování trezoru klíčů. Další informace najdete v tématu [přístup Azure Key Vault za bránou firewall](./access-behind-firewall.md).

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Brána firewall Key Vault povolena (pouze důvěryhodné služby)

Pokud povolíte bránu Key Vault firewall, budete mít možnost Povolit důvěryhodným službám Microsoftu obejít tuto bránu firewall. Seznam důvěryhodných služeb nepokrývá každou jednu službu Azure. Například Azure DevOps není v seznamu důvěryhodných služeb. **To neznamená, že služby, které se nezobrazí v seznamu důvěryhodných služeb, nejsou důvěryhodné nebo nezabezpečené.** Seznam důvěryhodných služeb zahrnuje služby, kde Microsoft kontroluje veškerý kód, který běží ve službě. Vzhledem k tomu, že uživatelé můžou psát vlastní kód v rámci služeb Azure, jako je například Azure DevOps, společnost Microsoft neposkytuje možnost pro vytvoření hromadného schválení služby. Kromě toho, že se služba zobrazuje v seznamu důvěryhodných služeb, neznamená to, že je povolená pro všechny scénáře. 

Pokud chcete zjistit, jestli je služba, kterou se pokoušíte použít, v seznamu důvěryhodných služeb, přečtěte si [následující dokument.](./overview-vnet-service-endpoints.md#trusted-services)
Návod, jak postupovat, najdete tady: [portál, Azure CLI a PowerShell](https://docs.microsoft.com/azure/key-vault/general/network-security#use-the-azure-portal) .

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Brána firewall Key Vault povolena (IPv4 adresy a rozsahy – statické IP adresy)

Pokud chcete autorizovat určitou službu pro přístup k trezoru klíčů prostřednictvím brány Key Vault firewall, můžete přidat IP adresu do seznamu povolených bran firewall trezoru klíčů. Tato konfigurace je nejvhodnější pro služby, které používají statické IP adresy nebo známé rozsahy. Pro tento případ je povolený limit rozsahu 1000 CIDR.

Pokud chcete u prostředku Azure, jako je webová aplikace nebo aplikace logiky, použít IP adresu nebo rozsah, proveďte následující kroky.

1. Přihlášení k webu Azure Portal
1. Vyberte prostředek (konkrétní instance služby).
1. V části Nastavení klikněte na okno Vlastnosti.
1. Vyhledejte pole IP adresa.
1. Zkopírujte tuto hodnotu nebo rozsah a zadejte ji do seznamu povolených bran firewall trezoru klíčů.

Pokud chcete pro celou službu Azure povolenou prostřednictvím Key Vault brány firewall, použijte [zde](https://www.microsoft.com/download/details.aspx?id=41653)seznam veřejně dokumentovaných IP adres datových center pro Azure. Vyhledejte IP adresy přidružené ke službě, kterou byste chtěli v požadované oblasti, a přidejte tyto IP adresy do brány firewall trezoru klíčů pomocí výše uvedených kroků.

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Povolená brána firewall Key Vault (virtuální sítě – dynamické IP adresy)

Pokud se snažíte umožnit prostředek Azure, jako je třeba virtuální počítač prostřednictvím trezoru klíčů, nebudete moct používat statické IP adresy a nebudete chtít, aby všechny IP adresy pro Azure Virtual Machines měly přístup k vašemu trezoru klíčů.

V takovém případě byste měli vytvořit prostředek v rámci virtuální sítě a pak pro přístup k trezoru klíčů umožníte provozu z konkrétní virtuální sítě a podsítě. Uděláte to tak, že provedete následující kroky.

1. Přihlášení k webu Azure Portal
1. Vyberte Trezor klíčů, který chcete nakonfigurovat.
1. Vyberte okno síť.
1. Vyberte + Přidat existující virtuální síť.
1. Vyberte virtuální síť a podsíť, které chcete umožňovat prostřednictvím brány firewall trezoru klíčů.

### <a name="key-vault-firewall-enabled-private-link"></a>Povolená brána firewall Key Vault (privátní odkaz)

Informace o tom, jak nakonfigurovat připojení privátního propojení v trezoru klíčů, najdete v dokumentu [zde](./private-link-service.md).

> [!IMPORTANT]
> Po uplatnění pravidel brány firewall můžou uživatelé provádět jenom Key Vault operace [roviny dat](secure-your-key-vault.md#data-plane-access-control) , když jejich požadavky pocházejí z povolených virtuálních sítí nebo rozsahů IPv4 adres. To platí také pro přístup k Key Vault z Azure Portal. I když uživatelé můžou přejít k trezoru klíčů z Azure Portal, nemusí být schopni zobrazit seznam klíčů, tajných kódů ani certifikátů, pokud jejich klientský počítač není v seznamu povolených. To má vliv také na Key Vault pro výběr jinými službami Azure. Uživatelé můžou zobrazit seznam trezorů klíčů, ale ne seznam klíčů, pokud pravidla brány firewall brání jejich klientskému počítači.

> [!NOTE]
> Mějte na paměti následující omezení konfigurace:
> * Povoluje se maximálně 127 pravidel virtuální sítě a 127 pravidel IPv4. 
> * Pravidla sítě IP jsou povolena pouze pro veřejné IP adresy. Rozsahy IP adres rezervované pro privátní sítě (definované v dokumentu RFC 1918) nejsou v pravidlech protokolu IP povoleny. Soukromé sítě obsahují adresy, které začínají na **10.**, **172.16-31** a **192,168.**. 
> * V tuto chvíli se podporují jenom IPv4 adresy.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Tady je postup konfigurace Key Vault bran firewall a virtuálních sítí pomocí Azure Portal:

1. Přejděte do trezoru klíčů, který chcete zabezpečit.
2. Vyberte **sítě** a pak vyberte kartu **brány firewall a virtuální sítě** .
3. V části **Povolení přístupu z** vyberte **vybrané sítě**.
4. Pokud chcete přidat existující virtuální sítě do bran firewall a pravidel virtuální sítě, vyberte **+ Přidat existující virtuální sítě**.
5. V novém okně, které se otevře, vyberte předplatné, virtuální sítě a podsítě, pro které chcete přístup k tomuto trezoru klíčů zpřístupnit. Pokud virtuální sítě a podsítě, které vyberete, nemají povolený koncový bod služby, potvrďte, že chcete povolit koncové body služby, a vyberte **Povolit**. Platnost může trvat až 15 minut.
6. V části sítě s protokolem **IP** přidejte rozsahy adres IPv4 zadáním rozsahů IPv4 adres v [CIDR (směrování mezi doménami) a zápisem](https://tools.ietf.org/html/rfc4632) nebo jednotlivými IP adresami.
7. Pokud chcete, aby důvěryhodné služby společnosti Microsoft používaly bránu Key Vault firewall, vyberte Ano. Úplný seznam aktuálních Key Vault důvěryhodných služeb najdete na následujícím odkazu. [Azure Key Vault důvěryhodné služby](./overview-vnet-service-endpoints.md#trusted-services)
7. Vyberte **Uložit**.

Můžete taky přidat nové virtuální sítě a podsítě a potom pro nově vytvořené virtuální sítě a podsítě povolit koncové body služby tak, že vyberete **+ Přidat novou virtuální síť**. Pak postupujte podle pokynů.

## <a name="use-the-azure-cli"></a>Použití Azure CLI 

Tady je postup konfigurace Key Vault bran firewall a virtuálních sítí pomocí Azure CLI.

1. Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a [Přihlaste se](/cli/azure/authenticate-azure-cli).

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

1. Nainstalujte nejnovější [Azure PowerShell](/powershell/azure/install-az-ps)a [přihlaste](/powershell/azure/authenticate-azureps)se.

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

## <a name="references"></a>Reference
* Odkaz na šablonu ARM: [odkaz na šablonu Azure Key Vault ARM](/azure/templates/Microsoft.KeyVault/vaults)
* Příkazy rozhraní příkazového řádku Azure: [AZ pro Trezor klíčů Network-Rule](/cli/azure/keyvault/network-rule)
* Rutiny Azure PowerShell: [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Další kroky

* [Koncové body služby virtuální sítě pro Key Vault](overview-vnet-service-endpoints.md)
* [Přehled zabezpečení Azure Key Vault](security-overview.md)
