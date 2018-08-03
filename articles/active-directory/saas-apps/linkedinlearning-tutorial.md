---
title: 'Kurz: Integrace Azure Active Directory s Linkedinem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 02e7d9d26b389e82365f3447cceb5566244236f1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440904"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Kurz: Integrace Azure Active Directory s Linkedinem

V tomto kurzu se dozvíte, jak integrovat LinkedIn Learning s Azure Active Directory (Azure AD).

Integrace LinkedIn Learning s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Linkedinem
- Uživatele, aby automaticky získat podepsaný ve službě LinkedIn Learning (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Linkedinem, potřebujete následující položky:

- S předplatným služby Azure AD
- Linkedinem jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání LinkedIn Learning v galerii
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-linkedin-learning-from-the-gallery"></a>Přidání LinkedIn Learning v galerii
Pokud chcete nakonfigurovat integrace LinkedIn Learning do služby Azure AD, budete muset přidat LinkedIn Learning v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat LinkedIn Learning v galerii, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Klikněte na tlačítko **přidat** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **LinkedIn Learning**. Na panelu výsledků klikněte na **LinkedIn Learning** pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Linkedinem podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek ve službě LinkedIn Learning je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele ve službě LinkedIn Learning musí být vytvořeno.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** ve službě LinkedIn Learning.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Linkedinem, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele LinkedIn Learning](#creating-a-linkedin-learning-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Linkedinem.

**Ke konfiguraci Azure AD jednotné přihlašování s Linkedinem, proveďte následující kroky:**

1. Na webu Azure Portal na **LinkedIn Learning** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/tutorial-linkedin_01.png)

1. V okně prohlížeče jiných webových přihlašování k vašemu tenantovi LinkedIn Learning jako správce.

1. V **centra pro účty**, klikněte na tlačítko **globální nastavení** pod **nastavení**. Kromě toho **Learning – výchozí** z rozevíracího seznamu.

    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

1. Klikněte na tlačítko **nebo klikněte sem pro načítání a kopírování jednotlivých polí ve formuláři** a zkopírujte **Entity Id** a **Url Assertion Consumer přístupu (ACS)**

    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

1. Na portálu Azure portal v části **LinkedIn Learning domény a adresy URL**, proveďte následující kroky, pokud chcete nakonfigurovat jednotné přihlašování v **zahájené pomocí IdP** režimu

    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_01.png)

    a. V **identifikátor** textového pole zadejte **Entity ID** zkopírovali z portálu LinkedIn 

    b. V **adresy URL odpovědi** textového pole zadejte **Assertion Consumer přístupu (ACS) adresy Url** zkopírovali z portálu LinkedIn

1. Pokud chcete nakonfigurovat jednotné přihlašování v **SP iniciované**, klikněte na možnost nastavení zobrazit pokročilé adresy URL v části o konfiguraci a nakonfigurujte adresu URL přihlašování s následujícím vzorem:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_02.png)   
    
1. LinkedIn Learning aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tuto. Výchozí hodnota **identifikátor uživatele** je **user.userprincipalname** ale Linkedinem očekává, že to namapovat s e-mailovou adresu uživatele. K tomu můžete použít **user.mail** atribut ze seznamu nebo použijte hodnotu odpovídajícího atributu na základě vaší konfigurace organizace. 

    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/updateusermail.png)
    
1. V **atributy uživatele** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** a nastavte atributy. Uživatel musí přidat čtyři deklarace identity s názvem **e-mailu**, **oddělení**, **firstname**, a **lastname** a hodnota má být namapována na žádnou **user.mail**, **user.department**, **user.givenname**, a **user.surname** v uvedeném pořadí

    | Název atributu | Hodnota atributu |
    | --- | --- |
    | e-mail| User.Mail |    
    | Oddělení| User.Department |
    | jméno| user.givenname |
    | Příjmení| user.surname |
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinlearning-tutorial/userattribute.png)
    
    a. Klikněte na tlačítko **přidat atribut** otevřete dialogové okno atribut.

    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinlearning-tutorial/tutorial_attribute_04.png)

    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinlearning-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **Ok**

1. Následující postup proveďte **název** – atribut

    a. Klikněte na atribut, který chcete otevřít **Upravit atribut** okna.

    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/url_update.png)

    b. Odstranit hodnotu adresy URL z **obor názvů**.
    
    c. Klikněte na tlačítko **Ok** nastavení uložte.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_certificate.png)

1. Klikněte na **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/tutorial_general_400.png)

1. Přejděte na **nastavení Linkedinu správce** oddílu. Nahrajte soubor XML, který jste si stáhli z webu Azure portal klikněte na možnost nahrát XML soubor.

    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

1. Klikněte na tlačítko **na** pro povolení jednotného přihlašování. Stav jednotného přihlašování se změní z **Nepřipojeno** k **připojeno**

    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinlearning-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinlearning-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinlearning-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinlearning-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-linkedin-learning-test-user"></a>Vytvoření zkušebního uživatele Linkedinem

LinkedIn Learning aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele se vytvoří automaticky v aplikaci. Na správce stránky nastavení na portálu vyfiltrují LinkedIn Learning přepínač **automaticky přiřadit licence** na aktivní pouze v čase zřizování a to se také přiřadit licenci uživateli.

   ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Linkedinem.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Linkedinem, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

1. V seznamu aplikací vyberte **LinkedIn Learning**.

    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_0001.png)

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Linkedinem na přístupovém panelu, měli byste získat na stránce Azure Sign-on a na po úspěšném přihlášení, měli byste obdržet do vaší aplikace s Linkedinem.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/linkedinlearning-tutorial/tutorial_general_01.png
[2]: ./media/linkedinlearning-tutorial/tutorial_general_02.png
[3]: ./media/linkedinlearning-tutorial/tutorial_general_03.png
[4]: ./media/linkedinlearning-tutorial/tutorial_general_04.png

[100]: ./media/linkedinlearning-tutorial/tutorial_general_100.png

[200]: ./media/linkedinlearning-tutorial/tutorial_general_200.png
[201]: ./media/linkedinlearning-tutorial/tutorial_general_201.png
[202]: ./media/linkedinlearning-tutorial/tutorial_general_202.png
[203]: ./media/linkedinlearning-tutorial/tutorial_general_203.png
