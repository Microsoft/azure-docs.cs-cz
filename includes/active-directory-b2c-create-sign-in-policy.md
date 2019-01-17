---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0ab34d6234db9c13ffe82ccd0e8580217085f631
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355460"
---
Pokud chcete povolit pouze přihlásit se na svou aplikaci, můžete použít **přihlášení** tok uživatele. Tento tok uživatele popisuje prostředí, které zákazníci budou procházet při přihlašování a obsah tokenů, které bude aplikace přijímat po na úspěšně přihlášení.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
V části **spravovat**vyberte **toky uživatelů**.

Klikněte na +**nový tok uživatele** v horní části okna.

V části **vybrat typ toku uživatele**vyberte **všechny**a pak vyberte verzi **přihlášení** chcete použít.

**Název** Určuje název toku uživatele přihlašování používaný vaší aplikací. Zadejte například **SiIn**.

V části **zprostředkovatelé Identity**, vyberete příslušnou možnost. Můžete také vybrat zprostředkovatele sociální identity, pokud už nakonfigurovaná. Klikněte na **OK**.

V části **deklarace identit aplikace**, klikněte na tlačítko **zobrazit více**.

V **návratový deklarace identity** sloupce, zvolte deklarace identit, které se mají vracet v tokenech odesílaných zpět do aplikace po úspěšné prostředí přihlášení. Vyberte například **Zobrazované jméno**, **Zprostředkovatel identity**, **PSČ** a **ID objektu uživatele**. Klikněte na **OK**.

Klikněte na možnost **Vytvořit**. Všimněte si, že právě vytvořený tok uživatele se zobrazí jako **B2C_1_SiIn** ( **B2C\_1\_**  se automaticky přidá fragment).

Klikněte na tlačítko **spustit tok uživatele**.

Vyberte **aplikace Contoso B2C** v **aplikace** rozevíracího seznamu a `https://localhost:44321/` v **adresy URL odpovědi** rozevíracího seznamu.

Klikněte na tlačítko **spustit tok uživatele**. Otevře se nová karta prohlížeče a můžete si projít uživatelské prostředí pro přihlášení k vaší aplikaci.

> [!NOTE]
> Trvá minutu pro vytvoření toku uživatele a aktualizace se projeví.
>