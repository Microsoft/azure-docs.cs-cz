---
title: 'Kurz: Integrace Azure Active Directory s LinkedIn Sales Navigatoru | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LinkedInSalesNavigator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: ca27b0a911e179ef8447163d34ce1b06db941521
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55169633"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Kurz: Integrace Azure Active Directory s LinkedIn Sales Navigatoru

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) LinkedIn Sales Navigatoru.

Integrace LinkedIn Sales Navigatoru s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k LinkedIn Sales Navigatoru
- Uživatele, aby automaticky získat přihlášeného LinkedIn Sales Navigatoru (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, Procházet [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s LinkedIn Sales Navigatoru, potřebujete následující položky:

- Předplatné Azure AD
- LinkedIn Sales Navigatoru jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Vyhněte se použití produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání LinkedIn Sales Navigatoru z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Přidání LinkedIn Sales Navigatoru z Galerie
Konfigurace integrace LinkedIn Sales Navigatoru do služby Azure AD, budete muset přidat LinkedIn Sales Navigatoru z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat LinkedIn Sales Navigatoru z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **LinkedIn Sales Navigatoru**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_search.png)

1. Na panelu výsledků vyberte **LinkedIn Sales Navigatoru**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí LinkedIn Sales Navigatoru podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v LinkedIn Sales Navigatoru je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v LinkedIn Sales Navigatoru.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v LinkedIn Sales Navigatoru.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s LinkedIn Sales Navigatoru, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele LinkedIn Sales Navigatoru](#creating-a-linkedin-sales-navigator-test-user)**  – Pokud chcete mít protějšek Britta Simon LinkedIn Sales Navigatoru, který je propojený s Azure AD zastoupení uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci LinkedIn Sales Navigatoru.

**Ke konfiguraci Azure AD jednotné přihlašování s LinkedIn Sales Navigatoru, proveďte následující kroky:**

1. Na webu Azure Portal na **LinkedIn Sales Navigatoru** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna v **režimu** vyberte **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_samlbase.png)

1. V okně prohlížeče jiných webových, přihlašování k vaší **LinkedIn Sales Navigatoru** webu jako správce.

1. V **centra pro účty**, klikněte na tlačítko **globální nastavení** pod **nastavení**. Kromě toho **Sales Navigatoru** z rozevíracího seznamu.

    ![Konfigurace jednotného přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Klikněte na tlačítko **nebo klikněte sem pro načítání a kopírování jednotlivých polí ve formuláři** a zkopírujte **Entity Id** a **Assertion Consumer přístupu (ACS) adresy Url**.

    ![Konfigurace jednotného přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

1. Na portálu Azure portal v části **LinkedIn Sales Navigator domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu.

    ![Konfigurace jednotného přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url1.png)

    a. V **identifikátor** textového pole zadejte **Entity ID** zkopírovali z portálu LinkedIn 

    b. V **adresy URL odpovědi** textového pole zadejte **Assertion Consumer přístupu (ACS) adresy Url** zkopírovali z portálu LinkedIn

1. Zkontrolujte **zobrazit pokročilé nastavení URL**, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu.

    ![Konfigurace jednotného přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url2.png)

    V **přihlašovací adresa URL** textového pole zadejte hodnotu pomocí následujícího vzorce: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. Vaše **LinkedIn Sales Navigatoru** aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad. Výchozí hodnota **identifikátor uživatele** je **user.userprincipalname** ale LinkedIn Sales Navigatoru očekává, že ji namapovat pomocí e-mailovou adresu uživatele. Můžete použít **user.mail** atribut ze seznamu nebo použijte hodnotu odpovídajícího atributu na základě vaší konfigurace organizace. 

    ![Konfigurace jednotného přihlašování](./media/linkedinsalesnavigator-tutorial/updateusermail.png)
    
1. V **atributy uživatele** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** a nastavte atributy. Uživatel musí přidat čtyři deklarace identity s názvem **e-mailu**, **oddělení**, **firstname**, a **lastname** a hodnota má být namapována na žádnou **user.mail**, **user.department**, **user.givenname**, a **user.surname** v uvedeném pořadí

    | Název atributu | Hodnota atributu |
    | --- | --- |    
    | e-mail| user.mail |
    | Oddělení| user.department |
    | jméno| user.givenname |
    | Příjmení| user.surname |
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinsalesnavigator-tutorial/userattribute.png)
    
    a. Klikněte na **přidat atribut** otevřete dialogové okno atribut.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_04.png)
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_05.png)
   
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **Ok**

1. Následující postup proveďte **název** – atribut

    a. Klikněte na atribut, který chcete otevřít **Upravit atribut** okna.

    ![Konfigurace jednotného přihlašování](./media/linkedinsalesnavigator-tutorial/url_update.png)

    b. Odstranit hodnotu adresy URL z **obor názvů**.
    
    c. Klikněte na tlačítko **Ok** nastavení uložte.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_general_400.png)

1. Přejděte na **nastavení Linkedinu správce** oddílu. Klikněte na tlačítko **soubor XML nahrajte** nahrát soubor metadat XML, který jste si stáhli z portálu Azure portal.

    ![Konfigurace jednotného přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Klikněte na tlačítko **na** pro povolení jednotného přihlašování. Stav jednotného přihlašování se změní z **Nepřipojeno** k **připojeno**

    ![Konfigurace jednotného přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)


> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_01.png) 

1. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_02.png) 

1. V horní části okna klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-linkedin-sales-navigator-test-user"></a>Vytvoření zkušebního uživatele LinkedIn Sales Navigatoru

Propojené aplikace Sales Navigator podporuje pouze v zřizování uživatelů Time (JIT) a po ověření uživatele se vytvoří automaticky v aplikaci. Aktivovat **automaticky přiřadit licence** přiřadit licenci uživateli.
   
   ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu LinkedIn Sales Navigatoru.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon LinkedIn Sales Navigatoru, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **LinkedIn Sales Navigatoru**.

    ![Konfigurace jednotného přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici LinkedIn Sales Navigatoru na přístupovém panelu, by měl přesměrováni na organizační stránku, kde je nutné zadat podrobnosti o vašem osobním účtu LinkedIn. Odkazuje váš osobní účet s vaším účtem LinkedIn firmy. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_01.png
[2]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_02.png
[3]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_03.png
[4]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_04.png

[100]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_100.png

[200]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_200.png
[201]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_201.png
[202]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_202.png
[203]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_203.png

