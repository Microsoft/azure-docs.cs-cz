---
title: 'Kurz: Azure Active Directory integrace s Peakon | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Peakon.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: d9850c83fc949aa3a2b0521dfa0d41d49b7283bd
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515621"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Kurz: Azure Active Directory integrace s Peakon

V tomto kurzu se dozvíte, jak integrovat Peakon s Azure Active Directory (Azure AD).
Integrace Peakon s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Peakon.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Peakon (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s Peakon potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným Peakonm jednotným přihlašováním

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Peakon podporuje **jednotné** přihlašování (SSO) a **IDP** .

## <a name="adding-peakon-from-the-gallery"></a>Přidání Peakon z Galerie

Pokud chcete nakonfigurovat integraci Peakon do služby Azure AD, musíte přidat Peakon z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Peakon z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Peakon**, vyberte **Peakon** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Peakon v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Peakon na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Peakon.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Peakon, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Peakon](#configure-peakon-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Peakon Test User](#create-peakon-test-user)** – pro Britta Simon v Peakon, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Peakon, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Peakon** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Informace o jednotném přihlašování v doméně Peakon a adresách URL](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://app.peakon.com/saml/<companyid>/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://app.peakon.com/saml/<companyid>/assert`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Snímek obrazovky se zvýrazněnou možností "nastavit další U R ls", která se zvýrazní v textovém poli "jednotlivě na U R L".](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://app.peakon.com/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi, která je vysvětlena dále v tomto kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na **Stáhnout** a Stáhněte si **certifikát (RAW)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

7. V části **Nastavení Peakon** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-peakon-single-sign-on"></a>Nakonfigurovat Peakon jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k Peakon jako správce.

2. V panelu nabídek na levé straně stránky klikněte na **Konfigurace**a potom přejděte k **integrací**.

    ![Konfigurace](./media/peakon-tutorial/tutorial_peakon_config.png)

3. Na stránce **integrace** klikněte na **jednotné přihlašování**.

    ![Jedna](./media/peakon-tutorial/tutorial_peakon_single.png)

4. V části **jednotné přihlašování** klikněte na **Povolit**.

    ![Povolit](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. V části **jednotné přihlašování pro zaměstnance používající SAML** proveďte následující kroky:

    ![SAML](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. Do textového pole **Adresa URL pro přihlášení SSO** vložte hodnotu **přihlašovací adresa URL**, kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **Adresa URL pro odhlášení jednotného přihlašování** vložte hodnotu **URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

    c. Kliknutím na **zvolit soubor** odešlete certifikát, který jste stáhli z Azure Portal, do pole certifikát.

    d. Kliknutím na **ikonu** zkopírujte v Azure Portal základní konfigurační pole **identifikátoru** **entity s ID** a vložením do **základního konfiguračního oddílu SAML** .

    e. Kliknutím na **ikonu** zkopírujte **adresu URL odpovědi (ACS)** a vložte text do pole **Adresa URL odpovědi** v **základní části Konfigurace SAML** na Azure Portal.

    f. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Peakon.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Peakon**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Peakon**.

    ![Odkaz Peakon v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-peakon-test-user"></a>Vytvořit testovacího uživatele Peakon

Aby se uživatelé Azure AD mohli přihlašovat k Peakon, musí se zřídit v Peakon.  
V případě Peakon je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu Peakon společnosti jako správce.

2. V panelu nabídek na levé straně stránky klikněte na **Konfigurace**a potom přejděte k **zaměstnancům**.

    ![Zaměstnanec](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. V pravém horním rohu stránky klikněte na **Přidat zaměstnance**.

      ![Přidat zaměstnance](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Na stránce **nový zaměstnanec** proveďte následující kroky:

     ![Nový zaměstnanec](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. Do textového pole **název** zadejte jako **Britta** a příjmení název jako **Simon**.

    b. Do textového pole **e-mail** zadejte e-mailovou adresu jako **Brittasimon \@ contoso.com**.

    c. Klikněte na **vytvořit zaměstnance**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Peakon, měli byste se automaticky přihlásit k Peakon, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)