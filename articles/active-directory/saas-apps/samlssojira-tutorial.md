---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML pro Jira podle rozlišení GmbH | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování SAML pro Jira podle rozlišení GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: 6ae8256f3485d49d42efeb2927a6838252a1aeee
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442903"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML pro Jira podle rozlišení GmbH

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování SAML pro Jira podle rozlišení GmbH se službou Azure Active Directory (Azure AD).

Integrace jednotného přihlašování SAML pro Jira podle rozlišení GmbH s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SAML SSO pro Jira podle rozlišení GmbH
- Můžete povolit uživatelům, aby automaticky získat přihlášení k jednotné přihlašování SAML pro Jira podle rozlišení GmbH (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním SAML pro Jira s rozlišení GmbH, potřebujete následující položky:

- S předplatným služby Azure AD
- SAML SSO pro Jira podle rozlišení GmbH jednotné přihlašování v předplatném povolené

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání jednotného přihlašování SAML pro Jira podle rozlišení GmbH z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Přidání jednotného přihlašování SAML pro Jira podle rozlišení GmbH z Galerie
Pokud chcete nakonfigurovat integraci jednotného přihlašování SAML pro Jira podle rozlišení GmbH do služby Azure AD, budete muset přidat jednotné přihlašování SAML pro Jira rozlišení GmbH z Galerie na váš seznam spravovaných aplikací SaaS.

**Pokud chcete přidat jednotné přihlašování SAML pro Jira z Galerie řešení GmbH, proveďte následující kroky:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **jednotné přihlašování SAML pro Jira podle rozlišení GmbH**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/samlssojira-tutorial/tutorial_samlssojira_search.png)

1. Na panelu výsledků vyberte **jednotné přihlašování SAML pro Jira podle rozlišení GmbH**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/samlssojira-tutorial/tutorial_samlssojira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním SAML pro Jira podle rozlišení, které GmbH podle testovacího uživatele nazývá "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, potřebuje vědět, jaké protějšek uživatele v jednotné přihlašování SAML pro Jira pomocí řešení GmbH je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v jednotné přihlašování SAML pro Jira podle rozlišení GmbH musí být vytvořeno.

V SAML SSO pro Jira podle rozlišení GmbH, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním SAML pro Jira podle rozlišení GmbH, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytváření SAML SSO pro Jira rozlišení GmbH testovací uživatel](#creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user)**  – Pokud chcete mít protějšek Britta Simon v jednotné přihlašování SAML pro Jira podle rozlišení GmbH, která souvisí s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a nakonfigurovat jednotné přihlašování ve vašich jednotné přihlašování SAML pro Jira rozlišení GmbH aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním SAML pro Jira podle rozlišení GmbH, proveďte následující kroky:**

1. Na webu Azure Portal na **jednotné přihlašování SAML pro Jira podle rozlišení GmbH** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/tutorial_samlssojira_samlbase.png)

1. Na **SAML SSO Jira podle rozlišení GmbH domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/tutorial_samlssojira_url_1.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<server-base-url>/plugins/servlet/samlsso`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<server-base-url>/plugins/servlet/samlsso`

1. Zkontrolujte **zobrazit pokročilé nastavení URL**. Pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/tutorial_samlssojira_url_2.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory jednotného přihlašování SAML pro Jira podle rozlišení GmbH klienta](https://www.resolution.de/go/support) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/tutorial_samlssojira_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/tutorial_general_400.png)
    
1. V okně jiné webové prohlížeče, přihlaste se k vaší **jednotné přihlašování SAML pro Jira pomocí portálu pro správu řešení GmbH** jako správce.

1. Najeďte myší na ikonu a klikněte na tlačítko **doplňky**.
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon1.png)

1. Budete přesměrováni na stránku přístup správce. Zadejte **heslo** a klikněte na tlačítko **potvrdit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon2.png)

1. Karta části doplňků, klikněte na tlačítko **najít nové doplňky**. Hledání **SAML jednotné přihlašování (SSO) pro JIRA** a klikněte na tlačítko **nainstalovat** tlačítko k instalaci nové zásuvný modul SAML.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon7.png)

1. Spustí se instalace modulu plug-in. Klikněte na **Zavřít**.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon8.png)

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon9.png)

1.  Klikněte na **Manage** (Spravovat).

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon10.png)
    
