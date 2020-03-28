---
title: 'Kurz: Integrace služby Azure Active Directory se společností Clarizen | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a službou Clarizen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b34e6087f3a6e7f3cc0f46c53d4fe903838d210
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158602"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Kurz: Integrace služby Azure Active Directory se společností Clarizen

V tomto kurzu se dozvíte, jak integrovat Clarizen s Azure Active Directory (Azure AD).
Integrace clarizenu s Azure AD vám přináší následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup ke službě Clarizen.
* Uživatelům můžete povolit automatické přihlášení ke službě Clarizen (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Clarizen, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením Clarizen

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Clarizen podporuje iniciované sso iniciované **idicí IndP**

## <a name="adding-clarizen-from-the-gallery"></a>Přidání Clarizen z galerie

Pokud chcete nakonfigurovat integraci Clarizenu do Azure AD, musíte clarizen přidat z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat clarizen z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Clarizen**, z panelu výsledků vyberte **Clarizen** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![Clarizen v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí clarizenu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v clarizenu.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby Clarizen, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace technologie Clarizen Single Sign-On](#configure-clarizen-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele Clarizen](#create-clarizen-test-user)** – chcete-li mít protějšek Britta Simon v Clarizen, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí služby Clarizen, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Clarizen** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** proveďte následující kroky:

    ![Informace o doméně a adresách URL clarizenu](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte hodnotu:`Clarizen`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

    > [!NOTE]
    > To nejsou skutečné hodnoty. Musíte použít skutečný identifikátor a adresu URL odpovědi. Zde doporučujeme použít jedinečnou hodnotu řetězce jako identifikátor. Chcete-li získat skutečné hodnoty, obraťte se na [tým podpory společnosti Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit clarizen zkopírujte** příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-clarizen-single-sign-on"></a>Konfigurace jednotného přihlašování Clarizen

1. V jiném okně webového prohlížeče se přihlaste na web společnosti Clarizen jako správce.

1. Klikněte na své uživatelské jméno a potom klikněte na **Nastavení**.

    ![Klikněte na "Nastavení" pod svým uživatelským jménem](./media/clarizen-tutorial/tutorial_clarizen_001.png "Nastavení")

1. Klikněte na kartu **Globální nastavení.** Potom vedle **položky Federated Authentication**klikněte na **upravit**.

    ![Karta Globální nastavení](./media/clarizen-tutorial/tutorial_clarizen_002.png "Globální nastavení")

1. V dialogovém okně **Federované ověřování** proveďte následující kroky:

    ![Dialogové okno Federated Authentication](./media/clarizen-tutorial/tutorial_clarizen_003.png "Federované ověřování")

    a. Vyberte **Povolit federované ověřování**.

    b. Kliknutím na **Nahrát** nahrajete stažený certifikát.

    c. Do pole **Přihlašovací adresa URL** zadejte hodnotu přihlašovací adresy **URL** z okna konfigurace aplikace Azure AD.

    d. Do pole **Odhlásit adresu URL** zadejte hodnotu **url odhlášení** z okna konfigurace aplikace Azure AD.

    e. Vyberte **použít post**.

    f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** zadejte **\@brittasimon vašecompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Clarizen.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Clarizen**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Clarizen**.

    ![Odkaz Clarizen v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-clarizen-test-user"></a>Vytvořit testovacího uživatele Clarizen

Cílem této sekce je vytvořit uživatele s názvem Britta Simon v Clarizen.

**Pokud potřebujete vytvořit uživatele ručně, proveďte následující kroky:**

Chcete-li povolit uživatelům Azure AD k přihlášení ke službě Clarizen, musíte zřídit uživatelské účty. V případě Clarizen zřizování je ruční úlohy.

1. Přihlaste se na firemní web Clarizen jako správce.

2. Klikněte na **Lidé**.

    ![Kliknutí na "Lidé"](./media/clarizen-tutorial/create_aaduser_001.png "People")

3. Klepněte na **tlačítko Pozvat uživatele**.

    ![Tlačítko "Pozvat uživatele"](./media/clarizen-tutorial/create_aaduser_002.png "Pozvat uživatele")

1. V dialogovém okně **Pozvat uživatele** proveďte následující kroky:

    ![Dialogové okno Pozvat lidi](./media/clarizen-tutorial/create_aaduser_003.png "Pozvání lidí")

    a. Do pole **E-mail** zadejte e-mailovou adresu účtu Britta Simon.

    b. Klepněte na **tlačítko Pozvat**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail a poté potvrdí odkaz, který potvrdí jeho účet, než se stane aktivním.


### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Clarizen na přístupovém panelu, můžete by měl být automaticky přihlášeni k Clarizen, u kterého nastavíte přistupující spo.; Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
