---
title: 'Kurz: Vynutit ověřování Multi-Factor Authentication pro uživatele typu Host Azure AD s B2B'
description: Zjistěte, jak vyžadovat vícefaktorové ověřování (MFA), když používáte B2B služby Azure AD ke spolupráci s externími uživateli a partnerskými organizacemi.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 88225ee7aeefd9030e754f2b1abc66e4c05e5c6b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431164"
---
# <a name="tutorial-enforce-multi-factor-authentication-for-b2b-guest-users"></a>Kurz: Vynutit ověřování Multi-Factor Authentication pro uživatele typu Host B2B

Když spolupracujete s externími uživateli typu host B2B, je vhodné si své aplikace chránit zásadami vícefaktorového ověřování (MFA). Externí uživatelé budou potřebovat víc než jen uživatelské jméno a heslo, aby získali přístup k vašim prostředkům. V Azure Active Directory (Azure AD) toho můžete dosáhnout pomocí zásad podmíněného přístupu, které pro přístup vyžadují vícefaktorové ověřování. Zásady vícefaktorového ověřování je možné vynucovat na úrovni tenanta, aplikace nebo jednotlivých uživatelů typu host úplně stejným způsobem, jako když se používají u členů vaší organizace.

Příklad:

![Aplikace B2B vyžadující vícefaktorové ověřování](media/tutorial-mfa/aad-b2b-mfa-example.png)

1.  Správce nebo zaměstnanec ve společnosti A pozve uživatele typu host, aby používal cloudovou nebo místní aplikaci, která je nakonfigurovaná tak, aby k přístupu vyžadovala vícefaktorové ověřování.
2.  Uživatel typu host se přihlásí pomocí pracovní, školní nebo sociální identity. 
3.  Potom se mu zobrazí výzva, aby dokončil výzvu vícefaktorového ověřování. 
4.  Uživatel si u společnosti A nastaví vícefaktorové ověřování a vybere si svou možnost. Uživatel k aplikaci získá přístup.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Před nastavením vícefaktorového ověřování otestujete přihlašovací prostředí.
> * Vytvoříte zásady podmíněného přístupu, které budou vyžadovat vícefaktorové ověřování pro přístup ke cloudové aplikaci ve vašem prostředí. V tomto kurzu budeme k znázornění procesu používat aplikaci Microsoft Azure Management.
> * Použijete nástroj What If k simulaci přihlášení pomocí vícefaktorového ověřování.
> * Otestujete zásady podmíněného přístupu.
> * Vymažete testovacího uživatele a zásady.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení scénáře v tomto kurzu budete potřebovat:

 - **Přístup k edici Azure AD Premium**, která umožňuje použití zásad podmíněného přístupu. Pokud chcete vícefaktorové ověřování vynutit, musíte vytvořit zásady podmíněného přístupu Azure AD. Nezapomeňte, že zásady vícefaktorového ověřování se ve vaší organizaci vynucují vždy – bez ohledu na to, jestli partner možnosti vícefaktorového ověřování má nebo ne. Když v organizaci vícefaktorové ověřování nastavíte, budete se muset přesvědčit, že pro své uživatele typu host máte dostatek licencí Azure AD Premium. 
 - **Platný externí e-mailový účet**, který můžete přidat do adresáře tenanta jako uživatele typu host a použít ho k přihlášení. Pokud nevíte, jak účet hosta vytvořit, přečtěte si článek o [přidání uživatele typu host B2B na webu Azure Portal](add-users-administrator.md).

## <a name="create-a-test-guest-user-in-azure-ad"></a>Vytvoření testovacího uživatele typu host v Azure AD

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/) jako správce.
2. V levém podokně vyberte **Azure Active Directory**.
3.  V části **Spravovat** vyberte **Uživatele**.
4.  Vyberte **Nový uživatel typu host**.

    ![Vyberte Azure Active Directory.](media/tutorial-mfa/tutorial-mfa-user-3.png)

5.  V části **Uživatelské jméno** zadejte e-mailovou adresu externího uživatele. Volitelně můžete zahrnout uvítací zprávu. 

    ![Vyberte Azure Active Directory.](media/tutorial-mfa/tutorial-mfa-user-4.png)

6.  Výběrem možnosti **Pozvat** automaticky odešlete uživateli typu host pozvánku. Zobrazí se zpráva **Uživatel je úspěšně pozvaný**. 
7.  Po odeslání e-mailové pozvánky se uživatelský účet automaticky přidá do adresáře jako uživatel typu host.

## <a name="test-the-sign-in-experience-before-mfa-setup"></a>Otestování přihlašovacího prostředí před nastavením vícefaktorového ověřování
1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí jména a hesla testovacího uživatele.
2.  Všimněte si, že k webu Azure Portal se přihlásíte pouze pomocí přihlašovacích údajů. Žádné další ověřování se nevyžaduje.
3.  Odhlaste se.

