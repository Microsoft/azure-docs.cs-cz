---
title: 'Kurz: Azure Active Directory integrace s Help Scout | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a help Scout.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: 7c5e8210bc8b805d72149fd2ef3335c1d637a58f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92445184"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Kurz: Azure Active Directory integrace s Help Scout

V tomto kurzu se naučíte integrovat Help Scout s Azure Active Directory (Azure AD).
Integrace Help Scout s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k nápovědě Scout.
* Můžete uživatelům povolit, aby se automaticky přihlásili k aplikaci Scout (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí programu Help Scout budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Přihlašování k předplatnému s jednotným přihlašováním v Scout

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Help Scout podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování
* Help Scout podporuje zřizování uživatelů **jenom v čase**

## <a name="adding-help-scout-from-the-gallery"></a>Přidání Help Scout z Galerie

Pokud chcete nakonfigurovat integraci Help Scout do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Help Scout z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **help Scout** .
1. Vyberte **help Scout** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí programu Help Scout na základě testovacího uživatele s názvem **B. Simon**.
Aby jednotné přihlašování fungovalo, je potřeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v nápovědě Scout.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Help Scout, musíte dokončit tyto stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Help Scout SSO](#configure-help-scout-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte Help Scout Test User](#create-help-scout-test-user)** – Pokud chcete mít protějšek B. Simon v nápovědě Scout, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Help Scout, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce aplikace **help Scout** Application Integration vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

1. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

1. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat identifikátor, odpovědět U R L a vybrat Uložit.](common/idp-intiated.png)

    a. **Identifikátor** je identifikátor **URI cílové skupiny (ID entity poskytovatele služeb)** z Help Scout, začíná na `urn:`

    b. **Adresa URL odpovědi** je **Adresa URL pro zpětné odeslání (adresa URL služby potvrzení)** z Help Scout, začíná na `https://` 

    > [!NOTE]
    > Hodnoty v těchto adresách URL jsou pouze pro ukázku. Tyto hodnoty je potřeba aktualizovat ze skutečné adresy URL a identifikátoru odpovědi. Tyto hodnoty získáte na kartě **jednotného přihlašování** v části ověřování, které jsou vysvětleny dále v tomto kurzu.

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL jako: `https://secure.helpscout.net/members/login/`

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit Help Scout** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **B. Simon**.
  
    b. Do pole **uživatelské jméno** zadejte **B. Simon \@ yourcompanydomain. extension.**  
    Například B.Simon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k nápovědě Scout.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **help Scout**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **help Scout**.

    ![Odkaz Help Scout v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-help-scout-sso"></a>Konfigurace Help Scout SSO

1. Chcete-li automatizovat konfiguraci v rámci programu Help Scout, je nutné nainstalovat **rozšíření prohlížeče zabezpečeného přihlašování aplikace** kliknutím na tlačítko **nainstalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na tlačítko **nastavit pomoc Scout** . budete přesměrováni na aplikaci Help Scout. Odtud zadejte přihlašovací údaje správce pro přihlášení k nápovědě Scout. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit aplikaci Help Scout ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu Help Scout společnosti jako správce a proveďte následující kroky:

1. V horní nabídce klikněte na možnost **Spravovat** a v rozevírací nabídce vyberte možnost **Společnost** .

    ![Snímek obrazovky s vybranou společností zobrazí nabídku Správa.](./media/helpscout-tutorial/settings1.png)

1. V levém navigačním podokně vyberte **ověřování** .

    ![Snímek obrazovky zobrazuje vybrané ověřování.](./media/helpscout-tutorial/settings2.png)

1. Tím přejdete do části nastavení SAML a provedete následující kroky:

    ![Snímek obrazovky zobrazuje jednu kartu Sign-On, kde zadáte zadané informace.](./media/helpscout-tutorial/settings3.png)

    a. Zkopírujte hodnotu **adresy URL pro zpětný příspěvek (URL služby assertion Consumer Service)** a vložte ji do textového pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

    b. Zkopírujte hodnotu **identifikátor URI cílové skupiny (ID entity poskytovatele služby)** a vložte hodnotu do textového pole **identifikátor** v části **základní konfigurace SAML** v Azure Portal.

1. Přepněte na **Povolit SAML** v a proveďte následující kroky:

    ![Snímek obrazovky ukazuje jednu kartu Sign-On, kde můžete povolit SAML a přidat další informace.](./media/helpscout-tutorial/settings4.png)

    a. Do textového pole **URL s jedním Sign-On** vložte hodnotu **přihlašovací adresy URL**, kterou jste zkopírovali z Azure Portal.

    b. Kliknutím na **nahrát certifikát** nahrajte **certifikát (Base64)** stažený z Azure Portal.

    c. Zadejte e-mailové domény vaší organizace e.x. – `contoso.com` v textovém poli **e-mailové domény** . Více domén můžete oddělit čárkou. Kdykoli vám pomůže uživatel nebo správce Scout, který zadá konkrétní doménu na přihlašovací [stránce help Scout](https://secure.helpscout.net/members/login/) , směrovat do zprostředkovatele identity, aby se ověřil s jejich přihlašovacími údaji.

    d. Nakonec můžete přepnout **vynucené přihlášení** pomocí protokolu SAML, pokud chcete, aby se uživatelé přihlásili jenom k nápovědě Scout prostřednictvím této metody. Pokud si chcete ponechat možnost, aby se přihlásili pomocí přihlašovacích údajů pro nápovědu Scout, můžete je nechat zapnuté. I když je tato možnost povolená, vlastník účtu se vždycky bude moct přihlásit k nápovědě Scout s heslem k účtu.

    e. Klikněte na **Uložit**.

### <a name="create-help-scout-test-user"></a>Vytvoření Help Scout Test User

V této části se v nápovědě Scout vytvoří uživatel s názvem B. Simon. Help Scout podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v nápovědě Scout neexistuje, vytvoří se po ověření nový.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Help Scout na přístupovém panelu byste se měli automaticky přihlášeni k nápovědě Scout, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Help Scout s Azure AD](https://aad.portal.azure.com/)