---
title: 'VPN Gateway: Upravte nastavení IP adresy brány: Azure Portal'
description: Tento článek vás provede změnou předpon IP adres pro bránu místní sítě pomocí Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/16/2020
ms.author: cherylmc
ms.openlocfilehash: 4ff4f1238764d7bdab6e74d29254a6388ea76d78
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143119"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Úprava nastavení místní síťové brány pomocí webu Azure Portal

Někdy se nastavení pro bránu místní sítě AddressPrefix nebo GatewayIPAddress změní. V tomto článku se dozvíte, jak upravit nastavení místní síťové brány. Tato nastavení můžete také upravit pomocí jiné metody výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Konfigurace brány místní sítě

Níže uvedený snímek obrazovky ukazuje stránku **Konfigurace** prostředku brány místní sítě pomocí koncového bodu veřejné IP adresy:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/settings.png" alt-text="Nastavení IP adresy" lightbox="./media/vpn-gateway-modify-local-network-gateway-portal/settings-expand.png":::

Toto je konfigurační stránka s koncovým bodem plně kvalifikovaného názvu domény:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/name.png" alt-text="Nastavení IP adresy":::

## <a name="to-modify-the-gateway-ip-address-or-fqdn"></a><a name="ip"></a>Úprava IP adresy brány nebo plně kvalifikovaného názvu domény

> [!NOTE]
> Nemůžete změnit bránu místní sítě mezi koncovým bodem a koncovým bodem IP adresy. Musíte odstranit všechna připojení přidružená k této bráně místní sítě, vytvořit novou s novým koncovým bodem (IP adresa nebo plně kvalifikovaný název domény) a pak znovu vytvořit připojení.
>

Pokud se zařízení VPN, ke kterému se chcete připojit, změnila jeho veřejná IP adresa, upravte bránu místní sítě pomocí následujících kroků:

1. V prostředku brány místní sítě v části **Nastavení** vyberte **Konfigurace**.
2. V poli **IP adresa** upravte IP adresu.
3. Vyberte **Uložit** a nastavení se uloží.

Pokud se zařízení VPN, ke kterému se chcete připojit, změnilo jeho plně kvalifikovaný název domény (plně kvalifikovaný název domény), upravte bránu místní sítě pomocí následujících kroků:

1. V prostředku brány místní sítě v části **Nastavení** vyberte **Konfigurace**.
2. V poli **plně kvalifikovaný název** domény upravte název domény.
3. Vyberte **Uložit** a nastavení se uloží.

## <a name="to-modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Úprava předpon IP adres

Přidání dalších předpon adres:

1. V prostředku brány místní sítě v části **Nastavení** vyberte **Konfigurace**.
2. Přidejte adresní prostor IP adres do pole *Přidat další rozsah adres* .
3. Vyberte **Uložit** a uložte nastavení.

Odebrání předpon adres:

1. V prostředku brány místní sítě v části **Nastavení** vyberte **Konfigurace**.
2. Na řádku obsahujícím předponu, kterou chcete odebrat, vyberte **...**
3. Vyberte **Odebrat**.
4. Vyberte **Uložit** a uložte nastavení.

## <a name="to-modify-bgp-settings"></a><a name="bgp"></a>Úprava nastavení protokolu BGP

Přidání nebo aktualizace nastavení protokolu BGP:

1. V prostředku brány místní sítě v části **Nastavení** vyberte **Konfigurace**.
2. Pokud chcete zobrazit nebo aktualizovat konfigurace protokolu BGP pro tuto bránu místní sítě, vyberte **Konfigurovat nastavení protokolu BGP** .
3. Přidat nebo aktualizovat číslo autonomního systému nebo IP adresy partnerského uzlu protokolu BGP v odpovídajících polích
4. Vyberte **Uložit** a uložte nastavení.

Odebrání nastavení protokolu BGP:

1. V prostředku brány místní sítě v části **Nastavení** vyberte **Konfigurace**.
2. Zrušte zaškrtnutí políčka **Konfigurovat nastavení protokolu BGP** pro odebrání existující ASN protokolu BGP a IP adresy partnerského uzlu protokolu BGP.
3. Vyberte **Uložit** a uložte nastavení.

## <a name="next-steps"></a>Další kroky

Můžete ověřit připojení brány. Viz [ověření připojení brány](vpn-gateway-verify-connection-resource-manager.md).
