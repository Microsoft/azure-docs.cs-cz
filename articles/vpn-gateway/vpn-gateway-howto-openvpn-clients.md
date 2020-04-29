---
title: Jak nakonfigurovat OpenVPN klienty pro Azure VPN Gateway | Microsoft Docs
description: Postup konfigurace OpenVPN klientů pro Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 09ff3ccebad0baa4148e68995254c818a29d7bd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066110"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurace klientů OpenVPN pro Azure VPN Gateway

Tento článek vám pomůže nakonfigurovat **klienty &reg; protokolu OpenVPN** .

## <a name="before-you-begin"></a>Před zahájením

Ověřte, že jste dokončili postup konfigurace OpenVPN pro vaši bránu VPN. Podrobnosti najdete v tématu [Konfigurace OpenVPN pro Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Další kroky

Pokud chcete, aby klienti VPN měli přístup k prostředkům v jiné virtuální síti, postupujte podle pokynů v článku [VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) a nastavte připojení typu VNet-to-VNet. Nezapomeňte povolit protokol BGP u bran a připojení, jinak přenos nebude pokračovat.

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**
