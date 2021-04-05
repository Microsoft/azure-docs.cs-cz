---
title: 'Kurz: Azure Active Directory integrace s pochvalné hodnocení | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a pochvalné hodnocení.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 656c7467507c105f492f266f06c05a9f41e8f76a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458825"
---
# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>Kurz: Azure Active Directory integrace s pochvalné hodnocení

V tomto kurzu se dozvíte, jak integrovat pochvalné hodnocení s Azure Active Directory (Azure AD).
Integrace pochvalné hodnocení s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k pochvalné hodnocení.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k pochvalné hodnocení (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s pochvalné hodnocení potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným pochvalné hodnocením jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Pochvalné hodnocení podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-kudos-from-the-gallery"></a>Přidání pochvalné hodnocení z Galerie

Pokud chcete nakonfigurovat integraci pochvalné hodnocení do služby Azure AD, musíte přidat pochvalné hodnocení z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat pochvalné hodnocení z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **pochvalné hodnocení**, vyberte **pochvalné hodnocení** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Pochvalné hodnocení v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí pochvalné hodnocení na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v pochvalné hodnocení.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí pochvalné hodnocení, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování pochvalné hodnocení](#configure-kudos-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření pochvalné hodnocení Test User](#create-kudos-test-user)** – pro Britta Simon v pochvalné hodnocení, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí pochvalné hodnocení, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **pochvalné hodnocení** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně pochvalné hodnocení a adresách URL](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<company>.kudosnow.com`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta pochvalné hodnocení](http://success.kudosnow.com/home) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení pochvalné hodnocení** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-kudos-single-sign-on"></a>Nakonfigurovat pochvalné hodnocení jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu pochvalné hodnocení společnosti jako správce.

1. V nabídce v horní části klikněte na **ikona nastavení**.

    ![Nastavení](./media/kudos-tutorial/ic787806.png "Nastavení")

1. Klikněte na **integrace > jednotné přihlašování** a proveďte následující kroky:

    ![Jednotné přihlašování](./media/kudos-tutorial/ic787807.png "Jednotné přihlašování")

    a. Do textového pole **přihlašovací adresa URL** vložte hodnotu  **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikátu X. 509.**

    c. Do textového pole **Odhlásit k adrese URL** vložte hodnotu  **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **Adresa URL pochvalné hodnocení** zadejte název vaší společnosti.

    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména**`brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k pochvalné hodnocení.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **pochvalné hodnocení**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **pochvalné hodnocení**.

    ![Odkaz pochvalné hodnocení v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-kudos-test-user"></a>Vytvořit testovacího uživatele pochvalné hodnocení

Aby se uživatelé Azure AD mohli přihlašovat k pochvalné hodnocení, musí se zřídit v pochvalné hodnocení. V případě pochvalné hodnocení je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu **pochvalné hodnocení** společnosti jako správce.

1. V nabídce v horní části klikněte na **ikona nastavení**.

   ![Nastavení](./media/kudos-tutorial/ic787806.png "Nastavení")

1. Klikněte na **Správce uživatelů**.

1. Klikněte na kartu **Uživatelé** a pak klikněte na **Přidat uživatele**.

   ![Správce uživatelů](./media/kudos-tutorial/ic787809.png "Správce uživatelů")

1. V části **Přidat uživatele** proveďte následující kroky:

    ![Přidat uživatele](./media/kudos-tutorial/ic787810.png "Přidat uživatele")

    a. Zadejte **jméno**, **příjmení**, **e-mail** a další podrobnosti platného Azure Active Directory účtu, který chcete zřídit do souvisejících textových polí.

    b. Klikněte na **vytvořit uživatele**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů pochvalné hodnocení nebo rozhraní API poskytovaná pochvalné hodnocení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici pochvalné hodnocení, měli byste se automaticky přihlásit k pochvalné hodnocení, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)