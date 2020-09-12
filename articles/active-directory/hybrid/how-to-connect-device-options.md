---
title: 'Azure AD Connect: možnosti zařízení | Microsoft Docs'
description: Tento dokument podrobně popisuje možnosti zařízení, které jsou k dispozici v Azure AD Connect
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
ms.topic: how-to
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90d8cc39174f6392b0c532948305fa713535f0c9
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89279223"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: možnosti zařízení

Následující dokumentace poskytuje informace o různých možnostech zařízení, které jsou k dispozici v Azure AD Connect. Azure AD Connect můžete použít ke konfiguraci následujících dvou operací: 
* **Připojení k hybridní službě Azure AD**: Pokud vaše prostředí obsahuje místní službu AD a potřebujete výhody služby Azure AD, můžete implementovat zařízení připojená k hybridní službě Azure AD. Tato zařízení jsou připojená k místní službě Active Directory a k vašemu Azure Active Directory.
* **Zpětný zápis zařízení**: zpětný zápis zařízení umožňuje povolit podmíněný přístup na základě zařízení, aby se AD FS (2012 R2 nebo novější) chráněná zařízení.

## <a name="configure-device-options-in-azure-ad-connect"></a>Konfigurace možností zařízení v Azure AD Connect

1.  Spusťte Azure AD Connect. Na stránce **další úlohy** vyberte **Konfigurovat možnosti zařízení**.  Klikněte na **Next** (Další).
    ![Konfigurace možností zařízení](./media/how-to-connect-device-options/deviceoptions.png) 

    Zobrazí se stránka **Přehled** s podrobnostmi.
    ![Přehled](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > Nové možnosti konfigurace zařízení jsou k dispozici pouze ve verzi 1.1.819.0 a novější.

2.  Po zadání přihlašovacích údajů pro Azure AD můžete na stránce Možnosti zařízení vybrat operaci, která se má provést.
    ![Operace zařízení](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Další kroky

* [Konfigurace hybridního připojení k Azure AD](../devices/hybrid-azuread-join-plan.md)
* [Konfigurovat nebo zakázat zpětný zápis zařízení](how-to-connect-device-writeback.md)