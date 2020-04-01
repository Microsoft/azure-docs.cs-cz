---
title: 'Kurz: Integrace služby Azure Active Directory se službou StatusPage | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a StavPage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: d947f610e6a753ce2ed349917640b07a55bbb735
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67089881"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Kurz: Integrace služby Azure Active Directory se službou StatusPage

V tomto kurzu se dozvíte, jak integrovat StatusPage s Azure Active Directory (Azure AD).
Integrace StatusPage s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k StatusPage.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke StatusPage (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí Služby StavuPage, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením služby StatusPage

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* StatusPage podporuje iniciované přihlašování k opětovnému přihlašování iniciovaného protokolem **IDP**

## <a name="adding-statuspage-from-the-gallery"></a>Přidání stránky StatusPage z galerie

Chcete-li nakonfigurovat integraci StatusPage do Azure AD, musíte přidat StatusPage z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat stránku StatusPage z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **StatusPage**, z panelu výsledků vyberte **StavPage** a klepnutím na **tlačítko Přidat** přidejte aplikaci.

    ![StatusPage v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí služby StatusPage na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem ve StatusPage.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby StatusPage, je třeba provést následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování stavové stránky](#configure-statuspage-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte stavPage test uživatele](#create-statuspage-test-user)** – mít protějšek Britta Simon v StatusPage, který je propojený s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování azure ad pomocí StatusPage, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **StatusPage** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** proveďte následující kroky:

    ![Informace o jednotném přihlášení domény a adresy URL služby StatusPage](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/`|
    | `https://<subdomain>.statuspage.io/`|

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume`|
    | `https://<subdomain>.statuspage.io/sso/saml/consume`|

    > [!NOTE]
    > Obraťte se na [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)tým podpory StatusPage na vyžádání metadat potřebných ke konfiguraci jednotného přihlášení. 
    >
    > a. Z metadat zkopírujte hodnotu Vystavitele a vložte ji do textového pole **Identifikátor.**
    >
    > b. Z metadat zkopírujte adresu URL pro odpověď a vložte ji do textového pole **Adresa URL pro odpověď.**

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit stavovou stránku** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-statuspage-single-sign-on"></a>Konfigurace jednotného přihlašování stavové stránky

1. V jiném okně prohlížeče se přihlaste k webu společnosti StatusPage jako správce.

1. Na hlavním panelu nástrojů klepněte na **položku Spravovat účet**.

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klikněte na kartu **Jednotné přihlášení.**

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Na stránce Nastavení přistaň provedete následující kroky:

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. V textovém poli **cílová adresa URL služby Přihlašuje** meze vložte hodnotu **přihlašovací adresy URL**, kterou jste zkopírovali z webu Azure Portal.

    b. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte obsah a vložte ho do textového pole **Certifikát.**

    c. Klepněte na tlačítko **ULOŽIT KONFIGURACI**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** typ pole`brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k StatusPage.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **StatusPage**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Možnost StavPage**.

    ![Odkaz StatusPage v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-statuspage-test-user"></a>Vytvořit uživatele testu StatusPage

Cílem této části je vytvořit uživatele s názvem Britta Simon v StatusPage.

StatusPage podporuje zřizování just-in-time. Už jste ji povolili v [aplikaci Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on).

**Chcete-li vytvořit uživatele s názvem Britta Simon v StatusPage, proveďte následující kroky:**

1. Přihlaste se k webu společnosti StatusPage jako správce.

1. V nabídce v horní části klikněte na **Spravovat účet**.

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klikněte na kartu **Členové týmu.**
  
    ![Vytvoření testovacího uživatele Azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Klikněte na **přidat člena týmu**.
  
    ![Vytvoření testovacího uživatele Azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Zadejte **e-mailovou adresu**, **jméno**a **příjmení** platného uživatele, kterého chcete zřídit, do souvisejících textových polí. 

    ![Vytvoření testovacího uživatele Azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Jako **roli**zvolte **Správce klienta**.

1. Klepněte na **tlačítko VYTVOŘIT ÚČET**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici StatusPage na přístupovém panelu, měli byste být automaticky přihlášeni k StatusPage, pro které nastavíte přiřazování k is lokace. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
