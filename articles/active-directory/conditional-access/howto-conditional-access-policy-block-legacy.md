---
title: Podmíněný přístup – blokování starších verzí ověřování – Azure Active Directory
description: Vytvoření vlastních zásad podmíněného přístupu pro blokování starších protokolů ověřování
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
ms.openlocfilehash: 6a868c8199ac34a498a280e2522d6b1e4c7ec370
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295220"
---
# <a name="conditional-access-block-legacy-authentication"></a>Podmíněný přístup: blokovat starší verze ověřování

V důsledku zvýšeného rizika spojeného se staršími protokoly pro ověřování společnost Microsoft doporučuje, aby organizace zablokovaly žádosti o ověření pomocí těchto protokolů a vyžadovala moderní ověřování.

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Následující kroky vám pomůžou vytvořit zásady podmíněného přístupu, které blokují požadavky na starší verzi ověřování. Tato zásada je nastavena do [režimu pouze](howto-conditional-access-report-only.md) pro spuštění sestav, takže správci mohou určit dopad, který budou mít u stávajících uživatelů. Když mají správci možnost, že se zásady použijí podle svých záměrů, **můžou na nasazení přepnout do nebo připravit** přidáním konkrétních skupin a vyloučením dalších.

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Vyhledejte **Azure Active Directory** > **Security** > **podmíněný přístup**zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení**vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout**vyberte **Všichni uživatelé**.
   1. V části **vyloučit**vyberte **Uživatelé a skupiny** a zvolte všechny účty, které musí udržovat možnost používat starší verze ověřování. Vyloučením alespoň jednoho účtu zabráníte jeho uzamknutí. Pokud nevylučujete žádný účet, nebudete moct vytvořit tuto zásadu.
   1. Vyberte **Done** (Hotovo).
1. V části **cloudové aplikace nebo akce**vyberte **všechny cloudové aplikace**.
   1. Vyberte **Done** (Hotovo).
1. V části **podmínky** > **klientské aplikace (Preview)** nastavte **Konfigurovat** na **Ano**.
   1. Zaškrtněte políčka pouze >  **mobilní aplikace a klienti klasické pracovní plochy****ostatní klienti**.
   1. Vyberte **Done** (Hotovo).
1. V části **řízení** > přístupu**udělit**vyberte **blokovat přístup**.
   1. Vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásady** na **pouze sestavy**.
1. Vyberte **vytvořit** a vytvořte tak, aby se zásady povolily.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze sestavy podmíněného přístupu](howto-conditional-access-report-only.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
