---
title: 'Kurz: Azure Active Directory integrace s helpdeskem Jitbit | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jitbit helpdeskem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: bd47a528b6c9aef354df8e52da63409002f6fb32
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850791"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Kurz: Azure Active Directory integrace s helpdeskem Jitbit

V tomto kurzu se dozvíte, jak integrovat Jitbit helpdesk s Azure Active Directory (Azure AD).
Integrace Jitbit helpdesku s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Jitbit helpdesku.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Jitbit helpdesku (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s Jitbit helpdeskem, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné Jitbit helpdesku s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* HelpDesk pro Jitbit podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Přidání helpdesku Jitbit z Galerie

Pokud chcete nakonfigurovat integraci Jitbit helpdesku do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Jitbit Helpdesk z galerie.

**Pokud chcete přidat Helpdesk Jitbit z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Jitbit Helpdesk**, vyberte **Jitbit Helpdesk** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Jitbit helpdesk v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí helpdesku Jitbit na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Jitbit helpdesku.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Jitbit helpdesku, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Jitbit Helpdesk](#configure-jitbit-helpdesk-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření testovacího uživatele Jitbit helpdesku](#create-jitbit-helpdesk-test-user)** – Pokud chcete mít protějšek Britta Simon v Jitbit helpdesku, která je propojená s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí helpdesku Jitbit, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **Jitbit Helpdesk** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v Jitbit helpdesku a adrese URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Tato hodnota není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL Sign-On. Pokud chcete získat tuto hodnotu, obraťte se na [tým podpory Jitbit Helpdesk klienta](https://www.jitbit.com/support/) .

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL následujícím způsobem: `https://www.jitbit.com/web-helpdesk/`

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení helpdesku pro Jitbit** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Konfigurace Jitbit helpdesku pro jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti Jitbit helpdesku jako správce.

1. Na panelu nástrojů v horní části klikněte na možnost **Správa**.

    ![Správa](./media/jitbit-helpdesk-tutorial/ic777681.png "Správa")

1. Klikněte na **Obecné nastavení**.

    ![Snímek obrazovky se zobrazeným odkazem na Obecné nastavení.](./media/jitbit-helpdesk-tutorial/ic777680.png "Uživatelé, společnosti a oprávnění")

1. V části konfigurace **nastavení ověřování** proveďte následující kroky:

    ![Nastavení ověřování](./media/jitbit-helpdesk-tutorial/ic777683.png "Nastavení ověřování")

    a. Vyberte **Povolit jednotné přihlašování SAML 2,0**, abyste se mohli přihlásit pomocí jednotného Sign-On (SSO), a to pomocí **OneLogin**.

    b. Do textového pole **Adresa URL koncového bodu** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Otevřete v programu Poznámkový blok certifikát s kódováním **Base-64** , zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikátu X. 509.**

    d. Klikněte na **Uložit změny**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Jitbit helpdesku.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **Jitbit Helpdesk**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Jitbit Helpdesk**.

    ![Odkaz helpdesku Jitbit v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-jitbit-helpdesk-test-user"></a>Vytvořit testovacího uživatele Jitbit Helpdesk

Aby se uživatelé Azure AD mohli přihlašovat k Jitbit helpdesku, musí se zřídit v Jitbit helpdesku. V případě helpdesku Jitbit je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **helpdesku Jitbit** .

1. V nabídce v horní části klikněte na **Správa**.

    ![Správa](./media/jitbit-helpdesk-tutorial/ic777681.png "Správa")

1. Klikněte na **Uživatelé, společnosti a oprávnění**.

    ![Uživatelé, společnosti a oprávnění](./media/jitbit-helpdesk-tutorial/ic777682.png "Uživatelé, společnosti a oprávnění")

1. Klikněte na **Přidat uživatele**.

    ![Přidat uživatele](./media/jitbit-helpdesk-tutorial/ic777685.png "Přidat uživatele")

1. V části vytvořit zadejte data účtu Azure AD, který chcete zřídit, následovně:

    ![Vytvořit](./media/jitbit-helpdesk-tutorial/ic777686.png "Vytvořit")

   a. Do textového pole **uživatelské jméno** zadejte uživatelské jméno uživatele, jako je **BrittaSimon**.

   b. Do textového pole **e-mailu** zadejte e-maily uživatele jako **BrittaSimon@contoso.com** .

   c. Do textového pole **jméno** zadejte jméno uživatele, jako je **Britta**.

   d. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

   e. Klikněte na **Vytvořit**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli další nástroje pro vytváření uživatelských účtů Jitbit helpdesku nebo rozhraní API poskytovaná Jitbit helpdesk.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici helpdesku Jitbit na přístupovém panelu byste se měli automaticky přihlášeni k helpdesku Jitbit, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