1. Klikněte na tlačítko **konfigurovat** konfigurace nového modulu plug-in.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon11.png)

1. Na **konfigurace modulu plug-in SingleSignOn SAML** klikněte na **přidat nového zprostředkovatele identity** tlačítko ke konfiguraci nastavení zprostředkovatele Identity.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon4.png)

1. Na **zvolte zprostředkovatele Identity SAML** stránce, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5a.png)
 
    a. Nastavte **Azure AD** jako typ zprostředkovatele identity.
    
    b. Přidat **název** zprostředkovatele Identity (např. Azure AD).
    
    c. Přidat **popis** zprostředkovatele Identity (např. Azure AD).
    
    d. Klikněte na **Další**.
    
1. Na **konfigurace zprostředkovatele Identity** klikněte na **Další** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5b.png)

1. Na **Import metadat zprostředkovatele identity SAML** stránce, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5c.png)

    a. Klikněte na tlačítko **načíst soubor** tlačítko a vyberte soubor metadat XML, které jste si stáhli v kroku 5.

    b. Klikněte na tlačítko **Import** tlačítko.
    
    c. Počkejte krátce import úspěšný.
    
    d. Klikněte na tlačítko **Další** tlačítko.
    
1. Na **atribut ID uživatele a transformace** klikněte na **Další** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5d.png)
    
1. Na **vytvoření uživatele a aktualizace** klikněte na **u & ložit Další** se uložit nastavení.    
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon6a.png)
    
1. Na **Test nastavení** klikněte na **přeskočit testovací & ručně nakonfigurovat** testovací uživatel prozatím přeskočit. To se provede v další části a vyžaduje některá nastavení na webu Azure portal. 
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon6b.png)
    
1. V dialogu čtení apprearing **přeskakování testů znamená...** , klikněte na tlačítko **OK**.
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon6c.png)

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/samlssojira-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/samlssojira-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/samlssojira-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/samlssojira-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Vytváření SAML SSO pro Jira podle rozlišení GmbH testovacího uživatele

Umožňuje uživatelům Azure AD k přihlášení na jednotné přihlašování SAML pro Jira rozlišení GmbH, musí být zřízená do jednotné přihlašování SAML pro Jira podle rozlišení GmbH.  
V SAML SSO pro Jira podle rozlišení GmbH zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k SAML SSO pro Jira rozlišení GmbH společnosti lokalita jako správce.

1. Najeďte myší na ikonu a klikněte na tlačítko **Správa uživatelů**.

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user1.png) 

1. Budete přesměrováni na stránku přístup správce k zadání **heslo** a klikněte na tlačítko **potvrdit** tlačítko.

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user2.png) 

1. V části **Správa uživatelů** kartě oddíl, klikněte na tlačítko **vytvořit uživatele**.

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user3.png) 

1. Na **"Vytvořit nový uživatel"** dialogového okna stránky, proveďte následující kroky:

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user4.png) 

    a. V **e-mailová adresa** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.

    b. V **jméno a příjmení** textového pole zadejte celé jméno uživatele jako Britta Simon.

    c. V **uživatelské jméno** , jako je textové pole, typ e-mailu uživatele Brittasimon@contoso.com.

    d. V **heslo** textového pole zadejte heslo uživatele.

    e. Klikněte na tlačítko **vytvořit uživatele**.   

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k jednotné přihlašování SAML pro Jira podle rozlišení GmbH.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon jednotné přihlašování SAML pro Jira podle rozlišení GmbH, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **jednotné přihlašování SAML pro Jira podle rozlišení GmbH**.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/tutorial_samlssojira_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na jednotné přihlašování SAML pro Jira podle rozlišení GmbH dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k vaší jednotné přihlašování SAML pro Jira podle rozlišení GmbH aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/samlssojira-tutorial/tutorial_general_01.png
[2]: ./media/samlssojira-tutorial/tutorial_general_02.png
[3]: ./media/samlssojira-tutorial/tutorial_general_03.png
[4]: ./media/samlssojira-tutorial/tutorial_general_04.png

[100]: ./media/samlssojira-tutorial/tutorial_general_100.png

[200]: ./media/samlssojira-tutorial/tutorial_general_200.png
[201]: ./media/samlssojira-tutorial/tutorial_general_201.png
[202]: ./media/samlssojira-tutorial/tutorial_general_202.png
[203]: ./media/samlssojira-tutorial/tutorial_general_203.png

