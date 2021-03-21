---
title: 'Kurz: Azure Active Directory integrace s ScreenSteps | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ScreenSteps.
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
ms.openlocfilehash: 3816239798290318404980ded388b726d8134395
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999786"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Kurz: Azure Active Directory integrace s ScreenSteps

V tomto kurzu se dozvíte, jak integrovat ScreenSteps s Azure Active Directory (Azure AD).
Integrace ScreenSteps s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k ScreenSteps.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k ScreenSteps (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s ScreenSteps potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným ScreenStepsm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ScreenSteps podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-screensteps-from-the-gallery"></a>Přidání ScreenSteps z Galerie

Pokud chcete nakonfigurovat integraci ScreenSteps do služby Azure AD, musíte přidat ScreenSteps z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat ScreenSteps z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ScreenSteps**, vyberte **ScreenSteps** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![ScreenSteps v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí ScreenSteps na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ScreenSteps.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ScreenSteps, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování ScreenSteps](#configure-screensteps-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření ScreenSteps Test User](#create-screensteps-test-user)** – pro Britta Simon v ScreenSteps, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí ScreenSteps, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **ScreenSteps** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně ScreenSteps a adresách URL](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Tato hodnota není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL Sign-On, která je vysvětlena dále v tomto kurzu.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení ScreenSteps** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-screensteps-single-sign-on"></a>Nakonfigurovat ScreenSteps jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu ScreenSteps společnosti jako správce.

1. Klikněte na **Nastavení účtu**.

    ![Správa účtů](./media/screensteps-tutorial/ic778523.png "Správa účtů")

1. Klikněte na **jednotné přihlašování**.

    ![Snímek obrazovky, který zobrazuje vybrané jednotné přihlašování](./media/screensteps-tutorial/ic778524.png "Vzdálené ověřování")

1. Klikněte na **vytvořit koncový bod jednotného přihlašování**.

    ![Vzdálené ověřování](./media/screensteps-tutorial/ic778525.png "Vzdálené ověřování")

1. V části **vytvořit koncový bod jednotného přihlašování** proveďte následující kroky:

    ![Vytvoření koncového bodu ověřování](./media/screensteps-tutorial/ic778526.png "Vytvoření koncového bodu ověřování")

    a. Do textového pole **název** zadejte název.

    b. V seznamu **režim** vyberte **SAML**.

    c. Klikněte na **Vytvořit**.

1. **Upravte** nový koncový bod.

    ![Upravit koncový bod](./media/screensteps-tutorial/ic778528.png "Upravit koncový bod")

1. V části **Upravit koncový bod jednotného přihlašování** proveďte následující kroky:

    ![Koncový bod vzdáleného ověřování](./media/screensteps-tutorial/ic778527.png "Koncový bod vzdáleného ověřování")

    a. Klikněte na **nahrát nový soubor certifikátu SAML** a pak Nahrajte certifikát, který jste si stáhli z Azure Portal.

    b. Vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal do pole **vzdálené přihlášení URL** .

    c. Vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal do textového pole **Adresa URL pro odhlášení** .

    d. Vyberte **skupinu** , do které chcete přiřadit uživatele při jejich zřízení.

    e. Klikněte na **Aktualizovat**.

    f. Zkopírujte **adresu URL příjemce SAML** do schránky a vložte ji do TEXTOVÉHO pole **URL pro přihlášení** v základní části **Konfigurace SAML** v Azure Portal.

    například Vraťte se do **koncového bodu pro úpravu jednotného přihlašování**.

    h. Klikněte na tlačítko **nastavit výchozí pro účet** pro použití tohoto koncového bodu pro všechny uživatele, kteří se přihlásí k ScreenSteps. Případně můžete kliknout na tlačítko **Přidat do webu** a použít tento koncový bod pro konkrétní weby v **ScreenSteps**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k ScreenSteps.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **ScreenSteps**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **ScreenSteps**.

    ![Odkaz ScreenSteps v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-screensteps-test-user"></a>Vytvořit testovacího uživatele ScreenSteps

V této části vytvoříte uživatele s názvem Britta Simon v ScreenSteps. Pokud chcete přidat uživatele na platformě ScreenSteps, pracujte s [týmem podpory klienta ScreenSteps](https://www.screensteps.com/contact) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici ScreenSteps, měli byste se automaticky přihlásit k ScreenSteps, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)