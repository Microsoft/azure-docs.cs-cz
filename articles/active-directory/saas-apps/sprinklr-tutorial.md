---
title: 'Kurz: Azure Active Directory Integration with autopostřikovače | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a postřikovače.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: c6a7784d052a03971fad84c90cdd70cd16b4a872
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122564"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Kurz: Azure Active Directory Integration with autopostřikovače

V tomto kurzu se naučíte integrovat automatický postřikovací program s Azure Active Directory (Azure AD).
Integrování programu pro automatických přihlášení do Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k automatickému postřikovači.
* Uživatelům můžete povolit, aby se automaticky přihlásili k programu pro automatické přihlašování (jednotné přihlašování) s účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD pomocí programu pro automatické nastavování potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Odběr povoleného jednotného přihlašování automatickým přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Automatický postřikovací systém podporuje jednotné přihlašování spouštěné v **SP**

## <a name="adding-sprinklr-from-the-gallery"></a>Přidání automatického postřikovače z Galerie

Pokud chcete nakonfigurovat integraci programu pro automatické nastavování do služby Azure AD, musíte přidat automatický postřikovací z Galerie do svého seznamu spravovaných aplikací SaaS.

**Chcete-li přidat automatický postřikovací z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího **pole zadejte program**pro automatické navýšení a pak klikněte na tlačítko **Sprinklr** **Přidat** , aby se aplikace přidala.

     ![Automatický postřikovač v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí programu pro automatické přihlašování na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo možné jednotné přihlašování pracovat, je nutné zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v postřikovači.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování pomocí programu pro automatické přihlášení, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování pro automatické přihlašování](#configure-sprinklr-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele](#create-sprinklr-test-user)** pro automatické přihlašování, abyste měli protějšek Britta Simon v autopostřikovače, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování pomocí programu pro automatické přihlášení, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **integrace aplikace pro** automatické **přihlašování vyberte jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování pro doménu a adresy URL automatického přihlašování](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.sprinklr.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory pro klienta pro automatické postřikovače](https://www.sprinklr.com/contact-us/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavit postřikovače** zkopírujte příslušné adresy URL podle vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-sprinklr-single-sign-on"></a>Konfigurace samostatného automatického Sign-On

1. V jiném okně webového prohlížeče se přihlaste k vaší společnosti automatického přihlašování jako správce.

1. Přejít na ** \> Nastavení správy**.

    ![Správa](./media/sprinklr-tutorial/ic782907.png "Správa")

1. V levém podokně můžete **Spravovat \> jednotné přihlašování partnerů** .

    ![Spravovat partnera](./media/sprinklr-tutorial/ic782908.png "Spravovat partnera")

1. Klikněte na **+ Přidat jednotné přihlašování**.

    ![Snímek obrazovky se zobrazí na tlačítku Přidat jednotné přihlašování.](./media/sprinklr-tutorial/ic782909.png "Jeden Sign-Ons")

1. Na stránce **jednotného přihlašování** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí na stránce jednotného přihlašování, kde můžete zadat hodnoty, které jsou popsány.](./media/sprinklr-tutorial/ic782910.png "Jeden Sign-Ons")

    a. Do textového pole **název** zadejte název vaší konfigurace (například: *WAADSSOTest*).

    b. Vyberte **Povoleno**.

    c. Vyberte **použít nový certifikát jednotného přihlašování**.

    d. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikát poskytovatele identity** .

    e. Do textového pole **ID entity** vložte hodnotu **IDENTIFIKÁTORu Azure AD** , kterou jste zkopírovali z webu Azure Portal.

    f. Vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z webu Azure Portal, do textového pole **Adresa URL pro přihlášení zprostředkovatele identity** .

    například Do textového pole **Adresa URL pro odhlášení zprostředkovatele identity** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z webu Azure Portal.

    h. Jako **Typ ID uživatele SAML**vyberte **kontrolní výraz obsahuje uživatelské jméno uživatele Sprinklr.com**.

    i. Jako **umístění ID uživatele SAML**vyberte **ID uživatele v prvku identifikátor názvu v příkazu Subject**.

    j. Klikněte na **Uložit**.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména****brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k automatickému postřikovači.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **automatických**přihlášení.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Autopostřikovače**.

    ![Odkaz na postřikovače v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-sprinklr-test-user"></a>Vytvořit testovacího uživatele pro automatické postřikovače

1. Přihlaste se k webu společnosti pro automatické přihlášení jako správce.

1. Přejít na ** \> Nastavení správy**.

    ![Správa](./media/sprinklr-tutorial/ic782907.png "Správa")

1. V levém podokně přejdete na **Spravovat \> uživatele klienta** .

    ![Snímek obrazovky se zobrazí tlačítko Přidat uživatele v nastavení/uživatelé.](./media/sprinklr-tutorial/ic782914.png "Nastavení")

1. Klikněte na **Add User** (Přidat uživatele).

    ![Snímek obrazovky se zobrazí dialogové okno Upravit uživatele, kde můžete zadat hodnoty, které jsou popsány.](./media/sprinklr-tutorial/ic782915.png "Nastavení")

1. V dialogovém okně **Upravit uživatele** proveďte následující kroky:

    ![Upravit uživatele](./media/sprinklr-tutorial/ic782916.png "Upravit uživatele")

    a. Do textového pole **e-mail**, **jméno** a **příjmení** a textová pole zadejte informace o účtu uživatele Azure AD, který chcete zřídit.

    b. Vyberte **heslo je zakázané**.

    c. Vyberte **jazyk**.

    d. Vyberte **typ uživatele**.

    e. Klikněte na **Aktualizovat**.

    > [!IMPORTANT]
    > Je nutné vybrat **heslo zakázané** , aby se uživatel mohl přihlásit prostřednictvím poskytovatele identity. 

1. Přejít na **roli**a pak proveďte následující kroky:

    ![Role partnerů](./media/sprinklr-tutorial/ic782917.png "Role partnerů")

    a. V seznamu **globální** vyberte možnost **ALL_Permissions**.  

    b. Klikněte na **Aktualizovat**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů nebo rozhraní API pro automatické postřikovače, které poskytuje postřikovače.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici automatických přihlášení na přístupovém panelu, měli byste se automaticky přihlásit do automatického přihlašování, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
