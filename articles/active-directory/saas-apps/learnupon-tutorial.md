---
title: 'Kurz: Integrace Služby Azure Active Directory s LearnUpon | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdcd8c6024b3cacb422b556718bbbdbb5d601c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098243"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Kurz: Integrace Azure Active Directory s LearnUpon

V tomto kurzu se dozvíte, jak integrovat LearnUpon s Azure Active Directory (Azure AD).
Integrace LearnUpon u Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k LearnUpon.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke službě LearnUpon (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s LearnUpon, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* LearnUpon jednotné přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.


* LearnUpon podporuje **IDP** iniciované sponesení

* LearnUpon podporuje **just in time** zřizování uživatelů


## <a name="adding-learnupon-from-the-gallery"></a>Přidání LearnUpon z galerie

Chcete-li nakonfigurovat integraci LearnUpon do Azure AD, musíte přidat LearnUpon z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat LearnUpon z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **LearnUpon**, zvolte **LearnUpon** z panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

    ![LearnUpon v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí LearnOn na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem ve LearnUponu.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí LearnUpon, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace LearnUpon Single Sign-On](#configure-learnupon-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte LearnUpon testovací ho uživatele](#create-learnupon-test-user)** – mít protějšek Britta Simon v LearnUpon, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí learnuponu, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce Integrace aplikací **LearnOn** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![LearnUpon Domény a adresy URL jednotné přihlašovací informace](common/idp-reply.png)

    Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > Hodnota není skutečná. Aktualizujte hodnotu skutečnou adresou URL pro odpověď. Kontaktujte [tým podpory klienta LearnUpon,](https://www.learnupon.com/features/support/) abyste získali hodnotu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování se samlem** vyhledejte **thumbprint** – to to bude přidáno do vašeho LearnUpon SAML Settings.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. V části **Nastavit LearnOn** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-learnupon-single-sign-on"></a>Konfigurace aplikace LearnUpon Single Sign-On

1. Otevřete jinou instanci prohlížeče a přihlaste se do LearnUpon pomocí účtu správce.

1. Klikněte na kartu **Nastavení.**

    ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Klikněte **na Jednotné přihlášení – SAML**a potom na obecné **nastavení** nakonfigurujte nastavení SAML.
   
    ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. V části **Obecné nastavení** proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Vyberte **Povoleno**.

    b. Vyberte **verzi** jako **2.0**.

    c. Vyberte **přeskočit podmínky** jako **ne**.

    d. Do textového pole **název param tokenu SAML Post** zadejte název parametru request post do výše uvedené adresy URL příjemce SAML, která obsahuje kontrolní výraz SAML, který má být ověřen a ověřen - například **SAMLResponse**.

    e. Do textového pole **Formát identifikátoru názvu** zadejte hodnotu, která označuje, kde se v `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`kontrolním výrazu SAML nachází identifikátor uživatelů (e-mailová adresa) – například .
  
    f. Do textového pole **Identifikovat umístění zprostředkovatele** zadejte hodnotu, která označuje, kam se uživatelé odesílají, pokud kliknou na vaši nahranou ikonu z přihlašovací obrazovky portálu Azure.
  
    g. Do textového pole **Odhlásit adresu URL** vložte hodnotu **url odhlášení,** kterou jste zkopírovali z portálu Azure.

    h. Klikněte na **Spravovat otisky prstů**a potom nahrajte otisk prstu staženého certifikátu.

1. Klikněte na **Nastavení uživatele**a proveďte následující kroky:

     ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. Do textového pole **Formát identifikátoru názvu** zadejte hodnotu, která nám říká, kde se `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`ve stolku SAML nachází jméno uživatele , například: .
  
    b. Do textového pole **Formát identifikátoru příjmení** zadejte hodnotu, která nám říká, kde se `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`ve stolku SAML nachází příjmení uživatelů – například: .

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** `brittasimon@yourcompanydomain.extension`jméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k LearnUpon.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **LearnUpon**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **LearnUpon**.

    ![Odkaz LearnUpon v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-learnupon-test-user"></a>Vytvořit LearnUpon test uživatele

V této části je uživatel s názvem Britta Simon vytvořen v LearnUpon. LearnUpon podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ještě neexistuje ve LearnUpon, nový je vytvořen po ověření. Pokud potřebujete vytvořit uživatele ručně, musíte kontaktovat [tým podpory LearnUpon](https://www.learnupon.com/features/support/).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici LearnUpon na přístupovém panelu, měli byste být automaticky přihlášeni k LearnUpon, pro které nastavíte přivytváření. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)