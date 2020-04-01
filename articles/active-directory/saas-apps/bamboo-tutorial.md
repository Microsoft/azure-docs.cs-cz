---
title: 'Kurz: Integrace služby Azure Active Directory se službou JEDNOTNÉHO PŘIbrádat služby SAML pro bamboo podle překladu GmbH | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a jednotném přihlašování SAML pro bamboo podle řešení GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 044a2f9a9ecd0ccceb99ce7999af7e2c8578950d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67106537"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Kurz: Integrace služby Azure Active Directory se službou JEDNOTNÉHO PŘIhrádka SAML pro bamboo podle rozlišení GmbH

V tomto kurzu se dozvíte, jak integrovat jednotné přihrádky SAML pro Bamboo podle rozlišení GmbH s Azure Active Directory (Azure AD).
Integrace jednotného přihrádání SAML pro bamboo pomocí řešení GmbH s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k jednotnému přijmutí SAML pro bamboo podle rozlišení GmbH.
* Uživatelům můžete povolit automatické přihlášení k jednotnému přihlašování SAML pro bamboo podle rozlišení GmbH (Single Sign-On) se svými účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s SAML Jednotné přibližování pro Bamboo podle rozlišení GmbH, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* SAML Jednotné přihlašování pro Bamboo podle rozlišení GmbH s povoleným jednorázovým přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SAML SSO pro Bamboo usnesením GmbH podporuje **SP a IDP** inicioval SSO
* SAML SSO pro Bamboo usnesením GmbH podporuje **just in time** zřizování uživatelů

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Přidání SAML SSO pro Bamboo usnesením GmbH z galerie

Chcete-li nakonfigurovat integraci jednotného přihrádky SAML pro bamboo podle rozlišení GmbH do Azure AD, je třeba přidat SAML Jednotné přihrádky pro Bamboo podle rozlišení GmbH z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat saml jednotné hodu pro bamboo podle rozlišení GmbH z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAML SSO for Bamboo by resolution GmbH**, z panelu výsledků vyberte **možnost SAML SSO pro bamboo podle rozlišení GmbH** a pak klepnutím na tlačítko **Přidat** aplikaci přidejte.

    ![SAML SSO pro Bamboo podle rozlišení GmbH v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování SAML pro bamboo podle překladu GmbH na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v jednotném přihlašování SAML pro bamboo podle rozlišení GmbH.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování SAML pro bamboo podle rozlišení GmbH, je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování SAML pro bamboo podle rozlišení GmbH Single Sign-On](#configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte SAML Jednotné přihlašování pro Bamboo podle rozlišení GmbH testovací uživatel](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** – mít protějšek Britta Simon v SAML Jednotné přihlašování pro Bamboo podle rozlišení GmbH, který je propojen s Azure AD reprezentace uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování SAML pro bamboo podle překladu GmbH, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **SAML SSO for Bamboo podle rozlišení GmbH** vyberte **jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Saml Jednotné přihlašování pro Bamboo podle rozlišení GmbH Domény a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/samlsso`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/samlsso`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Saml Jednotné přihlašování pro Bamboo podle rozlišení GmbH Domény a adresy URL jednotné přihlašovací informace](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a přihlašovací adresou URL. Obraťte se [na SAML SSO pro Bamboo usnesením GmbH Client support team](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) získat tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V části **Nastavit jednotné přihrádky SAML pro bamboo podle rozlišení GmbH** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on"></a>Konfigurace jednotného přihlašování SAML pro bamboo podle rozlišení GmbH Single Sign-On

1. Přihlaste se ke svému saml jednotnému přihlašování pro Bamboo usnesením společnosti GmbH jako správce.

1. Na pravé straně hlavního panelu nástrojů klepněte na **položku Nastavení** > **doplňků**.

    ![Nastavení](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Přejděte do sekce ZABEZPEČENÍ, klikněte na **SAML SingleSignOn** na panelu nabídek.

    ![The Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Na **stránce Konfigurace modulu plug-in SAML SIngleSignOn**klepněte na tlačítko **Přidat idp**.

    ![Přidat idp](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Na stránce **Vyberte si poskytovatele identity SAML** proveďte následující kroky:

    ![Zprostředkovatel identity](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Vyberte **typ Idp** jako **azure ad**.

    b. Do textového pole **Název** zadejte název.

    c. Do textového pole **Popis** zadejte popis.

    d. Klikněte na **Další**.

1. Na **konfigurační stránce zprostředkovatele identity** klepněte na tlačítko **Další**.

    ![Konfigurace identity](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. Na stránce **importovat metadata SAML Idp** klikněte na **Načíst soubor** a nahrajte soubor **METADATA XML,** který jste stáhli z webu Azure Portal.

    ![Idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Klikněte na **Další**.

1. Klikněte na **Uložit nastavení**.

    ![Uložení](./media/bamboo-tutorial/tutorial_bamboo_save.png)

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k jednotnému přihlašování SAML pro Bamboo podle rozlišení GmbH.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **SAML Jednotné přihlašování pro bamboo podle překladu GmbH**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **SAML SSO pro Bamboo podle rozlišení GmbH**.

    ![Odkaz SAML SSO pro Bamboo podle rozlišení GmbH v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Vytvoření saml sso pro bambus podle rozlišení GmbH testovací uživatel

Cílem této sekce je vytvořit uživatele s názvem Britta Simon v SAML SSO pro Bamboo usnesením GmbH. SAML SSO pro Bamboo usnesením GmbH podporuje just-in-time zřizování a také uživatelé mohou být vytvořeny ručně, kontaktujte [SAML SSO pro Bamboo usnesením GmbH Client support team](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) podle vašeho požadavku.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici SAML SSO pro bamboo podle rozlišení GmbH na přístupovém panelu, měli byste být automaticky přihlášeni k SAML Jednotné přihrádky pro Bamboo podle rozlišení GmbH, pro které nastavíte jednotné přistupující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
