---
title: Podmíněný přístup – kombinované informace o zabezpečení – Azure Active Directory
description: Vytvoření vlastní zásady podmíněného přístupu pro registraci bezpečnostních údajů
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: da68e21aa279ea2503a21ce35eee52f8e49d1434
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89049090"
---
# <a name="conditional-access-securing-security-info-registration"></a>Podmíněný přístup: zabezpečení registrace informací o zabezpečení

Zabezpečení, kdy a jak se uživatelé registrují pro Azure Multi-Factor Authentication a Samoobslužné resetování hesla, je teď možné u uživatelských akcí v zásadách podmíněného přístupu. Tato funkce ve verzi Preview je dostupná pro organizace, u kterých je povolená [Kombinovaná registrace ve verzi Preview](../authentication/concept-registration-mfa-sspr-combined.md). Tato funkce může být povolená v organizacích, kde chtějí použít podmínky jako důvěryhodné síťové umístění k omezení přístupu k registraci pro Azure Multi-Factor Authentication a Samoobslužné resetování hesla (SSPR). Další informace o použitelných podmínkách najdete v článku [podmíněný přístup: podmínky](concept-conditional-access-conditions.md).

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Vytvoření zásady, která vyžaduje registraci z důvěryhodného umístění

Následující zásady platí pro všechny vybrané uživatele, kteří se pokoušejí zaregistrovat pomocí kombinovaného prostředí pro registraci, a zablokují přístup, pokud se nepřipojují z umístění označeného jako důvěryhodná síť.

1. V **Azure Portal**přejděte na **Azure Active Directory**  >  **zabezpečení**  >  **podmíněný přístup**.
1. Vyberte **nové zásady**.
1. Do název zadejte název pro tuto zásadu. Například **Kombinovaná registrace informací o zabezpečení v důvěryhodných sítích**.
1. V části **přiřazení**vyberte **Uživatelé a skupiny**a vyberte uživatele a skupiny, pro které chcete tuto zásadu použít.

   > [!WARNING]
   > Pro [kombinovanou registraci](../authentication/howto-registration-mfa-sspr-combined.md)je nutné povolit uživatele.

1. V části **cloudové aplikace nebo akce**vyberte **akce uživatele**a zaškrtněte políčko **zaregistrovat informace o zabezpečení**.
1. V **Conditions**části  >  **umístění**podmínek.
   1. Nakonfigurujte **Ano**.
   1. Uveďte **libovolné umístění**.
   1. Vylučte **všechna důvěryhodná umístění**.
   1. V okně umístění vyberte **Hotovo** .
   1. V okně podmínky vyberte **Hotovo** .
1. V části **podmínky**  >  **klientské aplikace (Preview)** nastavte **Konfigurovat** na **Ano**a vyberte **Hotovo**.
1. V části **řízení přístupu**  >  **udělení oprávnění**.
   1. Vyberte **blokovat přístup**.
   1. Pak klikněte na **Vybrat**.
1. Nastavte **Povolit zásadu** na **Zapnuté**.
1. Pak vyberte **Uložit**.

V kroku 6 v této zásadě mají organizace volby, které můžou dělat. Výše uvedená zásada vyžaduje registraci z důvěryhodného umístění v síti. Organizace si můžou zvolit, že budou využívat jakékoli dostupné podmínky místo **umístění**. Mějte na paměti, že tato zásada je zásada blokování, takže cokoli zahrnuto je blokované a povoluje se cokoli, co se neshoduje s zahrnutím. 

Některé můžou místo umístění v kroku 6 použít stav zařízení:

6. V části **podmínky**  >  **stav zařízení (Preview)**.
   1. Nakonfigurujte **Ano**.
   1. Zahrnout **všechny stavy zařízení**
   1. Vyloučit **zařízení připojené k hybridní službě Azure AD** nebo **zařízení označené jako vyhovující**
   1. V okně umístění vyberte **Hotovo** .
   1. V okně podmínky vyberte **Hotovo** .

> [!WARNING]
> Pokud v zásadě použijete jako podmínku stav zařízení, může to mít vliv na uživatele typu Host v adresáři. [Režim pouze pro sestavy](concept-conditional-access-report-only.md) vám pomůže určit dopad rozhodnutí o zásadách.
> Režim pouze pro sestavy nelze použít pro zásady certifikační autority s oborem "akce uživatele".

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze sestavy podmíněného přístupu](howto-conditional-access-insights-reporting.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
