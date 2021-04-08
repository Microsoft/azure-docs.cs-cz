---
title: Podmíněný přístup – zablokuje přístup podle umístění – Azure Active Directory
description: Vytvoření vlastních zásad podmíněného přístupu pro blokování přístupu k prostředkům podle umístění IP adresy
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
ms.openlocfilehash: e257ab39257b23c52aaadbe32f0325e8d71a8409
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98597969"
---
# <a name="conditional-access-block-access-by-location"></a>Podmíněný přístup: blokovat přístup podle umístění

Pomocí podmínky umístění v podmíněném přístupu můžete řídit přístup k vašim cloudovým aplikacím na základě umístění uživatele v síti. Podmínka umístění se běžně používá k blokování přístupu ze zemí nebo oblastí, ve kterých vaše organizace ví, že by přenos dat neměl pocházet.

## <a name="define-locations"></a>Definovat umístění

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Vyhledejte **Azure Active Directory**  >  **zabezpečení**  >  **podmíněný přístup**  >  **s názvem umístění**.
1. Vyberte **nové umístění**.
1. Zadejte název svého umístění.
1. **Rozsahy IP** adres vyberte, pokud znáte konkrétní externě přístupné rozsahy IPv4 adres, které tvoří dané umístění nebo **země nebo oblasti**.
   1. Zadejte **rozsahy IP adres** nebo vyberte **země nebo oblasti** pro umístění, které zadáte.
      * Pokud zvolíte země/oblasti, můžete volitelně zahrnout neznámé oblasti.
1. Zvolit **Uložit**

Další informace o podmínkách umístění v podmíněném přístupu najdete v článku [co je to podmínka umístění v Azure Active Directory podmíněný přístup](location-condition.md) .

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Vyhledejte **Azure Active Directory**  >    >  **podmíněný přístup** zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení** vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout** vyberte **Všichni uživatelé**.
   1. V části **vyloučit** vyberte **Uživatelé a skupiny** a zvolte účty pro nouzový přístup nebo rozklad vaší organizace. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**  >  **patří** a vyberte **všechny cloudové aplikace**.
1. V části  >  **umístění** podmínek.
   1. Nastavte **Konfigurovat** na **Ano** .
   1. V části **Zahrnout** vyberte **Vybraná umístění** .
   1. Vyberte blokované umístění, které jste vytvořili pro vaši organizaci.
   1. Klikněte na **Vybrat**.
1. V části **řízení přístupu** > vybrat **přístup k bloku** a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte zásady podmíněného přístupu.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze sestavy podmíněného přístupu](howto-conditional-access-insights-reporting.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
