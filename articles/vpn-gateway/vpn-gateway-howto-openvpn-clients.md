---
title: Jak nakonfigurovat klienty OpenVPN pro bránu Azure VPN| Dokumenty společnosti Microsoft
description: Postup konfigurace klientů OpenVPN pro bránu Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 09ff3ccebad0baa4148e68995254c818a29d7bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066110"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurace klientů OpenVPN pro azure bránu VPN Gateway

Tento článek vám pomůže nakonfigurovat klienty **protokolu OpenVPN. &reg; **

## <a name="before-you-begin"></a>Než začnete

Ověřte, zda jste dokončili kroky konfigurace OpenVPN pro bránu VPN. Podrobnosti najdete [v tématu Konfigurace OpenVPN pro Bránu Azure VPN .](vpn-gateway-howto-openvpn.md)

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Další kroky

Pokud chcete, aby klienti VPN měli přístup k prostředkům v jiné virtuální síti, postupujte podle pokynů v článku [virtuální sítě k virtuální síti](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) a nastavte připojení virtuální sítě k virtuální síti. Ujistěte se, že povolit BGP na brány a připojení, jinak provoz nebude toku.

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**
