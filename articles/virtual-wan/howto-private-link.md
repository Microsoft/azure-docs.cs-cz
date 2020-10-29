---
title: Sdílení služby privátního propojení napříč virtuální sítí WAN
titleSuffix: Azure Virtual WAN
description: Postup konfigurace privátního odkazu ve virtuální síti WAN
services: virtual-wan
author: erjosito
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: jomore
ms.custom: fasttrack-new
ms.openlocfilehash: cc8e7314c941035207ecf809a9d85ef46bd58379
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913751"
---
# <a name="use-private-link-in-virtual-wan"></a>Použití privátního odkazu ve virtuální síti WAN

[Privátní propojení Azure](../private-link/private-link-overview.md) je technologie, která umožňuje připojit nabídky platformy Azure jako služby pomocí připojení privátních IP adres vystavení [privátních koncových bodů](../private-link/private-endpoint-overview.md). Pomocí Azure Virtual WAN můžete v jedné z virtuálních sítí připojených k libovolnému virtuálnímu rozbočovači nasadit privátní koncový bod. To umožňuje připojení k libovolné jiné virtuální síti nebo větvi připojené ke stejné virtuální síti WAN.

## <a name="before-you-begin"></a>Než začnete

V krocích v tomto článku se předpokládá, že jste už nasadili virtuální síť WAN s jedním nebo více rozbočovači a zároveň aspoň dvě virtuální sítě připojené k virtuální síti WAN.

Pokud chcete vytvořit novou virtuální síť WAN a nové centrum, použijte postup v následujících článcích:

* [Vytvoření virtuální sítě WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [Vytvoření rozbočovače](virtual-wan-site-to-site-portal.md#hub)
* [Připojení virtuální sítě k rozbočovači](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>Vytvoření koncového bodu privátního propojení

Můžete vytvořit koncový bod privátního propojení pro celou řadu různých služeb. V tomto příkladu použijeme Azure SQL Database. Další informace o tom, jak vytvořit privátní koncový bod pro Azure SQL Database v rychlém startu, najdete v tématu [Vytvoření privátního koncového bodu pomocí Azure Portal](../private-link/create-private-endpoint-portal.md). Následující obrázek ukazuje síťovou konfiguraci Azure SQL Database:

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="vytvořit privátní odkaz" lightbox="./media/howto-private-link/create-private-link.png":::

Po vytvoření Azure SQL Database můžete ověřit IP adresu privátního koncového bodu procházející vašimi privátními koncovými body:

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="vytvořit privátní odkaz" lightbox="./media/howto-private-link/endpoints.png":::

Kliknutím na soukromý koncový bod, který jsme vytvořili, by se měla zobrazit jeho privátní IP adresa a plně kvalifikovaný název domény (FQDN). Upozorňujeme, že privátní koncový bod obsahuje IP adresu v rozsahu virtuální sítě, ve které je nasazený (10.1.3.0/24):

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="vytvořit privátní odkaz" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>Ověření připojení ze stejné virtuální sítě

V tomto příkladu ověříme připojení k Azure SQL Database z virtuálního počítače s Ubuntu, na kterém jsou nainstalované nástroje MS SQL. Prvním krokem je ověření, že překlad DNS funguje, a Azure SQL Database plně kvalifikovaný název domény se přeloží na soukromou IP adresu ve stejné virtuální síti, ve které je nasazený privátní koncový bod (10.1.3.0/24):

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Jak vidíte v předchozím výstupu, je plně kvalifikovaný název domény `wantest.database.windows.net` namapovaný na `wantest.privatelink.database.windows.net` , aby se privátní zóna DNS vytvořená v privátním koncovém bodě přeložila na privátní IP adresu `10.1.3.228` . Při hledání v privátní zóně DNS se potvrdí, že existuje záznam A pro soukromý koncový bod mapovaný na privátní IP adresu:

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="vytvořit privátní odkaz" lightbox="./media/howto-private-link/dns-zone.png":::

Po ověření správného překladu DNS se můžeme pokusit připojit k databázi:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

Jak vidíte, používáme speciální dotaz SQL, který nám poskytuje zdrojovou IP adresu, kterou SQL Server uvidí od klienta. V takovém případě server uvidí klienta s jeho privátní IP adresou ( `10.1.3.75` ), což znamená, že provoz přechází z virtuální sítě přímo do privátního koncového bodu.

Všimněte si, že pokud chcete, aby `username` `password` Příklady v této příručce fungovaly, musíte nastavit proměnné a tak, aby odpovídaly přihlašovacím údajům definovaným v Azure SQL Database.

## <a name="connect-from-a-different-vnet"></a><a name="vnet"></a>Připojení z jiné virtuální sítě

Když je teď jedna virtuální síť v Azure Virtual WAN připojená k privátnímu koncovému bodu, může k nim mít přístup i všechny ostatní virtuální sítě a větve připojené k virtuální síti WAN. K pojmenování dvou příkladů je potřeba poskytnout připojení prostřednictvím kteréhokoli modelu podporovaného službou Azure Virtual WAN, jako je například [scénář any-to-Any](scenario-any-to-any.md) nebo [virtuální síť Shared Services](scenario-shared-services-vnet.md).

Jakmile budete mít připojení mezi virtuální sítí nebo větví k virtuální síti, ve které je nasazený privátní koncový bod, musíte nakonfigurovat překlad DNS:

* Pokud se připojujete k privátnímu koncovému bodu z virtuální sítě, můžete použít stejnou privátní zónu, která byla vytvořena pomocí Azure SQL Database.
* Pokud se připojujete k privátnímu koncovému bodu z větve (VPN typu Point-to-site, VPN nebo ExpressRoute), budete muset použít místní překlad DNS.

V tomto příkladu se připojíme z jiné virtuální sítě, proto připojíme privátní zónu DNS k nové virtuální síti, aby její úlohy mohly přeložit Azure SQL Database plně kvalifikovaný název domény na privátní IP adresu. To se provádí prostřednictvím propojení privátní zóny DNS s novou virtuální sítí:

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="vytvořit privátní odkaz" lightbox="./media/howto-private-link/dns-link.png":::

Virtuální počítač v připojené virtuální síti by teď měl správně přeložit Azure SQL Database plně kvalifikovaný název domény na privátní IP adresu privátního propojení:

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Aby bylo možné dvakrát zkontrolovat, že tato virtuální síť (10.1.1.0/24) má připojení k původní virtuální síti, ve které byl nakonfigurován privátní koncový bod (10.1.3.0/24), můžete ověřit tabulku efektivní trasy na jakémkoli virtuálním počítači ve virtuální síti:

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="vytvořit privátní odkaz" lightbox="./media/howto-private-link/effective-routes.png":::

Jak vidíte, existuje trasa ukazující na virtuální síť 10.1.3.0/24, kterou vychází Virtual Network brány v Azure Virtual WAN. Nyní můžeme otestovat připojení k databázi:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

V tomto příkladu jsme viděli, jak vytvořit privátní koncový bod v jednom z virtuální sítě připojených k virtuální síti WAN zajišťuje připojení ke zbytku virtuální sítě a větví ve virtuální síti WAN.

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
