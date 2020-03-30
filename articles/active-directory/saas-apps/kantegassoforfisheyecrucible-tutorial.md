---
title: 'Kurz: Integrace služby Azure Active Directory s přihlašováním k jednotnému přihlašování kantega pro fisheye/crucible | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a jednotném přihlašováním Kantega pro FishEye/Crucible.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9fe951fd-1530-4d33-a1a4-390385b99ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f262f94c451d5dcffd933bdebb1374b8733b9fd8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67595173"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-fisheyecrucible"></a>Kurz: Integrace služby Azure Active Directory s přihlašováním k jednotnému přihlašování Kantega pro fisheye/crucible

V tomto kurzu se dozvíte, jak integrovat zabezpečení přihlašování Kantega pro FishEye/Crucible s Azure Active Directory (Azure AD).
Integrace jednotného přihlašování Kantega pro FishEye/Crucible s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k zabezpečení jednotného přihlašování Kantega pro FishEye/Crucible.
* Můžete povolit uživatelům, aby se automaticky přihlásili k jednotnému přihlašování Kantega pro FishEye/Crucible (jednotné přihlašování) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s kantega jednotného přihlašování pro FishEye/Crucible, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Kantega Jednotné přihlašování pro předplatné s povoleným jedním přihlášením FishEye/Crucible

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Kantega SSO pro FishEye/Crucible podporuje **SP a IDP** inicioval Jednotné přihlašování

## <a name="adding-kantega-sso-for-fisheyecrucible-from-the-gallery"></a>Přidání Kantega SSO pro FishEye /Crucible z galerie

Chcete-li nakonfigurovat integraci jednotného přihlašování Kantega pro FishEye/Crucible do Azure AD, musíte přidat kantega jednotného přihlašování pro FishEye/Crucible z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Kantega SSO pro FishEye/Crucible z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Kantega SSO pro FishEye/Crucible**, zvolte **Kantega SSO pro FishEye/Crucible** z panelu výsledků a pak kliknutím na **tlačítko Přidat** přidejte aplikaci.

    ![Kantega SSO pro FishEye/Crucible v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro FishEye/Crucible na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v přihlašování k upisování služby Kantega pro FishEye/Crucible.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro FishEye/Crucible, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování Kantega pro jedno přihlášení FishEye/Crucible](#configure-kantega-sso-for-fisheyecrucible-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte Kantega jednotného přihlašování pro uživatele fisheye/crucible test](#create-kantega-sso-for-fisheyecrucible-test-user)** – mít protějšek Britta Simon v Kantega jednotného přihlašování pro FishEye/Crucible, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro FishEye/Crucible, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Kantega Pro FishEye/Crucible** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Kantega Jednotné přihlašování pro FishEye/Crucible Domain a URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Kantega Jednotné přihlašování pro FishEye/Crucible Domain a URL jednotné přihlašovací informace](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a adresou URL pro přihlášení. Tyto hodnoty jsou přijímány během konfigurace pluginu FishEye/Crucible, který je vysvětlen dále v tutoriálu.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V části **Nastavit jednotné přihlašování Kantega pro FishEye/Crucible** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-kantega-sso-for-fisheyecrucible-single-sign-on"></a>Konfigurace jednotného přihlašování Kantega pro jednotné přihlašování FishEye/Crucible

1. V jiném okně webového prohlížeče se přihlaste k místnímu serveru FishEye/Crucible jako správce.

1. Najeďte přes kolečko a klikněte **na doplňky**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/addon1.png)

1. V části Nastavení systému klikněte na **Najít nové doplňky**. 

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/add-on2.png)

1. Vyhledejte nového pluginu SAML klepnutím na tlačítko Nainstalovat pro **službu Kantega SSO pro crucible** a klepnutím na tlačítko **Nainstalovat.**

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/addon2.png)

1. Spustí se instalace pluginu. 

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/addon33.png)

1. Po dokončení instalace. Klikněte na **Zavřít**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/addon34.png)

1.  Klikněte na **Manage** (Spravovat).

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/addon35.png)

