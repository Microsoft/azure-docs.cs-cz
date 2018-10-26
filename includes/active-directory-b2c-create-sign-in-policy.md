---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 19e7c919345c0f56b274737840f8150f7d710501
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133049"
---
Pokud chcete povolit pouze přihlásit se na svou aplikaci, můžete použít **přihlášení** zásad. Tato zásada popisuje prostředí, které zákazníci budou procházet při přihlašování a obsah tokenů, které bude aplikace přijímat po na úspěšně přihlášení.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
Klikněte na tlačítko **zásady přihlašování**.

Klikněte na **Přidat** v horní části okna.

**Název** určuje název zásady přihlašování používaný vaší aplikací. Zadejte například **SiIn**.

Klikněte na **Zprostředkovatelé identity** a vyberte **Přihlášení místním účtem**. Volitelně můžete vybrat také zprostředkovatele sociální identity, pokud už jsou nakonfigurováni. Klikněte na **OK**.

Klikněte na **Deklarace identit aplikace**. Tady zvolíte deklarace identit, které se mají vracet v tokenech odesílaných zpět do aplikace po úspěšném přihlášení. Vyberte například **Zobrazované jméno**, **Zprostředkovatel identity**, **PSČ** a **ID objektu uživatele**. Klikněte na **OK**.

Klikněte na možnost **Vytvořit**. Všimněte si, že právě vytvořená zásada se zobrazí jako **B2C_1_SiIn** (fragment **B2C\_1\_** se přidá automaticky) v okně **Zásady přihlašování**.

Otevřete zásadu kliknutím na **B2C_1_SiIn**.

V rozevíracím seznamu **Aplikace** vyberte **Aplikace Contoso B2C** a v rozevíracím seznamu **Adresa URL odpovědi / Identifikátor URI přesměrování** vyberte `https://localhost:44321/`.

Klikněte na **Spustit**. Otevře se nová karta prohlížeče a můžete si projít uživatelské prostředí pro přihlášení k vaší aplikaci.

> [!NOTE]
> Trvá až minutu, než se vytvoření zásady a aktualizace projeví.
>