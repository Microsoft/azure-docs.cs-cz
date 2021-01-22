---
title: Integrace se službou Azure Private Link
description: Naučte se integrovat Azure Key Vault se službou Azure Private Link.
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 11/17/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8fee7eca780d81dcd5d9fa9359ab59994256db07
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678770"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>Integrace služby Key Vault se službou Azure Private Link

Služba privátního propojení Azure vám umožňuje přístup ke službám Azure (například Azure Key Vault, Azure Storage a Azure Cosmos DB) a hostovaným zákaznickým a partnerským službám Azure prostřednictvím privátního koncového bodu ve vaší virtuální síti.

Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služby se dá směrovat prostřednictvím privátního koncového bodu, takže se nevyžadují žádné brány, zařízení NAT, ExpressRoute, připojení VPN ani veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, která poskytuje nejvyšší úroveň členitosti v řízení přístupu.

Další informace najdete v tématu [co je privátní propojení Azure?](../../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Požadavky

K integraci trezoru klíčů s privátním propojením Azure budete potřebovat následující:

- Trezor klíčů.
- Virtuální síť Azure.
- Podsíť ve virtuální síti.
- Oprávnění vlastníka nebo přispěvatele pro Trezor klíčů i virtuální síť.

Váš privátní koncový bod a virtuální síť musí být ve stejné oblasti. Když vyberete oblast pro soukromý koncový bod pomocí portálu, automaticky se vyfiltrují jenom virtuální sítě, které jsou v této oblasti. Váš Trezor klíčů může být v jiné oblasti.

Váš privátní koncový bod používá privátní IP adresu ve vaší virtuální síti.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

## <a name="establish-a-private-link-connection-to-key-vault-using-the-azure-portal"></a>Navázání připojení privátního propojení k Key Vault pomocí Azure Portal 

Nejdřív vytvořte virtuální síť podle kroků v části [vytvoření virtuální sítě pomocí Azure Portal](../../virtual-network/quick-create-portal.md)

Pak můžete buď vytvořit nový trezor klíčů, nebo vytvořit připojení typu privátní odkaz k existujícímu trezoru klíčů.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Vytvoření nového trezoru klíčů a navázání připojení k privátnímu propojení

Nový trezor klíčů můžete vytvořit pomocí [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md)nebo [Azure PowerShell](../general/quick-create-powershell.md).

Po nakonfigurování základních informací o trezoru klíčů vyberte kartu síť a postupujte podle těchto kroků:

1. Na kartě síť vyberte přepínač privátního koncového bodu.
1. Kliknutím na tlačítko + Přidat přidáte soukromý koncový bod.

    ![Snímek obrazovky zobrazující kartu "sítě" na stránce vytvoření trezoru klíčů.](../media/private-link-service-1.png)
 
1. V poli umístění v okně vytvořit privátní koncový bod vyberte oblast, ve které se nachází vaše virtuální síť. 
1. V poli název Vytvořte popisný název, který vám umožní identifikovat Tento soukromý koncový bod. 
1. Z rozevírací nabídky vyberte virtuální síť a podsíť, ze kterých chcete tento privátní koncový bod vytvořit. 
1. Ponechte možnost integrace s DNS privátní zóny beze změny.  
1. Vyberte OK.

    ![Snímek obrazovky zobrazující stránku vytvořit privátní koncový bod s vybraným nastavením](../media/private-link-service-8.png)
 
Teď budete moct zobrazit nakonfigurovaný soukromý koncový bod. Teď máte možnost Tento soukromý koncový bod odstranit a upravit. Vyberte tlačítko "revize + vytvořit" a vytvořte Trezor klíčů. Dokončení nasazení bude trvat 5-10 minut. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Navázání připojení k existujícímu trezoru klíčů pomocí privátního propojení

Pokud už máte Trezor klíčů, můžete vytvořit připojení k privátnímu propojení pomocí následujících kroků:

1. Přihlaste se k portálu Azure. 
1. Na panelu hledání zadejte "trezory klíčů".
1. V seznamu vyberte Trezor klíčů, do kterého chcete přidat privátní koncový bod.
1. V části nastavení vyberte kartu "sítě".
1. Vyberte kartu připojení privátního koncového bodu v horní části stránky.
1. V horní části stránky vyberte tlačítko + privátní koncový bod.

    ![Snímek obrazovky, na které se na stránce síť zobrazuje tlačítko + soukromý koncový bod](../media/private-link-service-3.png)
    ![Snímek obrazovky zobrazující kartu základy na stránce vytvoření privátního koncového bodu (Preview)](../media/private-link-service-4.png)

Pro libovolný prostředek Azure v tomto okně se můžete rozhodnout vytvořit privátní koncový bod. Pomocí rozevíracích nabídek můžete vybrat typ prostředku a vybrat prostředek ve vašem adresáři, nebo se můžete připojit k libovolnému prostředku Azure pomocí ID prostředku. Ponechte možnost integrace s DNS privátní zóny beze změny.  

![Snímek obrazovky, který ukazuje přidání privátního koncového bodu pomocí aktuálního okna. ](../media/private-link-service-3.png)
 ![ Snímek obrazovky, který ukazuje příklad stránky vytvořit privátní koncový bod (Preview).](../media/private-link-service-4.png)

Při vytváření privátního koncového bodu musí být připojení schváleno. Pokud je prostředek, pro který vytváříte privátní koncový bod, ve vašem adresáři, budete moci schválit žádost o připojení, pokud máte dostatečná oprávnění. Pokud se připojujete k prostředku Azure v jiném adresáři, musíte počkat, až vlastník tohoto prostředku schválí žádost o připojení.

Existují čtyři stavy zřizování:

| Služba poskytuje akci | Stav privátního koncového bodu příjemce služby | Description |
|--|--|--|
| Žádná | Čekající | Připojení je vytvořeno ručně a čeká na schválení vlastníkem prostředku privátního odkazu. |
| Schválení | Schválené | Připojení bylo automaticky nebo ručně schváleno a je připraveno k použití. |
| Odmítnout | Zamítnuto | Připojení bylo odmítnuto vlastníkem prostředku privátního odkazu. |
| Odebrat | Odpojeno | Připojení bylo odebráno vlastníkem prostředku privátního propojení, soukromý koncový bod bude informativní a měl by být odstraněn pro vyčištění. |

### <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Správa připojení privátního koncového bodu k Key Vault pomocí Azure Portal 

1. Přihlaste se k webu Azure Portal.
1. Na panelu hledání zadejte "trezory klíčů".
1. Vyberte Trezor klíčů, který chcete spravovat.
1. Vyberte kartu "sítě".
1. Pokud existují nějaká připojení, která čekají na vyřízení, zobrazí se ve stavu zřizování zpráva "čeká na vyřízení". 
1. Vyberte privátní koncový bod, který chcete schválit.
1. Vyberte tlačítko Schválit.
1. Pokud existují nějaká připojení privátního koncového bodu, která chcete zamítnout, ať už se jedná o nevyřízenou žádost nebo existující připojení, vyberte připojení a klikněte na tlačítko "zamítnout".

    ![Image](../media/private-link-service-7.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

## <a name="establish-a-private-link-connection-to-key-vault-using-cli-initial-setup"></a>Navázání připojení privátního propojení k Key Vault pomocí rozhraní příkazového řádku (počáteční nastavení)

```azurecli
az login                                                         # Login to Azure CLI
az account set --subscription {SUBSCRIPTION ID}                  # Select your Azure Subscription
az group create -n {RESOURCE GROUP} -l {REGION}                  # Create a new Resource Group
az provider register -n Microsoft.KeyVault                       # Register KeyVault as a provider
az keyvault create -n {VAULT NAME} -g {RG} -l {REGION}           # Create a Key Vault
az keyvault update -n {VAULT NAME} -g {RG} --default-action deny # Turn on Key Vault Firewall
az network vnet create -g {RG} -n {vNet NAME} -location {REGION} # Create a Virtual Network

    # Create a Subnet
az network vnet subnet create -g {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}

    # Disable Virtual Network Policies
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true

    # Create a Private DNS Zone
az network private-dns zone create --resource-group {RG} --name privatelink.vaultcore.azure.net

    # Link the Private DNS Zone to the Virtual Network
az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.vaultcore.azure.net --name {dnsZoneLinkName} --registration-enabled true

```

### <a name="add-private-dns-records"></a>Přidat záznamy Privátní DNS
```azurecli
# https://docs.microsoft.com/en-us/azure/dns/private-dns-getstarted-cli#create-an-additional-dns-record
az network private-dns zone list -g $rg_name
az network private-dns record-set a add-record -g $rg_name -z "privatelink.vaultcore.azure.net" -n $vault_name -a $kv_network_interface_private_ip
az network private-dns record-set list -g $rg_name -z "privatelink.vaultcore.azure.net"

# From home/public network, you wil get a public IP. If inside a vnet with private zone, nslookup will resolve to the private ip.
nslookup $vault_name.vault.azure.net
nslookup $vault_name.privatelink.vaultcore.azure.net
```

### <a name="create-a-private-endpoint-automatically-approve"></a>Vytvoření privátního koncového bodu (automaticky schvalovat) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION}
```

### <a name="create-a-private-endpoint-manually-request-approval"></a>Vytvoření privátního koncového bodu (žádost o schválení ručně) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```

### <a name="manage-private-link-connections"></a>Správa připojení privátních odkazů

```azurecli
# Show Connection Status
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}

# Approve a Private Link Connection Request
az keyvault private-endpoint-connection approve --approval-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}

# Deny a Private Link Connection Request
az keyvault private-endpoint-connection reject --rejection-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}

# Delete a Private Link Connection Request
az keyvault private-endpoint-connection delete --resource-group {RG} --vault-name {KEY VAULT NAME} --name {PRIVATE LINK CONNECTION NAME}
```

---

## <a name="validate-that-the-private-link-connection-works"></a>Ověření, že připojení privátního propojení funguje

Měli byste ověřit, že prostředky ve stejné podsíti prostředku privátního koncového bodu se připojují k vašemu trezoru klíčů přes privátní IP adresa a že mají správnou integraci privátních zón DNS.

Nejdřív vytvořte virtuální počítač podle kroků v [části Vytvoření virtuálního počítače s Windows v Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

Na kartě sítě:

1. Zadejte virtuální síť a podsíť. Můžete vytvořit novou virtuální síť, nebo vybrat některou z existujících. Pokud vyberete existující, ujistěte se, že oblast odpovídá.
1. Zadejte prostředek veřejné IP adresy.
1. V části Skupina zabezpečení sítě NIC vyberte None (žádné).
1. V části vyrovnávání zatížení vyberte ne.

Otevřete příkazový řádek a spusťte následující příkaz:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Pokud spustíte příkaz pro vyhledání NS k překladu IP adresy trezoru klíčů přes Veřejný koncový bod, zobrazí se výsledek podobný tomuto:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Pokud spustíte příkaz pro vyhledání NS k překladu IP adresy trezoru klíčů přes privátní koncový bod, zobrazí se výsledek podobný tomuto:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

* Zkontrolujte, zda je privátní koncový bod ve stavu schváleno. 
    1. Zkontrolovat a napravit to můžete na webu Azure Portal. Otevřete prostředek služby Key Vault a klikněte na možnost Sítě. 
    2. Pak vyberte kartu připojení privátního koncového bodu. 
    3. Ujistěte se, že stav připojení je schváleno a že je stav zřizování úspěšné. 
    4. Můžete také přejít na prostředek privátního koncového bodu a zkontrolovat stejné vlastnosti, a pak dvakrát zkontrolovat, jestli virtuální síť odpovídá používanému typu.

* Ověřte, že máte prostředek zóny Privátní DNS. 
    1. Musíte mít prostředek zóny Privátní DNS s přesným názvem: privatelink.vaultcore.azure.net. 
    2. Další informace o tom, jak tento postup nastavit, najdete na následujícím odkazu. [Privátní DNS zóny](../../dns/private-dns-privatednszone.md)
    
* Zkontrolujte, jestli zóna privátního DNS není propojená s virtuální sítí. To může být problém, pokud se vám stále vrátí veřejná IP adresa. 
    1. Pokud DNS privátní zóny není propojena s virtuální sítí, dotaz DNS pocházející z virtuální sítě vrátí veřejnou IP adresu trezoru klíčů. 
    2. Přejděte do prostředku zóny Privátní DNS v Azure Portal a klikněte na možnost odkazy virtuální sítě. 
    4. Musí být uvedena virtuální síť, která provede volání do trezoru klíčů. 
    5. Pokud tam není, přidejte ho. 
    6. Podrobný postup najdete v následujícím dokumentu [Virtual Network odkaz na privátní DNS Zone](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)

* Ověřte, že v zóně Privátní DNS chybí záznam A pro Trezor klíčů. 
    1. Přejděte na stránku Privátní DNS zóna. 
    2. Klikněte na Přehled a zkontrolujte, jestli existuje záznam A s jednoduchým názvem vašeho trezoru klíčů (tj. fabrikam). Nezadávejte žádnou příponu.
    3. Nezapomeňte zkontrolovat pravopis a vytvořte nebo upravte záznam A. Jako hodnotu TTL můžete použít 3600 (1 hodina). 
    4. Ujistěte se, že jste zadali správnou privátní IP adresu. 
    
* Ověřte, že záznam A má správnou IP adresu. 
    1. IP adresu můžete potvrdit otevřením prostředku privátního koncového bodu v Azure Portal.
    2. Na webu Azure Portal přejděte k prostředku Microsoft.Network/privateEndpoints (ne k prostředku služby Key Vault).
    3. Na stránce Přehled vyhledejte síťové rozhraní a klikněte na něj. 
    4. Odkaz zobrazí přehled prostředku síťové karty, který obsahuje privátní IP adresu vlastnosti. 
    5. Ověřte, zda se jedná o správnou IP adresu, která je zadána v záznamu A.

## <a name="limitations-and-design-considerations"></a>Omezení a faktory návrhu

> [!NOTE]
> Počet trezorů klíčů s povolenými soukromými koncovými body pro každé předplatné je upravitelný limit. Níže uvedený limit je nastaven jako výchozí. Pokud chcete pro vaši službu požádat o zvýšení limitu, pošlete prosím e-mail na adresu akv-privatelink@microsoft.com . Tyto žádosti schválíme na základě případu.

**Ceny**: informace o cenách najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link/).

**Omezení**: privátní koncový bod pro Azure Key Vault je k dispozici jenom ve veřejných oblastech Azure.

**Maximální počet privátních koncových bodů na Key Vault**: 64.

**Výchozí počet trezorů klíčů s privátními koncovými body na předplatné**: 400.

Další informace najdete v tématu [Služba privátního propojení Azure: omezení](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Další kroky

- Další informace o [privátním propojení Azure](../../private-link/private-link-service-overview.md)
- Další informace o [Azure Key Vault](overview.md)