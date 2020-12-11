---
title: Přidání podmíněného přístupu do toku uživatele v Azure AD B2C
description: Naučte se, jak přidat podmíněný přístup k Azure AD B2C toků uživatelů. Nakonfigurujte nastavení vícefaktorového ověřování (MFA) a zásady podmíněného přístupu ve vašich uživatelských tocích, abyste vynutili zásady a napravili rizikové přihlášení.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c120f343ec539783f04fe35e96891c5372c5d39
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109075"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Přidání podmíněného přístupu do toků uživatelů v Azure Active Directory B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Podmíněný přístup se dá přidat do Azure Active Directory B2C toků uživatelů ke správě rizikových přihlášení k vašim aplikacím. Integrace Identity Protection a podmíněného přístupu v Azure AD B2C umožňuje nastavit zásady, které identifikují rizikové přihlašování, a vynutit zásady, které vyžadují další zásah uživatele nebo správce. Jedná se o komponenty, které umožňují podmíněný přístup v Azure AD B2C toky uživatelů:

- **Tok uživatele**. Vytvořte uživatelský tok, který provede uživatele prostřednictvím procesu přihlášení a registrace. V nastavení toku uživatele určete, jestli se mají aktivovat zásady podmíněného přístupu, když uživatel postupuje podle toku uživatele.
- **Aplikace, která uživatele přesměruje na tok uživatele**. Nakonfigurujte svou aplikaci tak, aby uživatelům směrovala příslušný uživatelský tok pro registraci a přihlašování zadáním koncového bodu toku uživatele v aplikaci.
- **Zásady podmíněného přístupu**. [Vytvořte zásady podmíněného přístupu](conditional-access-identity-protection-setup.md) a určete aplikace, pro které chcete zásady použít. Když uživatel postupuje při přihlašování nebo registraci uživatelského toku vaší aplikace, používá zásady podmíněného přístupu signály ochrany identity k identifikaci rizikových přihlášení a v případě potřeby prezentuje příslušné akce při nápravě.

Podmíněný přístup je podporován v nejnovějších verzích uživatelských toků. Zásady podmíněného přístupu můžete přidat do nových toků uživatelů při jejich vytváření nebo je můžete přidat do stávajících toků uživatelů, pokud verze podporuje podmíněný přístup. Při přidávání podmíněného přístupu do stávajícího toku uživatele je třeba zkontrolovat dvě nastavení:

- **Multi-Factor Authentication (MFA)**: uživatelé teď můžou používat jednorázový kód prostřednictvím SMS nebo hlasu nebo jednorázové heslo e-mailem pro službu Multi-Factor Authentication. Nastavení MFA jsou nezávislá na nastavení podmíněného přístupu. Vícefaktorové ověřování můžete nastavit na **vždycky zapnuto** , aby MFA se vždycky vyžadovalo bez ohledu na nastavení podmíněného přístupu. Nebo můžete nastavit vícefaktorové ověřování na **podmíněné** , aby se MFA vyžadovalo jenom v případě, že to vyžaduje zásada podmíněného přístupu.

- **Podmíněný přístup**: Toto nastavení by mělo být vždycky **zapnuté**. Obvykle byste **Toto nastavení zapnuli** během řešení potíží nebo migrace nebo pro starší implementace.

Přečtěte si další informace o [identitě a podmíněném přístupu](conditional-access-identity-protection-overview.md) v Azure AD B2C, nebo se podívejte, [jak ji nastavit](conditional-access-identity-protection-setup.md).

## <a name="add-conditional-access-to-a-new-user-flow"></a>Přidání podmíněného přístupu do nového toku uživatele

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **toky uživatelů** a pak vyberte **Nový tok uživatele**.
1. Na stránce **vytvořit tok uživatele** vyberte typ toku uživatele.
1. V části **vybrat verzi** vyberte **doporučeno** a pak vyberte **vytvořit**. (Další[informace](user-flow-versions.md) o verzích toku uživatele)

    ![Stránka vytvoření toku uživatele v Azure Portal se zvýrazněnými vlastnostmi](./media/tutorial-create-user-flows/select-version.png)

1. Zadejte **název** toku uživatele. Například *signupsignin1*.
1. V části **Zprostředkovatelé identity** vyberte zprostředkovatele identity, které chcete pro tento tok uživatelů použít.
2. V části vícefaktorového **ověřování** vyberte požadovanou **metodu MFA** a potom v části **vynucení MFA** vyberte **podmíněný (doporučeno)**.
 
   ![Konfigurace vícefaktorového ověřování](media/conditional-access-user-flow/configure-mfa.png)

