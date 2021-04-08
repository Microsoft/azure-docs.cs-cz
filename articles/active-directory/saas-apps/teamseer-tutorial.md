---
title: 'Kurz: Azure Active Directory integrace s TeamSeer | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TeamSeer.
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
ms.openlocfilehash: 6085ba5091b2b9973354280175aeb01f93ad7e28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521165"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Kurz: Azure Active Directory integrace s TeamSeer

V tomto kurzu se dozvíte, jak integrovat TeamSeer s Azure Active Directory (Azure AD).
Integrace TeamSeer s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k TeamSeer.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k TeamSeer (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s TeamSeer potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným TeamSeerm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TeamSeer podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-teamseer-from-the-gallery"></a>Přidání TeamSeer z Galerie

Pokud chcete nakonfigurovat integraci TeamSeer do služby Azure AD, musíte přidat TeamSeer z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat TeamSeer z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TeamSeer**, vyberte **TeamSeer** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![TeamSeer v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí TeamSeer na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v TeamSeer.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí TeamSeer, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování TeamSeer](#configure-teamseer-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření TeamSeer Test User](#create-teamseer-test-user)** – pro Britta Simon v TeamSeer, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí TeamSeer, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **TeamSeer** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně TeamSeer a adresách URL](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://www.teamseer.com/<companyid>`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta TeamSeer](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení TeamSeer** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-teamseer-single-sign-on&quot;></a>Nakonfigurovat TeamSeer jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu TeamSeer společnosti jako správce.

1. Přejít na **správce lidských zdrojů**

    ![Snímek obrazovky ukazuje správce H R vybraný v okně TeamSeer.](./media/teamseer-tutorial/ic789634.png &quot;Správce lidských zdrojů")

1. Klikněte na tlačítko **nastavit**.

    ![Nastavení](./media/teamseer-tutorial/ic789635.png "Nastavení")

1. Klikněte na **nastavení podrobnosti o poskytovateli SAML**.

    ![Snímek obrazovky s vybraným nastavením podrobností o poskytovateli SAML.](./media/teamseer-tutorial/ic789636.png "Nastavení SAML")

1. V části Podrobnosti o poskytovateli SAML proveďte následující kroky:

    ![Snímek obrazovky s podrobnostmi o poskytovateli SAML, kde můžete zadat hodnoty, které jsou popsány.](./media/teamseer-tutorial/ic789637.png "Nastavení SAML")

    a. Do textového pole **Adresa URL** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64, zkopírujte jeho obsah do schránky a vložte ho do textového pole **veřejné certifikáty IDP** .

1. Konfiguraci zprostředkovatele SAML dokončíte provedením následujících kroků:

    ![Snímek obrazovky se zobrazí v konfiguraci zprostředkovatele SAML, kde můžete zadat hodnoty, které jsou popsány.](./media/teamseer-tutorial/ic789638.png "Nastavení SAML")

    a. Do pole **testovací e-mailové adresy** zadejte e-mailovou adresu testovacího uživatele.
  
    b. Do textového pole **vystavitele** zadejte adresu URL vydavatele poskytovatele služeb.
  
    c. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k TeamSeer.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **TeamSeer**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **TeamSeer**.

    ![Odkaz TeamSeer v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-teamseer-test-user&quot;></a>Vytvořit testovacího uživatele TeamSeer

Aby se uživatelé Azure AD mohli přihlašovat k TeamSeer, musí se zřídit v ShiftPlanning. V případě TeamSeer je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu **TeamSeer** společnosti jako správce.

1. Přejděte na **správce řízení \> uživatelských zdrojů** a potom klikněte na **Spustit Průvodce vytvořením nového uživatele**.

    ![Snímek obrazovky se zobrazí na kartě Správce H R, kde můžete vybrat průvodce, který se má spustit.](./media/teamseer-tutorial/ic789640.png &quot;Správce lidských zdrojů")

1. V části **Podrobnosti o uživateli** proveďte následující kroky:

    ![Podrobnosti o uživateli](./media/teamseer-tutorial/ic789641.png "Podrobnosti o uživateli")

    a. Do příslušných textových polí zadejte **jméno**, **příjmení**, **uživatelské jméno (e-mailová adresa)** platného účtu Azure AD, který chcete zřídit.
  
    b. Klikněte na **Next** (Další).

1. Podle pokynů na obrazovce přidejte nového uživatele a klikněte na **Dokončit**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů TeamSeer nebo rozhraní API poskytovaná TeamSeer.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici TeamSeer, měli byste se automaticky přihlásit k TeamSeer, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)