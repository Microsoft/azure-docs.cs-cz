---
title: Podmíněný přístup – kombinované informace o zabezpečení – Azure Active Directory
description: Vytvoření vlastní zásady podmíněného přístupu, která vyžaduje důvěryhodné umístění pro registraci bezpečnostních údajů
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c9b01cc06b3d0ef8f47b34e9ef86bec9adac03f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424855"
---
# <a name="conditional-access-require-trusted-location-for-mfa-registration"></a>Podmíněný přístup: vyžadovat důvěryhodné umístění pro registraci MFA

Zabezpečení, kdy a jak se uživatelé registrují pro Azure Multi-Factor Authentication a Samoobslužné resetování hesla, je teď možné u uživatelských akcí v zásadách podmíněného přístupu. Tato funkce ve verzi Preview je dostupná pro organizace, u kterých je povolená [Kombinovaná registrace ve verzi Preview](../authentication/concept-registration-mfa-sspr-combined.md). Tato funkce může být povolená v organizacích, kde chtějí použít podmínky jako důvěryhodné síťové umístění k omezení přístupu k registraci pro Azure Multi-Factor Authentication a SSPR. Další informace o vytváření důvěryhodných umístění v podmíněném přístupu najdete v článku [co je podmínka umístění v Azure Active Directory podmíněný přístup?](../conditional-access/location-condition.md#named-locations)

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Vytvoření zásady, která vyžaduje registraci z důvěryhodného umístění

Následující zásady platí pro všechny vybrané uživatele, kteří se pokoušejí zaregistrovat pomocí kombinovaného prostředí pro registraci, a zablokují přístup, pokud se nepřipojují z umístění označeného jako důvěryhodná síť.

1. V **Azure Portal**přejděte do **Azure Active Directory** > **zabezpečení** > **podmíněný přístup**.
1. Vyberte **nové zásady**.
1. Do název zadejte název pro tuto zásadu. Například **Kombinovaná registrace informací o zabezpečení v důvěryhodných sítích**.
1. V části **přiřazení**klikněte na **Uživatelé a skupiny**a vyberte uživatele a skupiny, pro které chcete tuto zásadu použít.

   > [!WARNING]
   > Uživatelé musí mít povolený [Náhled pro kombinovanou registraci](../authentication/howto-registration-mfa-sspr-combined.md).

1. V části **cloudové aplikace nebo akce**vyberte **akce uživatele**a zaškrtněte políčko **zaregistrovat informace o zabezpečení (Preview)** .
1. V části **podmínky** > **umístění**.
   1. Nakonfigurujte **Ano**.
   1. Uveďte **libovolné umístění**.
   1. Vylučte **všechna důvěryhodná umístění**.
   1. V okně umístění klikněte na **Hotovo** .
   1. V okně podmínky klikněte na **Hotovo** .
1. V části **řízení přístupu** > **udělení**.
   1. Klikněte na **blokovat přístup**.
   1. Pak klikněte na **Vybrat**.
1. Nastavte **Povolit zásadu** na **Zapnuto**.
1. Potom klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze sestavy podmíněného přístupu](howto-conditional-access-report-only.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
