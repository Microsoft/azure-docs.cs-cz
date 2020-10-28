---
title: 'Kurz: Azure Active Directory integrace s Sequr | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sequr.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: d1993f89f8b1da4789541006c15380d2145d3d81
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674489"
---
# <a name="tutorial-azure-active-directory-integration-with-sequr"></a>Kurz: Azure Active Directory integrace s Sequr

V tomto kurzu se dozvíte, jak integrovat Sequr s Azure Active Directory (Azure AD).
Integrace Sequr s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Sequr.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Sequr (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s Sequr potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným Sequrm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Sequr podporuje jednotné přihlašování (SSO) **a IDP** .

## <a name="adding-sequr-from-the-gallery"></a>Přidání Sequr z Galerie

Pokud chcete nakonfigurovat integraci Sequr do služby Azure AD, musíte přidat Sequr z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Sequr z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Sequr** , vyberte **Sequr** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Sequr v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Sequr na základě testovacího uživatele s názvem **Britta Simon** .
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Sequr.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Sequr, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Sequr](#configure-sequr-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Sequr Test User](#create-sequr-test-user)** – pro Britta Simon v Sequr, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Sequr, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Sequr** vyberte **jednotné přihlašování** .

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující krok:

    ![Informace o jednotném přihlašování v doméně Sequr a adresách URL](common/idp-identifier.png)

    Do textového pole **identifikátor** zadejte adresu URL:  `https://login.sequr.io`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![image](common/both-advanced-urls.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://login.sequr.io`

    b. Do textového pole **stav přenosu** se zobrazí tato hodnota, která je vysvětleno dále v tomto kurzu.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavení Sequr** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-sequr-single-sign-on"></a>Nakonfigurovat Sequr jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu Sequr společnosti jako správce.

1. V levém navigačním panelu klikněte na **integrace** .

    ![Snímek obrazovky znázorňuje integraci vybranou z navigačního panelu.](./media/sequr-tutorial/configure1.png)

1. Přejděte dolů k části **jednotné přihlašování** a klikněte na **Spravovat** .

    ![Snímek obrazovky s vybraným tlačítkem spravovat zobrazí oddíl jednotného přihlašování.](./media/sequr-tutorial/configure2.png)

1. V části **Spravovat jednotné přihlašování** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí oddíl spravovat jednu Sign-On, kde můžete zadat hodnoty, které jsou popsány.](./media/sequr-tutorial/configure3.png)

    a. Do textového pole **URL s jedním Sign-On URL poskytovatele identity** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. Přetáhněte soubor **certifikátu** , který jste stáhli z Azure Portal, nebo ručně zadejte obsah certifikátu.

    c. Po uložení konfigurace se vygeneruje hodnota stav přenosu. Zkopírujte hodnotu **stavu přenosu** a vložte ji do textového pole **stav přenosu** **základního konfiguračního oddílu SAML** v Azure Portal.

    d. Klikněte na **Uložit** .

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory** , vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé** .

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon** .
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit** .

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Sequr.

1. V Azure Portal vyberte **podnikové aplikace** , vyberte **všechny aplikace** a pak vyberte **Sequr** .

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Sequr** .

    ![Odkaz Sequr v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny** .

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-sequr-test-user"></a>Vytvořit testovacího uživatele Sequr

V této části vytvoříte uživatele s názvem Britta Simon v Sequr. Pokud chcete přidat uživatele na platformě Sequr, pracujte s [týmem podpory klienta Sequr](mailto:support@sequr.io) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Sequr, měli byste se automaticky přihlásit k Sequr, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)