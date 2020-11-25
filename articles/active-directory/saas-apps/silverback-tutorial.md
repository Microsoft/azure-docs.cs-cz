---
title: 'Kurz: Azure Active Directory integrace s Silverback | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Silverback.
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
ms.openlocfilehash: a70b6bb50b397429af1af41869bbe9ecf7e8bad9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004173"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Kurz: Azure Active Directory integrace s Silverback

V tomto kurzu se dozvíte, jak integrovat Silverback s Azure Active Directory (Azure AD).
Integrace Silverback s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Silverback.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Silverback (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Silverback potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným Silverbackm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Silverback podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-silverback-from-the-gallery"></a>Přidání Silverback z Galerie

Pokud chcete nakonfigurovat integraci Silverback do služby Azure AD, musíte přidat Silverback z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Silverback z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Silverback**, vyberte **Silverback** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Silverback v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Silverback na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Silverback.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Silverback, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Silverback](#configure-silverback-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Silverback Test User](#create-silverback-test-user)** – pro Britta Simon v Silverback, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Silverback, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Silverback** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Silverback a adresách URL](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<YOURSILVERBACKURL>.com/ssp`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `<YOURSILVERBACKURL>.com`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátor a adresu URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Silverback](mailto:helpdesk@matrix42.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-silverback-single-sign-on"></a>Nakonfigurovat Silverback jednu Sign-On

1. V jiném webovém prohlížeči se přihlaste k serveru Silverback jako správce.

2. Přejděte k **Admin**  >  **poskytovateli ověřování** správce.

3. Na stránce **nastavení zprostředkovatele ověřování** proveďte následující kroky:

    ![Správce](./media/silverback-tutorial/tutorial_silverback_admin.png)

    a.  Klikněte na **importovat z adresy URL**.

    b.  Vložte zkopírovanou adresu URL metadat a klikněte na **OK**.

    c.  Potvrďte hodnotu **OK** , hodnoty se naplní automaticky.

    d.  Povolit **zobrazení na přihlašovací stránce**

    e.  Povolit **dynamické vytváření uživatelů** , pokud chcete přidat autorizované uživatele Azure AD automaticky (volitelné).

    f.  Vytvoří **název** tlačítka na samoobslužném portálu.

    například  Kliknutím na **zvolit soubor** nahrajte **ikonu** .

    h.  Vyberte **barvu** pozadí tlačítka.

    i.  Klikněte na **Uložit**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Silverback.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **Silverback**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Silverback**.

    ![Odkaz Silverback v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-silverback-test-user"></a>Vytvořit testovacího uživatele Silverback

Aby se uživatelé Azure AD mohli přihlásit k Silverback, musí se zřídit v Silverback. V Silverback je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k serveru Silverback jako správce.

2. Přejděte na **uživatele** a **přidejte nového uživatele zařízení**.

3. Na stránce **základní** proveďte následující kroky:

    ![Uživatel](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. Do textového pole **uživatelské jméno** zadejte jméno uživatele, jako je **Britta**.

    b. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako například **Britta**.

    c. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

    d. Do textového pole **e-mailová adresa** zadejte e-maily uživatele, jako je **Brittasimon \@ contoso.com**.

    e. Do textového pole **heslo** zadejte heslo.

    f. Do textového pole **Potvrdit heslo** zadejte znovu heslo a potvrďte ho.

    například Klikněte na **Uložit**.

> [!NOTE]
> Pokud nechcete každého uživatele sami vytvořit, zaškrtněte v části **Admin** zprostředkovatel ověřování správce políčko **dynamického vytvoření uživatele**  >  **Authentication Provider**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Silverback, měli byste se automaticky přihlásit k Silverback, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)