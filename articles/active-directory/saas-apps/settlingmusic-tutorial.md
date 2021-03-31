---
title: 'Kurz: Azure Active Directory integrace s vypořádáním hudby | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a vypořádáním hudby.
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
ms.openlocfilehash: eafbc1543db1681b58ed499f4f29659e14a06e9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518459"
---
# <a name="tutorial-azure-active-directory-integration-with-settling-music"></a>Kurz: Azure Active Directory integrace s vypořádáním hudby

V tomto kurzu se naučíte integrovat hudbu s Azure Active Directory (Azure AD).
Integrace vyrovnávání hudby s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k vyřízení hudby.
* Můžete uživatelům povolit, aby se automaticky přihlásili k řešení hudby (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s využitím hudby potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Vyrovnávání s povoleným odběrem jednotného přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Vyrovnávání hudby podporuje jednotné přihlašování spouštěné v **SP**

## <a name="adding-settling-music-from-the-gallery"></a>Přidávání k vyrovnávání hudby z Galerie

Pokud chcete nakonfigurovat integraci řešení pro vyrovnávání hudby do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat vyrovnávání hudby z galerie.

**Chcete-li přidat k vyrovnávání hudby z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte příkaz pro **Vyrovnávání hudby**, vyberte možnost vyčíst **hudbu** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Vyrovnávání hudby v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí řešení hudby na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, je potřeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v řešení hudby.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s využitím hudby, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování přes hudbu](#configure-settling-music-single-sign-on)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. Vytvořte řešení bezplatných **[hudebních testů](#create-settling-music-test-user)** , abyste měli protějšek Britta Simon při vyrovnávání hudby, která je propojená s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD s využitím hudby, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **Vyrovnávání hudební** aplikace vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Vyrovnávání informací o jednotném přihlašování k hudební doméně a adresám URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Chcete-li získat tyto hodnoty, obraťte se na kontaktování [týmu podpory pro hudební klienty](https://rakurakuseisan.jp/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavení vyrovnávání hudby** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-settling-music-single-sign-on"></a>Konfigurace vyrovnání jedné Sign-On hudby

1. V jiném okně webového prohlížeče se přihlaste, abyste mohli považovat hudbu za správce zabezpečení.

1. V horní části stránky klikněte na kartu **Správa** .

    ![Vyrovnávání hudebních krok 1](./media/settlingmusic-tutorial/tutorial_settlingmusic_step1.png)

1. Klikněte na kartu  **nastavení systému** .

    ![Vyrovnávání hudebních STEP2](./media/settlingmusic-tutorial/tutorial_settlingmusic_step2.png)

1. Přepněte na kartu **zabezpečení** .

    ![Vyrovnávání hudebních step3](./media/settlingmusic-tutorial/tutorial_settlingmusic_step3.png)

1. V části **nastavení jednotného přihlašování** proveďte následující kroky:

    ![Vyrovnávání hudebních Step5](./media/settlingmusic-tutorial/tutorial_settlingmusic_step4.png)

    a. Kliknutím **povolíte**.

    b. Do pole **Adresa URL pro přihlášení** v textovém poli poskytovatele ID vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL pro odhlášení poskytovatele ID** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    d. Kliknutím na **zvolit soubor** nahrajte **certifikát (Base64)** , který jste stáhli Azure Portal formuláře.

    e. Klikněte na tlačítko **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k vyrovnávání hudby.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte možnost **Vyrovnávání hudby**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Vyrovnávání hudby**.

    ![Odkaz k vyrovnávání hudby v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-settling-music-test-user"></a>Vytvořit řešení pro testování hudby uživatele

V této části vytvoříte uživatele s názvem Britta Simon při vyrovnávání hudby. Pokud chcete přidat uživatele do řešení pro vyrovnávání hudby, pracujte s doplňováním [týmu podpory pro hudbu](https://rakurakuseisan.jp/) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici vyrovnávání hudby na přístupovém panelu byste se měli automaticky přihlášeni k řešení hudby, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)