## <a name="create-a-conditional-access-policy-that-requires-mfa"></a>Vytvoření zásad podmíněného přístupu vyžadujících vícefaktorové ověřování
1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
2.  Na portálu Azure Portal vyberte **Azure Active Directory**. 
3.  Na stránce **Azure Active Directory** v části **Zabezpečení** vyberte **Podmíněný přístup**.
4.  Na stránce **Podmíněný přístup** vyberte nahoře na panelu nástrojů možnost **Nové zásady**.
5.  Do textového pole **Název** na stránce **Nový** zadejte **Vyžadovat vícefaktorové ověřování pro B2B přístup k webu Azure Portal**.
6.  V části **Přiřazení** vyberte **Uživatelé a skupiny**.
7.  Na stránce **Uživatelé a skupiny** zvolte **Vyberte uživatele a skupiny** a potom vyberte **Všichni uživatelé typu host (Preview)**.

    ![Vyberte Azure Active Directory.](media/tutorial-mfa/tutorial-mfa-policy-6.png)
9.  Vyberte **Done** (Hotovo).
10. V části **Přiřazení** na stránce **Nový** vyberte **Cloudové aplikace**.
11. Na stránce **Cloudové aplikace** zvolte **Vybrat aplikace** a pak zvolte **Vybrat**.

    ![Vyberte Azure Active Directory.](media/tutorial-mfa/tutorial-mfa-policy-10.png)

12. Na stránce **Vybrat** zvolte **Microsoft Azure Management** a potom zvolte **Vybrat**.

    ![Vyberte Azure Active Directory.](media/tutorial-mfa/tutorial-mfa-policy-11.png)

13. Na stránce **Cloudové aplikace** vyberte **Hotovo**.
14. V části **Ovládací prvky přístupu** na stránce **Nový** vyberte **Udělení**.
15. Na stránce **Udělení** zvolte **Udělit přístup**, vyberte zaškrtávací políčko **Vyžadovat vícefaktorové ověřování** a potom zvolte **Vybrat**.

    ![Vyberte Azure Active Directory.](media/tutorial-mfa/tutorial-mfa-policy-13.png)

16. V části **Povolit zásadu** vyberte **Zapnuté**.

    ![Vyberte Azure Active Directory.](media/tutorial-mfa/tutorial-mfa-policy-14.png)

17. Vyberte **Vytvořit**.

## <a name="use-the-what-if-option-to-simulate-sign-in"></a>Použití možnosti What If k simulaci přihlášení

1.  Na stránce **Podmíněný přístup – Zásady** vyberte **What If**. 

    ![Vyberte Azure Active Directory.](media/tutorial-mfa/tutorial-mfa-whatif-1.png)

2.  Vyberte **Uživatel**, zvolte svého testovacího uživatele typu host a potom zvolte **Vybrat**.

    ![Vyberte Azure Active Directory.](media/tutorial-mfa/tutorial-mfa-whatif-2.png)

3.  Vyberte **Cloudové aplikace**.
4.  Na stránce **Cloudové aplikace** zvolte **Vybrat aplikace** a pak klikněte na **Vybrat**. V seznamu aplikací vyberte **Microsoft Azure Management** a pak klikněte na **Vybrat**. 

    ![Vyberte Azure Active Directory.](media/tutorial-mfa/tutorial-mfa-whatif-3.png)

5.  Na stránce **Cloudové aplikace** vyberte **Hotovo**.
6.  Vyberte **What If** a ověřte, že se vaše nová zásada zobrazí na kartě **Zásady, které se použijí** v části **Výsledek hodnocení**.

    ![Vyberte Azure Active Directory.](media/tutorial-mfa/tutorial-mfa-whatif-4.png)

## <a name="test-your-conditional-access-policy"></a>Otestování zásady podmíněného přístupu
1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí jména a hesla testovacího uživatele.
2.  Měli byste vidět žádost o další metody ověřování. Nezapomeňte, že než se zásady projeví, může to nějakou dobu trvat.

    ![Vyberte Azure Active Directory.](media/tutorial-mfa/mfa-required.png)
 
3.  Odhlaste se.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Když už testovacího uživatele ani testovací zásady podmíněného přístupu nepotřebujete, můžete je odebrat.
1.  Přihlaste se na web [Azure Portal](https://portal.azure.com/) jako správce.
2.  V levém podokně vyberte **Azure Active Directory**.
3.  V části **Spravovat** vyberte **Uživatele**.
4.  Vyberte testovacího uživatele a potom vyberte **Odstranit uživatele**.
5.  V levém podokně vyberte **Azure Active Directory**.
6.  V části **Zabezpečení** vyberte **Podmíněný přístup**.
7.  Na seznamu **Název zásady** vyberte u testovací zásady místní nabídku (...) a pak vyberte **Odstranit**. Odstranění potvrďte výběrem **Ano**.
## <a name="next-steps"></a>Další postup
V tomto kurzu jste vytvořili zásady podmíněného přístupu, které vyžadují, aby uživatelé typu host používali při přihlašování k jedné z vašich cloudových aplikací vícefaktorové ověřování. Další informace o přidávání uživatelů typu host ke spolupráci najdete v článku o [přidávání uživatelů pro spolupráci B2B služby Azure Active Directory na webu Azure Portal](add-users-administrator.md).
