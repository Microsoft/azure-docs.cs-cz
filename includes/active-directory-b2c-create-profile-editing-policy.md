---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0d9f0a24d84bd18bdf1fac84c744cc34a7d89ab3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742513"
---
Pokud chcete povolit upravování profilu ve své aplikaci, můžete použít **upravování profilu** tok uživatele. Tento tok uživatele popisuje prostředí, které zákazníci budou procházet při upravování profilu a obsah tokenů, které bude aplikace přijímat po úspěšném dokončení.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

V části **spravovat**vyberte **toky uživatelů** a klikněte na +**nový tok uživatele**.

![Vyberte nový tok uživatele](media/active-directory-b2c-create-profile-editing-policy/add-b2c-new-user-flow.png)

Na **doporučená** kartu, vyberte možnost **upravování profilu**.

Zadejte tok uživatele **název** pro vaši aplikaci odkazovat. Zadejte například `SiPe`.

V části **zprostředkovatelé Identity**, zkontrolujte **přihlášení místním účtem**. Volitelně můžete vybrat také zprostředkovatele sociální identity, pokud už jsou nakonfigurováni.

![Výběr možnosti Přihlášení místním účtem jako zprostředkovatele identity a kliknutí na tlačítko OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-identity-providers.png)

V části **atributy uživatele**, klikněte na tlačítko **zobrazit více**. V **shromažďování atribut** sloupce, vyberte atributy, můžete zobrazit a upravit příjemce ve svém profilu. Zaškrtněte například políčka **Země/oblast**, **Zobrazované jméno** a **PSČ**.

V **návratový deklarace identity** sloupce, zvolte deklarace identit, které se mají vracet v autorizačních tokenech odesílaných zpět do aplikace po úspěšném upravování profilu. Vyberte například **Zobrazované jméno** a **PSČ**.

Klikněte na **OK**.

![Výběr deklarací identit aplikace a kliknutí na tlačítko OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-user-attributes.png)

Klikněte na tlačítko **vytvořit** přidat tok uživatele. Tok uživatele je uveden jako **B2C_1_SiPe**. K názvu se připojí předpona **B2C_1_**.

Vyberte **spustit tok uživatele**. Ověřte nastavení uvedená v tabulce a klikněte na **spustit tok uživatele**.

![Vyberte tok uživatele a spusťte jej](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-run-user-flow.png)

| Nastavení      | Hodnota  |
| ------------ | ------ |
| **Aplikace** | Aplikace Contoso B2C |
| **Adresa URL odpovědi** | `https://localhost:44316/` |

Otevře se nová karta prohlížeče a můžete zkontrolovat nakonfigurované uživatelské prostředí pro upravování profilu.

> [!NOTE]
> Trvá minutu pro vytvoření toku uživatele a aktualizace se projeví.
>