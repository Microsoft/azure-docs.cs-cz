---
title: 'Azure AD Connect: Možnosti zařízení | Microsoft Docs'
description: Tento dokument podrobně popisuje možnosti zařízení, které jsou k dispozici ve službě Azure AD Connect
services: active-directory
documentationcenter: ''
author: anandy
manager: samueld
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.component: hybrid
ms.author: anandy
ms.openlocfilehash: 0eb3a33ee030dcda6a811a771578c9e976e36619
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593278"
---
#<a name="azure-ad-connect-device-options"></a>Azure AD Connect: Možnosti zařízení

V následující dokumentaci naleznete informace o různých možnostech zařízení, která je k dispozici ve službě Azure AD Connect. Azure AD Connect můžete použít ke konfiguraci těchto dvou akcí: 
* **Hybridní Azure AD join**: Pokud vaše prostředí disponuje místní AD nároky a vy chcete také výhody z možnosti poskytované službou Azure Active Directory, můžete implementovat hybridní Azure AD, které jsou připojené k zařízení. Jedná se o zařízení, které jsou obě, připojený k místní službě Active Directory a Azure Active Directory.
* **Zpětný zápis zařízení**: zpětný zápis zařízení slouží k povolení podmíněného přístupu podle zařízení do služby AD FS (2012 R2 nebo vyšší) chráněný zařízení

## <a name="configure-device-options-in-azure-ad-connect"></a>Konfigurovat možnosti zařízení v Azure AD Connect

1.  Spustit Azure AD Connect. V **další úkoly** vyberte **konfigurovat možnosti zařízení**.
    ![Konfigurace možností zařízení](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    Po kliknutí na tlačítko Další, **přehled** se zobrazí stránka, která podrobnosti operace, které lze provést.
    ![Přehled](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > Nové možnosti konfigurace zařízení je k dispozici pouze ve verzi 1.1.819.0 a novější.

2.  Po zadání přihlašovacích údajů pro Azure AD, můžete zvolit operaci provést na stránce Možnosti zařízení.
    ![Operace zařízení](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Další postup

* [Konfigurace připojení k hybridní Azure AD](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Nebo nakonfigurujte zakázat zpětný zápis zařízení.](./active-directory-aadconnect-feature-device-writeback.md)

