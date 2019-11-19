---
title: 'Povolit MFA pro uživatele sítě VPN: ověřování Azure AD | Microsoft Docs'
description: Povolení služby Multi-Factor Authentication pro uživatele sítě VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: alzam
ms.openlocfilehash: 3f407ecfcbfdff72b79bfe6b5bc3e6b7606b1cf0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174626"
---
# <a name="enable-multi-factor-authentication-mfa-for-vpn-users"></a>Povolit Multi-Factor Authentication (MFA) pro uživatele sítě VPN

VÍCEFAKTOROVÉ ověřování můžete povolit pro uživatele ve vašem tenantovi Azure AD, aby se uživatelům zobrazila výzva k ověření druhého faktoru před udělením přístupu.

> [! Předpoklad] nakonfigurovali jste klienta služby Azure AD, jak je popsáno v dokumentu konfigurace tenanta.
>

### <a name="tenant"></a>1. Přihlaste se k Azure Portal a přejděte do **Azure Active Directory** , **Všichni uživatelé** a klikněte na **Multi-Factor Authentication**


   ![Nový tenant Azure AD](./media/openvpn-azure-ad-mfa/mfa1.jpg)

### <a name="users"></a>2. Vyberte uživatele, u kterých chcete povolit vícefaktorové ověřování, a klikněte na **Povolit** .

   ![Nový tenant Azure AD](./media/openvpn-azure-ad-mfa/mfa2.jpg)

### <a name="enable-authentication"></a>3. přejděte na **Azure Active Directory** , **podnikové aplikace**, **všechny aplikace** a klikněte na **Azure VPN** .


   ![ID adresáře](./media/openvpn-azure-ad-mfa/user1.jpg)

### <a name="users"></a>4. Zajistěte, aby se **Uživatelé, kteří mají povolené přihlášení** , nastavili na Ano. Pokud chcete, aby se mohli přihlásit jenom uživatelé, kteří mají oprávnění k síti VPN v Azure, aby se mohli přihlásit a nastavit **požadované přiřazení uživatelů?** Ano, jinak se všichni uživatelé v tenantovi služby AD budou moci připojit k síti VPN úspěšně.

   ![Oprávnění](./media/openvpn-azure-ad-mfa/user2.jpg)


## <a name="next-steps"></a>Další kroky

Aby bylo možné se připojit k virtuální síti, je nutné vytvořit a nakonfigurovat profil klienta VPN. Viz téma [Konfigurace klienta VPN pro připojení P2S VPN](openvpn-azure-ad-client.md).
