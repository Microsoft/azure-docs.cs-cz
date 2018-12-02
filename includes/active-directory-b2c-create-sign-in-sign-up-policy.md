---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: f23d2b02bc2a23c5333a48a50532c03f3aa6a031
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742397"
---
[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

V části **spravovat**vyberte **toky uživatelů** a klikněte na +**nový tok uživatele**.

![Vyberte nový tok uživatele](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow.png)

Na **doporučená** kartu, vyberte možnost **podepsat a přihlašování**.

![Vyberte si a přihlaste se tok uživatele.](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow-type.png)

Zadejte tok uživatele **název** pro vaši aplikaci odkazovat. Zadejte například `SiUpIn`.

V části **zprostředkovatelé Identity** a zkontrolujte **e-mailová registrace**. Volitelně můžete vybrat také zprostředkovatele sociální identity, pokud už jsou nakonfigurováni.

V části **Vícefaktorové ověřování**, zvolte buď **povoleno** nebo **zakázané**.

![Zadejte název a vyberte přihlášení místním účtem jako zprostředkovatele identity](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-name-identity-providers.png)

V části **atributy uživatele a deklarace identity**vyberte **zobrazit více** zobrazíte úplný seznam atributů a můžete si vybrat z deklarací identity.

V **shromažďování atribut** sloupce, vyberte atributy, které chcete shromažďovat od uživatele během registrace. Zaškrtněte například políčka **Země/oblast**, **Zobrazované jméno** a **PSČ**.

V **návratový deklarace identity** sloupce, zvolte deklarace identit, které se mají vracet v autorizačních tokenech odesílaných zpět do aplikace po úspěšné registraci nebo přihlašování prostředí. Vyberte například **Zobrazované jméno**, **Zprostředkovatel identity**, **PSČ**, **Uživatel je nový** a **ID objektu uživatele**.

Klikněte na **OK**.

![Vyberte některé atributy uživatele a deklarace identity a klikněte na tlačítko OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-all-attributes.png)

Klikněte na tlačítko **vytvořit** přidat tok uživatele. Tok uživatele je uveden jako **B2C_1_SiUpIn**. K názvu se připojí předpona **B2C_1_**.

Vyberte **spustit tok uživatele**. Ověřte nastavení uvedená v tabulce a klikněte na **spustit tok uživatele**.

![Vyberte možnost spustit tok uživatele.](media/active-directory-b2c-create-sign-in-sign-up-policy/run-user-flow-b2c-signup-signin.png)

| Nastavení      | Hodnota  |
| ------------ | ------ |
| **Aplikace** | Aplikace Contoso B2C |
| **Adresa URL odpovědi** | `https://localhost:44316/` |

Otevře se nová karta prohlížeče a můžete zkontrolovat nakonfigurované uživatelské prostředí pro registraci a přihlášení.

> [!NOTE]
> Trvá minutu pro vytvoření toku uživatele a aktualizace se projeví.
>