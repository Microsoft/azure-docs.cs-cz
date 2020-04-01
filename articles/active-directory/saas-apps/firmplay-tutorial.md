---
title: 'Kurz: Integrace služby Azure Active Directory s firmplay – advokacii zaměstnanců pro nábor zaměstnanců | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a FirmPlay – advokacii zaměstnanců pro nábor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2bdc3a13582f079cc9325ef1c6949c3ae10138eb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102525"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Kurz: Integrace služby Azure Active Directory s firmplay – advokacii zaměstnanců pro nábor

V tomto kurzu se dozvíte, jak integrovat FirmPlay – advokacii zaměstnanců pro nábor s Azure Active Directory (Azure AD).
Integrace FirmPlay – advokacii zaměstnanců pro nábor s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k FirmPlay – advokacii zaměstnanců pro nábor.
* Můžete povolit, aby se uživatelé automaticky přihlásili ke firmplay – advokacii zaměstnanců pro nábor (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s FirmPlay – advokacii zaměstnanců pro nábor, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* FirmPlay - Advokacii zaměstnanců pro nábor jednotného přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* FirmPlay - Advokacie zaměstnanců pro nábor podporuje **SP** zahájila SSO

## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Přidání FirmPlay - Advokacie zaměstnanců pro nábor z galerie

Chcete-li nakonfigurovat integraci FirmPlay – advokacii zaměstnanců pro nábor do Azure AD, je třeba přidat FirmPlay – advokacii zaměstnanců pro nábor z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat FirmPlay - Advokacii zaměstnanců pro nábor z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **FirmPlay - Employee Advocacy for Recruiting**, vyberte **Možnost FirmaPlay – Advokacie zaměstnanců pro nábor** z panelu výsledků a klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![FirmPlay - Advokacie zaměstnanců pro nábor v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí firmplay – advokacii zaměstnanců pro nábor na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem ve FirmPlay – advokacii zaměstnanců pro nábor.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí firmplay – advokacii zaměstnanců pro nábor, je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace FirmPlay - Advokacii zaměstnanců pro nábor jednotného přihlášení](#configure-firmplay---employee-advocacy-for-recruiting-single-sign-on)** - konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit FirmPlay - Advokacii zaměstnanců pro nábor testovacího uživatele](#create-firmplay---employee-advocacy-for-recruiting-test-user)** - mít protějšek Britta Simon v FirmPlay - advokacii zaměstnanců pro nábor, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí firmplay – advokacii zaměstnanců pro nábor, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce **FirmPlay – advokacie zaměstnanců pro nábor** aplikací vyberte Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![FirmPlay - Advokacie zaměstnanců pro nábor domén a adres URL jednotné přihlašovací informace](common/sp-signonurl.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<your-subdomain>.firmplay.com/`

    > [!NOTE]
    > Hodnota není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Kontaktujte [FirmPlay - Advokacii zaměstnanců pro nábor týmu podpory klienta](mailto:engineering@firmplay.com) získat hodnotu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit FirmPlay – Advokacie zaměstnanců pro nábor** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-firmplay---employee-advocacy-for-recruiting-single-sign-on"></a>Konfigurace FirmPlay - Advokacie zaměstnanců pro nábor jednotného přihlášení

Chcete-li nakonfigurovat jednotné přihlašování na **firmplay – advokacii zaměstnanců pro nábor,** musíte odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z portálu Azure na [firmplay – advokacii zaměstnanců pro tým podpory náboru](mailto:engineering@firmplay.com). Toto nastavení nastaví tak, aby bylo připojení s přizasazené k samovazbě SAML správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** brittasimon@yourcompanydomain.extensionjméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k FirmPlay – advokacii zaměstnanců pro nábor.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **FirmPlay – Advokacii zaměstnanců pro nábor**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu žádostí vyberte **možnost FirmPlay – Advokacii zaměstnanců pro nábor**.

    ![Odkaz FirmPlay - Advokacie zaměstnanců pro nábor v seznamu Žádostí](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-firmplay---employee-advocacy-for-recruiting-test-user"></a>Vytvořit FirmPlay - Advokacii zaměstnanců pro nábor testovacího uživatele

V této části vytvoříte uživatele s názvem Britta Simon v FirmPlay - Advokacii zaměstnanců pro nábor. Spolupracujte s [FirmPlay - Employee Advocacy for Recruiting support team](mailto:engineering@firmplay.com) to add the users in the FirmPlay - Employee Advocacy for Recruiting platform. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici FirmPlay – advokacie zaměstnanců pro nábor na přístupovém panelu byste měli být automaticky přihlášeni k firmplay – advokacii zaměstnanců pro nábor, pro který nastavíte službu SSO. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

