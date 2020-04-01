---
title: 'Kurz: Integrace Azure Active Directory s e-mailem skydesk | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a e-mailem SkyDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: faaa5dcc435452d6ed9e0f2c5b481df1e352dfd2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090430"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Kurz: Integrace Azure Active Directory s e-mailem skydesk

V tomto kurzu se dozvíte, jak integrovat SkyDesk Email s Azure Active Directory (Azure AD).
Integrace e-mailu SkyDesk s Azure AD vám přináší následující výhody:

* Můžete řídit ve službě Azure AD, kdo má přístup k e-mailu SkyDesk.
* Uživatelům můžete povolit automatické přihlášení k e-mailu SkyDesk (jednotné přihlašování) pomocí jejich účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí e-mailu SkyDesk, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* SkyDesk E-mail s povoleným předplatným pro jednotné přihlášení

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SkyDesk Email podporuje **sp** inicioval SSO

## <a name="adding-skydesk-email-from-the-gallery"></a>Přidání e-mailu SkyDesk z galerie

Pokud chcete nakonfigurovat integraci e-mailu SkyDesk do Azure AD, musíte přidat e-mail SkyDesk z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat e-mail skydesku z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SkyDesk Email**, z panelu výsledků vyberte **SkyDesk Email** a pak klikněte na **tlačítko Přidat** a přidejte aplikaci.

     ![SkyDesk Email v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí e-mailu SkyDesk na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v e-mailu SkyDesk.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí e-mailu SkyDesk, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlášení e-mailu SkyDesk](#configure-skydesk-email-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele e-mailového testu SkyDesk](#create-skydesk-email-test-user)** – chcete-li mít v e-mailu SkyDesk protějšek Britty Simonové, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí e-mailu SkyDesk, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace **e-mailových** aplikací SkyDesk vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![SkyDesk E-mailová doména a adresy URL jednotné přihlašovací informace](common/sp-signonurl.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > Hodnota není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Chcete-li získat hodnotu, obraťte se na [tým podpory e-mailového klienta SkyDesk.](https://www.skydesk.jp/apps/support/) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit e-mail skydesk** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-skydesk-email-single-sign-on"></a>Konfigurace jednotného přihlášení e-mailu skydesk

1. V jiném webovém prohlížeči se přihlaste ke svému e-mailovému účtu SkyDesk jako správce.

1. V horní nabídce klepněte na **tlačítko Nastavení**a vyberte **možnost Organizace**.

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Klikněte na **Domény** z levého panelu.

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Klikněte na **Přidat doménu**.

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Zadejte název domény a ověřte doménu.

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Klikněte na **SAML Authentication** z levého panelu.

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Na stránce dialogového okna **Ověřování SAML** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Chcete-li použít ověřování založené na saml, měli byste mít **ověřené** nastavení adresy URL domény nebo **portálu.** Adresu URL portálu můžete nastavit s jedinečným názvem.

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. Do textového pole **Přihlašovací adresa URL** vložte hodnotu přihlašovací adresy **URL**, kterou jste zkopírovali z webu Azure Portal.

    b. Do textového pole Adresa URL **odhlášení** vložte hodnotu **adresy URL odhlášení**, kterou jste zkopírovali z webu Azure Portal.

    c. **Změnit adresu URL hesla** je volitelná, proto ji ponechejte prázdnou.

    d. Kliknutím na **Získat klíč ze souboru** vyberte stažený certifikát z webu Azure Portal a kliknutím na **Otevřít** certifikát nahrajte.

    e. Jako **algoritmus**vyberte **rsa**.

    f. Klepnutím na **tlačítko Ok** změny uložte.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** typ pole**brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k SkyDesk email.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **SkyDesk Email**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **SkyDesk Email**.

    ![Odkaz E-mail skydesk v seznamu Aplikací](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-skydesk-email-test-user"></a>Vytvoření uživatele e-mailového testu SkyDesk

V této části vytvoříte uživatele s názvem Britta Simon v e-mailu SkyDesk.

Klikněte na **Přístup uživatelů** z levého panelu v e-mailu SkyDesk a zadejte své uživatelské jméno.

![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Pokud potřebujete vytvořit hromadné uživatele, musíte kontaktovat [tým podpory e-mailového klienta SkyDesk](https://www.skydesk.jp/apps/support/).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici E-mail skydesk na přístupovém panelu, měli byste být automaticky přihlášeni k e-mailu SkyDesk, pro který nastavíte službu Zabezpečení. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

