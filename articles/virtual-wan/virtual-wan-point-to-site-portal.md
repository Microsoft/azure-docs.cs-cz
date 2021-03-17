---
title: 'Kurz: použití služby Azure Virtual WAN k vytvoření připojení typu Point-to-site k Azure'
description: V tomto kurzu zjistíte, jak pomocí služby Azure Virtual WAN vytvořit připojení VPN typu point-to-site k Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: db7345906605ce117f0d57deb80f9d26ebf84179
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430513"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Kurz: vytvoření připojení VPN uživatele pomocí Azure Virtual WAN

V tomto kurzu se dozvíte, jak se pomocí služby Virtual WAN připojit ke svým prostředkům v Azure přes připojení VPN IPsec/IKE (IKEv2) nebo OpenVPN. Tento typ připojení vyžaduje, aby byl klient VPN nakonfigurovaný v klientském počítači. Další informace o službě Virtual WAN najdete v tématu [Přehled služby Virtual WAN](virtual-wan-about.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě WAN
> * Vytvoření konfigurace P2S
> * Vytvořit virtuální rozbočovač
> * Zvolit fondy adres klientů
> * Zadat servery DNS
> * Generovat konfigurační balíček profilu klienta VPN
> * Konfigurace klientů VPN
> * Zobrazení virtuální sítě WAN

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Vytvoření virtuální sítě WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Vytvoření konfigurace P2S

Konfigurace Point-to-Site (P2S) definuje parametry pro připojení vzdálených klientů.

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-virtual-hub-and-gateway"></a><a name="hub"></a>Vytvořit virtuální rozbočovač a bránu

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="choose-p2s-client-address-pools"></a><a name="chooseclientpools"></a> Zvolit fondy adres klienta P2S

[!INCLUDE [Choose pools](../../includes/virtual-wan-allocating-p2s-pools.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>Zadat server DNS

Toto nastavení můžete nakonfigurovat při vytváření centra nebo ho později změnit. Pokud ho chcete upravit, najděte virtuální rozbočovač. V části **uživatelské rozhraní VPN (Point-to-site)** vyberte **Konfigurovat** a zadejte IP adresy serveru DNS do TEXTOVÉHO pole **vlastní servery DNS** . Můžete zadat až 5 serverů DNS.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="vlastní DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="generate-vpn-client-profile-package"></a><a name="download"></a>Generovat balíček profilu klienta VPN

Vygenerujte a Stáhněte balíček profilu klienta VPN pro konfiguraci klientů VPN.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>Konfigurace klientů VPN

Pomocí staženého balíčku profilu nakonfigurujte klienty VPN pro vzdálený přístup. Postup pro každý operační systém se liší. Postupujte podle pokynů, které se vztahují k vašemu systému.
Po dokončení konfigurace klienta se můžete připojit.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Zobrazení virtuální sítě WAN

1. Přejděte na virtuální síť WAN.
1. Na stránce **Přehled** představuje každý bod na mapě rozbočovač.
1. V části **centra a připojení** můžete zobrazit stav centra, lokalitu, oblast, stav připojení VPN a v a v bajtech.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Vyčištění prostředků

Když už nepotřebujete prostředky, které jste vytvořili, odstraňte je. Některé virtuální prostředky sítě WAN je potřeba z důvodu závislostí odstranit v určitém pořadí. Dokončení odstranění může trvat přibližně 30 minut.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti WAN najdete tady:

> [!div class="nextstepaction"]
> * [Nejčastější dotazy ke službě Virtual WAN](virtual-wan-faq.md)
