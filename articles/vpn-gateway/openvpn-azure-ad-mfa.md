---
title: 'Povolit MFA pro uživatele sítě VPN: ověřování Azure AD'
description: Povolení služby Multi-Factor Authentication pro uživatele sítě VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 771dea2d9ae2979bc71880368ed3a9a538e8a803
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964694"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Povolit Azure Multi-Factor Authentication (MFA) pro uživatele sítě VPN

Pokud chcete, aby se uživatelé před udělením přístupu zobrazovali pro druhý faktor ověřování, můžete službu Azure Multi-Factor Authentication (MFA) nakonfigurovat na základě jednotlivých uživatelů nebo využít Multi-Factor Authentication (MFA) prostřednictvím [podmíněného přístupu](../active-directory/conditional-access/overview.md) pro přesnější kontrolu. Konfigurace Multi-Factor Authentication na uživatele se dá povolit bez dalších poplatků. když ale povolíte MFA na uživatele, zobrazí se uživateli výzva k zadání druhého faktoru ověřování proti všem aplikacím, které jsou vázané na tenanta Azure AD. Podmíněný přístup umožní jemnější kontrolu nad tím, jak by měl být povýšen druhý faktor, a může umožňovat přiřazení MFA jenom k síti VPN, a ne jiným aplikacím, které jsou vázané na tenanta Azure AD.

## <a name="enableauth"></a>Povolit ověřování

1. Přejděte na **Azure Active Directory > podnikové aplikace – > všechny aplikace**.
2. Na stránce **podnikové aplikace – všechny aplikace** vyberte **Azure VPN**.

   ![ID adresáře](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>Konfigurovat nastavení přihlášení

Na stránce **Azure VPN – vlastnosti** nakonfigurujte nastavení přihlášení.

1. Nastavte možnost **Povolit uživatelům přihlášení?** na **Ano**. To umožní všem uživatelům v tenantovi služby AD se úspěšně připojit k síti VPN.
2. Nastavit **přiřazení uživatele jako povinné?** Pokud chcete omezit přihlašování jenom na uživatele, kteří mají oprávnění k Azure VPN, nastavte **hodnotu Ano** .
3. Uložte provedené změny.

   ![Oprávnění](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="option-1---enable-multi-factor-authentication-mfa-via-conditional-access"></a>Možnost 1 – povolení Multi-Factor Authentication (MFA) prostřednictvím podmíněného přístupu

Podmíněný přístup umožňuje jemně odstupňované řízení přístupu na základě jednotlivých aplikací.  Upozorňujeme, že pokud chcete využít podmíněný přístup, měli byste mít Azure AD Premium 1 nebo vyšší licence, které se vztahují na uživatele, na které budou platit pravidla podmíněného přístupu.

1. Na stránce **podnikové aplikace – všechny aplikace** vyberte **Azure VPN**, vyberte **podmíněný přístup**a klikněte na **Nová zásada**.
2. V části Uživatelé a skupiny zaškrtněte na kartě *Zahrnout* **možnost vybrat uživatele a skupiny**, zaškrtněte položku **Uživatelé a skupiny**a vyberte skupinu nebo skupinu uživatelů, kteří by měli být předmětem MFA.  Klikněte na **Done** (Hotovo).
![Přiřazení](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. V **části udělit**zaškrtněte políčko **udělit přístup**, zaškrtněte políčko **vyžadovat vícefaktorové ověřování**, zaškrtněte políčko **vyžadovat všechny vybrané ovládací prvky**a klikněte na tlačítko **Vybrat** .
![udělení přístupu – MFA](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Zaškrtněte v části **Povolit zásadu** a **klikněte na tlačítko** **vytvořit** .
![povolit zásady](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)

## <a name="option-2---enable-multi-factor-authentication-mfa-per-user"></a>Možnost 2 – povolení Multi-Factor Authentication (MFA) na uživatele

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="next-steps"></a>Další kroky

Pokud se chcete připojit k virtuální síti, musíte vytvořit a nakonfigurovat profil klienta VPN. Viz téma [Konfigurace klienta VPN pro připojení P2S VPN](openvpn-azure-ad-client.md).
