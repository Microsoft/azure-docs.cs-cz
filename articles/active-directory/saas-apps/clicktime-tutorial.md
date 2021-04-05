---
title: 'Kurz: Azure Active Directory integrace s ClickTime | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ClickTime.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.openlocfilehash: 3337d66934c1fe317296cffaa9a663e212cce12d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673167"
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Kurz: Azure Active Directory integrace s ClickTime

V tomto kurzu se dozvíte, jak integrovat ClickTime s Azure Active Directory (Azure AD).
Integrace ClickTime s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k ClickTime.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k ClickTime (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s ClickTime potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným ClickTimem jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ClickTime podporuje jednotné přihlašování **IDP** .

## <a name="adding-clicktime-from-the-gallery"></a>Přidání ClickTime z Galerie

Pokud chcete nakonfigurovat integraci ClickTime do služby Azure AD, musíte přidat ClickTime z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat ClickTime z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ClickTime**, vyberte **ClickTime** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![ClickTime v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí ClickTime na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ClickTime.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ClickTime, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování ClickTime](#configure-clicktime-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření ClickTime Test User](#create-clicktime-test-user)** – pro Britta Simon v ClickTime, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí ClickTime, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **ClickTime** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně ClickTime a adresách URL](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL: `https://app.clicktime.com/sp/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    ```http
    https://app.clicktime.com/Login/
    https://app.clicktime.com/App/Login/Consume.aspx
    ```

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení ClickTime** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-clicktime-single-sign-on"></a>Nakonfigurovat ClickTime jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu ClickTime společnosti jako správce.

1. Na panelu nástrojů v horní části klikněte na **Předvolby** a pak klikněte na **nastavení zabezpečení**.

1. V části konfigurace **jednoho Sign-On předvoleb** proveďte následující kroky:

    ![Nastavení zabezpečení](./media/clicktime-tutorial/tic777280.png "Nastavení zabezpečení")

    a.  Vyberte možnost **Povolení** přihlášení pomocí jednotného Sign-On (SSO) se službou **Azure AD**.

    b. V textovém poli **koncového bodu poskytovatele identity** vložte **přihlašovací adresu URL** , kterou jste zkopírovali z Azure Portal.

    c.  Otevřete v **programu Poznámkový blok** **certifikát s kódováním Base-64** stažený z Azure Portal, zkopírujte ho a vložte ho do textového pole **certifikát X. 509** .

    d.  Klikněte na **Uložit**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k ClickTime.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **ClickTime**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **ClickTime**.

    ![Odkaz ClickTime v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-clicktime-test-user"></a>Vytvořit testovacího uživatele ClickTime

Aby se uživatelé Azure AD mohli přihlašovat k ClickTime, musí se zřídit v ClickTime.  
V případě ClickTime je zřizování ručním úkolem.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů ClickTime nebo rozhraní API poskytovaná ClickTime.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **ClickTime** .

1. Na panelu nástrojů v horní části klikněte na **Společnost** a pak klikněte na **lidé**.

    ![Snímek obrazovky s vybraným podnikem a lidmi zobrazuje klienta ClickTime.](./media/clicktime-tutorial/tic777282.png "People")

1. Klikněte na **Přidat osobu**.

    ![Přidat osobu](./media/clicktime-tutorial/tic777283.png "Přidat osobu")

1. V části nová osoba proveďte následující kroky:

    ![Snímek obrazovky se zobrazí část přidat osobu, kde můžete přidat informace v tomto kroku.](./media/clicktime-tutorial/tic777284.png "People")

    a.  Do textového pole **celé jméno** zadejte jméno a příjmení uživatele, jako je **Britta Simon**. 

    b.  Do textového pole **e-mailová adresa** zadejte e-maily uživatele, jako je **brittasimon \@ contoso.com**.

    > [!NOTE]
    > Pokud chcete, můžete nastavit další vlastnosti objektu New Person.

    c.  Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici ClickTime, měli byste se automaticky přihlásit k ClickTime, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)