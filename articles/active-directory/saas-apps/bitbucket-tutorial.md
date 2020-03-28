---
title: 'Kurz: Integrace služby Azure Active Directory se službou JEDNOTNÉ HO Jednotného přibližuje službu SAML pro bitbucket podle rozlišení GmbH | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a jednotném přihlašování SAML pro bitbucket podle řešení GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: fc947df1-f24e-43ae-9a34-518293583d69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57ae83aee563d4893f767331fff2289a4595cc60
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157640"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Kurz: Integrace služby Azure Active Directory se službou JEDNOTNÉHO PŘIřazuje k systému JEDNOTNÉHO PŘIBLIŽitu pro bitbucket podle rozlišení GmbH

V tomto kurzu se dozvíte, jak integrovat jednotné přihrádky SAML pro Bitbucket podle rozlišení GmbH s Azure Active Directory (Azure AD).
Integrace jednotného přihrádání SAML pro Bitbucket pomocí řešení GmbH s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k jednotnému přijmutí SAML pro Bitbucket podle rozlišení GmbH.
* Můžete povolit, aby vaši uživatelé byli automaticky přihlášeni k jednotnému přihlašování SAML pro Bitbucket podle rozlišení GmbH (Single Sign-On) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s SAML Jednotné hospo, pro Bitbucket podle rozlišení GmbH, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Saml Jednotné přihlašování pro Bitbucket podle rozlišení GmbH jednotné přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SAML Jednotné přihlašované pro Bitbucket usnesením GmbH podporuje **SP a IDP** inicioval jednotné přihlašovat
* SAML Jednotné přihrádání pro Bitbucket podle rozlišení GmbH podporuje **just in time** zřizování uživatelů


## <a name="adding-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Přidání SAML SSO pro Bitbucket usnesením GmbH z galerie

Chcete-li nakonfigurovat integraci jednotného přihrádky SAML pro Bitbucket podle rozlišení GmbH do Azure AD, je třeba přidat SAML Jednotné přihrádky pro Bitbucket podle rozlišení GmbH z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat jednotné přihrádky SAML pro Bitbucket podle rozlišení GmbH z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAML SSO pro Bitbucket podle rozlišení GmbH**, zvolte **SAML SSO pro Bitbucket podle rozlišení GmbH** z panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![SAML SSO pro Bitbucket podle rozlišení GmbH v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování SAML pro bitbucket podle rozlišení GmbH na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v jednotném přihlašování SAML pro bitbucket podle rozlišení GmbH.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování SAML pro Bitbucket podle rozlišení GmbH, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH Single Sign-On](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte jednotné přihlašování SAML pro bitbucket podle rozlišení uživatele gmbh –](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** mít protějšek Britta Simon v SAML Jednotné přihlašování pro Bitbucket podle rozlišení GmbH, který je propojen s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování SAML pro Bitbucket podle překladu GmbH, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **SAML SSO pro Bitbucket podle rozlišení vyberte** **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky, pokud chcete nakonfigurovat aplikaci v režimu iniciovaném **protokolem IDP:**

    ![Saml Jednotné přihlašování pro Bitbucket podle rozlišení GmbH Domény a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/samlsso`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/samlsso`

    c. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Saml Jednotné přihlašování pro Bitbucket podle rozlišení GmbH Domény a adresy URL jednotné přihlašovací informace](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a přihlašovací adresou URL. Obraťte se [na SAML Jednotné hospo- Toto číslo bitbucket podle řešení GmbH Tým podpory klienta](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support) získat tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on"></a>Konfigurace jednotného přihlašování SAML pro bitbucket podle rozlišení GmbH Single Sign-On

1. Přihlaste se k vašemu jednotnému přihlašování SAML pro Bitbucket usnesením společnosti GmbH jako správce.

2. Na pravé straně hlavního panelu nástrojů klepněte na **tlačítko Nastavení**.

3. Přejděte do sekce Účty, klikněte na **SAML SingleSignOn** na panelu nabídek.

    ![The Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. Na **stránce Konfigurace modulu plug-in SAML SIngleSignOn**klepněte na tlačítko **Přidat idp**. 

    ![Přidat idp](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. Na stránce **Vyberte si poskytovatele identity SAML** proveďte následující kroky:

    ![Zprostředkovatel identity](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Vyberte **typ Idp** jako **azure ad**.

    b. Do textového pole **Název** zadejte název.

    c. Do textového pole **Popis** zadejte popis.

    d. Klikněte na **Další**.

6. Na **stránce Konfigurace zprostředkovatele identity**klepněte na tlačítko **Další**.

    ![Konfigurace identity](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  Na stránce **importovat metadata SAML Idp** klikněte na **Načíst soubor** a nahrajte soubor **METADATA XML,** který jste stáhli z webu Azure Portal.

    ![Idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)
    
8. Klikněte na **Další**.

9. Klikněte na **Uložit nastavení**.

    ![Uložení](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k jednotnému přihlašování SAML pro Bitbucket podle řešení GmbH.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte JEDNOTNÉ **PŘIHLAŠOVÁNÍ SAML pro Bitbucket podle překladu GmbH**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **SAML SSO pro Bitbucket podle rozlišení GmbH**.

    ![Odkaz SAML SSO pro Bitbucket podle rozlišení GmbH v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Vytvoření jednotného přihrádky SAML pro bitbucket podle rozlišení uživatele testu GmbH

Cílem této části je vytvořit uživatele s názvem Britta Simon v SAML SSO pro Bitbucket podle rozlišení GmbH. SAML SSO pro Bitbucket usnesením GmbH podporuje zřizování just-in-time a také uživatelé mohou být vytvořeny ručně, kontaktujte [SAML SSO pro Bitbucket usnesením GmbH Client support team](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) podle vašeho požadavku.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici SAML SSO pro Bitbucket podle rozlišení GmbH na přístupovém panelu, měli byste být automaticky přihlášeni k saml jednotného přihrádání pro Bitbucket podle rozlišení GmbH, pro které nastavíte jednotné přihrádání. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

