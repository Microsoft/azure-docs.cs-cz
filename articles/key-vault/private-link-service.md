---
title: Integrace se službou Azure Private Link Service
description: Zjistěte, jak integrovat Azure Key Vault se službou Azure Private Link Service
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 03/08/2020
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: c24be648e4ca1433c7c2af3d659bf4520a7a188c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79457283"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>Integrace trezoru klíčů s privátním propojením Azure

Služba Azure Private Link Service umožňuje přístup ke službám Azure Services (například Azure Key Vault, Azure Storage a Azure Cosmos DB) a službám hostovaným zákazníkům/partnerům Azure přes privátní koncový bod ve vaší virtuální síti.

Azure Private Endpoint je síťové rozhraní, které vás soukromě a bezpečně propojuje se službou využívající Azure Private Link. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě, efektivně uvedení služby do virtuální sítě. Veškerý provoz na službu lze směrovat přes soukromý koncový bod, takže nejsou potřeba žádné brány, zařízení NAT, připojení ExpressRoute nebo VPN nebo veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, což vám nejvyšší úroveň rozlišovací schopnost v řízení přístupu.

Další informace najdete v tématu [Co je privátní propojení Azure?](../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Požadavky

Chcete-li integrovat trezor klíčů s Privátním linkem Azure, budete potřebovat následující:

- Trezor klíčů.
- Virtuální síť Azure.
- Podsíť ve virtuální síti.
- Oprávnění vlastníka nebo přispěvatele pro trezor klíčů i virtuální síť.

Váš soukromý koncový bod a virtuální síť musí být ve stejné oblasti. Když vyberete oblast pro soukromý koncový bod pomocí portálu, bude automaticky filtrovat pouze virtuální sítě, které jsou v této oblasti. Trezor klíčů může být v jiné oblasti.

Váš soukromý koncový bod používá privátní IP adresu ve virtuální síti.

## <a name="establish-a-private-link-connection-to-key-vault-using-the-azure-portal"></a>Vytvoření privátního připojení k trezoru klíčů pomocí portálu Azure 

Nejprve vytvořte virtuální síť podle kroků v [části Vytvoření virtuální sítě pomocí portálu Azure Portal](../virtual-network/quick-create-portal.md)

Potom můžete buď vytvořit nový trezor klíčů, nebo vytvořit privátní připojení k existujícímu trezoru klíčů.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Vytvoření nového trezoru klíčů a vytvoření privátního propojení

Nový trezor klíčů můžete vytvořit podle kroků v [nastavení a načíst tajný klíč z Azure Key Vault pomocí portálu Azure](quick-create-portal.md)

Po konfiguraci základů trezoru klíčů vyberte kartu Síť a postupujte takto:

1. Na kartě Síť vyberte přepínací tlačítko Soukromý koncový bod.
1. Kliknutím na tlačítko +Přidat přidáte a přidejte soukromý koncový bod.

    ![Image](./media/private-link-service-1.png)
 
1. V poli Umístění v okně Vytvořit privátní koncový bod vyberte oblast, ve které se virtuální síť nachází. 
1. V poli Název vytvořte popisný název, který vám umožní identifikovat tento soukromý koncový bod. 
1. V rozevírací nabídce vyberte virtuální síť a podsíť, ve které má být tento soukromý koncový bod vytvořen. 
1. Ponechte možnost "integrovat se soukromou zónou DNS" beze změny.  
1. Vyberte možnost "Ok".

    ![Image](./media/private-link-service-2.png)
 
Nyní budete moci zobrazit nakonfigurovaný soukromý koncový bod. Nyní máte možnost odstranit a upravit tento soukromý koncový bod. Vyberte tlačítko "Revize + vytvoření" a vytvořte trezor klíčů. Dokončení nasazení bude trvat 5-10 minut. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Navázání privátního připojení k existujícímu trezoru klíčů

Pokud již trezor klíčů máte, můžete vytvořit privátní připojení propojení následujícím postupem:

1. Přihlaste se k portálu Azure. 
1. Na vyhledávacím panelu zadejte "trezory klíčů"
1. Ze seznamu, do kterého chcete přidat soukromý koncový bod, vyberte trezor klíčů.
1. V části Nastavení vyberte kartu "Vytváření sítí"
1. V horní části stránky vyberte kartu Soukromá připojení koncových bodů.
1. V horní části stránky vyberte tlačítko "+ soukromý koncový bod".

    ![Obrázek](./media/private-link-service-3.png) ![](./media/private-link-service-4.png)

Můžete vytvořit privátní koncový bod pro všechny prostředky Azure v použití tohoto okna. Pomocí rozevíracích nabídek můžete buď vybrat typ prostředku a vybrat prostředek ve vašem adresáři, nebo se můžete připojit k libovolnému prostředku Azure pomocí ID prostředku. Ponechte možnost "integrovat se soukromou zónou DNS" beze změny.  

![Obrázek](./media/private-link-service-3.png)
![](./media/private-link-service-4.png)

## <a name="establish-a-private-link-connection-to-key-vault-using-cli"></a>Vytvoření privátního připojení k trezoru klíčů pomocí funkce cli

### <a name="login-to-azure-cli"></a>Přihlášení do azure cli
```console
az login 
```
### <a name="select-your-azure-subscription"></a>Vyberte si předplatné Azure 
```console
az account set --subscription {AZURE SUBSCRIPTION ID}
```
### <a name="create-a-new-resource-group"></a>Vytvoření nové skupiny prostředků 
```console
az group create -n {RG} -l {AZURE REGION}
```
### <a name="register-microsoftkeyvault-as-a-provider"></a>Registrace microsoft.KeyVault jako poskytovatele 
```console
az provider register -n Microsoft.KeyVault
```
### <a name="create-a-new-key-vault"></a>Vytvoření nového trezoru klíčů
```console
az keyvault create --name {KEY VAULT NAME} --resource-group {RG} --location {AZURE REGION}
```
### <a name="turn-on-key-vault-firewall"></a>Zapnutí brány firewall trezoru klíčů
```console
az keyvault update --name {KEY VAULT NAME} --resource-group {RG} --location {AZURE REGION} --default-action deny
```
### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
```console
az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
```
### <a name="add-a-subnet"></a>Přidání podsítě
```console
az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
```
### <a name="disable-virtual-network-policies"></a>Zakázání zásad virtuální sítě 
```console
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
```
### <a name="add-a-private-dns-zone"></a>Přidání soukromé zóny DNS 
```console
az network private-dns zone create --resource-group {RG} --name privatelink.vaultcore.azure.net
```
### <a name="link-private-dns-zone-to-virtual-network"></a>Propojení privátní zóny DNS s virtuální sítí 
```console
az network private-dns link vnet create --resoruce-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.vaultcore.azure.net --name {dnsZoneLinkName} --registration-enabled true
```
### <a name="create-a-private-endpoint-automatically-approve"></a>Vytvoření soukromého koncového bodu (automaticky schválit) 
```console
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION}
```
### <a name="create-a-private-endpoint-manually-request-approval"></a>Vytvoření soukromého koncového bodu (ruční schválení žádosti) 
```console
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```
### <a name="show-connection-status"></a>Zobrazit stav připojení 
```console
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
```
## <a name="manage-private-link-connection"></a>Správa připojení k soukromému propojení

Při vytváření soukromého koncového bodu musí být připojení schváleno. Pokud je prostředek, pro který vytváříte soukromý koncový bod, ve vašem adresáři, budete moci schválit žádost o připojení za předpokladu, že máte dostatečná oprávnění. Pokud se připojujete k prostředku Azure v jiném adresáři, musíte počkat na vlastníka tohoto prostředku schválit váš požadavek na připojení.

Existují čtyři stavy zřizování:

| Služba poskytuje akci | Stav koncového bodu příjemce služby | Popis |
|--|--|--|
| Žádný | Čekající na vyřízení | Připojení je vytvořeno ručně a čeká na schválení od vlastníka prostředku private link. |
| Schválení | Schválené | Připojení bylo automaticky nebo ručně schváleno a je připraveno k použití. |
| Odmítnout | Rejected | Připojení bylo odmítnuto vlastníkem prostředku soukromého propojení. |
| Odebrat | Odpojen | Připojení bylo odebráno vlastníkem prostředku soukromého propojení, soukromý koncový bod se stane informativní a měl by být odstraněn pro vyčištění. |
 
###  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Jak spravovat privátní koncovépřipojení k trezoru klíčů pomocí portálu Azure 

1. Přihlaste se k webu Azure Portal.
1. Na vyhledávacím panelu zadejte "trezory klíčů"
1. Vyberte trezor klíčů, který chcete spravovat.
1. Vyberte kartu "Networking".
1. Pokud existují všechna připojení, která čekají na vyřízení, uvidíte připojení uvedené s "Čeká na vyřízení" ve stavu zřizování. 
1. Vyberte soukromý koncový bod, který chcete schválit.
1. Vyberte tlačítko schválit.
1. Pokud existují soukromá připojení koncového bodu, která chcete odmítnout, ať už se jedná o čekající požadavek nebo existující připojení, vyberte připojení a klepněte na tlačítko Odmítnout.

    ![Image](./media/private-link-service-7.png)

##  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-azure-cli"></a>Jak spravovat privátní koncového bodu připojení k trezoru klíčů pomocí Azure CLI

### <a name="approve-a-private-link-connection-request"></a>Schválení žádosti o připojení k soukromému propojení
```console
az keyvault private-endpoint-connection approve --approval-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}
```

### <a name="deny-a-private-link-connection-request"></a>Odepřít žádost o připojení k soukromému propojení
```console
az keyvault private-endpoint-connection reject --rejection-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}
```

### <a name="delete-a-private-link-connection-request"></a>Odstranění požadavku na připojení k soukromému propojení
```console
az keyvault private-endpoint-connection delete --resource-group {RG} --vault-name {KEY VAULT NAME} --name {PRIVATE LINK CONNECTION NAME}
```

## <a name="validate-that-the-private-link-connection-works"></a>Ověření, zda funguje privátní připojení

Měli byste ověřit, že prostředky v rámci stejné podsítě prostředku soukromého koncového bodu se připojují k trezoru klíčů přes privátní IP adresu a že mají správnou privátní integraci zóny DNS.

Nejdřív vytvořte virtuální počítač podle kroků v [části Vytvoření virtuálního počítače s Windows na webu Azure Portal.](../virtual-machines/windows/quick-create-portal.md)

Na kartě "Networking":

1. Zadejte virtuální síť a podsíť. Můžete vytvořit novou virtuální síť nebo vybrat existující. Pokud vyberete existující, ujistěte se, že se oblast shoduje.
1. Zadejte veřejný prostředek IP.
1. Ve skupině zabezpečení sítě NIC vyberte možnost Žádný.
1. V části "Vyrovnávání zatížení" vyberte "Ne".

Otevřete příkazový řádek a spusťte následující příkaz:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Pokud spustíte příkaz vyhledávání ns k vyřešení IP adresy trezoru klíčů přes veřejný koncový bod, zobrazí se výsledek, který vypadá takto:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Pokud spustíte příkaz vyhledávání ns k vyřešení IP adresy trezoru klíčů přes soukromý koncový bod, zobrazí se výsledek, který vypadá takto:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="limitations-and-design-considerations"></a>Omezení a aspekty návrhu

**Ceny**: Informace o cenách najdete v článku [Ceny Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

**Omezení:** Privátní koncový bod pro Azure Key Vault je k dispozici jenom ve veřejných oblastech Azure.

**Maximální počet soukromých koncových bodů na trezor klíčů**: 64.

**Maximální počet trezorů klíčů s privátními koncovými body na předplatné:** 64.

Další informace najdete v tématu [Azure Private Link service: Omezení](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Další kroky

- Další informace o [privátním propojení Azure](../private-link/private-link-service-overview.md)
- Další informace o [Azure Key Vault](key-vault-overview.md)
