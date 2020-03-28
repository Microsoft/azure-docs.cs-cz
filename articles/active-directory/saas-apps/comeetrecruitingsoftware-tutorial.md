---
title: 'Kurz: Integrace služby Azure Active Directory se softwarem Comeet Recruiting Software | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a softwarem Comeet Recruiting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 75f51dc9-9525-4ec6-80bf-28374f0c8adf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25a88e3abd7eacc323b5d45fd4fae1304010253b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158547"
---
# <a name="tutorial-azure-active-directory-integration-with-comeet-recruiting-software"></a>Kurz: Integrace Azure Active Directory se softwarem Comeet Recruiting Software

V tomto kurzu se dozvíte, jak integrovat software Comeet Recruiting software s Azure Active Directory (Azure AD).
Integrace náborového softwaru Comeet s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Comeet Nábor software.
* Uživatelům můžete povolit automatické přihlášení k náborovému softwaru Comeet (Single Sign-On) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí softwaru Comeet Recruiting, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Comeet Recruiting Software s povoleným přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Comeet Recruiting Software podporuje **SP a IDP** inicioval sso

## <a name="adding-comeet-recruiting-software-from-the-gallery"></a>Přidání comeet Nábor software z galerie

Chcete-li nakonfigurovat integraci náborového softwaru Comeet do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat software Comeet Recruiting Software z galerie.

**Chcete-li přidat software Comeet Recruiting Software z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Comeet Recruiting Software**, z panelu výsledků vyberte **Comeet Recruiting Software** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![Comeet Recruiting Software v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí softwaru Comeet Recruiting Software na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v comeet Náborový software.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí softwaru Comeet Recruiting Software, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte jednopřihlásek na software Comeet Recruiting Software](#configure-comeet-recruiting-software-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu náborového softwaru Comeet](#create-comeet-recruiting-software-test-user)** – chcete-li mít protějšek Britty Simonové v softwaru Comeet Recruiting, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí softwaru Comeet Recruiting Software, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Comeet Recruiting Software** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** Pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Comeet Nábor Software domény a adresy URL jednotného přihlášení informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL pro odpověď. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta náboru softwaru Comeet.](https://support.comeet.co/knowledgebase/adfs-single-sign-on/) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Comeet Nábor Software domény a adresy URL jednotného přihlášení informace](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL:`https://app.comeet.co`

5. Comeet Recruiting Software aplikace očekává, že saml kontrolní výrazy v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Hodnoty těchto atributů můžete spravovat v části **Atributy uživatele** na stránce integrace aplikace. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na **tlačítko Upravit** otevřete dialogové okno **Atributy uživatele.**

    ![image](common/edit-attribute.png)

6. V části **Deklarace identity uživatelů** v dialogovém okně **Atributy uživatele** upravte deklarace pomocí **ikony Upravit** nebo přidejte deklarace pomocí funkce Přidat **novou deklaraci** pro konfiguraci atributu tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky: 

    | Name (Název) |  Atribut zdroje|
    | ---------------| --------------- |
    | nameidentifier | user.mail |
    | comeet_id | user.userprincipalname |

    a. Kliknutím na **Přidat novou deklaraci** otevřete dialogové okno **Spravovat deklarace identity uživatelů.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **Název** zadejte název atributu zobrazený pro daný řádek.

    c. Ponechejte **obor názvů** prázdný.

    d. Vyberte zdroj jako **atribut**.

    e. Ze seznamu **atributů Zdroj** zadejte hodnotu atributu zobrazenou pro daný řádek.

    f. Klikněte na **Ok.**

    g. Klikněte na **Uložit**.

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavit software pro nábor spolupracovníků** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-comeet-recruiting-software-single-sign-on"></a>Konfigurace jednotného přihlášení k softwaru Comeet Recruiting Software

Chcete-li nakonfigurovat jednotné přihlašování na straně **Comeet Recruiting Software,** musíte odeslat stažený **xml metadat federace** a příslušné zkopírované adresy URL z portálu Azure do týmu podpory [náborového softwaru Comeet](https://support.comeet.co/knowledgebase/adfs-single-sign-on/). Toto nastavení nastaví tak, aby bylo připojení s přizasazené k samovazbě SAML správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** zadejte **\@brittasimon vašecompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Comeet Nábor software.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Comeet Recruiting Software**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **možnost Comeet Recruiting Software**.

    ![Odkaz Comeet Recruiting Software v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-comeet-recruiting-software-test-user"></a>Vytvořit uživatele testu náborového softwaru Comeet

V této části vytvoříte uživatele s názvem Britta Simon v Comeet Nábor Software. Spolupracujte s [týmem podpory comeet Recruiting Software](mailto:support@comeet.co) a přidejte uživatele do platformy Comeet Recruiting Software. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Comeet Nábor Software na přístupovém panelu, měli byste být automaticky přihlášeni k Comeet Nábor software, pro které nastavíte přispojené k rámcové služby. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

