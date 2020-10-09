---
title: 'Kurz: Azure Active Directory integrace se systémy PEGA | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi systémy Azure Active Directory a PEGA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 3dd09770ef5f747f008090368c35b1f274374c5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88549063"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Kurz: Azure Active Directory integrací se systémy PEGA

V tomto kurzu se dozvíte, jak integrovat systémy PEGA s Azure Active Directory (Azure AD).

Tato integrace poskytuje tyto výhody:

* Pomocí Azure AD můžete řídit, kdo má přístup k systémům PEGA.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k systémům PEGA (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD se systémy PEGA, musíte mít následující:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete se zaregistrovat k měsíční [zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné PEGA systémů s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Systémy PEGA podporují jednotné přihlašování (SSO) iniciované v SP a IdP.

## <a name="add-pega-systems-from-the-gallery"></a>Přidání systémů PEGA z Galerie

K nastavení integrace systémů PEGA do služby Azure AD je nutné přidat systémy PEGA z Galerie do seznamu spravovaných aplikací SaaS.

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejít k **podnikovým aplikacím**  >  **všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace** :

    ![Vybrat novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **PEGA Systems**. Ve výsledcích hledání vyberte **systémy PEGA** a pak vyberte **Přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s PEGA systémy pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, musíte vytvořit relaci mezi uživatelem služby Azure AD a odpovídajícím uživatelem v PEGA systémech.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí systémů PEGA, musíte provést tyto kroky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** , abyste funkci povolili uživatelům.
2. **[Konfigurace jednotného přihlašování systémů PEGA](#configure-pega-systems-single-sign-on)** na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** povolení jednotného přihlašování Azure AD pro uživatele.
5. **[Vytvořte testovacího uživatele systémů PEGA](#create-a-pega-systems-test-user)** , který je propojený s Předprezentací Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí systémů PEGA, proveďte tyto kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **PEGA Systems** vyberte **jednotné přihlašování**:

    ![Vyberte jednotné přihlašování.](common/select-sso.png)

2. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** pro povolení jednotného přihlašování:

    ![Vyberte metodu jednotného přihlašování.](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** vyberte ikonu **Upravit** a otevřete DIALOGOVÉ okno **základní konfigurace SAML** :

    ![Ikona úprav](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované IdP, proveďte v dialogovém okně **základní konfiguraci SAML** následující kroky.

    ![Základní dialogové okno Konfigurace SAML](common/idp-intiated.png)

    1. Do pole **identifikátor** zadejte adresu URL v tomto vzoru:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Do pole **Adresa URL odpovědi** zadejte adresu URL v tomto vzoru:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Chcete-li nakonfigurovat aplikaci v režimu iniciované SP, vyberte možnost **nastavit další adresy URL** a proveďte následující kroky.

    ![Informace o jednotném přihlašování PEGA systémů a adres URL](common/both-advanced-urls.png)

    1. Do pole **přihlašovací adresa URL** zadejte hodnotu adresy URL pro přihlášení.

    1. Do pole **stav přenosu** zadejte adresu URL v tomto vzoru: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Zde uvedené hodnoty jsou zástupné symboly. Musíte použít skutečný identifikátor, adresu URL odpovědi, adresu URL pro přihlášení a adresu URL stavu přenosu. Hodnoty adresy URL pro odpovědi můžete získat z aplikace PEGA, jak je vysvětleno dále v tomto kurzu. Pokud chcete získat hodnotu stavu přenosu, obraťte se na [tým podpory PEGA Systems](https://www.pega.com/contact-us). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Aplikace PEGA Systems potřebuje, aby byly kontrolní výrazy SAML v konkrétním formátu. Pokud je chcete získat ve správném formátu, je nutné přidat mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje výchozí atributy. Výběrem ikony **Upravit** otevřete dialogové okno **atributy uživatele** :

    ![Atributy uživatele](common/edit-attribute.png)

7. Kromě atributů uvedených na předchozím snímku obrazovky vyžaduje aplikace PEGA Systems několik dalších atributů, které se vrátí zpět v odpovědi SAML. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky a přidejte tyto atributy tokenu SAML:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Tyto hodnoty jsou specifické pro vaši organizaci. Zadejte odpovídající hodnoty.

    1. Výběrem možnosti **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** :

    ![Vyberte Přidat novou deklaraci identity.](common/new-save-attribute.png)

    ![Dialogové okno Spravovat deklarace identity uživatelů](common/new-attribute-details.png)

    1. Do pole **název** zadejte název atributu zobrazený pro tento řádek.

    1. Pole **oboru názvů** nechejte prázdné.

    1. Jako **zdroj**vyberte **atribut**.

    1. V seznamu **zdrojový atribut** vyberte hodnotu atributu zobrazenou pro tento řádek.

    1. Vyberte **OK**.

    1. Vyberte **Uložit**.

8. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** vyberte odkaz **ke stažení** vedle **metadat federace XML**podle vašich požadavků a uložte certifikát do počítače:

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

9. V části **Nastavení systémů PEGA** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat konfigurační adresy URL](common/copy-configuration-urls.png)

    1. **Přihlašovací adresa URL**

    1. **Identifikátor Azure AD**.

    1. **Odhlašovací adresa URL**

### <a name="configure-pega-systems-single-sign-on"></a>Nakonfigurovat jednotné přihlašování pro systémy PEGA

1. Pokud chcete nakonfigurovat jednotné přihlašování na straně **systémů PEGA** , přihlaste se k portálu PEGA pomocí účtu správce v jiném okně prohlížeče.

2. Vyberte **vytvořit**  >  **SysAdmin**  >  **ověřovací službu**sysadmin:

    ![Vybrat ověřovací službu](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Na obrazovce **Vytvoření ověřovací služby** proveďte následující kroky.

    ![Obrazovka pro vytvoření ověřovací služby](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. V seznamu **typ** vyberte **SAML 2,0**.

    1. Do pole **název** zadejte libovolný název (například **Azure AD SSO**).

    1. Do pole **krátký popis** zadejte popis.  

    1. Vyberte **vytvořit a otevřít**.
    
4. V části **informace o poskytovateli identity (IDP)** vyberte **importovat metadata IDP** a vyhledejte soubor metadat, který jste stáhli z Azure Portal. Kliknutím na **Odeslat** načtěte metadata:

    ![IdP (informace o poskytovateli identity) – oddíl](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Import naplní data IdP, jak je znázorněno zde:

    ![Importovaná data IdP](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. V části **Nastavení poskytovatele služeb (SP)** proveďte následující kroky.

    ![Nastavení poskytovatele služeb](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Zkopírujte hodnotu **Identifikace entity** a vložte ji do pole **identifikátor** v **základní části Konfigurace SAML** v Azure Portal.

    1. Zkopírujte hodnotu **umístění služby vyhodnocení uživatele (ACS)** a vložte ji do pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

    1. Vyberte **zakázat podepisování požadavků**.

7. Vyberte **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon v Azure Portal.

1. V Azure Portal v levém podokně vyberte **Azure Active Directory** , vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**:

    ![Vyberte Všichni uživatelé.](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** :

    ![Vybrat nového uživatele](common/new-user.png)

3. V dialogovém okně **uživatel** proveďte následující kroky.

    ![Uživatel – dialogové okno](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon@ \<yourcompanydomain> . \<extension> **. (Například BrittaSimon@contoso.com .)

    c. Vyberte možnost **Zobrazit heslo**a pak zapište hodnotu, která je uvedena v poli **heslo** .

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k systémům PEGA.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **systémy PEGA**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte možnost **systémy PEGA**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **Uživatelé a skupiny**:

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-pega-systems-test-user"></a>Vytvořit testovacího uživatele pro systémy PEGA

Dále musíte vytvořit uživatele s názvem Britta Simon v systémech PEGA. Pracujte s [týmem podpory PEGA Systems](https://www.pega.com/contact-us) , aby mohli vytvářet uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když vyberete dlaždici PEGA Systems na přístupovém panelu, měli byste se automaticky přihlásit k instanci systému PEGA, pro kterou jste nastavili jednotné přihlašování. Další informace najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)