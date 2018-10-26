---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 511b05e6cae769a5b39ae81a3e67efd05d374511
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133338"
---
Pokud chcete povolit pouze registraci ve vaší aplikaci, můžete použít **registrace** zásad. Tato zásada popisuje prostředí, které zákazníkům procházet při registraci a obsah tokenů, které aplikace obdrží po úspěšné registraci.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Klikněte na **Zásady registrace**.

Klikněte na **Přidat** v horní části okna.

**Název** určuje název zásady registrace používaný vaší aplikací. Zadejte například **SiUp**.

Klikněte na **Zprostředkovatelé identity** a vyberte **Registrace pomocí e-mailu**. Volitelně můžete vybrat také zprostředkovatele sociální identity, pokud už jsou nakonfigurováni. Klikněte na **OK**.

Klikněte na **Atributy registrace**. Tady zvolíte atributy, které od uživatele chcete během registrace shromažďovat. Vyberte například **Země/oblast**, **Zobrazované jméno** a **PSČ**. Klikněte na **OK**.

Klikněte na **Deklarace identit aplikace**. Tady zvolíte deklarace identit, které se mají vracet v tokenech odesílaných zpět do aplikace po úspěšné registraci. Vyberte například **Zobrazované jméno**, **Zprostředkovatel identity**, **PSČ**, **Uživatel je nový** a **ID objektu uživatele**.

Klikněte na možnost **Vytvořit**. Vytvořená zásada se zobrazí jako **B2C_1_SiUp** (fragment **B2C\_1\_** se přidá automaticky) v okně **Zásady registrace**.

Otevřete zásadu kliknutím na **B2C_1_SiUp**.

V rozevíracím seznamu **Aplikace** vyberte **Aplikace Contoso B2C** a v rozevíracím seznamu **Adresa URL odpovědi / Identifikátor URI přesměrování** vyberte `https://localhost:44321/`.

Klikněte na **Spustit**. Otevře se nová karta prohlížeče a můžete si projít uživatelské prostředí pro registraci ve vaší aplikaci.

> [!NOTE]
> Trvá až minutu, než se vytvoření zásady a aktualizace projeví.
>