1. V části **podmíněný přístup** zaškrtněte políčko **vyhovět zásadám podmíněného přístupu** .

   ![Konfigurace nastavení podmíněného přístupu](media/conditional-access-user-flow/configure-conditional-access.png)

1. V části **atributy a deklarace identity uživatele** vyberte deklarace identity a atributy, které chcete shromáždit a odeslat uživateli během registrace. Vyberte například možnost **Zobrazit více** a pak zvolte možnost atributy a deklarace identity pro **zemi nebo oblast** a **zobrazované jméno**. Vyberte **OK**.

    ![Stránka pro výběr atributů a deklarací se třemi vybranými deklaracemi](./media/conditional-access-user-flow/configure-user-attributes-claims.png)

1. Kliknutím na **vytvořit** přidejte tok uživatele. Předpona *B2C_1* je automaticky Předpona názvu.

## <a name="add-conditional-access-to-an-existing-user-flow"></a>Přidání podmíněného přístupu do existujícího toku uživatele

> [!NOTE]
> Stávající tok uživatele musí být verze, která podporuje podmíněný přístup. Tyto verze uživatelských toků jsou označeny jako **Doporučené**.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.

1. V části **zásady** vyberte **toky uživatelů**. Pak vyberte tok uživatele.

1. Vyberte **vlastnosti** a ujistěte se, že tok uživatele podporuje podmíněný přístup, a to tak, že vybere **vlastnosti** a vyhledá nastavení s označením **podmíněný přístup**.
 
   ![Konfigurace vícefaktorového ověřování a podmíněného přístupu ve vlastnostech](media/conditional-access-user-flow/add-conditional-access.png)

1. V části vícefaktorového **ověřování** vyberte požadovanou **metodu MFA** a potom v části **vynucení MFA** vyberte **podmíněný (doporučeno)**.
 
1. V části **podmíněný přístup** zaškrtněte políčko **vyhovět zásadám podmíněného přístupu** .

1. Vyberte **Uložit**.

## <a name="test-the-user-flow"></a>Testování toku uživatele

Pokud chcete v toku uživatele otestovat podmíněný přístup, [Vytvořte zásady podmíněného přístupu](conditional-access-identity-protection-setup.md) a v toku uživatele povolte podmíněný přístup, jak je popsáno výše. 

### <a name="prerequisites"></a>Předpoklady

- Pro vytváření zásad rizikového přihlašování se vyžaduje Azure AD B2C Premium 2. Klienti Premium P1 můžou vytvářet umístění, aplikace nebo zásady na základě skupin.
- Pro účely testování můžete [zaregistrovat testovací webovou aplikaci](tutorial-register-applications.md) `https://jwt.ms` , což je webová aplikace vlastněná společností Microsoft, která zobrazuje dekódování obsahu tokenu (obsah tokenu nikdy nezůstane v prohlížeči). 
- Chcete-li simulovat rizikové přihlašování, Stáhněte si prohlížeč pro mandát a pokuste se přihlásit ke koncovému bodu toku uživatele.
- Pomocí následujících nastavení [Vytvořte zásady podmíněného přístupu](conditional-access-identity-protection-setup.md):
   
   - Pro **uživatele a skupiny** vyberte testovacího uživatele (nevybírejte možnost **Všichni uživatelé** nebo jste se mohli zablokovat přihlášení).
   - U **cloudových aplikací nebo akcí** zvolte **vybrat aplikace** a pak zvolte svoji aplikaci předávající strany.
   - V případě podmínek vyberte možnost **rizika přihlášení** a **Vysoká**, **střední** a **Nízká** úroveň rizika.
   - V případě **udělení** vyberte možnost **blokovat přístup**.

      ![Detekce rizik](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

### <a name="run-the-user-flow"></a>Spuštění toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**. V části **aplikace** vyberte *WebApp1*. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .

   ![Stránka spustit tok uživatele na portálu s zvýrazněným tlačítkem Spustit uživatele toku](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Zkopírujte adresu URL v části **Spustit koncový bod toku uživatele**.

1. Chcete-li simulovat rizikové přihlašování, otevřete prohlížeč pro [mandát](https://www.torproject.org/download/) a pomocí adresy URL, kterou jste zkopírovali v kroku Preview, se přihlaste k registrované aplikaci.

1. Na přihlašovací stránce zadejte požadované informace a pokuste se o přihlášení. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat. V tokenu Dekódovatelné v jwt.ms byste měli vidět, že přihlášení bylo blokované:

   ![Otestování blokovaného přihlášení](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="next-steps"></a>Další kroky

[Přizpůsobení uživatelského rozhraní v Azure AD B2C toku uživatele](customize-ui-with-html.md)
