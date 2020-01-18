---
title: 'Povolit MFA pro uživatele sítě VPN: ověřování Azure AD'
description: Povolení služby Multi-Factor Authentication pro uživatele sítě VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: b22581d012b2c69081bc7b4eee093227c060b4c2
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169717"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Povolit Azure Multi-Factor Authentication (MFA) pro uživatele sítě VPN

Pokud chcete, aby se uživatelé před udělením přístupu zobrazovali k druhému faktoru ověřování, můžete pro vašeho tenanta Azure AD nakonfigurovat službu Azure Multi-Factor Authentication (MFA). Kroky v tomto článku vám pomůžou zajistit požadavek na dvoustupňové ověřování.

## <a name="prereq"></a>Požadovaných součástí

Předpokladem pro tuto konfiguraci je konfigurovaný tenant Azure AD pomocí kroků v části [Konfigurace tenanta](openvpn-azure-ad-tenant.md).

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="enablesign"></a>Konfigurovat nastavení přihlášení

Na stránce **Azure VPN – vlastnosti** nakonfigurujte nastavení přihlášení.

1. Nastavte možnost **Povolit uživatelům přihlášení?** na **Ano**. To umožní všem uživatelům v tenantovi služby AD se úspěšně připojit k síti VPN.
2. Nastavit **přiřazení uživatele jako povinné?** Pokud chcete omezit přihlašování jenom na uživatele, kteří mají oprávnění k Azure VPN, nastavte **hodnotu Ano** .
3. Uložte provedené změny.

   ![Oprávnění](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Další kroky

Pokud se chcete připojit k virtuální síti, musíte vytvořit a nakonfigurovat profil klienta VPN. Viz téma [Konfigurace klienta VPN pro připojení P2S VPN](openvpn-azure-ad-client.md).
