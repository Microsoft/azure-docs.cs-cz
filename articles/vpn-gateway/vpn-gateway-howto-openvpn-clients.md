---
title: Jak nakonfigurovat OpenVPN klienty pro Azure VPN Gateway | Microsoft Docs
description: Postup konfigurace OpenVPN klientů pro Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: bdddf097265e7af688175688b6f3214413a90fdc
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2020
ms.locfileid: "84984093"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurace klientů OpenVPN pro Azure VPN Gateway

Tento článek vám pomůže nakonfigurovat **klienty &reg; protokolu OpenVPN** .

## <a name="before-you-begin"></a>Než začnete

Ověřte, že jste dokončili postup konfigurace OpenVPN pro vaši bránu VPN. Podrobnosti najdete v tématu [Konfigurace OpenVPN pro Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Další kroky

Pokud chcete, aby klienti VPN měli přístup k prostředkům v jiné virtuální síti, postupujte podle pokynů v článku [VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) a nastavte připojení typu VNet-to-VNet. Nezapomeňte povolit protokol BGP u bran a připojení, jinak přenos nebude pokračovat.

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**
