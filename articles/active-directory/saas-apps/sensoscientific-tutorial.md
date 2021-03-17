---
title: 'Kurz: Azure Active Directory integrací se systémem pro monitorování teploty SensoScientific pro bezdrátové sítě | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SensoScientific systémem pro monitorování teploty bezdrátových sítí.
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
ms.openlocfilehash: 27512ac694f20544f1fdd5b79b27d7bf2cabdd66
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675493"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Kurz: Azure Active Directory integrací se systémem SensoScientific pro monitorování teploty bezdrátových sítí

V tomto kurzu se dozvíte, jak integrovat systém SensoScientific pro monitorování teploty bezdrátových sítí pomocí Azure Active Directory (Azure AD).
Integrace SensoScientific systému monitorování teploty pomocí Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k SensoScientific systému monitorování teploty bezdrátových sítí.
* Můžete uživatelům povolit, aby se automaticky přihlásili k SensoScientific systému pro monitorování teploty bezdrátových sítí (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD se systémem SensoScientific pro monitorování teploty bezdrátové sítě potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* SensoScientific odběr monitorování bezdrátových teplot systému s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Systém SensoScientific pro monitorování teploty pro bezdrátové sítě podporuje **IDP** iniciované jednotné přihlašování.

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Přidání systému monitorování bezdrátové teploty SensoScientific z Galerie

Pokud chcete nakonfigurovat integraci SensoScientific systému pro sledování teploty do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat SensoScientific systém monitorování teploty bezdrátové sítě z galerie.

**Chcete-li přidat systém SensoScientific pro monitorování teploty bezdrátové sítě z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SensoScientific systém pro monitorování bezdrátové teploty** , vyberte **SensoScientific systém monitorování teploty bezdrátové sítě** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![SensoScientific systém monitorování teploty bezdrátové sítě v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD se systémem SensoScientific pro monitorování bezdrátových teplot na základě testovacího uživatele s názvem **Britta Simon** .
Aby jednotné přihlašování fungovalo, je potřeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SensoScientific systému monitorování teploty bezdrátových sítí.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SensoScientific systému pro monitorování teploty bezdrátových sítí, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování systému SensoScientific pro monitorování teploty bezdrátové sítě](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** , abyste mohli konfigurovat nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření SensoScientific bezdrátového monitorování teploty systému – uživatel](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** , který bude mít protějšek Britta Simon v systému SensoScientific pro monitorování teploty bezdrátové sítě, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí SensoScientific systému pro monitorování teploty bezdrátové sítě, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **SensoScientific pro monitorování teploty systému pro systém lokality** vyberte **jednotné přihlašování** .

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V **základní části Konfigurace SAML** nemusí uživatel provádět žádný krok, protože aplikace už je předem integrovaná s Azure.

    ![SensoScientific informace o jednotném přihlašování k systémové doméně a k adresám URL pro sledování bezdrátové teploty](common/preintegrated.png)

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavení systému monitorování bezdrátové teploty SensoScientific** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>Konfigurovat systém SensoScientific pro monitorování bezdrátových teplot v jednom Sign-On

1. Přihlaste se k systémové aplikaci monitorování bezdrátové teploty SensoScientific jako správce.

1. V navigační nabídce v horní části klikněte na **Konfigurace** a přejít na **Konfigurovat** v rámci **jednotného přihlašování** a otevřete nastavení jednotného přihlašování a proveďte následující kroky:

    ![Konfigurace jednoho Sign-On](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Jako Azure AD vyberte **název vystavitele** .

    b. Do textového pole **Adresa URL vystavitele** vložte **identifikátor Azure AD** , který jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL služby Single Sign-On Service** vložte **přihlašovací adresu URL** , kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **Adresa URL služby Single Sign-Out Service** vložte **adresu URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    e. Vyhledejte certifikát, který jste si stáhli z Azure Portal a nahrajte ho sem.

    f. Klikněte na **Uložit** .

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k systému pro monitorování teploty SensoScientific Wireless.

1. V Azure Portal vyberte možnost **podnikové aplikace** , vyberte možnost **všechny aplikace** a pak vyberte **SensoScientific systém monitorování teploty bezdrátových sítí** .

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **SensoScientific systém monitorování teploty bezdrátových sítí** .

    ![Odkaz na systém SensoScientific pro monitorování teploty bezdrátové sítě v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny** .

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Vytvořit testovacího uživatele systému SensoScientific pro monitorování teploty bezdrátové sítě

Aby se uživatelé Azure AD mohli přihlašovat k SensoScientific systému pro monitorování teploty bezdrátové sítě, musí se zřídit do SensoScientific systému monitorování teploty bezdrátových sítí. Pokud chcete přidat uživatele do SensoScientific systémové platformy pro monitorování teploty bezdrátové sítě, pracujte s [SensoScientificmi bezdrátových monitorování teploty systému](https://www.sensoscientific.com/contact-us/) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici systém SensoScientific pro monitorování teploty bezdrátové sítě, měli byste se automaticky přihlásit k systému monitorování bezdrátových teplot SensoScientific, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)