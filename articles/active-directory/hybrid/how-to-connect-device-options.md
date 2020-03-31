---
title: 'Azure AD Connect: Možnosti zařízení | Dokumenty společnosti Microsoft'
description: Tento dokument podrobně popisuje možnosti zařízení, které jsou dostupné ve službě Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96ddcdb67ef079cfa23902a1dcb03b0ec61077fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109529"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: Možnosti zařízení

Následující dokumentace obsahuje informace o různých možnostech zařízení, které jsou k dispozici ve službě Azure AD Connect. Azure AD Connect můžete použít ke konfiguraci následujících dvou operací: 
* **Hybridní připojení Azure AD**: Pokud vaše prostředí má místní stopu služby AD a chcete výhody Azure AD, můžete implementovat hybridní zařízení připojená k Azure AD. Tato zařízení jsou připojena k místnímu službě Active Directory i k vaší službě Azure Active Directory.
* **Zpětný zápis zařízení**: Zpětný zápis zařízení se používá k povolení podmíněného přístupu na základě zařízení chráněných zařízení služby AD FS (2012 R2 nebo vyšší).

## <a name="configure-device-options-in-azure-ad-connect"></a>Konfigurace možností zařízení ve službě Azure AD Connect

1.  Spusťte Azure AD Connect. Na stránce **Další úkoly** vyberte **Konfigurovat možnosti zařízení**.  Klikněte na **Další**.
    ![Konfigurace možností zařízení](./media/how-to-connect-device-options/deviceoptions.png) 

    Na stránce **Přehled** se zobrazí podrobnosti.
    ![Přehled](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > Nové možnosti konfigurovat zařízení je k dispozici pouze ve verzi 1.1.819.0 a novější.

2.  Po zadání přihlašovacích údajů pro Azure AD, můžete zvolit operaci, která má být provedena na stránce možnosti zařízení.
    ![Provoz zařízení](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Další kroky

* [Konfigurace hybridního připojení Azure AD](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Konfigurace / zakázání zpětného zápisu zařízení](how-to-connect-device-writeback.md)

