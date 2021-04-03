---
title: 'Kurz: Azure Active Directory integrace se službou UserVoice | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a UserVoice.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 39b3104f9d6cbc004383bf2c50d07c4dd345fa3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353574"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Kurz: Azure Active Directory integrace se službou UserVoice

V tomto kurzu se naučíte integrovat UserVoice s Azure Active Directory (Azure AD).
Integrace služby UserVoice s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k UserVoice.
* Uživatelům můžete povolit, aby se automaticky přihlásili ke službě UserVoice (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD se službou UserVoice potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povolenou jednotným přihlašováním UserVoice

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* UserVoice podporuje **jednotné** přihlašování (SSO) zahájené.

## <a name="adding-uservoice-from-the-gallery"></a>Přidává se UserVoice z galerie.

Pokud chcete nakonfigurovat integraci UserVoice do Azure AD, musíte přidat UserVoice z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat UserVoice z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **UserVoice**, vyberte **UserVoice** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![UserVoice v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD se službou UserVoice na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v UserVoice.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby UserVoice, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování UserVoice](#configure-uservoice-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte si testovacího uživatele UserVoice](#create-uservoice-test-user)** , abyste měli protějšek Britta Simon ve službě UserVoice, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí služby UserVoice, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **UserVoice** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování domény UserVoice a adres URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<tenantname>.UserVoice.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory pro klienta UserVoice](https://www.uservoice.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. V části **podpisový certifikát SAML** kliknutím na tlačítko **Upravit** otevřete dialogové okno **podpisový certifikát SAML** .

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

6. V části **podpisový certifikát SAML** zkopírujte **kryptografický otisk** a uložte ho do svého počítače.

    ![Kopírovat hodnotu kryptografického otisku](common/copy-thumbprint.png)

7. V části **nastavit UserVoice** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-uservoice-single-sign-on"></a>Konfigurace jednotného Sign-On UserVoice

1. V jiném okně webového prohlížeče se přihlaste k webu UserVoice společnosti jako správce.

2. Na panelu nástrojů v horní části klikněte na **Nastavení** a v nabídce vyberte **webový portál** .
   
    ![Oddíl nastavení na straně aplikace](./media/uservoice-tutorial/ic777519.png "Nastavení")

3. Na kartě **webový portál** v části **ověřování uživatele** klikněte na **Upravit** . otevře se dialogové okno **Upravit ověření uživatele** .
   
    ![Karta webový portál](./media/uservoice-tutorial/ic777520.png "Webový portál")

4. Na stránce **Upravit ověření uživatele** proveďte následující kroky:
   
    ![Upravit ověřování uživatelů](./media/uservoice-tutorial/ic777521.png "Upravit ověřování uživatelů")
   
    a. Klikněte na možnost **jednotné Sign-On (SSO)**.
 
    b. Vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal do textového pole pro **vzdálené přihlášení SSO** .

    c. Vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal do **textového pole vzdáleného Sign-Out jednotného přihlašování**.
 
    d. Vložte hodnotu **kryptografického otisku** , kterou jste zkopírovali z Azure Portal do aktuálního textového pole **otisku SHA1 certifikátu** .
    
    e. Klikněte na **Uložit nastavení ověřování**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k UserVoice.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **UserVoice**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **UserVoice**.

    ![Odkaz na UserVoice v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-uservoice-test-user"></a>Vytvořit testovacího uživatele UserVoice

Aby se uživatelé Azure AD mohli přihlašovat ke službě UserVoice, musí se zřídit ve službě UserVoice. V případě služby UserVoice je zřizování ručním úkolem.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Chcete-li zřídit uživatelský účet, proveďte následující kroky:

1. Přihlaste se ke svému tenantovi **UserVoice** .

2. Přejít na **Nastavení**.
   
    ![Nastavení](./media/uservoice-tutorial/ic777811.png "Nastavení")

3. Klikněte na **Obecné**.

4. Klikněte na **agenti a oprávnění**.
   
    ![Agenti a oprávnění](./media/uservoice-tutorial/ic777812.png "Agenti a oprávnění")

5. Klikněte na **přidat správce**.
   
    ![Přidat správce](./media/uservoice-tutorial/ic777813.png "Přidat správce")

6. V dialogovém okně **pozvat správce** proveďte následující kroky:
   
    ![Pozvat správce](./media/uservoice-tutorial/ic777814.png "Pozvat správce")
   
    a. Do textového pole e-maily Zadejte e-mailovou adresu účtu, který chcete zřídit, a pak klikněte na **Přidat**.
   
    b. Klikněte na **pozvat**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli další nástroje pro vytváření uživatelských účtů UserVoice nebo rozhraní API poskytovanou službou UserVoice.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici UserVoice na přístupovém panelu, měli byste se automaticky přihlásit ke službě UserVoice, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)