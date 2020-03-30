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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471500"
---
Podmíněný přístup umožňuje jemně odstupňované řízení přístupu na základě aplikace. Chcete-li používat podmíněný přístup, měli byste mít Azure AD Premium 1 nebo vyšší licencování použít pro uživatele, kteří budou podléhat pravidlům podmíněného přístupu.

1. Přejděte na **stránku Podnikové aplikace – všechny aplikace** a klikněte na Azure **VPN**.

   - Klepněte na **položku Podmíněný přístup**.
   - Kliknutím na **Nová zásada** otevřete **podokno Nový.**
2. V podokně **Nový** přejděte na **příkaz Přiřazení -> Uživatelé a skupiny**. Na kartě **Uživatelé a skupiny ->** **Zahrnout:**

   - Klepněte na **tlačítko Vybrat uživatele a skupiny**.
   - Zkontrolujte **uživatele a skupiny**.
   - Klepnutím na **tlačítko Vybrat** vyberte skupinu nebo sadu uživatelů, které mají být ovlivněny vícefaktorovou pomocí.
   - Klikněte na **Done** (Hotovo).

   ![Přiřazení](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. V podokně **Nový** přejděte do podokna **Access controls -> Grant:**

   - Klepněte na tlačítko **Udělit přístup**.
   - Klepněte na **tlačítko Vyžadovat vícefaktorové ověřování**.
   - Klepněte na **tlačítko Vyžadovat všechny vybrané ovládací prvky**.
   - Klepněte na **tlačítko Vybrat**.
   
   ![Udělit přístup - Vícefaktorové finanční hod.](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. V části **Povolit zásady:**

   - Vyberte **Možnost Zapnuto**.
   - Klikněte na **Vytvořit**.

   ![Povolit zásady](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)