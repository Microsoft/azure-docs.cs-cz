---
title: 'Kurz: Integrace služby Azure Active Directory s Envi MMIS | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Službou Envi MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1689517042713e9a3ce75c6ada822cb3d6ff138
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158265"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Kurz: Integrace Služby Azure Active Directory s Envi MMIS

V tomto kurzu se dozvíte, jak integrovat Envi MMIS s Azure Active Directory (Azure AD).
Integrace Envi MMIS s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Envi MMIS.
* Můžete povolit uživatelům, aby se automaticky přihlásili k Envi MMIS (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Envi MMIS, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné envi MMIS s povoleným jedním přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Envi MMIS podporuje **SP** a **IDP** inicioval sso

## <a name="adding-envi-mmis-from-the-gallery"></a>Přidání Envi MMIS z galerie

Chcete-li nakonfigurovat integraci Envi MMIS do Azure AD, musíte přidat Envi MMIS z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Envi MMIS z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Envi MMIS**, z panelu výsledků vyberte **Envi MMIS** a pak klepněte na **tlačítko Přidat** a přidejte aplikaci.

     ![Envi MMIS v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Envi MMIS na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Envi MMIS.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Envi MMIS, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte envi MMIS Single Sign-On](#configure-envi-mmis-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu Envi MMIS](#create-envi-mmis-test-user)** – chcete-li mít protějšek Britta Simon v Envi MMIS, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí envi MMIS, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Envi MMIS** vyberte **Možnost Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavení jednotného přihlášení pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno **Základní konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** Pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Envi MMIS Domény a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://www.<CUSTOMER DOMAIN>.com/Account`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Envi MMIS Domény a adresy URL jednotné přihlašovací informace](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://www.<CUSTOMER DOMAIN>.com/Account`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a přihlašovací adresou URL. Obraťte [se na tým podpory klienta Envi MMIS](mailto:support@ioscorp.com) a získejte tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V části **Nastavit Envi MMIS** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-envi-mmis-single-sign-on"></a>Konfigurace jednotného přihlašování Provi MMIS

1. V jiném okně webového prohlížeče se přihlaste na web Envi MMIS jako správce.

2. Klikněte na kartu **Moje doména.**

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/envimmis-tutorial/configure1.png)

3. Klikněte na **Upravit**.

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/envimmis-tutorial/configure2.png)

4. Zaškrtněte **políčko Použít vzdálené ověřování** a v rozevíracím poli **Typ ověřování** vyberte možnost Přesměrování **PROTOKOLU HTTP.**

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/envimmis-tutorial/configure3.png)

5. Vyberte **kartu Zdroje** a klepněte na **tlačítko Nahrát metadata**.

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/envimmis-tutorial/configure4.png)

6. V místní části **Nahrát metadata** proveďte následující kroky:

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/envimmis-tutorial/configure5.png)

    a. V rozevíracím **Upload From** souboru vyberte **Soubor.**

    b. Nahrajte stažený soubor metadat z webu Azure Portal výběrem **ikony výběru souboru**.

    c. Klikněte na **OK**.

7. Po nahrání staženého souboru metadat se pole automaticky naplní. Klikněte na **Aktualizovat.**

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/envimmis-tutorial/configure6.png)

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Envi MMIS.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Envi MMIS**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Envi MMIS**.

    ![Odkaz Envi MMIS v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-envi-mmis-test-user"></a>Vytvořit testovacího uživatele Envi MMIS

Aby se uživatelé Azure AD mohli přihlásit k Envi MMIS, musí být zřízeni do Envi MMIS. V případě Envi MMIS zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu společnosti Envi MMIS jako správce.

2. Klikněte na kartu **Seznam uživatelů.**

    ![Přidat zaměstnance](./media/envimmis-tutorial/user1.png)

3. Klikněte na **tlačítko Přidat uživatele.**

    ![Přidat zaměstnance](./media/envimmis-tutorial/user2.png)

4. V části **Přidat uživatele** proveďte následující kroky:

    ![Přidat zaměstnance](./media/envimmis-tutorial/user3.png)

    a. Do textového pole **Uživatelské jméno** zadejte uživatelské jméno účtu Britta Simon, jako **je brittasimon\@contoso.com**.
    
    b. Do textového pole **Křestní jméno** zadejte křestní jméno BrittaSimon jako **Britta**.

    c. Do textového pole **Příjmení** zadejte příjmení BrittaSimon jako **Simon**.

    d. Do pole **Název** textového pole zadejte název uživatele.
    
    e. Do textového pole **E-mailová adresa** zadejte e-mailovou adresu účtu Britta Simon, například **brittasimon\@contoso.com**.

    f. Do textového pole **Uživatelské jméno při uživatelském jménu s** tezaujeme uživatelským jménem zadejte uživatelské jméno účtu Britta Simon, jako je **\@brittasimon contoso.com**.

    g. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Envi MMIS na přístupovém panelu, můžete by měl být automaticky přihlášeni k Envi MMIS, pro které nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

