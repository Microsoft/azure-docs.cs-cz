---
title: Kurz – vícefaktorové ověřování pro B2B – Azure AD
description: V tomto kurzu se dozvíte, jak vyžadovat vícefaktorové ověřování (MFA) při použití Azure AD B2B ke spolupráci s externími uživateli a partnerskými organizacemi.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bddf1642b2013567fbc23278b3d8d32692601d55
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74420593"
---
# <a name="tutorial-enforce-multi-factor-authentication-for-b2b-guest-users"></a>Kurz: Vynucení vícefaktorového ověřování pro uživatele typu host B2B

Když spolupracujete s externími uživateli typu host B2B, je vhodné si své aplikace chránit zásadami vícefaktorového ověřování (MFA). Externí uživatelé budou potřebovat víc než jen uživatelské jméno a heslo, aby získali přístup k vašim prostředkům. Ve službě Azure Active Directory (Azure AD) můžete tohoto cíle dosáhnout pomocí zásad podmíněného přístupu, které vyžadují vícefaktorové zabezpečení pro přístup. Zásady vícefaktorového ověřování je možné vynucovat na úrovni tenanta, aplikace nebo jednotlivých uživatelů typu host úplně stejným způsobem, jako když se používají u členů vaší organizace.

Příklad:

![Diagram znázorňující uživatele typu Host, který se přihlašuje k firemním aplikacím](media/tutorial-mfa/aad-b2b-mfa-example.png)

1.  Správce nebo zaměstnanec ve společnosti A pozve uživatele typu host, aby používal cloudovou nebo místní aplikaci, která je nakonfigurovaná tak, aby k přístupu vyžadovala vícefaktorové ověřování.
2.  Uživatel typu host se přihlásí pomocí pracovní, školní nebo sociální identity. 
3.  Potom se mu zobrazí výzva, aby dokončil výzvu vícefaktorového ověřování. 
4.  Uživatel si u společnosti A nastaví vícefaktorové ověřování a vybere si svou možnost. Uživatel k aplikaci získá přístup.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Před nastavením vícefaktorového ověřování otestujete přihlašovací prostředí.
> * Vytvořte zásadu podmíněného přístupu, která vyžaduje vícefaktorové zabezpečení pro přístup ke cloudové aplikaci ve vašem prostředí. V tomto kurzu budeme k znázornění procesu používat aplikaci Microsoft Azure Management.
> * Použijete nástroj What If k simulaci přihlášení pomocí vícefaktorového ověřování.
> * Otestujte zásady podmíněného přístupu.
> * Vymažete testovacího uživatele a zásady.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení scénáře v tomto kurzu budete potřebovat:

 - **Přístup k edici Azure AD Premium**, která zahrnuje možnosti zásad podmíněného přístupu. Chcete-li vynutit vícefaktorové zabezpečení, musíte vytvořit zásady podmíněného přístupu Azure AD. Nezapomeňte, že zásady vícefaktorového ověřování se ve vaší organizaci vynucují vždy – bez ohledu na to, jestli partner možnosti vícefaktorového ověřování má nebo ne. Když v organizaci vícefaktorové ověřování nastavíte, budete se muset přesvědčit, že pro své uživatele typu host máte dostatek licencí Azure AD Premium. 
 - **Platný externí e-mailový účet**, který můžete přidat do adresáře tenanta jako uživatele typu host a použít ho k přihlášení. Pokud nevíte, jak účet hosta vytvořit, přečtěte si článek o [přidání uživatele typu host B2B na webu Azure Portal](add-users-administrator.md).

## <a name="create-a-test-guest-user-in-azure-ad"></a>Vytvoření testovacího uživatele typu host v Azure AD

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/) jako správce.
2. V levém podokně vyberte **Azure Active Directory**.
3.  V části **Spravovat** vyberte **Uživatele**.
4.  Vyberte **Nový uživatel typu host**.

    ![Snímek obrazovky s výběrem možnosti Nový uživatel hosta](media/tutorial-mfa/tutorial-mfa-user-3.png)

5.  V části **Uživatelské jméno** zadejte e-mailovou adresu externího uživatele. Volitelně můžete zahrnout uvítací zprávu. 

    ![Snímek obrazovky s cílem zadat zprávu pozvánky hosta](media/tutorial-mfa/tutorial-mfa-user-4.png)

6.  Výběrem možnosti **Pozvat** automaticky odešlete uživateli typu host pozvánku. Zobrazí se zpráva **Uživatel je úspěšně pozvaný**. 
7.  Po odeslání e-mailové pozvánky se uživatelský účet automaticky přidá do adresáře jako uživatel typu host.

## <a name="test-the-sign-in-experience-before-mfa-setup"></a>Otestování přihlašovacího prostředí před nastavením vícefaktorového ověřování
1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí jména a hesla testovacího uživatele.
2.  Všimněte si, že k webu Azure Portal se přihlásíte pouze pomocí přihlašovacích údajů. Žádné další ověřování se nevyžaduje.
3.  Odhlaste se.

