---
title: 'Azure AD Connect: Možnosti zařízení | Dokumentace Microsoftu'
description: Tento dokument podrobně popisuje možnosti zařízení, které jsou k dispozici ve službě Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
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
ms.author: billmath
ms.openlocfilehash: e52f691c75d491897b06a4ebb492d87fda682e38
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917842"
---
#<a name="azure-ad-connect-device-options"></a>Azure AD Connect: Možnosti zařízení

V následující dokumentaci najdete informace o různých možnostech zařízení, která je k dispozici ve službě Azure AD Connect. Azure AD Connect můžete nakonfigurovat následující dvě operace: 
* **Připojení k hybridní službě Azure AD**: Pokud má místní prostředí AD nároky na místo a chcete také výhody poskytované službou Azure Active Directory, můžete implementovat hybridních zařízení připojených k Azure AD. Jde o zařízení, která jsou obě, připojené k vaší místní služby Active Directory a Azure Active Directory.
* **Zpětný zápis zařízení**: zpětný zápis zařízení se používá k povolení podmíněného přístupu založené na zařízení se službou AD FS (2012 R2 nebo vyšší) chráněný zařízení

## <a name="configure-device-options-in-azure-ad-connect"></a>Konfigurovat možnosti zařízení ve službě Azure AD Connect

1.  Spuštění služby Azure AD Connect. V **další úkoly** stránce **konfigurovat možnosti zařízení**.
    ![Konfigurovat možnosti zařízení](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    Po kliknutí na tlačítko Další, **přehled** se zobrazí stránka, která podrobně popisuje operace, které lze provést.
    ![Přehled](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > K dispozici pouze ve verzi 1.1.819.0 a novějších verzí se nakonfigurovat možnosti zařízení.

2.  Po zadání přihlašovacích údajů pro službu Azure AD, můžete zvolit operaci provést na stránce Možnosti zařízení.
    ![Operace zařízení](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Další postup

* [Konfigurace připojení k hybridní službě Azure AD](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Konfigurace / zakázat zpětný zápis zařízení](./active-directory-aadconnect-feature-device-writeback.md)

