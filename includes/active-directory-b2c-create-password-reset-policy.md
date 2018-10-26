---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: bff2543ec48c66c10db697650def0077e3de28be
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134122"
---
Pokud chcete povolit jemné resetování hesla na aplikace, použijete **resetování hesla** zásad. Všimněte si, že možnost resetování hesel v rámci celého tenanta je specifikovaná [zde](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md). Tato zásada popisuje prostředí, které zákazníci budou procházet při resetování hesla a obsah tokenů, které bude aplikace přijímat po úspěšném dokončení.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

V nastavení v části Zásady vyberte **Zásady resetování hesel** a klikněte na **+ Přidat**.

![Výběr zásad registrace nebo přihlášení a kliknutí na tlačítko Přidat](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-policy.png)

Zadejte **Název** zásady, který v aplikaci použijete jako referenci. Zadejte například `SSPR`.

Vyberte **Zprostředkovatelé identity** a zaškrtněte políčko **Resetovat heslo pomocí e-mailové adresy**. Klikněte na **OK**.

![Výběr možnosti Resetovat heslo pomocí e-mailové adresy jako zprostředkovatele identity a kliknutí na tlačítko OK](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

Vyberte **Deklarace identit aplikace**. Zvolte deklarace identit, které se mají vracet v autorizačních tokenech odesílaných zpět do aplikace po úspěšném resetování hesel. Vyberte například **ID objektu uživatele**.

![Výběr deklarací identit aplikace a kliknutí na tlačítko OK](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

Kliknutím na **Vytvořit** přidejte zásadu. Zásada se zobrazí jako **B2C_1_SSPR**. K názvu se připojí předpona **B2C_1_**.

Otevřete zásadu výběrem **B2C_1_SSPR**. Ověřte nastavení uvedená v tabulce a potom klikněte na **Spustit**.

![Výběr a spuštění zásady](media/active-directory-b2c-create-password-reset-policy/run-b2c-password-reset-policy.png)

| Nastavení      | Hodnota  |
| ------------ | ------ |
| **Aplikace** | Aplikace Contoso B2C |
| **Výběr adresy URL odpovědi** | `https://localhost:44316/` |

Otevře se nová karta prohlížeče a můžete zkontrolovat uživatelské prostředí pro resetování hesla ve vaší aplikaci.

> [!NOTE]
> Trvá až minutu, než se vytvoření zásady a aktualizace projeví.
>
