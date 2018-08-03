---
title: 'Kurz: Integrace Azure Active Directory se službou KnowledgeOwl | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: e902f5969611dd3b1074e899003abe5067857c04
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446133"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Kurz: Integrace Azure Active Directory se službou KnowledgeOwl

V tomto kurzu se dozvíte, jak integrovat KnowledgeOwl s Azure Active Directory (Azure AD).

KnowledgeOwl integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k KnowledgeOwl.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k KnowledgeOwl (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s KnowledgeOwl, potřebujete následující položky:

- S předplatným služby Azure AD
- KnowledgeOwl jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání KnowledgeOwl z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-knowledgeowl-from-the-gallery"></a>Přidání KnowledgeOwl z Galerie
Konfigurace integrace KnowledgeOwl do služby Azure AD, budete muset přidat KnowledgeOwl z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat KnowledgeOwl z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **KnowledgeOwl**vyberte **KnowledgeOwl** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![KnowledgeOwl v seznamu výsledků](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí KnowledgeOwl podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v KnowledgeOwl je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v KnowledgeOwl potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s KnowledgeOwl, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele KnowledgeOwl](#create-a-knowledgeowl-test-user)**  – Pokud chcete mít protějšek Britta Simon KnowledgeOwl, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci KnowledgeOwl.

**Ke konfiguraci Azure AD jednotné přihlašování s KnowledgeOwl, proveďte následující kroky:**

1. Na webu Azure Portal na **KnowledgeOwl** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

1. Na **KnowledgeOwl domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![KnowledgeOwl domény a adresy URL jednotného přihlašování – informace](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. V **identifikátor (Entity ID)** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![KnowledgeOwl domény a adresy URL jednotného přihlašování – informace](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||
     
    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Budete muset aktualizovat tyto hodnoty od skutečné identifikátor adresy URL odpovědi a přihlašovací adresa URL, která je vysvětlen později v tomto kurzu.

1. Aplikace KnowledgeOwl očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci.

    ![Konfigurace jednotného přihlašování](./media/knowledgeowl-tutorial/attribute.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na předchozím obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu | Názvový prostor|
    | ------------------- | -------------------- | -----|
    | ssoid | User.Mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.
    
    ![Konfigurace jednotného přihlašování](./media/knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d.From **Namespace** seznamu, zadejte hodnotu oboru názvů zobrazený pro tento řádek.
    
    e. Klikněte na tlačítko **OK**.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Raw)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/knowledgeowl-tutorial/tutorial_general_400.png)
    
1. Na **KnowledgeOwl konfigurace** klikněte na tlačítko **nakonfigurovat KnowledgeOwl** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti KnowledgeOwl.

1. Klikněte na **nastavení** a pak vyberte **zabezpečení**.

    ![Konfigurace KnowledgeOwl](./media/knowledgeowl-tutorial/configure1.png)

1. Přejděte dolů až **integraci jednotného přihlašování SAML** a proveďte následující kroky:
    
    ![Konfigurace KnowledgeOwl](./media/knowledgeowl-tutorial/configure2.png)

    a. Vyberte **povolení jednotného přihlašování SAML**.

    b. Kopírovat **SP Entity ID** hodnotu a vložte ho do **identifikátor (Entity ID)** v **KnowledgeOwl domény a adresy URL** části na webu Azure portal.

    c. Kopírovat **SP přihlašovací adresa URL** hodnotu a vložte ho do **přihlašovací adresu URL a adresy URL odpovědi** textová pole v **KnowledgeOwl domény a adresy URL** části na webu Azure portal.

    d. V **IdP entityID** vložit do textového pole **SAML Entity ID** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    e. V **přihlašovací adresa URL zprostředkovatele identity** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    f. V **odhlašovací adresa URL zprostředkovatele identity** vložit do textového pole **odhlašování URL** hodnotu, kterou jste zkopírovali z portálu Azure portal

    g. Nahrát certifikát stažený formuláře na webu Azure portal kliknutím **nahrát certifikát IdP**.

    h. Klikněte na **atributy SAML mapy** mapování atributů a proveďte následující kroky:
    
    ![Konfigurace KnowledgeOwl](./media/knowledgeowl-tutorial/configure3.png)

    * Zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` do **jednotného přihlašování ID** textové pole
    * Zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` do **uživatelské jméno nebo e-mailu** textového pole.
    * Zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` do **křestní jméno** textového pole.
    * Zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` do **příjmení** textového pole.
    * Klikněte na **Uložit**.

    i. V dolní části stránky klikněte na **Uložit**.

    ![Konfigurace KnowledgeOwl](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/knowledgeowl-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/knowledgeowl-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/knowledgeowl-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/knowledgeowl-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-knowledgeowl-test-user"></a>Vytvoření zkušebního uživatele KnowledgeOwl

Cílem této části je vytvořte uživatele Britta Simon v KnowledgeOwl. KnowledgeOwl podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k KnowledgeOwl, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory KnowledgeOwl](mailto:support@knowledgeowl.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k KnowledgeOwl použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit KnowledgeOwl Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **KnowledgeOwl**.

    ![Odkaz KnowledgeOwl v seznamu aplikací](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici KnowledgeOwl na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci KnowledgeOwl.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/knowledgeowl-tutorial/tutorial_general_203.png

