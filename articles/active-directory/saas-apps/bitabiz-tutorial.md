---
title: 'Kurz: Azure Active Directory integrace s BitaBIZ | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a BitaBIZ.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: f564032873be6e4c70426d48c4576371862ea35d
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673597"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Kurz: Azure Active Directory integrace s BitaBIZ

V tomto kurzu se dozvíte, jak integrovat BitaBIZ s Azure Active Directory (Azure AD).
Integrace BitaBIZ s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k BitaBIZ.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k BitaBIZ (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s BitaBIZ potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným BitaBIZm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* BitaBIZ podporuje jednotné přihlašování (SSO) **a IDP** .

## <a name="adding-bitabiz-from-the-gallery"></a>Přidání BitaBIZ z Galerie

Pokud chcete nakonfigurovat integraci BitaBIZ do služby Azure AD, musíte přidat BitaBIZ z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat BitaBIZ z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **BitaBIZ**, vyberte **BitaBIZ** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![BitaBIZ v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí BitaBIZ na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v BitaBIZ.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí BitaBIZ, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování BitaBIZ](#configure-bitabiz-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření BitaBIZ Test User](#create-bitabiz-test-user)** – pro Britta Simon v BitaBIZ, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí BitaBIZ, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **BitaBIZ** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující krok:

    ![Informace o jednotném přihlašování v doméně BitaBIZ a adresách URL](common/idp-identifier.png)

    Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:  `https://www.bitabiz.com/<instanceId>`

    > [!NOTE]
    > Hodnota uvedená v poli Adresa URL je určena pouze pro ukázku. Aktualizujte hodnotu skutečným identifikátorem, který je vysvětlen později v tomto kurzu.

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![image](common/both-preintegrated-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://www.bitabiz.com/dashboard`

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavení BitaBIZ** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-bitabiz-single-sign-on"></a>Nakonfigurovat BitaBIZ jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k tenantovi BitaBIZ jako správce.

2. Klikněte na **Správce instalace**.

    ![Snímek obrazovky zobrazuje část okna prohlížeče se zvoleným správcem instalace.](./media/bitabiz-tutorial/settings1.png)

3. V části **Přidat hodnotu** klikněte na **integrace Microsoftu** .

    ![Snímek obrazovky ukazuje možnost Přidat hodnotu s vybranými integrací Microsoftu.](./media/bitabiz-tutorial/settings2.png)

4. Přejděte dolů k části **Microsoft Azure AD (Povolit jednotné přihlašování)** a proveďte následující kroky:

    ![Snímek obrazovky ukazuje Microsoft Azure oddíl D, kde zadáte informace popsané v tomto kroku.](./media/bitabiz-tutorial/settings3.png)

    a. Zkopírujte hodnotu z pole **ID entity ("identifikátor" v Azure AD)** TextBox a vložte ji do textového pole **identifikátoru** v **základní části Konfigurace SAML** v Azure Portal. 

    b. Do textového pole **Adresa URL služby Azure AD Single Sign-On Service** vložte **přihlašovací adresu URL**, kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **ID entity Azure AD SAML** vložte **identifikátor Azure AD**, který jste zkopírovali z Azure Portal.

    d. Otevřete stažený soubor **certifikátu (Base64)** v programu Poznámkový blok, zkopírujte jeho obsah do schránky a vložte ho do textového pole pro **podpisový certifikát Azure AD (kódovaný v kódování Base64)** .

    e. Přidejte název vaší obchodní e-mailové domény, mycompany.com v textovém poli **název domény** , abyste uživatelům ve vaší společnosti přiřadili jednotné přihlašování k této e-mailové doméně (není povinná).

    f. Označte **jednotné přihlašování s povoleným** účtem BitaBIZ.

    například Kliknutím na **Uložit konfiguraci Azure AD** uložte a aktivujte konfiguraci jednotného přihlašování.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.

    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k BitaBIZ.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **BitaBIZ**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **BitaBIZ**.

    ![Odkaz BitaBIZ v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-bitabiz-test-user"></a>Vytvořit testovacího uživatele BitaBIZ

Aby se uživatelé Azure AD mohli přihlásit k BitaBIZ, musí se zřídit v BitaBIZ.  
V případě BitaBIZ je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu BitaBIZ společnosti jako správce.

2. Klikněte na **Správce instalace**.

    ![Snímek obrazovky zobrazí část okna prohlížeče se zvoleným správcem instalace.](./media/bitabiz-tutorial/settings1.png)

3. Klikněte na **Přidat uživatele** v části **organizace** .

    ![Snímek obrazovky s vybranými možnostmi Přidat uživatele zobrazí oddíl organizace.](./media/bitabiz-tutorial/user1.png)

4. Klikněte na **Přidat nového zaměstnance**.

    ![Snímek obrazovky ukazuje přidat uživatele s vybraným přidat nového zaměstnance.](./media/bitabiz-tutorial/user2.png)

5. Na stránce **Přidat nového zaměstnance** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí stránka, kde zadáte informace popsané v tomto kroku.](./media/bitabiz-tutorial/user3.png)

    a. Do textového pole **jméno v prvním** poli zadejte jméno uživatele jako Britta.

    b. Do textového pole **příjmení** zadejte jméno uživatele jako Simon.

    c. Do textového pole **e-mail** zadejte e-mailovou adresu uživatele Brittasimon@contoso.com .

    d. Vyberte datum v **datu zaměstnání**.

    e. Existují i jiné nepovinné atributy uživatele, které lze nastavit pro uživatele. Další podrobnosti najdete v [dokumentu o nastavení zaměstnanců](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) .

    f. Klikněte na **Uložit zaměstnance**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail a provede odkaz k potvrzení jeho účtu předtím, než se aktivuje.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici BitaBIZ, měli byste se automaticky přihlásit k BitaBIZ, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)