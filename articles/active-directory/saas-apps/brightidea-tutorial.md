---
title: 'Kurz: Integrace služby Azure Active Directory s Brightidea | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Brightidea.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3adae3e0-f43b-492f-b373-6a512d2d6046
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff89f7323ba4cdf14ed1c052b2dd4e4e06356e00
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157617"
---
# <a name="tutorial-azure-active-directory-integration-with-brightidea"></a>Kurz: Integrace služby Azure Active Directory s Brightidea

V tomto kurzu se dozvíte, jak integrovat Brightidea s Azure Active Directory (Azure AD).
Integrace Brightidea s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Brightidea.
* Můžete povolit uživatelům, aby se automaticky přihlásili k Brightidea (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí aplikace Brightidea, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením Brightidea

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.


* Brightidea podporuje **sp a IDP** inicioval sso
* Brightidea podporuje zřizování uživatelů **just in time**


## <a name="adding-brightidea-from-the-gallery"></a>Přidání Brightidea z galerie

Chcete-li nakonfigurovat integraci Brightidea do Azure AD, musíte přidat Brightidea z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Brightidea z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Brightidea**, z panelu výsledků vyberte **Brightidea** a pak klepnutím na **tlačítko Přidat** přidejte aplikaci.

     ![Brightidea v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí aplikace Brightidea na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v aplikaci Brightidea.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí aplikace Brightidea, je třeba provést následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace brightidea jednotného přihlášení](#configure-brightidea-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu Brightidea](#create-brightidea-test-user)** – chcete-li mít protějšek Britta Simon v Brightidea, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí aplikace Brightidea, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikace **Brightidea** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud máte **soubor metadat poskytovatele služeb** a chcete je konfigurovat v intiatovaném režimu **IDP,** proveďte v části **Základní konfigurace SAML** následující kroky:

    a. Klikněte na **Nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klepněte na tlačítko **Nahrát**.

    ![výběr souboru metadat](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL identifikátorů** a **odpovědí** automaticky vyplní v textovém poli oddílu Brightidea:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Pokud hodnoty **adresy URL** **identifikátorů** a odpovědí nejsou automaticky polulated, vyplňte hodnoty ručně podle vašeho požadavku.

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![image](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<SUBDOMAIN>.brightidea.com`

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavit Aplikaci Brightidea** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-brightidea-single-sign-on"></a>Konfigurace jednotného přihlašování Brightidea

1. V jiném okně webového prohlížeče se přihlaste k aplikaci Brightidea pomocí přihlašovacích údajů správce.

2. Chcete-li přejít k funkci s teso s vlastním nastavením v systému Brightidea, přejděte na kartu Ověřování **podle nastavení** -> rozlehlé**sítě**. Zde uvidíte dvě dílčí karty: Auth Selection & SAML profily.

    ![Konfigurace brightidea](./media/brightidea-tutorial/configure1.png)

3. Vyberte **výběr auth**. Ve výchozím nastavení zobrazuje pouze dvě standardní metody: Brightidea Login & Registrace. Když je přidána metoda s přiřazování, zobrazí se v seznamu.

    ![Konfigurace brightidea](./media/brightidea-tutorial/configure2.png)

4. Vyberte **profily SAML** a proveďte následující kroky:

    ![Konfigurace brightidea](./media/brightidea-tutorial/configure3.png)

    a. Klikněte na **stáhnout metadata** a nahrát v části **Základní konfigurace SAML** na webu Azure Portal.

    b. Klikněte na tlačítko **Přidat nový** pod **nastavením zprostředkovatele identity** a proveďte následující kroky:
    
    ![Konfigurace brightidea](./media/brightidea-tutorial/configure4.png)
    
   * Zadejte **název profilu SAML,** například`Azure Ad SSO`
    
   * V **části Nahrát metadata**klikněte na vybrat soubor a nahrajte stažený soubor metadat z webu Azure Portal.

     > [!NOTE]
     > Po nahrání souboru metadat se automaticky naplní zbývající pole **Služba jednotného přihlášení, Vydavatel zprostředkovatele identity, Nahrát veřejný klíč.**

   * Do textového pole **E-mail** zadejte hodnotu jako `mail`.
     
   * Do textového pole **Název obrazovky** `givenName`zadejte hodnotu jako .
     
   * Klikněte na **Save Changes** (Uložit změny).  

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. Do pole **Uživatelské jméno** zadejte **\@brittasimon yourcompanydomain.extension .**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Brightidea.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Brightidea**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Brightidea**.

    ![Odkaz Brightidea v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-brightidea-test-user"></a>Vytvořit uživatele testu Brightidea

V této části je vytvořen uživatel s názvem Britta Simon v Brightidea. Brightidea podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ještě neexistuje v Aplikaci Brightidea, vytvoří se po ověření nový uživatel.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Brightidea na přístupovém panelu, můžete by měl být automaticky přihlášeni k Brightidea, u kterého nastavíte přistupující ho. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

