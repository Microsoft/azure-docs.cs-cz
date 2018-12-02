---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 17c0213d63879687e9c6d5f8dca06b9113c44af8
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742561"
---
Pokud chcete povolit pouze registraci ve vaší aplikaci, můžete použít **registrace** tok uživatele. Tento tok uživatele popisuje prostředí, které zákazníkům procházet při registraci a obsah tokenů, které aplikace obdrží po úspěšné registraci.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

V části **spravovat**vyberte **toky uživatelů**.

Klikněte na +**nový tok uživatele** v horní části okna.

V části **vybrat typ toku uživatele**vyberte **všechny**a pak vyberte verzi **zaregistrovat** chcete použít.

**Název** Určuje název toku uživatele registrace používaný vaší aplikací. Zadejte například **SiUp**.

V části **zprostředkovatelé Identity**vyberte **e-mailová registrace**. Volitelně můžete vybrat také zprostředkovatele sociální identity, pokud už jsou nakonfigurováni.

V části **atributy uživatele a deklarace identity**, klikněte na tlačítko **zobrazit více**.

V **shromažďování atribut** sloupce, vyberte atributy, které chcete shromažďovat od uživatele během registrace. Vyberte například **Země/oblast**, **Zobrazované jméno** a **PSČ**.

V **návratový deklarace identity** sloupce, zvolte deklarace identit, které se mají vracet v tokenech odesílaných zpět do aplikace po úspěšné registraci. Vyberte například **Zobrazované jméno**, **Zprostředkovatel identity**, **PSČ**, **Uživatel je nový** a **ID objektu uživatele**.

Klikněte na **OK**.

Klikněte na možnost **Vytvořit**. Vytvořit tok uživatele se zobrazí jako **B2C_1_SiUp** ( **B2C\_1\_**  se automaticky přidá fragment).

Klikněte na tlačítko **spustit tok uživatele**.

Vyberte **aplikace Contoso B2C** v **aplikace** rozevíracího seznamu a `https://localhost:44321/` v **adresy URL odpovědi** rozevíracího seznamu.

Klikněte na tlačítko **spustit tok uživatele**. Otevře se nová karta prohlížeče a můžete si projít uživatelské prostředí pro registraci ve vaší aplikaci.

> [!NOTE]
> Trvá minutu pro vytvoření toku uživatele a aktualizace se projeví.
>