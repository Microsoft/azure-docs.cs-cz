---
title: 'Kurz: Integrace služby Azure Active Directory s organizačním diagramem nyní | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a organizačním diagramem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: b96606b5558e0fbb81733b2f548a89bfb38d5f99
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095437"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Kurz: Integrace Služby Azure Active Directory s organizačním diagramem

V tomto kurzu se dozvíte, jak integrovat organizační diagram nyní s Azure Active Directory (Azure AD).
Integrace organizačního diagramu nyní s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k organizačního diagramu nyní.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k orgchartnow now (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí organizačního diagramu, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením OrgChart Now

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* OrgChart nyní podporuje **SP** a **IDP** iniciované přihlašovat

## <a name="adding-orgchart-now-from-the-gallery"></a>Přidání organizačního diagramu nyní z galerie

Chcete-li nakonfigurovat integraci organizačního diagramu nyní do Azure AD, musíte přidat OrgChart Now z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat organizační diagram nyní z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Organizační diagram Nyní**, z **panelu** výsledků vyberte Organizační diagram nyní a pak klepnutím na **tlačítko Přidat** aplikaci přidejte.

     ![Organizační diagram nyní v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí organizačního diagramu nyní na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v organizačním diagramu.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí organizačního diagramu, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace organizačního diagramu nyní jednotné přihlášení](#configure-orgchart-now-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte orgchart nyní testovací uživatele](#create-orgchart-now-test-user)** – mít protějšek Britta Simon v orgchart nyní, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí organizačního diagramu, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **OrgChart Now** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** Pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující krok:

    ![OrgChart Nyní Informace o doméně a adresURL jednotného přihlášení](common/idp-identifier.png)

    Do textového pole **Identifikátor** zadejte adresu URL:`https://sso2.orgchartnow.com`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![image](common/both-preintegrated-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>`je **identifikátor Azure AD** zkopírovaný z části **Nastavit organizační diagram,** popsanou dále v kurzu.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V části **Nastavit organizační diagram nyní** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-orgchart-now-single-sign-on"></a>Konfigurace organizačního diagramu nyní jednotné ho přihlašování

Chcete-li nakonfigurovat jednotné přihlašování na straně **OrgChart Now,** musíte odeslat stažený **xml metadat federace** a příslušné zkopírované adresy URL z portálu Azure do týmu podpory [Organizačního diagramu Nyní](mailto:ocnsupport@officeworksoftware.com). Toto nastavení nastaví tak, aby bylo připojení s přizasazené k samovazbě SAML správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** typ pole**brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k OrgChart Now.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte Organizační **diagram teď**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **organizační diagram nyní**.

    ![Odkaz Organizační diagram nyní v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-orgchart-now-test-user"></a>Vytvořit organizační diagram nyní testovací ho uživatele

Chcete-li povolit uživatelům Azure AD k přihlášení k organizačnímu diagramu nyní, musí být zřízeny do organizačního diagramu nyní. 

1. OrgChart Nyní podporuje zřizování just-in-time, což je ve výchozím nastavení povoleno. Nový uživatel je vytvořen během pokusu o přístup k organizačnímu diagramu nyní, pokud ještě neexistuje. Funkce zřizování uživatele just-in-time vytvoří uživatele **jen pro čtení pouze** v případě, že požadavek na služby SSO pochází z rozpoznaného idp a e-mail v kontrolním výrazu SAML nebyl nalezen v seznamu uživatelů. Pro tuto funkci automatického zřizování je třeba vytvořit přístupovou skupinu s názvem **Obecné** v organizačním diagramu nyní. Chcete-li vytvořit přístupovou skupinu, postupujte podle následujících kroků:

    a. Po kliknutí na **ozubené kolo** v pravém horním rohu uhlavního uživatele přejděte na možnost **Spravovat skupiny.**

    ![Skupiny Organizační diagram](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Vyberte ikonu **Přidat** a pojmenujte skupinu **Obecné** a klepněte na tlačítko **OK**. 

    ![Organizační diagram nyní přidat](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Vyberte složky, ke kterým mají mít přístup, ale k obecným uživatelům nebo uživatelům jen pro čtení:

    ![Složky Organizační diagram](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Uzamkněte** složky tak, aby je mohli upravovat pouze uživatelé správce. Poté stiskněte **tlačítko OK**.

    ![Organizační diagram nyní zámek](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Chcete-li vytvořit uživatele **správce** a **číst a zapisovat** uživatele, musíte ručně vytvořit uživatele, abyste získali přístup k jejich úrovni oprávnění prostřednictvím služby SSO. Chcete-li zřídit uživatelský účet, proveďte následující kroky:

    a. Přihlaste se k organizačnímu diagramu nyní jako správce zabezpečení.

    b.  Klikněte na **Nastavení** v pravém horním rohu a přejděte na **Spravovat uživatele**.

    ![Nastavení organizačního diagramu](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Klikněte na **Přidat** a proveďte následující kroky:

    ![Organizační diagram nyní spravovat](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Do textového pole **ID uživatele** zadejte ID uživatele jako **\@brittasimon contoso.com**.

    * Do textového pole **E-mailová adresa** zadejte e-mail uživatele, jako **je\@brittasimon contoso.com**.

    * Klikněte na **Přidat**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Organizační diagram nyní na přístupovém panelu, můžete by měl být automaticky přihlášeni k organizačního diagramu nyní, pro které nastavíte přihlašující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

