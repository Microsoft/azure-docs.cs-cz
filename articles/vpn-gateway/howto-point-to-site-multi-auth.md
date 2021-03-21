---
title: 'Připojení k virtuální síti pomocí P2S VPN & více typů ověřování: portál'
titleSuffix: Azure VPN Gateway
description: Připojte se k virtuální síti přes P2S s využitím několika typů ověřování v Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: d405f4b10808b7d39c0d116f2c9006c85532b4f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744876"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-multiple-authentication-types-azure-portal"></a>Konfigurace připojení VPN typu Point-to-site k virtuální síti s použitím více typů ověřování: Azure Portal

Tento článek vám pomůže bezpečně připojit jednotlivé klienty se systémem Windows, Linux nebo macOS k virtuální síti Azure. Připojení VPN typu Point-to-Site jsou užitečná, když se chcete ke své virtuální síti připojit ze vzdáleného umístění, například při práci z domova nebo z místa konání konference. Místo sítě VPN Site-to-Site můžete také použít P2S, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti. Připojení typu Point-to-Site nevyžadují zařízení VPN ani veřejnou IP adresu. P2S vytvoří připojení VPN prostřednictvím protokolu SSTP (Secure Socket Tunneling Protocol) nebo protokolu IKEv2. Další informace o síti VPN Point-to-Site najdete v článku věnovaném [síti VPN typu Point-to-Site](point-to-site-about.md).

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Připojení počítače k virtuální síti Azure – diagram připojení typu Point-to-site":::

Další informace o síti VPN typu Point-to-site najdete v tématu věnovaném [síti VPN typu Point-to-site](point-to-site-about.md). Pokud chcete tuto konfiguraci vytvořit pomocí Azure PowerShell, přečtěte si téma [Konfigurace sítě VPN typu Point-to-site pomocí Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="prerequisites"></a>Předpoklady

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

Více typů ověřování ve stejné bráně VPN je podporováno pouze s typem tunelu OpenVPN.

### <a name="example-values"></a><a name="example"></a>Příklady hodnot

Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku:

* **Název virtuální sítě:** VNet1
* **Adresní prostor:** 10.1.0.0/16<br>V tomto příkladu používáme pouze jeden adresní prostor. Pro svoji virtuální síť můžete mít více adresních prostorů.
* **Název podsítě:** FrontEnd
* **Rozsah adres podsítě:** 10.1.0.0/24
* **Předplatné:** Ujistěte se, že máte správné předplatné, pokud máte více než jedno.
* **Skupina prostředků:** TestRG1
* **Umístění:** Východní USA
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Název brány virtuální sítě:** VNet1GW
* **Typ brány:** S2S
* **Typ sítě VPN:** Založené na trasách
* **Název veřejné IP adresy:** VNet1GWpip
* **Typ připojení:** Point-to-site
* **Fond adres klienta:** 172.16.201.0/24<br>Klienti VPN, kteří se budou k virtuální síti připojovat pomocí tohoto připojení typu Point-to-Site, získají IP adresu ze zadaného fondu adres klienta.

## <a name="create-a-virtual-network"></a><a name="createvnet"></a>Vytvoření virtuální sítě

Než začnete, ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>Brána virtuální sítě

V tomto kroku vytvoříte bránu virtuální sítě pro svou virtuální síť. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány.

>[!NOTE]
>SKU brány úrovně Basic nepodporuje typ tunelu OpenVPN.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>Fond adres klienta

Fond adres klienta je rozsah privátních IP adres, který zadáte. Klienti připojující se přes síť VPN typu Point-to-Site dynamicky obdrží IP adresu z tohoto rozsahu. Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého se připojujete, ani s virtuální sítí, ke které se chcete připojit. Pokud nakonfigurujete více protokolů a SSTP je jedním z protokolů, nakonfigurované fondy adres se rovnoměrně rozdělí mezi nakonfigurované protokoly.

1. Jakmile je brána virtuální sítě vytvořená, přejděte do části **Nastavení** na stránce brány virtuální sítě. V **Nastavení** vyberte **Konfigurace Point-to-site**. Kliknutím na **Konfigurovat nyní** otevřete stránku konfigurace.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Snímek obrazovky se stránkou konfigurace typu Point-to-site." lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. Na stránce **Konfigurace typu Point-to-site** můžete nakonfigurovat celou řadu nastavení. Do pole **fond adres** přidejte rozsah privátních IP adres, který chcete použít. Klienti VPN dynamicky obdrží IP adresu z rozsahu, který zadáte. Minimální maska podsítě je 29 bitů pro aktivní/pasivní a 28 bitů pro konfiguraci aktivní/aktivní.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/address.jpg" alt-text="Snímek obrazovky s fondem adres.":::

1. Přejděte k další části Konfigurace ověřování a typů tunelového propojení.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>Typy ověřování a tunelu

V této části nakonfigurujete typ ověřování a typ tunelového propojení. Pokud nevidíte **Typ tunelového propojení** nebo **typ ověřování** na stránce **Konfigurace typu Point-to-site** , vaše brána používá základní SKU. Skladová položka Basic nepodporuje ověřování IKEv2 ani RADIUS. Pokud chcete použít tato nastavení, musíte bránu odstranit a znovu vytvořit pomocí jiné SKU brány.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/multiauth.jpg" alt-text="Snímek obrazovky s typem ověřování":::

### <a name="tunnel-type"></a><a name="tunneltype"></a>Typ tunelového propojení

Na stránce **Konfigurace typu Point-to-site** vyberte jako typ tunelu možnost **OpenVPN (SSL)** .

### <a name="authentication-type"></a><a name="authenticationtype"></a>Typ ověřování

Jako **typ ověřování** vyberte požadované typy. Dostupné možnosti:

* Certifikát Azure
* RADIUS
* Azure Active Directory

V závislosti na vybraných typech ověřování se zobrazí různá pole nastavení konfigurace, která budou muset být vyplněna. Zadejte požadované informace a v horní části stránky vyberte **Uložit** a uložte všechna nastavení konfigurace.

Další informace o typu ověřování najdete v tématu:

* [Certifikát Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#type)
* [RADIUS](point-to-site-how-to-radius-ps.md)
* [Azure Active Directory](openvpn-azure-ad-tenant.md)

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>Konfigurační balíček klienta VPN

Klienti VPN musí být nakonfigurováni s nastavením konfigurace klienta. Konfigurační balíček klienta VPN obsahuje soubory s nastavením pro konfiguraci klientů VPN, aby se mohli připojit k virtuální síti přes připojení P2S.

Pokyny ke generování a instalaci konfiguračních souborů klienta VPN najdete v článku, který se týká vaší konfigurace:

* [Vytvoření a instalace konfiguračních souborů klienta VPN pro konfigurace P2S nativního ověřování certifikátů Azure](point-to-site-vpn-client-configuration-azure-cert.md)
* [Ověřování Azure Active Directory: Nakonfigurujte klienta VPN pro připojení protokolu P2S OpenVPN](openvpn-azure-ad-client.md).

## <a name="point-to-site-faq"></a><a name="faq"></a>Nejčastější dotazy týkající se připojení Point-to-Site

Tato část obsahuje informace o nejčastějších dotazech, které se týkají konfigurací Point-to-site. Další informace o VPN Gateway najdete také v tématu [VPN Gateway – Nejčastější dotazy](vpn-gateway-vpn-faq.md) .

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Další kroky

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](../index.yml). Bližší informace o sítích a virtuálních počítačích najdete v tématu s [přehledem sítě virtuálních počítačů s Linuxem v Azure](../virtual-machines/network-overview.md).

Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích s připojeními Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
