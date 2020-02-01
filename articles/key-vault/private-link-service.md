---
title: Integrace se službou Azure Private Link
description: Naučte se integrovat Azure Key Vault se službou Azure Private Link.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: e058e643f4c37336f09b43c41cd09aa361a23d15
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907072"
---
# <a name="integrate-key-vault-with-azure-private-link-preview"></a>Integrace Key Vault s privátním odkazem Azure (Preview)

Služba privátního propojení Azure vám umožňuje přístup ke službám Azure (například Azure Key Vault, Azure Storage a Azure Cosmos DB) a hostovaným zákaznickým a partnerským službám Azure prostřednictvím privátního koncového bodu ve vaší virtuální síti.

Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služeb se dá směrovat přes privátní koncový bod, takže nejsou potřeba žádné brány, zařízení pro překlad adres (NAT), připojení VPN nebo ExpressRoute ani veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, která poskytuje nejvyšší úroveň členitosti v řízení přístupu.

Další informace najdete v tématu [co je to Azure Private Link (Preview)?](../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Požadavky

Pokud chcete integrovat Trezor klíčů s privátním odkazem na Azure (Preview), budete potřebovat následující:

- Trezor klíčů.
- Virtuální síť Azure.
- Podsíť ve virtuální síti.
- Oprávnění vlastníka nebo přispěvatele pro Trezor klíčů i virtuální síť.

Váš privátní koncový bod a virtuální síť musí být ve stejné oblasti. Když vyberete oblast pro soukromý koncový bod pomocí portálu, automaticky se vyfiltrují jenom virtuální sítě, které jsou v této oblasti. Váš Trezor klíčů může být v jiné oblasti.

Váš privátní koncový bod používá privátní IP adresu ve vaší virtuální síti.

## <a name="establish-a-private-link-connection-to-key-vault"></a>Navázání připojení privátního propojení k trezoru klíčů

Nejdřív vytvořte virtuální síť podle kroků v části [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md)

Pak můžete buď vytvořit nový trezor klíčů, nebo vytvořit připojení typu privátní odkaz k existujícímu trezoru klíčů.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Vytvoření nového trezoru klíčů a navázání připojení k privátnímu propojení

Nový trezor klíčů můžete vytvořit podle kroků v části [nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure Portal](quick-create-portal.md)

Po nakonfigurování základních informací o trezoru klíčů vyberte kartu síť a postupujte podle těchto kroků:

1. Na kartě síť vyberte přepínač privátního koncového bodu (Preview).
1. Kliknutím na tlačítko + Přidat přidáte soukromý koncový bod.

    ![Obrázek](./media/private-link-service-1.png)
 
1. V poli umístění v okně vytvořit privátní koncový bod vyberte oblast, ve které se nachází vaše virtuální síť. 
1. V poli název Vytvořte popisný název, který vám umožní identifikovat Tento soukromý koncový bod. 
1. Z rozevírací nabídky vyberte virtuální síť a podsíť, ze kterých chcete tento privátní koncový bod vytvořit. 
1. Ponechte možnost integrace s DNS privátní zóny beze změny.  
1. Vyberte OK.

    ![Obrázek](./media/private-link-service-2.png)
 
Teď budete moct zobrazit nakonfigurovaný soukromý koncový bod. Teď máte možnost Tento soukromý koncový bod odstranit a upravit. Vyberte tlačítko "revize + vytvořit" a vytvořte Trezor klíčů. Dokončení nasazení bude trvat 5-10 minut. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Navázání připojení k existujícímu trezoru klíčů pomocí privátního propojení

Pokud už máte Trezor klíčů, můžete vytvořit připojení k privátnímu propojení pomocí následujících kroků:

1. Přihlaste se k portálu Azure. 
1. Na panelu hledání zadejte "trezory klíčů".
1. V seznamu vyberte Trezor klíčů, do kterého chcete přidat privátní koncový bod.
1. V části nastavení vyberte kartu "sítě".
1. V horní části stránky vyberte kartu připojení privátního koncového bodu (Preview).
1. V horní části stránky vyberte tlačítko + privátní koncový bod.

    Obrázek ![](./media/private-link-service-3.png) ![obrázku](./media/private-link-service-4.png)

Pro libovolný prostředek Azure v tomto okně se můžete rozhodnout vytvořit privátní koncový bod. Pomocí rozevíracích nabídek můžete vybrat typ prostředku a vybrat prostředek ve vašem adresáři, nebo se můžete připojit k libovolnému prostředku Azure pomocí ID prostředku. Ponechte možnost integrace s DNS privátní zóny beze změny.  

Obrázek ![](./media/private-link-service-3.png)
![obrázku](./media/private-link-service-4.png)

## <a name="manage-private-link-connection"></a>Spravovat připojení privátního propojení

Při vytváření privátního koncového bodu musí být připojení schváleno. Pokud je prostředek, pro který vytváříte privátní koncový bod, ve vašem adresáři, budete moci schválit žádost o připojení, pokud máte dostatečná oprávnění. Pokud se připojujete k prostředku Azure v jiném adresáři, musíte počkat, až vlastník tohoto prostředku schválí žádost o připojení.

Existují čtyři stavy zřizování:

| Služba poskytuje akci | Stav privátního koncového bodu příjemce služby | Popis |
|--|--|--|
| Žádné | Čekající na vyřízení | Připojení je vytvořeno ručně a čeká na schválení vlastníkem prostředku privátního odkazu. |
| Schválení | Schválené | Připojení bylo automaticky nebo ručně schváleno a je připraveno k použití. |
| Odmítnout | Odmítnutí | Připojení bylo odmítnuto vlastníkem prostředku privátního odkazu. |
| Odebrat | Odpojení | Připojení bylo odebráno vlastníkem prostředku privátního propojení, soukromý koncový bod bude informativní a měl by být odstraněn pro vyčištění. |
 
###  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault"></a>Jak spravovat připojení privátního koncového bodu k trezoru klíčů

1. Přihlaste se k webu Azure Portal.
1. Na panelu hledání zadejte "trezory klíčů".
1. Vyberte Trezor klíčů, který chcete spravovat.
1. Vyberte kartu "sítě".
1. Pokud existují nějaká připojení, která čekají na vyřízení, zobrazí se ve stavu zřizování zpráva "čeká na vyřízení". 
1. Vyberte privátní koncový bod, který chcete schválit.
1. Vyberte tlačítko Schválit.
1. Pokud existují nějaká připojení privátního koncového bodu, která chcete zamítnout, ať už se jedná o nevyřízenou žádost nebo existující připojení, vyberte připojení a klikněte na tlačítko "zamítnout".

    ![Obrázek](./media/private-link-service-7.png)

## <a name="validate-that-the-private-link-connection-works"></a>Ověření, že připojení privátního propojení funguje

Měli byste ověřit, že prostředky ve stejné podsíti prostředku privátního koncového bodu se připojují k vašemu trezoru klíčů přes privátní IP adresa a že mají správnou integraci privátních zón DNS.

Nejdřív vytvořte virtuální počítač podle kroků v [části Vytvoření virtuálního počítače s Windows v Azure Portal](../virtual-machines/windows/quick-create-portal.md)

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

## <a name="limitations-and-design-considerations"></a>Omezení a faktory návrhu

**Ceny**: informace o cenách najdete v tématu [ceny pro privátní propojení Azure (Preview)](https://azure.microsoft.com/pricing/details/private-link/).

**Omezení**: privátní koncový bod pro Azure Key Vault je ve verzi Public Preview. Tato funkce je dostupná ve všech veřejných oblastech Azure.

**Maximální počet privátních koncových bodů na Key Vault**: 64.

**Maximální počet trezorů klíčů s privátními koncovými body na předplatné**: 64.

Další informace najdete v tématu [Služba privátního propojení Azure: omezení](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Další kroky

- Další informace o [privátním propojení Azure (Preview)](../private-link/private-link-service-overview.md)
- Další informace o [Azure Key Vault](key-vault-overview.md)
