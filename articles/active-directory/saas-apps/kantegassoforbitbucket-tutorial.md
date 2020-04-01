---
title: 'Kurz: Integrace služby Azure Active Directory s připojetím služby Kantega pro bitbucket | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi službou Azure Active Directory a jednotném přihlašování Kantega pro bitbucket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b04b44c907e3210f3cc3975b36639f4fe275eef9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099216"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Kurz: Integrace služby Azure Active Directory s připojením jednotného připojetého systému Kantega pro bitbucket

V tomto kurzu se dozvíte, jak integrovat zabezpečení přistupuje kantega pro Bitbucket s Azure Active Directory (Azure AD).
Integrace jednotného přibližování kantega pro Bitbucket s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k přistupuje k kantega jednotného připojetí pro Bitbucket.
* Můžete povolit uživatelům, aby se automaticky přihlásili k přihlašování Kantega jednotného přihlašování pro Bitbucket (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s kantega jednotného přikládání k bitbucket, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Jednotné přihlašování Kantega pro předplatné s povoleným jedním přihlášením Bitbucket

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Kantega Jednotné přihlašované místo pro Bitbucket podporuje **SP a IDP** inicioval jednotné přihlašovat

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Přidání Kantega SSO pro Bitbucket z galerie

Chcete-li nakonfigurovat integraci jednotného přibližovaného zařízení Kantega pro Bitbucket do Azure AD, musíte přidat přiřazuje kantega jednotného připojovat pro Bitbucket z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat jednotné přistupovat k systému Kantega pro Bitbucket z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Kantega Jednotné přihlašovací služby pro Bitbucket**, vyberte **Kantega Jednotné přihlašovací služby pro Bitbucket** z panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

    ![Kantega Jednotné přibližné dítě pro Bitbucket v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro bitbucket na základě testovacího uživatele s názvem **Britta Simon**.
Pro jednotné přihlašování k práci, propojení vztah mezi uživatelem Azure AD a související uživatele v Kantega jednotného přihlašování pro Bitbucket musí být vytvořena.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro Bitbucket, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování Kantega pro bitbucket jednotné přihlášení](#configure-kantega-sso-for-bitbucket-single-sign-on)** - ke konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte přihlašování k systému jednotného přihlašování Kanega pro uživatele bitbucket testování](#create-kantega-sso-for-bitbucket-test-user)** – mít protějšek Britta Simon v Kantega jednotného přihlašování pro Bitbucket, který je propojen s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro bitbucket, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Kantega pro bitbucket** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** Pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Jednotné přihlašování kantega pro doménu Bitbucket a adresy URL jednotného přihlášení](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Jednotné přihlašování kantega pro doménu Bitbucket a adresy URL jednotného přihlášení](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a adresou URL pro přihlášení. Tyto hodnoty jsou přijímány během konfigurace pluginu Bitbucket, který je vysvětlen dále v tutoriálu.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V **části Nastavit jednotné přistupovače Kantega pro Bitbucket** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-kantega-sso-for-bitbucket-single-sign-on"></a>Konfigurace jednotného přihlašování Kantega pro jednotné přihlašování bitbucket

1. V jiném okně webového prohlížeče se přihlaste k portálu pro správu Bitbucketu jako správce.

1. Klepněte na kolečko a klepněte **na tlačítko Najít nové doplňky**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. Vyhledejte **službu Jednotné ho přibližovacího systému Kantega pro nástroj Bitbucket SAML & Kerberos** a klepnutím na tlačítko **Nainstalovat** nainstalujte nový plugin SAML.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. Spustí se instalace pluginu.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. Po dokončení instalace. Klikněte na **Zavřít**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon33.png)

1. Klikněte na **Manage** (Spravovat).

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon34.png)

1. Chcete-li nakonfigurovat nový plugin, klepněte na **tlačítko Konfigurovat.**

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. V sekci **SAML.** V rozevíracím seznamu **Přidat zprostředkovatele identity** vyberte **Azure Active Directory (Azure AD).**

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Vyberte úroveň předplatného jako **základní**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. V části **Vlastnosti aplikace** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Zkopírujte hodnotu **IDENTIFIKÁTOR URI id aplikace** a použijte ji jako **identifikátor, adresu URL pro odpověď a adresu URL přihlášení** v části Základní konfigurace **SAML** na webu Azure Portal.

    b. Klikněte na **Další**.

1. V části **Import metadat** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Vpočítači vyberte **soubor metadat**a nahrajte soubor metadat, který jste stáhli z webu Azure Portal.

    b. Klikněte na **Další**.

1. V části **Název a umístění přistaň s tezací služby** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Přidejte název zprostředkovatele identity v textovém poli **názvu zprostředkovatele identity** (například Azure AD).

    b. Klikněte na **Další**.

1. Ověřte podpisový certifikát a klepněte na tlačítko **Další**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. V části **Uživatelské účty Bitbucket** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. V případě potřeby vyberte **vytvořit uživatele v interním adresáři bitbucketu** a zadejte příslušný název skupiny pro uživatele (může být vícenásobné ne. skupin oddělených čárkou).

    b. Klikněte na **Další**.

1. Klikněte na **Finish** (Dokončit).

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. V části **Známé domény pro Azure AD** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. V levém panelu stránky **vyberte Známé domény.**

    b. Zadejte název domény do textového pole **Známé domény.**

    c. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k přihlašování Kantega pro Bitbucket.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte Možnost **jednotného přihlašování Kantega pro bitbucket**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Možnost jednotného přihlašování Kantega pro bitbucket**.

    ![Odkaz Kantega SSO pro Bitbucket v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-kantega-sso-for-bitbucket-test-user"></a>Vytvořit přivytváření jednotného přibližují systému Obnovení přístupu Kantega pro uživatele testu Bitbucket

Chcete-li povolit uživatelům Azure AD k přihlášení k Bitbucket, musí být zřízena do Bitbucket. V případě Kantega jednotného přihlašování pro Bitbucket zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu společnosti Bitbucket jako správce.

1. Klikněte na ikonu nastavení.

    ![Přidat zaměstnance](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. V části **Karta Správa** klikněte na **Uživatelé**.

    ![Přidat zaměstnance](./media/kantegassoforbitbucket-tutorial/user2.png)

1. Klepněte na **tlačítko Vytvořit uživatele**.

    ![Přidat zaměstnance](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. Na stránce dialogového okna **Vytvořit uživatele** proveďte následující kroky:

    ![Přidat zaměstnance](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. Do textového pole **Uživatelské jméno** zadejte Brittasimon@contoso.come-mail uživatele jako .

    b. Do textového pole **Celé jméno** zadejte celé jméno uživatele, jako je Britta Simon.

    c. Do textového pole **E-mailová adresa** Brittasimon@contoso.comzadejte e-mailovou adresu uživatele jako .

    d. Do textového pole **Heslo** zadejte heslo uživatele.

    e. Do textového pole **Potvrdit heslo** zadejte znovu heslo uživatele.

    f. Klepněte na **tlačítko Vytvořit uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Jednotné ho jednotného přijmutí Kantega pro Bitbucket na přístupovém panelu, můžete automaticky přihlášeni k přisymu Kantega jednotného připínděk pro Bitbucket, pro které nastavíte jednotné přistupující místo. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

