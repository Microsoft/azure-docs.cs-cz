---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "77471500"
---
Podmíněný přístup umožňuje jemně odstupňované řízení přístupu na základě jednotlivých aplikací. Aby bylo možné používat podmíněný přístup, měli byste mít Azure AD Premium 1 nebo vyšší licence pro uživatele, na které budou platit pravidla podmíněného přístupu.

1. Přejděte na stránku **podnikové aplikace – všechny aplikace** a klikněte na **Azure VPN**.

   - Klikněte na **podmíněný přístup**.
   - Kliknutím na **Nová zásada** otevřete **nové** podokno.
2. V podokně **Nový** přejděte na **přiřazení – > uživatelů a skupin**. Na kartě **Uživatelé a skupiny – >** **Zahrnout** :

   - Klikněte na **Vybrat uživatele a skupiny**.
   - Kontrolovat **uživatele a skupiny**.
   - Kliknutím na **Vybrat** vyberte skupinu nebo skupinu uživatelů, které mají být ovlivněné MFA.
   - Klikněte na **Hotovo**.

   ![Přiřazení](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. V podokně **Nový** přejděte do podokna **ovládací prvky přístupu – > udělení** :

   - Klikněte na **udělit přístup**.
   - Klikněte na **vyžadovat službu Multi-Factor Authentication**.
   - Klikněte na **vyžadovat všechny vybrané ovládací prvky**.
   - Klikněte na **Vybrat**.
   
   ![Udělení přístupu – MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. V části **Povolit zásady** :

   - Vyberte **zapnuto**.
   - Klikněte na **Vytvořit**.

   ![Povolit zásadu](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)