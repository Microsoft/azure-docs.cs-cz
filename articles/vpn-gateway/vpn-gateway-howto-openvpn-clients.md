---
title: Jak nakonfigurovat OpenVPN klienty pro Azure VPN Gateway | Microsoft Docs
description: Naučte se konfigurovat klienty protokolu OpenVPN pro Azure VPN Gateway, včetně klientů se systémy Windows, Mac a Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 136dcb0b2d1740aa5cadbd716b4a8386ad5cf486
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926208"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurace klientů OpenVPN pro Azure VPN Gateway

Tento článek vám pomůže nakonfigurovat **klienty &reg; protokolu OpenVPN** .

## <a name="before-you-begin"></a>Než začnete

Ověřte, že jste dokončili postup konfigurace OpenVPN pro vaši bránu VPN. Podrobnosti najdete v tématu [Konfigurace OpenVPN pro Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Další kroky

Pokud chcete, aby klienti VPN měli přístup k prostředkům v jiné virtuální síti, postupujte podle pokynů v článku [VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) a nastavte připojení typu VNet-to-VNet. Nezapomeňte povolit protokol BGP u bran a připojení, jinak přenos nebude pokračovat.

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**
