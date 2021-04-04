---
title: 'Kurz: Azure Active Directory Integration s LockPathm Light | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LockPath.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 81949736603d22cac779d08d14bd6db65065d730
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459080"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Kurz: Azure Active Directory integrace s LockPath

V tomto kurzu se naučíte, jak integrovat LockPath-Light s Azure Active Directory (Azure AD).
Integrace LockPath-Light s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k LockPath.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k LockPathmu Lightu (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s LockPathm, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* LockPath předplatné s povoleným jedním přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* LockPath-Light podporuje jednotné přihlašování iniciované v **SP**
* LockPath-Light podporuje zřizování uživatelů **jenom v čase**

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Přidání LockPathho světla z Galerie

Pokud chcete nakonfigurovat integraci LockPathového světla do služby Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat LockPathu klávesnici z galerie.

**Pokud chcete do galerie přidat LockPath-Light, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **LockPath-Light**, vyberte **LockPath-Light** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![LockPath – indikátor v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí LockPathho testu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v LockPath.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí LockPathho nástroje, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte LockPath jednotné přihlašování](#configure-lockpath-keylight-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření LockPathho testového uživatele](#create-lockpath-keylight-test-user)** – Pokud chcete mít protějšek Britta Simon v LockPath, která je propojená s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí LockPath, postupujte takto:

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **LockPath** , vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![LockPath informace o jednotném přihlašování k doméně a adresám URL](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<company name>.keylightgrc.com/`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<company name>.keylightgrc.com`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí vlastního přihlašovací adresy URL, identifikátoru a adresy URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory LockPath pro klienta](https://www.lockpath.com/contact/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na **Stáhnout** a Stáhněte si **certifikát (RAW)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

6. V části **Nastavení LockPathho světla** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-lockpath-keylight-single-sign-on"></a>Konfigurace LockPath – jeden Sign-On

1. Pokud chcete povolit jednotné přihlašování v LockPathi, proveďte následující kroky:

    a. Přihlaste se ke svému účtu LockPath Light jako správce.

    b. V nabídce v horní části klikněte na **osoba** a pak vyberte nastavení pro velmi **světel**.

    ![Snímek obrazovky, na kterém je vybraná ikona osoba a v rozevíracím seznamu se vybrala možnost "vzhled světla".](./media/keylight-tutorial/401.png)

    c. V zobrazení stromu na levé straně klikněte na **SAML**.

    ![Snímek obrazovky, který zobrazuje "S A M L" vybraný ve stromovém zobrazení.](./media/keylight-tutorial/402.png)

    d. V dialogovém okně **Nastavení SAML** klikněte na **Upravit**.

    ![Snímek obrazovky, který zobrazuje okno S nastavením A M L s vybraným tlačítkem Upravit.](./media/keylight-tutorial/404.png)

1. Na stránce **Upravit nastavení SAML** proveďte následující kroky:

    ![Konfigurace jednoho Sign-On](./media/keylight-tutorial/405.png)

    a. Nastavte **ověřování SAML** na **aktivní**.

    b. Do textového pole **Adresa URL pro přihlášení zprostředkovatele identity** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. V textovém poli **Adresa URL pro odhlášení zprostředkovatele identity** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    d. Klikněte na **zvolit soubor** a vyberte stažený LockPath certifikát. potom klikněte na **otevřít** a nahrajte certifikát.

    e. Nastavte **umístění ID uživatele SAML** na **NameIdentifier element v příkazu Subject**.

    f. **Poskytovatele služby Light** poskytněte pomocí následujícího vzoru: `https://<CompanyName>.keylightgrc.com` .

    například Nastavte **Automatické zřizování uživatelů** na **aktivní**.

    h. Nastavte **typ účtu pro Automatické zřizování** na **úplný uživatel**.

    i. Nastavte **roli zabezpečení pro Automatické zřizování** a jako **SAML vyberte standardní uživatel**.

    j. Nastavte konfiguraci **zabezpečení pro Automatické zřizování**, vyberte **standardní uživatelská konfigurace**.

    k. Do textového pole **atribut e-mailu** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    l. Do textového pole **název atributu First** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    m. Do textového pole **název atributu příjmení** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

    n. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k LockPathmu Light.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte **LockPath**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **LockPath**.

    ![LockPath v seznamu aplikací odkaz na světlo.](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-lockpath-keylight-test-user"></a>Vytvořit LockPathho testovacího uživatele

V této části se uživatel s názvem Britta Simon vytvoří v LockPathi Light. LockPath-Light podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě neexistuje v LockPathi, bude po ověření vytvořen nový. Pokud potřebujete ručně vytvořit uživatele, musíte se obrátit na [tým podpory LockPath Client support](https://www.lockpath.com/contact/).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici LockPath, měli byste se automaticky přihlásit k LockPath, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)