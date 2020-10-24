---
title: 'Kurz: Azure Active Directory integrace s nástrojem sjednocuje | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a sjednocuje.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: ed7bda2875fa68254fb3842dac8b713a0bbddfd4
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486986"
---
# <a name="tutorial-azure-active-directory-integration-with-unifi"></a>Kurz: Azure Active Directory integrace s nástrojem sjednocuje

V tomto kurzu se dozvíte, jak integrovat s Azure Active Directory (Azure AD).
Integrace nástroje sjednocuje s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k prvku sjednocuje.
* Uživatelům můžete povolit, aby se automaticky přihlásili k organizaci sjednocuje (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD pomocí nástroje sjednocuje budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným jednotným přihlašováním ve službě sjednocuje

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SJEDNOCUJE podporuje **SP a IDP** iniciované jednotné přihlašování.
* SJEDNOCUJE podporuje **automatizované** zřizování uživatelů.

## <a name="adding-unifi-from-the-gallery"></a>Přidání pole sjednocuje z Galerie

Pokud chcete nakonfigurovat integraci nástroje sjednocuje do Azure AD, musíte z Galerie přidat do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat sjednocuje z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **sjednocuje**, vyberte možnost **sjednocuje** z panelu výsledků a kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![SJEDNOCUJE v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí nástroje sjednocuje na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v prvku sjednocuje.

Pokud chcete konfigurovat a testovat jednotné přihlašování Azure AD pomocí nástroje sjednocuje, je nutné dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování](#configure-unifi-single-sign-on)** pro aplikaci sjednocuje – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele sjednocuje](#create-unifi-test-user)** , abyste měli protějšek Britta Simon v sjednocuje, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí nástroje sjednocuje, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **sjednocuje** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Informace o jednotném přihlašování domény a adresy URL](common/idp-identifier.png)

    Do textového pole **identifikátor** zadejte adresu URL: `INVIEWlabs`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://app.discoverunifi.com/login`

    ![image](common/both-preintegrated-signon.png)

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **nastavit sjednocuje** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-unifi-single-sign-on"></a>Konfigurace jednoho Sign-Onu sjednocuje

1. V jiném okně webového prohlížeče se přihlaste k webu svého společnosti **sjednocuje** jako správce.

2. Klikněte na **uživatele**.

    ![Snímek obrazovky se zobrazí uživatelé vybraní z webu sjednocuje.](./media/unifi-tutorial/app1.png)

3. Klikněte na **Přidat nového zprostředkovatele identity**.

    ![Snímek obrazovky zobrazuje vybraný Nový zprostředkovatel identity.](./media/unifi-tutorial/app2.png)

4. V části **Přidat zprostředkovatele identity** proveďte následující kroky:

    ![Snímek obrazovky s informacemi o přidání zprostředkovatele identity, kde můžete zadat hodnoty, které jsou popsány.](./media/unifi-tutorial/app3.png) 

    a. Do textového pole **název poskytovatele** zadejte název zprostředkovatele identity.

    b. Do textového pole **Adresa URL poskytovatele** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Otevřete certifikát, který jste stáhli z Azure Portal v programu Poznámkový blok, odeberte **---zahajte---certifikátu** a **------** a vložte zbývající obsah do textového pole **certifikátu** .

    d. Zaškrtněte políčko **je výchozím zprostředkovatelem** .

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k sjednocuje.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **sjednocuje**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **sjednocuje**.

    ![Odkaz sjednocuje v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-unifi-test-user"></a>Vytvořit testovacího uživatele |

V této části vytvoříte uživatele s názvem Britta Simon. Funkce **sjednocuje** podporuje automatické zřizování uživatelů, takže není nutné provádět žádné ruční kroky. Uživatelé se automaticky vytvoří po úspěšném ověření z Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici ' sjednocuje ' na přístupovém panelu byste měli být automaticky přihlášeni k vlastnosti sjednocuje, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

