---
title: Samoobslužná registrace pro externí identity – Azure AD
description: Naučte se, jak povolit externím uživatelům registraci vašich aplikací tím, že povolíte samoobslužné registrace. Přizpůsobené prostředí pro registraci vytvoříte přizpůsobením uživatelského toku samoobslužné registrace.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e0325b43b6726f04d5994b60404f218ac58122d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597531"
---
# <a name="self-service-sign-up-preview"></a>Samoobslužná registrace (Preview)
|     |
| --- |
| Samoobslužná registrace je funkce veřejné verze Preview služby Azure Active Directory. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.|
|     |

Když sdílíte aplikace s externími uživateli, možná nebudete vždy znát předem, který bude potřebovat přístup k aplikaci. Jako alternativu k zasílání pozvánek přímo jednotlivcům můžete povolit externím uživatelům zaregistrovat se ke konkrétním aplikacím sami tím, že povolíte samoobslužné registrace. Přizpůsobené prostředí pro registraci můžete vytvořit přizpůsobením uživatelského toku samoobslužné registrace. Můžete například poskytnout možnosti pro poskytovatele služeb Azure AD nebo sociální identity a shromažďovat informace o uživateli.

## <a name="user-flow-for-self-service-sign-up"></a>Tok uživatele pro samoobslužné přihlášení

Uživatelský tok pro samoobslužné zápisy vytváří pro externí uživatele prostředí pro registraci prostřednictvím aplikace, kterou chcete sdílet. Tok uživatele je možné přidružit k jedné nebo více aplikacím. Nejprve povolíte samoobslužnou registraci svého tenanta a federovat se všemi zprostředkovateli identity, kterým chcete povolit použití externích uživatelů pro přihlášení. Pak vytvoříte a přizpůsobíte uživatelský tok pro registraci a přiřadíte k němu své aplikace.
Nastavení toku uživatele můžete nakonfigurovat, abyste mohli řídit, jak se uživatel zaregistruje do aplikace:

- Typy účtů používané pro přihlašování, jako jsou účty na Facebooku nebo účty Azure AD
- Atributy, které se mají shromáždit od uživatele, který se registruje, jako je jméno, PSČ nebo země sídla

Když se uživatel chce přihlásit ke svojí aplikaci, ať už se jedná o webovou, mobilní, desktopovou nebo jednostránkovou aplikaci (SPA), zahájí žádost o autorizaci koncovému bodu zadaného uživatelem. Tok uživatele definuje a řídí uživatelské prostředí. Po dokončení uživatelského toku registrace Azure AD vygeneruje token a pak přesměruje uživatele zpět do vaší aplikace. Stejný tok uživatelů může používat více aplikací.

## <a name="example-of-self-service-sign-up"></a>Příklad samoobslužné registrace

Následující příklad ukazuje, jak přinášíme poskytovatelům sociálních identit do služby Azure AD s funkcemi samoobslužné registrace pro uživatele typu Host.  
Partner Woodgrove otevře aplikaci Woodgrove. Rozhodují, že chtějí zaregistrovat účet dodavatele, takže si vyberou žádost o účet dodavatele, který iniciuje samoobslužný postup registrace.

![Příklad úvodní stránky pro samoobslužné přihlášení](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

K registraci používají e-mail podle svého výběru.

![Příklad zobrazující výběr Facebooku pro přihlášení](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

Azure AD vytvoří relaci s Woodgrove pomocí účtu Facebook partnera a vytvoří nový účet.

Woodgrove chce o uživateli získat další informace, jako je název, obchodní název, registrační kód pro firmy, telefonní číslo.

![Příklad zobrazení atributů pro registraci uživatelů](media/self-service-sign-up-overview/example-enter-user-attributes.png)

Uživatel zadá informace, bude pokračovat v toku registrace a získá přístup k potřebným prostředkům.

![Příklad ukazující přihlášeného uživatele](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>Další kroky

 Podrobnosti najdete v tématu Postup [Přidání samoobslužné registrace do aplikace](self-service-sign-up-user-flow.md).