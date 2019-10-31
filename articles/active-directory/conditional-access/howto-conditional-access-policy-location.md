---
title: Podmíněný přístup – zablokuje přístup podle umístění – Azure Active Directory
description: Vytvoření vlastních zásad podmíněného přístupu pro blokování přístupu k prostředkům podle umístění IP adresy
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64b7add9ddb1734d763c46e93d6788ce21a2c321
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150889"
---
# <a name="conditional-access-block-access-by-location"></a>Podmíněný přístup: blokovat přístup podle umístění

Pomocí podmínky umístění v podmíněném přístupu můžete řídit přístup k vašim cloudovým aplikacím na základě umístění uživatele v síti. Podmínka umístění se běžně používá k blokování přístupu ze zemí, ve kterých vaše organizace ví, že by přenos dat neměl pocházet.

## <a name="define-locations"></a>Definovat umístění

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na **Azure Active Directory** **podmíněný přístup** > .
1. Vyberte **nové umístění**.
1. Zadejte název svého umístění.
1. **Rozsahy IP** adres vyberte, pokud znáte konkrétní externě přístupné rozsahy IPv4 adres, které tvoří dané umístění nebo **země nebo oblasti**.
   1. Zadejte **rozsahy IP adres** nebo vyberte **země nebo oblasti** pro umístění, které zadáte.
      * Pokud jste zvolili země/oblasti, můžete volitelně zahrnout neznámé oblasti.
1. Zvolit **Uložit**

Další informace o podmínkách umístění v podmíněném přístupu najdete v článku [co je to podmínka umístění v Azure Active Directory podmíněný přístup](location-condition.md) .

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na **Azure Active Directory** **podmíněný přístup** > .
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení**vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout**vyberte **Všichni uživatelé**.
   1. Vyberte **Done** (Hotovo).
1. V části **cloudové aplikace nebo akce** > **Zahrnout**, vyberte **všechny cloudové aplikace**a vyberte **Hotovo**.
1. V části **podmínky** > **umístění**.
   1. Nastavte **Konfigurovat** na **Ano** .
   1. **Zahrnout** výběr **vybraných umístění**
   1. Vyberte blokované umístění, které jste vytvořili pro vaši organizaci.
   1. Klikněte na **vybrat** > **dokončeno** > **Hotovo**.
1. V části **ovládací prvky přístupu** > **zablokujte**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte tak, aby se zásady povolily.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
