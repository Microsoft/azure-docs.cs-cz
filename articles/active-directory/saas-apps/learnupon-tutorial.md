---
title: 'Kurz: Azure Active Directory integrace s LearnUpon | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LearnUpon.
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
ms.openlocfilehash: 297395fbd0a7fd3fc8959eed5c0aeabd7b8e5751
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458672"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Kurz: Azure Active Directory integrace s LearnUpon

V tomto kurzu se dozvíte, jak integrovat LearnUpon s Azure Active Directory (Azure AD).
Integrace LearnUpon s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k LearnUpon.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k LearnUpon (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s LearnUpon potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným LearnUponm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.


* LearnUpon podporuje jednotné přihlašování **IDP** .

* LearnUpon podporuje zřizování uživatelů **jenom v čase** .


## <a name="adding-learnupon-from-the-gallery"></a>Přidání LearnUpon z Galerie

Pokud chcete nakonfigurovat integraci LearnUpon do služby Azure AD, musíte přidat LearnUpon z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat LearnUpon z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **LearnUpon**, vyberte **LearnUpon** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![LearnUpon v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí LearnUpon na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v LearnUpon.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí LearnUpon, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování LearnUpon](#configure-learnupon-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření LearnUpon Test User](#create-learnupon-test-user)** – pro Britta Simon v LearnUpon, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí LearnUpon, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **LearnUpon** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně LearnUpon a adresách URL](common/idp-reply.png)

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:  `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou URL odpovědi. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta LearnUpon](https://www.learnupon.com/features/support/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML Najděte **kryptografický otisk** , který se přidá do nastavení LearnUpon SAML.

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

6. V části **Nastavení LearnUpon** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-learnupon-single-sign-on"></a>Nakonfigurovat LearnUpon jednu Sign-On

1. Otevřete jinou instanci prohlížeče a přihlaste se k LearnUpon pomocí účtu správce.

1. Klikněte na kartu **Nastavení** .

    ![Snímek obrazovky se zobrazí karta nastavení.](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Klikněte na **jednotné přihlašování – SAML**a pak klikněte na **Obecné nastavení** a nakonfigurujte nastavení SAML.
   
    ![Snímek obrazovky, který zobrazuje jednotné přihlašování-SAML, je vybraný s obecným nastavením.](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. V části **Obecné nastavení** proveďte následující kroky:
   
    ![Snímek obrazovky se zobrazí v sekci Obecné nastavení, kde můžete zadat hodnoty, které jsou popsány.](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Vyberte **Povoleno**.

    b. Vyberte **verzi** jako **2,0**.

    c. Vyberte možnost **Přeskočit podmínky** jako **ne**.

    d. Do textového pole **název parametru post tokenu SAML** zadejte název parametru post adresy URL příjemce SAML, který je uvedený výše, který obsahuje kontrolní výraz SAML, který se má ověřit a ověřit – například **SAMLResponse**.

    e. Do textového pole **Formát identifikátoru názvu** zadejte hodnotu, která označuje, kde ve výrazu SAML je identifikátor uživatele (e-mailová adresa), například `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .
  
    f. Do textového pole **určení umístění poskytovatele** zadejte hodnotu, která označuje, kam se uživatelé odesílají, pokud na obrazovce Azure Portal přihlašovací obrazovky kliknete na nahranou ikonu.
  
    například V poli **Adresa URL** pro odhlášení vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    h. Klikněte na **Spravovat prst tiskne**a pak nahrajte tisk svého staženého certifikátu.

1. Klikněte na **nastavení uživatele**a pak proveďte následující kroky:

     ![Snímek obrazovky se zobrazí oddíl nastavení uživatele, kde můžete zadat hodnoty, které jsou popsány.](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. Do textového pole **první formát identifikátoru názvu** zadejte hodnotu, která nám sdělí, kde ve vašem kontrolním výrazu SAML se nacházejí uživatelé, například: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .
  
    b. Do textového pole **Poslední formát identifikátoru názvu** zadejte hodnotu, která nám sdělí, kde ve vašem kontrolním výrazu SAML se nachází příjmení uživatelé – například: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k LearnUpon.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **LearnUpon**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **LearnUpon**.

    ![Odkaz LearnUpon v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-learnupon-test-user"></a>Vytvořit testovacího uživatele LearnUpon

V této části se v LearnUpon vytvoří uživatel s názvem Britta Simon. LearnUpon podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v LearnUpon neexistuje, vytvoří se po ověření nový. Pokud potřebujete ručně vytvořit uživatele, musíte se obrátit na [tým podpory LearnUpon](https://www.learnupon.com/features/support/).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici LearnUpon, měli byste se automaticky přihlásit k LearnUpon, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)