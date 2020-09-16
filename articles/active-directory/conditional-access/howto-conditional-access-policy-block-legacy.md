---
title: Podmíněný přístup – blokování starších verzí ověřování – Azure Active Directory
description: Vytvoření vlastních zásad podmíněného přístupu pro blokování starších protokolů ověřování
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b4a0a411f51eed3a262ee59eb8e93b983755af3
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90601550"
---
# <a name="conditional-access-block-legacy-authentication"></a>Podmíněný přístup: blokovat starší verze ověřování

V důsledku zvýšeného rizika spojeného se staršími protokoly pro ověřování společnost Microsoft doporučuje, aby organizace zablokovaly žádosti o ověření pomocí těchto protokolů a vyžadovala moderní ověřování.

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Následující kroky vám pomůžou vytvořit zásady podmíněného přístupu, které blokují požadavky na starší verzi ověřování. Tato zásada je nastavena do [režimu pouze](howto-conditional-access-insights-reporting.md) pro spuštění sestav, takže správci mohou určit dopad, který budou mít u stávajících uživatelů. Když mají správci možnost, že se zásady použijí podle svých záměrů, **můžou na nasazení přepnout do nebo připravit** přidáním konkrétních skupin a vyloučením dalších.

1. Přihlaste se k webu **Azure Portal** jako globální správce.
1. Vyhledejte **Azure Active Directory**  >  **Security**  >  **podmíněný přístup**zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení**vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout**vyberte **Všichni uživatelé**.
   1. V části **vyloučit**vyberte **Uživatelé a skupiny** a zvolte všechny účty, které musí udržovat možnost používat starší verze ověřování. Vyloučením alespoň jednoho účtu zabráníte jeho uzamknutí. Pokud nevylučujete žádný účet, nebudete moct vytvořit tuto zásadu.
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**vyberte **všechny cloudové aplikace**.
   1. Vyberte **Hotovo**.
1. V části **podmínky**  >  **klientských aplikací**nastavte **Konfigurovat** na **Ano**.
   1. Zaškrtněte pouze políčka **klienti Exchange ActiveSync** a **Další klienti**.
   1. Vyberte **Hotovo**.
1. V části **řízení přístupu**  >  **udělit**vyberte **blokovat přístup**.
   1. Vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásady** na **pouze sestavy**.
1. Vyberte **vytvořit** a vytvořte tak, aby se zásady povolily.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze sestavy podmíněného přístupu](howto-conditional-access-insights-reporting.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)

[Jak nastavit multifunkční zařízení nebo aplikaci pro odesílání e-mailů pomocí Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-office-3)
