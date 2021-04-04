---
title: 'Kurz: Azure Active Directory integrace s UserEcho | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a UserEcho.
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
ms.openlocfilehash: be52d220848a27c1307e71fdb7cc4dccfaeb9618
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92509331"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Kurz: Azure Active Directory integrace s UserEcho

V tomto kurzu se dozvíte, jak integrovat UserEcho s Azure Active Directory (Azure AD).
Integrace UserEcho s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k UserEcho.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k UserEcho (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s UserEcho potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným UserEchom jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* UserEcho podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-userecho-from-the-gallery"></a>Přidání UserEcho z Galerie

Pokud chcete nakonfigurovat integraci UserEcho do služby Azure AD, musíte přidat UserEcho z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat UserEcho z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **UserEcho**, vyberte **UserEcho** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![UserEcho v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí UserEcho na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v UserEcho.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí UserEcho, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování UserEcho](#configure-userecho-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření UserEcho Test User](#create-userecho-test-user)** – pro Britta Simon v UserEcho, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí UserEcho, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **UserEcho** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně UserEcho a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.userecho.com/`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta UserEcho](https://feedback.userecho.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení UserEcho** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-userecho-single-sign-on"></a>Nakonfigurovat UserEcho jednu Sign-On

1. V jiném okně prohlížeče se přihlaste k webu UserEcho společnosti jako správce.

2. Na panelu nástrojů v horní části klikněte na své uživatelské jméno a rozbalte nabídku a potom klikněte na tlačítko **nastavit**.
   
    ![Snímek obrazovky se zobrazí nastavení vybrané na webu UserEcho.](./media/userecho-tutorial/tutorial_userecho_06.png) 

3. Klikněte na **integrace**.
   
    ![Snímek obrazovky zobrazuje integrace vybrané z nabídky nastavení.](./media/userecho-tutorial/tutorial_userecho_07.png) 

4. Klikněte na **Web** a potom klikněte na **jednotné přihlašování (typu Saml2)**.
   
    ![Snímek obrazovky se v nabídce integrace zobrazuje typu Saml2 s jednotným přihlašováním.](./media/userecho-tutorial/tutorial_userecho_08.png) 

5. Na stránce **jednotného přihlašování (SAML)** proveďte následující kroky:
   
    ![Snímek obrazovky se zobrazí na stránce SAML jednotného přihlašování, kde můžete zadat hodnoty, které jsou popsány.](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. V případě, že je **povoleno SAML**, vyberte **Ano**.
    
    b. Vložte **adresu URL pro přihlášení**, kterou jste zkopírovali z Azure Portal do textového pole **adresy URL jednotného přihlašování SAML** .
    
    c. Vložte **adresu URL pro odhlášení**, kterou jste zkopírovali z Azure Portal do textového pole **Adresa URL pro vzdálené odhlášení** .
    
    d. Otevřete stažený certifikát v programu Poznámkový blok, zkopírujte obsah a vložte ho do textového pole **certifikát X. 509** .
    
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
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k UserEcho.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **UserEcho**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **UserEcho**.

    ![Odkaz UserEcho v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-userecho-test-user"></a>Vytvořit testovacího uživatele UserEcho

Cílem této části je vytvořit uživatele s názvem Britta Simon v UserEcho.

**Chcete-li vytvořit uživatele s názvem Britta Simon v UserEcho, proveďte následující kroky:**

1. Přihlaste se k webu UserEcho společnosti jako správce.

2. Na panelu nástrojů v horní části klikněte na své uživatelské jméno a rozbalte nabídku a potom klikněte na tlačítko **nastavit**.
   
    ![Snímek obrazovky se zobrazí nastavení vybrané na webu UserEcho.](./media/userecho-tutorial/tutorial_userecho_06.png)

3. Kliknutím na **Uživatelé** rozbalte oddíl **Uživatelé** .
   
    ![Snímek obrazovky zobrazuje uživatele vybrané v nabídce nastavení.](./media/userecho-tutorial/tutorial_userecho_10.png)

4. Klikněte na **Uživatelé**.
   
    ![Snímek obrazovky znázorňující vybrané uživatele.](./media/userecho-tutorial/tutorial_userecho_11.png)

5. Klikněte na **pozvat nového uživatele**.
   
    ![Snímek obrazovky s výzvou k vytvoření nového uživatelského ovládacího prvku](./media/userecho-tutorial/tutorial_userecho_12.png)

6. V dialogovém okně **pozvat nového uživatele** proveďte následující kroky:
   
    ![Snímek obrazovky se zobrazí dialogové okno pozvat nového uživatele, kde můžete zadat informace o uživateli.](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. Do textového pole **název** zadejte název uživatele, například Britta Simon.
    
    b.  Do textového pole **e-mail** zadejte e-mailovou adresu uživatele Brittasimon@contoso.com .
    
    c. Klikněte na **pozvat**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici UserEcho, měli byste se automaticky přihlásit k UserEcho, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)