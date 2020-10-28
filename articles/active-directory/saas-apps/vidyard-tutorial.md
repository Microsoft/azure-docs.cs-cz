---
title: 'Kurz: Azure Active Directory integrace s Vidyard | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Vidyard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 4176c92d48b67b9f9207f22ebd8939b5ec1437ee
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636744"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Kurz: Azure Active Directory integrace s Vidyard

V tomto kurzu se dozvíte, jak integrovat Vidyard s Azure Active Directory (Azure AD).
Integrace Vidyard s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Vidyard.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Vidyard (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s Vidyard potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným Vidyardm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Vidyard podporuje **jednotné** přihlašování (SSO) a **IDP** .

* Vidyard podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-vidyard-from-the-gallery"></a>Přidání Vidyard z Galerie

Pokud chcete nakonfigurovat integraci Vidyard do služby Azure AD, musíte přidat Vidyard z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Vidyard z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Vidyard** , vyberte **Vidyard** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Vidyard v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Vidyard na základě testovacího uživatele s názvem **Britta Simon** .
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Vidyard.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Vidyard, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Vidyard](#configure-vidyard-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Vidyard Test User](#create-vidyard-test-user)** – pro Britta Simon v Vidyard, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Vidyard, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Vidyard** vyberte **jednotné přihlašování** .

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat identifikátor, odpovědět U R L a vybrat Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Tyto hodnoty aktualizujete skutečným identifikátorem, adresou URL odpovědi a adresou URL Sign-On, která je vysvětlena dále v tomto kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavení Vidyard** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-vidyard-single-sign-on"></a>Nakonfigurovat Vidyard jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu Vidyard software společnosti jako správce.

2. Z řídicího panelu Vidyard vyberte **Group**  >  **zabezpečení** skupiny.

    ![Snímek obrazovky zobrazuje zabezpečení vybraný ze skupiny na webu Vidyard softwaru.](./media/vidyard-tutorial/configure1.png)

3. Klikněte na kartu **Nový profil** .

    ![Snímek obrazovky se zobrazeným tlačítkem nový profil.](./media/vidyard-tutorial/configure2.png)

4. V části **Konfigurace SAML** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v sekci konfigurace SAML, kde můžete zadat hodnoty, které jsou popsány.](./media/vidyard-tutorial/configure3.png)

    a. Do textového pole **název profilu** zadejte obecný název profilu.

    b. Zkopírujte hodnotu **přihlašovací stránky uživatele SSO** a vložte ji do textového pole **přihlašovací adresa URL** v **základní části Konfigurace SAML** na Azure Portal.

    c. Zkopírujte hodnotu **adresy URL služby ACS** a vložte ji do textového pole **Adresa URL odpovědi** v **základní části Konfigurace SAML** na Azure Portal.

    d. Zkopírujte hodnotu **URL pro vystavitele/metadata** a vložte ji do textového pole **identifikátoru** v **základní části Konfigurace SAML** na Azure Portal.

    e. Otevřete stažený soubor certifikátu z Azure Portal v programu Poznámkový blok a vložte ho do textového pole **certifikátu X. 509** .

    f. Do textového pole **Adresa URL koncového bodu SAML** vložte hodnotu **přihlašovací adresy url** zkopírované z Azure Portal.

    například Klikněte na **Confirm** (Potvrdit).

5. Na kartě jednotné přihlašování vyberte **přiřadit** vedle existujícího profilu.

    ![Snímek obrazovky s tlačítkem přiřadit pro profil Azure A D S S O.](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Jakmile vytvoříte profil jednotného přihlašování, přiřaďte ho ke všem skupinám, pro které budou uživatelé potřebovat přístup prostřednictvím Azure. Pokud uživatel ve skupině, ke které byl přiřazen, neexistuje, Vidyard automaticky vytvoří uživatelský účet a přiřadí jeho roli v reálném čase.

6. Vyberte skupinu organizace, která je viditelná ve skupinách, které je **možné přiřadit** .

    ![Snímek obrazovky zobrazuje část přiřazení konfigurace SAML k organizacím, kde můžete vybrat svou skupinu.](./media/vidyard-tutorial/configure5.png)

7. Přiřazené skupiny můžete zobrazit v **aktuálně přiřazených skupinách** . Vyberte roli pro skupinu dle vaší organizace a klikněte na **Potvrdit** .

    ![Snímek obrazovky zobrazuje část přiřazení konfigurace SAML k organizacím, kde můžete vybrat roli.](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Další informace najdete v [tomto dokumentu](https://knowledge.vidyard.com/hc/articles/360009990033-SAML-based-Single-Sign-On-SSO-in-Vidyard).

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory** , vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé** .

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon** .
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit** .

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Vidyard.

1. V Azure Portal vyberte **podnikové aplikace** , vyberte **všechny aplikace** a pak vyberte **Vidyard** .

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Vidyard** .

    ![Odkaz Vidyard v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny** .

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-vidyard-test-user"></a>Vytvořit testovacího uživatele Vidyard

V této části se v Vidyard vytvoří uživatel s názvem Britta Simon. Vidyard podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Vidyard neexistuje, vytvoří se po ověření nový.

>[!Note]
>Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory Vidyard](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Vidyard, měli byste se automaticky přihlásit k Vidyard, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)