1. Chcete-li nakonfigurovat nový plugin, klepněte na **tlačítko Konfigurovat.** 

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/addon3.png)

1. V sekci **SAML.** V rozevíracím seznamu **Přidat zprostředkovatele identity** vyberte **Azure Active Directory (Azure AD).**

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/addon4.png)

1. Vyberte úroveň předplatného jako **základní**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/addon5.png)

1. V části **Vlastnosti aplikace** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/addon6.png)

    a. Zkopírujte hodnotu **IDENTIFIKÁTOR URI id aplikace** a použijte ji jako **identifikátor, adresu URL pro odpověď a adresu URL přihlášení** v části Základní konfigurace **SAML** na webu Azure Portal.

    b. Klikněte na **Další**.

1. V části **Import metadat** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/addon7.png)

    a. Vpočítači vyberte **soubor metadat**a nahrajte soubor metadat, který jste stáhli z webu Azure Portal.

    b. Klikněte na **Další**.

1. V části **Název a umístění přistaň s tezací služby** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/addon8.png)

    a. Přidejte název zprostředkovatele identity v textovém poli **názvu zprostředkovatele identity** (například Azure AD).

    b. Klikněte na **Další**.

1. Ověřte podpisový certifikát a klepněte na tlačítko **Další**.   

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/addon9.png)

1. V části **Uživatelské účty FishEye** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/addon10.png)

    a. V případě potřeby vyberte **vytvořit uživatele v interním adresáři fisheye** a zadejte příslušný název skupiny pro uživatele (může být vícenásobné ne. skupin oddělených čárkou).

    b. Klikněte na **Další**.

1. Klikněte na **Finish** (Dokončit).

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/addon11.png)

1. V části **Známé domény pro Azure AD** proveďte následující kroky:  

    ![Konfigurace jednotného přihlašování](./media/kantegassoforfisheyecrucible-tutorial/addon12.png)

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
  
    b. V poli **Uživatelské** `brittasimon@yourcompanydomain.extension`jméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Kantega jednotného přihlašování pro FishEye/Crucible.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Kantega Jednotné přihlašování pro FishEye/Crucible**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **možnost Kantega SSO pro FishEye/Crucible**.

    ![Odkaz Kantega SSO pro FishEye/Crucible v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-kantega-sso-for-fisheyecrucible-test-user"></a>Vytvořit Kantega SSO pro uživatele testu FishEye/Crucible

Chcete-li povolit uživatelům Azure AD přihlásit se k FishEye/Crucible, musí být zřízena do FishEye/Crucible. V Kantega Jednotné ho disponiálu pro FishEye/Crucible zřizování je ruční úloha.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k místnímu serveru Crucible jako správce.

1. Najeďte přes kolečko a klikněte na **tlačítko Uživatelé**.

    ![Přidat zaměstnance](./media/kantegassoforfisheyecrucible-tutorial/user1.png)

1. V části **Uživatelé** klikněte na **Přidat uživatele**.

    ![Přidat zaměstnance](./media/kantegassoforfisheyecrucible-tutorial/user2.png)

1. Na stránce dialogového okna **Přidat nového uživatele** proveďte následující kroky:

    ![Přidat zaměstnance](./media/kantegassoforfisheyecrucible-tutorial/user3.png)

    a. Do textového pole **Uživatelské jméno** zadejte Brittasimon@contoso.come-mail uživatele jako .

    b. Do textového pole **Zobrazované jméno** zadejte zobrazované jméno uživatele, jako je Britta Simon.

    c. Do textového pole **E-mailová adresa** Brittasimon@contoso.comzadejte e-mailovou adresu uživatele jako .

    d. Do textového pole **Heslo** zadejte heslo uživatele.

    e. Do textového pole **Potvrdit heslo** zadejte znovu heslo uživatele.

    f. Klikněte na **Přidat**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Kantega Jednotné přihlašování pro FishEye/Crucible na přístupovém panelu, měli byste být automaticky přihlášeni k kantega jednotného přihlašování pro FishEye/Crucible, pro které nastavíte jednotné přihlašování. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)