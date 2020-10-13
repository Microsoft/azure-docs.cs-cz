---
title: 'VPN Gateway: Upravte nastavení IP adresy brány: Azure Portal'
description: Tento článek vás provede změnou předpon IP adres pro bránu místní sítě pomocí Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/16/2020
ms.author: cherylmc
ms.openlocfilehash: af3513c4a4f3b3187e85c65de51ad2e6e2d7279c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983180"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Úprava nastavení místní síťové brány pomocí webu Azure Portal

Někdy se nastavení pro bránu místní sítě AddressPrefix nebo GatewayIPAddress změní. V tomto článku se dozvíte, jak upravit nastavení místní síťové brány. Tato nastavení můžete také upravit pomocí jiné metody výběrem jiné možnosti z následujícího seznamu:

Než připojení odstraníte, možná budete chtít stáhnout konfiguraci pro připojení zařízení, aby se získal definovaný PSK. Tímto způsobem ho nemusíte předefinovat na druhé straně.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Konfigurace brány místní sítě

Níže uvedený snímek obrazovky ukazuje stránku **Konfigurace** prostředku brány místní sítě pomocí koncového bodu veřejné IP adresy:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/ip-address.png" alt-text="Konfigurace brány místní sítě – IP adresa":::

Jedná se o stejnou konfigurační stránku s koncovým bodem plně kvalifikovaného názvu domény:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/fqdn.png" alt-text="Konfigurace brány místní sítě – IP adresa":::

## <a name="modify-the-gateway-ip-address"></a><a name="ip"></a>Úprava IP adresy brány

Pokud zařízení VPN, ke kterému se chcete připojit, změnilo svou veřejnou IP adresu, musíte upravit bránu místní sítě, aby odrážela tuto změnu.

1. V prostředku brány místní sítě v části **Nastavení** klikněte na **Konfigurace**.
2. V poli **IP adresa** upravte IP adresu.
3. Kliknutím na **Uložit** nastavení uložte.

## <a name="modify-the-gateway-fqdn"></a><a name="fqdn"></a>Úprava plně kvalifikovaného názvu domény brány

Pokud se zařízení VPN, ke kterému se chcete připojit, změnilo jeho plně kvalifikovaný název domény (plně kvalifikovaný název domény), musíte upravit bránu místní sítě tak, aby odrážela tuto změnu.

1. V prostředku brány místní sítě v části **Nastavení** klikněte na **Konfigurace**.
2. V poli **plně kvalifikovaný název** domény upravte název domény.
3. Kliknutím na **Uložit** nastavení uložte.

> ! ZNAČTE Nemůžete změnit bránu místní sítě mezi koncovým bodem a koncovým bodem IP adresy. Musíte odstranit všechna připojení přidružená k této bráně místní sítě, vytvořit novou s novým koncovým bodem (IP adresa nebo plně kvalifikovaný název domény) a pak znovu vytvořit připojení.

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Upravit předpony IP adres

### <a name="to-add-additional-address-prefixes"></a>Přidání dalších předpon adres:

1. V prostředku brány místní sítě v části **Nastavení** klikněte na **Konfigurace**.
2. Přidejte adresní prostor IP adres do pole *Přidat další rozsah adres* .
3. Uložte nastavení kliknutím na **Uložit** .

### <a name="to-remove-address-prefixes"></a>Odebrání předpon adres:

1. V prostředku brány místní sítě v části **Nastavení** klikněte na **Konfigurace**.
2. Klikněte na **'... '** na řádku obsahujícím předponu, kterou chcete odebrat.
3. Klikněte na **Odebrat**.
4. Uložte nastavení kliknutím na **Uložit** .

## <a name="modify-bgp-settings"></a><a name="bgp"></a>Úprava nastavení protokolu BGP

### <a name="to-add-or-update-bgp-settings"></a>Přidání nebo aktualizace nastavení protokolu BGP:

1. V prostředku brány místní sítě v části **Nastavení** klikněte na **Konfigurace**.
2. Pokud chcete zobrazit nebo aktualizovat konfigurace protokolu BGP pro tuto bránu místní sítě, vyberte **Konfigurovat nastavení protokolu BGP** .
3. Přidat nebo aktualizovat číslo autonomního systému nebo IP adresy partnerského uzlu protokolu BGP v odpovídajících polích
4. Uložte nastavení kliknutím na **Uložit** .

### <a name="to-remove-bgp-settings"></a>Odebrání nastavení protokolu BGP:

1. V prostředku brány místní sítě v části **Nastavení** klikněte na **Konfigurace**.
2. Pokud chcete odebrat existující ASN protokolu BGP a IP adresu partnerského zařízení BGP, zrušte výběr **Konfigurace nastavení protokolu BGP** .
3. Uložte nastavení kliknutím na **Uložit** .

## <a name="next-steps"></a>Další kroky

Můžete ověřit připojení brány. Viz [ověření připojení brány](vpn-gateway-verify-connection-resource-manager.md).
