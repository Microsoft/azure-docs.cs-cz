---
title: Podmíněný přístup – kombinované informace o zabezpečení – Služba Azure Active Directory
description: Vytvoření vlastní zásady podmíněného přístupu pro registraci bezpečnostních údajů
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f69a94e17155ff93510d09f666bce12f628274f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295163"
---
# <a name="conditional-access-securing-security-info-registration"></a>Podmíněný přístup: Zabezpečení registrace bezpečnostních údajů

Zabezpečení, kdy a jak se uživatelé registrují pro azure vícefaktorové ověřování a samoobslužné resetování hesla je teď možné s akcemi uživatelů v zásadách podmíněného přístupu. Tato funkce náhledu je k dispozici organizacím, které povolily [kombinovaný náhled registrace](../authentication/concept-registration-mfa-sspr-combined.md). Tato funkce může být povolena v organizacích, kde chtějí používat podmínky, jako je důvěryhodné umístění v síti, k omezení přístupu k registraci pro azure multi-factor authentication a samoobslužné resetování hesla (SSPR). Další informace o použitelných podmínkách naleznete v článku [Podmíněný přístup: Podmínky](concept-conditional-access-conditions.md).

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Vytvoření zásady, která vyžaduje registraci z důvěryhodného umístění

Následující zásady platí pro všechny vybrané uživatele, kteří se pokoušejí zaregistrovat pomocí kombinovaného registračního prostředí, a zablokují přístup, pokud se nepřipojují z umístění označeného jako důvěryhodná síť.

1. Na **webu Azure Portal**přejděte na Azure **Active Directory** > **Security** > **Conditional Access**.
1. Vyberte **nové zásady**.
1. V pojmenujete název této zásady. Například **kombinovaná registrace informací o zabezpečení v důvěryhodných sítích**.
1. V části **Přiřazení**vyberte **Položku Uživatelé a skupiny**a vyberte uživatele a skupiny, na které se má tato zásada vztahovat.

   > [!WARNING]
   > Uživatelé musí být povolena pro [kombinovanou registraci náhled](../authentication/howto-registration-mfa-sspr-combined.md).

1. V části **Cloudové aplikace nebo akce**vyberte **Akce uživatele**a **zkontrolujte Zaregistrovat informace o zabezpečení (preview).**
1. V **podmínkách** > **Umístění**.
   1. Konfigurace **programu Ano**.
   1. Zahrnout **libovolné umístění**.
   1. Vyloučit **všechna důvěryhodná umístění**.
   1. V yberte **Hotovo** v okně Umístění.
   1. V yberte **Hotovo** na okně Podmínky.
1. V **části Podmínky** > **Klientské aplikace (Preview)** nastavte **Configure** to **Yes**a vyberte **Hotovo**.
1. V části **Access controls** > **Grant**.
   1. Vyberte **možnost Blokovat přístup**.
   1. Pak klikněte na **Vybrat**.
1. Nastavte **Povolit zásadu** na **Zapnuté**.
1. Potom vyberte **Uložit**.

V kroku 6 v této zásadě mají organizace možnosti, které mohou provést. Výše uvedené zásady vyžadují registraci z důvěryhodného síťového umístění. Organizace se mohou rozhodnout využít všechny dostupné podmínky místo **lokalit**. Nezapomeňte, že tato zásada je bloková zásada, takže vše, co je zahrnuto, je blokováno a vše, co neodpovídá zahrnutí, je povoleno. 

Někteří se mohou rozhodnout použít stav zařízení namísto umístění v kroku 6 výše:

6. V **části Podmínky** > **Stav zařízení (náhled)**.
   1. Konfigurace **programu Ano**.
   1. Zahrnout **stav všech zařízení**.
   1. Vyloučit **připojení hybridního zařízení Azure AD** nebo **zařízení označené jako vyhovující**
   1. V yberte **Hotovo** v okně Umístění.
   1. V yberte **Hotovo** na okně Podmínky.

> [!WARNING]
> Pokud použijete stav zařízení jako podmínku v zásadách, může to mít vliv na uživatele typu Host v adresáři. [Režim pouze pro sestavu](concept-conditional-access-report-only.md) může pomoci určit dopad rozhodnutí zásad.

## <a name="next-steps"></a>Další kroky

[Podmíněné přístupové běžné zásady](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze pro sestavu podmíněného přístupu](howto-conditional-access-report-only.md)

[Simulovat chování přihlášení pomocí nástroje Co-li podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
