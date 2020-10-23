---
title: 'Kurz: Azure Active Directory integrace se Infogix Data3Sixty | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Infogix Data3Sixty.
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
ms.openlocfilehash: b59a764510b93daf358e00a4d75bcefd31d2980f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460220"
---
# <a name="tutorial-azure-active-directory-integration-with-infogix-data3sixty-govern"></a>Kurz: Azure Active Directory integrace se Infogix Data3Sixty se řídí

V tomto kurzu se naučíte, jak integrovat Infogix Data3Sixty s Azure Active Directory (Azure AD).
Integrace Infogix Data3Sixty, která se řídí pomocí Azure AD, poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k Infogix Data3Sixty.
* Můžete uživatelům povolit, aby se automaticky přihlásili k Infogix Data3Sixty (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Infogix Data3Sixty se řídí tyto položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Infogix Data3Sixty řídí předplatné s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Infogix Data3Sixty se řídí podporou **SP a IDP** , kterou iniciují jednotné přihlašování.
* Infogix Data3Sixty řídí podporu **při zřizování uživatelů jenom včas**

## <a name="adding-infogix-data3sixty-govern-from-the-gallery"></a>Přidání Infogix Data3Sixty se řídí z Galerie

Pokud chcete nakonfigurovat integraci Infogix Data3Sixty na Azure AD, musíte přidat Infogix Data3Sixty z Galerie na svůj seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Infogix Data3Sixty z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Infogix Data3Sixty**, vyberte **Infogix Data3Sixty** řízení z panelu výsledek a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Infogix Data3Sixty se řídí v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Infogix Data3Sixty na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Infogix Data3Sixty.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Infogix Data3Sixty, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Konfigurace Infogix Data3Sixty řídí jednotné přihlašování](#configure-infogix-data3sixty-govern-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Infogix Data3Sixty řídí testovacího uživatele](#create-infogix-data3sixty-govern-test-user)** – aby bylo možné mít protějšek Britta Simon v Infogix Data3Sixty, která se odkazuje na reprezentaci uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Infogix Data3Sixty, postupujte takto:

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **Infogix Data3Sixty** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat identifikátor, odpovědět U R L a vybrat Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL: `https://data3sixty.com/ui`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.data3sixty.com/sso/acs`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<subdomain>.data3sixty.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a adresy URL Sign-On. Pokud chcete získat tyto hodnoty, kontaktujte [Infogix Data3Sixty tým podpory klientů](mailto:data3sixtysupport@infogix.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Aplikace řízení Infogix Data3Sixty očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jeden Sign-On se** stránkou SAML kliknutím na tlačítko **Upravit** otevřete dialogové okno **atributy uživatele** .

    ![Snímek obrazovky zobrazuje atributy uživatele s vybranou ikonou pro úpravy.](common/edit-attribute.png)

7. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** upravte deklarace pomocí **ikony upravit** nebo přidejte deklarace pomocí **Přidat novou deklaraci identity** , jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Name | Zdrojový atribut|
    | -----------| -------------- |
    | FirstName  | User. křestní jméno |
    | polím   | User. příjmení |
    | username   | uživatel. pošta    |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![Snímek obrazovky zobrazuje deklarace identity uživatelů s možností přidání nové deklarace identity.](common/new-save-attribute.png)

    ![Snímek obrazovky se zobrazí dialogové okno Spravovat deklarace identity uživatelů, kde můžete zadat hodnoty, které jsou popsány.](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **OK** .

    například Klikněte na **Uložit**.

8. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na **Stáhnout** a Stáhněte si **certifikát (RAW)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

9. V části **Nastavení Data3Sixty řízení Infogix** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-infogix-data3sixty-govern-single-sign-on"></a>Konfigurace Infogix Data3Sixty řídí jednu Sign-On

Chcete-li nakonfigurovat jednotné přihlašování na **Infogix Data3Sixty** , je třeba odeslat stažený **certifikát (RAW)** a příslušné zkopírované adresy URL z Azure Portal do [Infogix Data3Sixty řídí tým podpory](mailto:data3sixtysupport@infogix.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Infogix Data3Sixty řízení.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **Infogix Data3Sixty**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Infogix Data3Sixty**.

    ![Odkaz na Infogix Data3Sixty řídí v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-infogix-data3sixty-govern-test-user"></a>Vytvoření Infogix Data3Sixty řídí testovacího uživatele

V této části se v Infogix Data3Sixty řídí uživatel s názvem Britta Simon. Infogix Data3Sixty Určuje podporu zřizování uživatelů za běhu, která je ve výchozím nastavení povolená. V této části není žádná položka akce. Pokud uživatel ještě neexistuje v Infogix Data3Sixty, vytvoří se po ověření nový.

> [!Note]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na Infogix Data3Sixty, který se [řídí týmem podpory](mailto:data3sixtysupport@infogix.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Infogix Data3Sixty, měli byste být automaticky přihlášeni k Infogix Data3Sixty řízení, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)