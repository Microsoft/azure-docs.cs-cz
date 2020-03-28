---
title: 'Kurz: Integrace služby Azure Active Directory s aplikací RStudio Connect | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a službou RStudio Connect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9bc78022-6d38-4476-8f03-e3ca2551e72e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9a9b49f75ad377a9377a2311ed16c17ca3d749e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092581"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect"></a>Kurz: Integrace služby Azure Active Directory s aplikací RStudio Connect

V tomto kurzu se dozvíte, jak integrovat RStudio Connect s Azure Active Directory (Azure AD).
Integrace Služby RStudio Connect s Azure AD vám přináší následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k RStudio Connect.
* Můžete povolit uživatelům, aby se automaticky přihlásili k RStudio Connect (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí služby RStudio Connect, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* RStudio Connect. K dispozici je [45 denní hodnocení zdarma.](https://www.rstudio.com/products/connect/)

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* RStudio Connect podporuje **sp a IDP** inicioval sso

* RStudio Connect podporuje zřizování uživatelů **just in time**

## <a name="adding-rstudio-connect-from-the-gallery"></a>Přidání rstudio connect z galerie

Chcete-li nakonfigurovat integraci služby RStudio Connect do služby Azure AD, je třeba přidat připojení RStudio z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat aplikaci RStudio Connect z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **RStudio Connect**, vyberte **RStudio Connect** z panelu výsledků a kliknutím na **tlačítko Přidat** přidejte aplikaci.

    ![RStudio Connect v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí služby RStudio Connect na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v RStudio Connect.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí služby RStudio Connect, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování RStudio Connect](#configure-rstudio-connect-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele RStudio Connect](#create-rstudio-connect-test-user)** – chcete-li mít protějšek Britta Simon v RStudio Connect, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí služby RStudio Connect, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **RStudio Connect** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** pokud chcete nakonfigurovat aplikaci v režimu iniciovaném **protokolem IDP,** proveďte následující kroky a nahraďte `<example.com>` ji adresou a portem serveru RStudio Connect Server:

    ![RStudio Connect Domény a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<example.com>/__login__/saml`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<example.com>/__login__/saml/acs`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![RStudio Connect Domény a adresy URL jednotné přihlašovací informace](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<example.com>/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a přihlašovací adresou URL. Jsou určeny z adresy serveru`https://example.com` RStudio Connect (ve výše uvedených příkladech). Pokud máte potíže, obraťte se na [tým podpory RStudio Connect.](mailto:support@rstudio.com) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Aplikace RStudio Connect očekává kontrolní výrazy SAML v určitém formátu, který vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky zobrazuje seznam výchozích atributů, kde jako **nameidentifier** je mapován s **user.userprincipalname**. Aplikace RStudio Connect očekává, že **identifikátor nameidentifier** bude mapován pomocí **user.mail**, takže je třeba upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![image](common/edit-attribute.png)

7. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na tlačítko Kopírovat, chcete-li zkopírovat **adresu URL metadat federace aplikací** a uložit ji do počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-rstudio-connect-single-sign-on"></a>Konfigurace jednotného přihlášení aplikace RStudio Connect

Chcete-li nakonfigurovat jednotné přihlašování pro **službu RStudio Connect**, musíte použít adresu **URL metadat federace aplikací** a adresu serveru **použitou** výše. To se provádí v konfiguračním souboru RStudio Connect na adrese `/etc/rstudio-connect.rstudio-connect.gcfg`.

Toto je příklad konfiguračního souboru:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

Uložte **adresu** serveru `Server.Address` do hodnoty a adresu URL `SAML.IdPMetaData` metadat federace **aplikací** v hodnotě.

Pokud máte potíže s konfigurací, můžete si přečíst [RStudio Connect Admin Guide](https://docs.rstudio.com/connect/admin/authentication.html#authentication-saml) nebo e-mailem [týmu podpory RStudio](mailto:support@rstudio.com) o pomoc.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** `brittasimon@yourcompanydomain.extension`jméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k RStudio Connect.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **RStudio Connect**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Možnost Připojení k aplikaci RStudio**.

    ![Odkaz RStudio Connect v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-rstudio-connect-test-user"></a>Vytvořit testovacího uživatele RStudio Connect

V této části je vytvořen uživatel s názvem Britta Simon v RStudio Connect. RStudio Connect podporuje zřizování just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ještě neexistuje v RStudio Connect, vytvoří se při pokusu o přístup k RStudio Connect nový uživatel.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici RStudio Connect na přístupovém panelu, můžete by měl být automaticky přihlášeni k RStudio Connect, u kterého nastavíte přiřazovací služby. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

