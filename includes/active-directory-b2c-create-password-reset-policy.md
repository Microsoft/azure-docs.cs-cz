---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 78abb190dccd27c5bf70dfe12f978e1118601815
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355469"
---
Pokud chcete povolit jemné resetování hesla na aplikace, použijete **resetování hesla** tok uživatele. Všimněte si, že možnost resetování hesel v rámci celého tenanta je zadán [tady](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md). Tento tok uživatele popisuje prostředí, které zákazníci budou procházet při resetování hesla a obsah tokenů, které bude aplikace přijímat po úspěšném dokončení.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

V části **spravovat**vyberte **toky uživatelů** a klikněte na +**nový tok uživatele**.

![Vyberte nový tok uživatele](media/active-directory-b2c-create-password-reset-policy/add-b2c-new-user-flow.png)

Na **doporučená** kartu, vyberte možnost **resetování hesla**.

Zadejte tok uživatele **název** pro vaši aplikaci odkazovat. Zadejte například `SSPR`.

V části **zprostředkovatelé Identity**, zkontrolujte **resetovat heslo pomocí e-mailovou adresu**.

![Zadejte název a vyberte možnost Resetovat heslo pomocí e-mailové adresy jako zprostředkovatele identity](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

V části **deklarace identit aplikace**, klikněte na tlačítko **zobrazit více** a zvolte deklarace identit, které se mají vracet v autorizačních tokenech odesílaných zpět do aplikace po úspěšném resetování hesla prostředí. Vyberte například **ID objektu uživatele**.

Klikněte na **OK**.

![Výběr deklarací identit aplikace a kliknutí na tlačítko OK](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

Klikněte na tlačítko **vytvořit** přidat tok uživatele. Tok uživatele je uveden jako **B2C_1_SSPR**. K názvu se připojí předpona **B2C_1_**.

Klikněte na tlačítko **spustit tok uživatele**. Ověřte nastavení uvedená v tabulce a klikněte na **spustit tok uživatele**.

![Vyberte tok uživatele a spusťte jej](media/active-directory-b2c-create-password-reset-policy/add-b2c-sspr-run-user-flow.png)

| Nastavení      | Hodnota  |
| ------------ | ------ |
| **Aplikace** | Aplikace Contoso B2C |
| **Výběr adresy URL odpovědi** | `https://localhost:44316/` |

Otevře se nová karta prohlížeče a můžete zkontrolovat uživatelské prostředí pro resetování hesla ve vaší aplikaci.

> [!NOTE]
> Trvá až minutu, než se vytvoření zásady a aktualizace projeví.
>
