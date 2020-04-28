---
title: 'Povolit MFA pro uživatele sítě VPN: ověřování Azure AD'
description: Povolení služby Multi-Factor Authentication pro uživatele sítě VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: alzam
ms.openlocfilehash: 34ef1b73b06870fd4eaabe88147cd98b281c1f11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77472345"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Povolit Azure Multi-Factor Authentication (MFA) pro uživatele sítě VPN

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>Povolit ověřování

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>Konfigurovat nastavení přihlášení

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>Možnost 1 – přístup na uživatele

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>Možnost 2 – podmíněný přístup

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Další kroky

Pokud se chcete připojit k virtuální síti, musíte vytvořit a nakonfigurovat profil klienta VPN. Viz téma [Konfigurace klienta VPN pro připojení P2S VPN](openvpn-azure-ad-client.md).