---
title: 'Kurz: Integrace služby Azure Active Directory s iKvalivem LMS | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a iKvalil LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: a1db4784eb63df14b7e7971d0273512ba657df96
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "68944998"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Kurz: Integrace služby Azure Active Directory s iKvalivem LMS

V tomto kurzu se dozvíte, jak integrovat iKvalil LMS s Azure Active Directory (Azure AD).
Integrace iKvali LMS s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k iKvalil LMS.
* Můžete povolit, aby se uživatelé automaticky přihlásili k iKvalil LMS (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s iKvaliLMS, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* předplatné s povoleným jedním přihlášením iKvali LMS

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* iQualify LMS podporuje **sp a IDP** iniciované přihlašující do mj.
* iQualify LMS podporuje zřizování uživatelů **just in time**

## <a name="adding-iqualify-lms-from-the-gallery"></a>Přidání iQualify LMS z galerie

Chcete-li nakonfigurovat integraci iKvalil LMS do Azure AD, musíte přidat iKvaliva LMS z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat iKvaliva LMS z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **iKvaliva LMS**, z panelu výsledků vyberte **iKvalil LMS** a klepnutím na **tlačítko Přidat** přidejte aplikaci.

     ![iKvaliL LMS v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí iKvalida LMS na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v iKvaliLMS.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí iKvalil LMS, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace iKvalil LMS jednotné přihlášení](#configure-iqualify-lms-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte iKvalil LMS testovací ho uživatele](#create-iqualify-lms-test-user)** – mít protějšek Britta Simon v iKvalil LMS, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování azure ad s iKvaliLMS, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce integrace aplikací **iKvali LMS** **možnost Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** Pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![iKvalifikovat informace o doméně lms a adresách URL s jediným přihlášením](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:
    | |
    |--|--|
    | Produkční prostředí:`https://<yourorg>.iqualify.com/`|
    | Testovací prostředí:`https://<yourorg>.iqualify.io`|

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:
    | |
    |--|--|
    | Produkční prostředí:`https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Testovací prostředí:`https://<yourorg>.iqualify.io/auth/saml2/callback` |

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![iKvalifikovat informace o doméně lms a adresách URL s jediným přihlášením](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:
    | |
    |--|--|
    | Produkční prostředí:`https://<yourorg>.iqualify.com/login` |
    | Testovací prostředí:`https://<yourorg>.iqualify.io/login` |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta iKvalil LMS.](https://www.iqualify.com/) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Aplikace iQualify LMS očekává kontrolní výrazy SAML v určitém formátu, který vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky zobrazuje seznam výchozích atributů. Kliknutím na ikonu **Upravit** otevřete dialogové okno **Atributy uživatele.**

    ![image](common/edit-attribute.png)

7. V části **Deklarace identity uživatelů** v dialogovém okně **Atributy uživatele** upravte deklarace pomocí **ikony Upravit** nebo přidejte deklarace pomocí funkce Přidat **novou deklaraci** pro konfiguraci atributu tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Name (Název) | Atribut zdroje|
    | --- | --- |
    | e-mail | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.příjmení |
    | person_id | "váš atribut" |

    a. Kliknutím na **Přidat novou deklaraci** otevřete dialogové okno **Spravovat deklarace identity uživatelů.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **Název** zadejte název atributu zobrazený pro daný řádek.

    c. Ponechejte **obor názvů** prázdný.

    d. Vyberte zdroj jako **atribut**.

    e. Ze seznamu **atributů Zdroj** zadejte hodnotu atributu zobrazenou pro daný řádek.

    f. Klikněte na **Ok.**

    g. Klikněte na **Uložit**.

    > [!Note]
    > Atribut **person_id** je **Volitelné**

8. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

9. V části **Nastavit iKvali LMS** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-iqualify-lms-single-sign-on"></a>Konfigurace jednotného přihlašování iKvali LMS

1. Otevřete nové okno prohlížeče a přihlaste se do prostředí iKvali jako správce.

1. Jakmile jste přihlášeni, klikněte na svého avatara v pravém horním rohu, pak klikněte na **nastavení účtu**

    ![Nastavení účtu](./media/iqualify-tutorial/setting1.png)

1. V oblasti nastavení účtu klikněte na nabídku pásu karet vlevo a klikněte na **INTEGRACE**

    ![Integrace](./media/iqualify-tutorial/setting2.png)

1. V části INTEGRACE klikněte na ikonu **SAML.**

    ![Ikona aplikace SAML](./media/iqualify-tutorial/setting3.png)

1. V dialogovém okně **Nastavení ověřování SAML** proveďte následující kroky:

    ![Nastavení ověřování SAML](./media/iqualify-tutorial/setting4.png)

    a. V poli **adresa URL služby JEDNOTNÉ PŘIHLAŠOVÁNÍ SAML** vložte hodnotu **přihlašovací adresy URL** zkopírovanou z okna konfigurace aplikace Azure AD.

    b. V poli **ADRESA URL odhlášení SAML** vložte hodnotu **adresy URL odhlášení** zkopírované z okna konfigurace aplikace Azure AD.

    c. Otevřete stažený soubor certifikátu v poznámkovém bloku, zkopírujte obsah a vložte jej do pole **VEŘEJNÝ CERTIFIKÁT.**

    d. Do **pole LOGIN BUTTON LABEL** zadejte název tlačítka, které se má zobrazit na přihlašovací stránce.

    e. Klepněte na tlačítko **ULOŽIT**.

    f. Klepněte na tlačítko **AKTUALIZOVAT**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k iKvali LMS.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **iKvalifikující LMS**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **iKvalifikující LMS**.

    ![Odkaz iKvalifikující LMS v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-iqualify-lms-test-user"></a>Vytvořit testovacího uživatele iKvalil LMS

V této části je vytvořen uživatel s názvem Britta Simon v iKvaliLMS. iKvaliLMS podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ještě neexistuje v iKvalil LMS, vytvoří se po ověření nový uživatel.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici iQualify LMS na přístupovém panelu, měli byste získat přihlašovací stránku aplikace iQualify LMS. 

   ![přihlašovací stránka](./media/iqualify-tutorial/login.png) 

Klikněte na **Přihlásit se pomocí** tlačítka Azure AD a měli byste se automaticky přihlásit k aplikaci iQualify LMS.

Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)