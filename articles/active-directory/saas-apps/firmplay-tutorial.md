---
title: 'Kurz: Azure Active Directory integraci s FirmPlay-Employee Advocacy pro nábor | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a FirmPlay-Employee Advocacy pro nábor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2230958fb41d8e42967beeca57cf10ea048d1ef9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453463"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Kurz: Azure Active Directory integrace s FirmPlay-Employee Advocacy pro nábor

V tomto kurzu se naučíte, jak integrovat Advocacy FirmPlay-Employees pro nábor pomocí Azure Active Directory (Azure AD).
Integrace FirmPlay-Employee Advocacy pro nábor s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k Advocacy FirmPlay-Employee pro nábor.
* Uživatelům můžete povolit, aby se automaticky přihlásili k FirmPlay-Employee Advocacy pro nábor (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Pokud chcete nakonfigurovat integraci Azure AD s FirmPlay-Employee Advocacy pro nábor, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* FirmPlay-Employee Advocacy pro nábor odběru s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* FirmPlay-Employee Advocacy for nábor podporuje jednotné přihlašování s využitím služeb **SP**

## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Přidání FirmPlay-Employee Advocacy pro nábor z Galerie

Pokud chcete nakonfigurovat integraci FirmPlay-Employee Advocacy pro nábor do služby Azure AD, musíte přidat FirmPlay-Employee Advocacy pro nábor z Galerie do vašeho seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat FirmPlay-Employee Advocacy pro nábor z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **FirmPlay-Employee Advocacy for nábor**, vyberte **FirmPlay-Employee Advocacy for nábor** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![FirmPlay – zaměstnanci Advocacy pro nábor v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí FirmPlay-Employee Advocacy pro nábor na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo možné jednotné přihlašování pracovat, je nutné zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v FirmPlay-Employee Advocacy pro nábor.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí FirmPlay-Employee Advocacy pro nábor, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte FirmPlay-Employee Advocacy pro nábor jednotného přihlašování](#configure-firmplay---employee-advocacy-for-recruiting-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte FirmPlay-Employee Advocacy for náborového testovacího uživatele](#create-firmplay---employee-advocacy-for-recruiting-test-user)** – abyste měli protějšek Britta Simon v FirmPlay-Employee Advocacy pro nábor, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete pro nábor nakonfigurovat jednotné přihlašování Azure AD pomocí Advocacy FirmPlay-Employee, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **FirmPlay-Employee Advocacy for nábor** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![FirmPlay-Employee Advocacy for nábor v doméně a adresách URL jednotného přihlašování](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<your-subdomain>.firmplay.com/`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, kontaktujte [FirmPlay-Employee Advocacy for Nábor týmu podpory klientů](mailto:engineering@firmplay.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení FirmPlay-Employee Advocacy for nábor** zkopírujte příslušné adresy URL dle vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-firmplay---employee-advocacy-for-recruiting-single-sign-on"></a>Konfigurace FirmPlay-Employee Advocacy pro nábor jednoho Sign-On

Pokud chcete nakonfigurovat jednotné přihlašování na **FirmPlay-Employee Advocacy pro náborovou** stranu, je potřeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory FirmPlay-Employee Advocacy for nábor](mailto:engineering@firmplay.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Advocacyi FirmPlay-Employee pro nábor.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **FirmPlay-Employee Advocacy for nábor**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **FirmPlay-Employee Advocacy for nábor**.

    ![Odkaz FirmPlay-Employee Advocacy for nábor v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-firmplay---employee-advocacy-for-recruiting-test-user"></a>Vytvoření FirmPlay-Employee Advocacy for nábor zkušebního uživatele

V této části vytvoříte uživatele s názvem Britta Simon v FirmPlay-zaměstnanec Advocacy pro nábor. Pomocí [týmu podpory FirmPlay-Employee Advocacy for nábor](mailto:engineering@firmplay.com) můžete přidat uživatele v FirmPlay-Employee Advocacy pro náborovou platformu. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici FirmPlay-Employee Advocacy for nábor na přístupovém panelu byste se měli automaticky přihlášeni k Advocacy FirmPlay-Employee, aby bylo možné přihlašování nastavit. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)