## <a name="create-a-conditional-access-policy-that-requires-mfa"></a>Vytvoření zásady podmíněného přístupu, která vyžaduje vícefaktorové povolení
1.  Přihlaste se na [svůj portál Azure](https://portal.azure.com/) jako správce zabezpečení nebo správce podmíněného přístupu.
2.  Na portálu Azure Portal vyberte **Azure Active Directory**. 
3.  Na stránce **Azure Active Directory** vyberte v části **Zabezpečení** **podmíněný přístup**.
4.  Na stránce **Podmíněný přístup** vyberte nahoře na panelu nástrojů možnost **Nové zásady**.
5.  Do textového pole **Název** na stránce **Nový** zadejte **Vyžadovat vícefaktorové ověřování pro B2B přístup k webu Azure Portal**.
6.  V části **Přiřazení** vyberte **Uživatelé a skupiny**.
7.  Na stránce **Uživatelé a skupiny** zvolte **Vyberte uživatele a skupiny** a potom vyberte **Všichni uživatelé typu host (Preview)**.

    ![Snímek obrazovky s výběrem všech uživatelů typu Host](media/tutorial-mfa/tutorial-mfa-policy-6.png)
9.  Vyberte **Done** (Hotovo).
10. V části **Přiřazení** na stránce **Nový** vyberte **Cloudové aplikace**.
11. Na stránce **Cloudové aplikace** zvolte **Vybrat aplikace** a pak zvolte **Vybrat**.

    ![Snímek obrazovky se stránkou Cloudové aplikace a možností Vybrat](media/tutorial-mfa/tutorial-mfa-policy-10.png)

12. Na stránce **Vybrat** zvolte **Microsoft Azure Management** a potom zvolte **Vybrat**.

    ![Snímek obrazovky s vybranou aplikací Microsoft Azure Management](media/tutorial-mfa/tutorial-mfa-policy-11.png)

13. Na stránce **Cloudové aplikace** vyberte **Hotovo**.
14. V části **Ovládací prvky přístupu** na stránce **Nový** vyberte **Udělení**.
15. Na stránce **Udělení** zvolte **Udělit přístup**, vyberte zaškrtávací políčko **Vyžadovat vícefaktorové ověřování** a potom zvolte **Vybrat**.

    ![Snímek obrazovky s možností Vyžadovat vícefaktorové ověřování](media/tutorial-mfa/tutorial-mfa-policy-13.png)

16. V části **Povolit zásadu** vyberte **Zapnuté**.

    ![Snímek obrazovky s možností Povolit zásady nastavenou na Zapnuto](media/tutorial-mfa/tutorial-mfa-policy-14.png)

17. Vyberte **Vytvořit**.

## <a name="use-the-what-if-option-to-simulate-sign-in"></a>Použití možnosti What If k simulaci přihlášení

1.  Na stránce **Podmíněný přístup – zásady** vyberte **what if**. 

    ![Snímek obrazovky s výběrem možnosti Co-li](media/tutorial-mfa/tutorial-mfa-whatif-1.png)

2.  Vyberte **Uživatel**, zvolte svého testovacího uživatele typu host a potom zvolte **Vybrat**.

    ![Snímek obrazovky s vybraným uživatelem typu Host](media/tutorial-mfa/tutorial-mfa-whatif-2.png)

3.  Vyberte **Cloudové aplikace**.
4.  Na stránce **Cloudové aplikace** zvolte **Vybrat aplikace** a pak klikněte na **Vybrat**. V seznamu aplikací vyberte **Microsoft Azure Management** a pak klikněte na **Vybrat**. 

    ![Snímek obrazovky s vybranou aplikací Microsoft Azure Management](media/tutorial-mfa/tutorial-mfa-whatif-3.png)

5.  Na stránce **Cloudové aplikace** vyberte **Hotovo**.
6.  Vyberte **What If** a ověřte, že se vaše nová zásada zobrazí na kartě **Zásady, které se použijí** v části **Výsledek hodnocení**.

    ![Snímek obrazovky s výběrem možnosti Co-li](media/tutorial-mfa/tutorial-mfa-whatif-4.png)

## <a name="test-your-conditional-access-policy"></a>Otestujte zásady podmíněného přístupu
1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí jména a hesla testovacího uživatele.
2.  Měli byste vidět žádost o další metody ověřování. Nezapomeňte, že než se zásady projeví, může to nějakou dobu trvat.

    ![Snímek obrazovky s textovou zprávou Další informace požadované](media/tutorial-mfa/mfa-required.png)
 
3.  Odhlaste se.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud již není potřeba, odeberte testovacího uživatele a zásady podmíněného přístupu test.
1.  Přihlaste se na web [Azure Portal](https://portal.azure.com/) jako správce.
2.  V levém podokně vyberte **Azure Active Directory**.
3.  V části **Spravovat** vyberte **Uživatele**.
4.  Vyberte testovacího uživatele a potom vyberte **Odstranit uživatele**.
5.  V levém podokně vyberte **Azure Active Directory**.
6.  V části **Zabezpečení** vyberte **Podmíněný přístup**.
7.  Na seznamu **Název zásady** vyberte u testovací zásady místní nabídku (...) a pak vyberte **Odstranit**. Výběrem **Ano** potvrďte.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili zásady podmíněného přístupu, které vyžadují, aby uživatelé typu Host používali vícefaktorové povolení při přihlašování k jedné z vašich cloudových aplikací. Další informace o přidávání uživatelů typu host ke spolupráci najdete v článku o [přidávání uživatelů pro spolupráci B2B služby Azure Active Directory na webu Azure Portal](add-users-administrator.md).
