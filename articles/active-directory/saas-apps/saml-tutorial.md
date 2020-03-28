---
title: 'Kurz: Integrace služby Azure Active Directory s lobovou aplikací s podporou tokenů SAML 1.1 tokenu | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi službou Azure Active Directory a obchodní aplikací s podporou tokenů SAML 1.1.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6de8906f15b170511c5548eeaf13173bd39a2734
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160060"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Kurz: Integrace služby Azure Active Directory s obchodní aplikací s podporou tokenu SAML 1.1

V tomto kurzu se dozvíte, jak integrovat SAML 1.1 Token povoleno LOB app s Azure Active Directory (Azure AD).
Integrace obchodní aplikace s podporou tokenu SAML 1.1 s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k SAML 1.1 Token povoleno LOB App.
* Můžete povolit, aby vaši uživatelé byli automaticky přihlášeni k OBCHODNÍ aplikaci s podporou tokenu SAML 1.1 token (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí obchodní aplikace s podporou tokenu SAML 1.1 tokenu, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Saml 1.1 Token povoleno LOB App jednotné přihlášení app povolení předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* LOB aplikace s podporou služby ASO iniciované tokenem 1.1 token podporuje služby SSO iniciované **sp.**

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>Přidání OBCHODNÍ APLIKACE S podporou tokenu SAML 1.1 z galerie

Chcete-li nakonfigurovat integraci OBCHODNÍ aplikace s podporou tokenů SAML 1.1 tokenu do Azure AD, musíte přidat LOB aplikaci s podporou tokenu SAML 1.1 z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li z galerie přidat obchodní aplikaci s podporou tokenu SAML 1.1 token, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **LOB aplikaci s podporou tokenu SAML 1.1 Token**, z panelu výsledků vyberte **OBCHODNÍ APLIKACI s podporou tokenu SAML 1.1 token** a pak klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![OBCHODNÍ APLIKACE S podporou tokenu SAML 1.1 v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí lobové aplikace s podporou tokenu SAML 1.1 na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Služby Azure AD a souvisejícím uživatelem v tokenu SAML 1.1 Token.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí lobové aplikace s podporou tokenu SAML 1.1 tokenu, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace saml 1.1 token povoleno LOB App Single Sign-On](#configure-saml-11-token-enabled-lob-app-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte SAML 1.1 Token povoleno LOB App test uživatele](#create-saml-11-token-enabled-lob-app-test-user)** – mít protějšek Britta Simon v SAML 1.1 Token povoleno LOB app, který je propojen s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí obchodní aplikace s podporou tokenu SAML 1.1 tokenem, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací LOB s **podporou tokenu SAML 1.1** vyberte **možnost Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Saml 1.1 Token povoleno LOB domény aplikace a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://your-app-url`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://your-app-url`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Chcete-li získat tyto hodnoty, obraťte se na tým podpory klienta LOB s podporou tokenu SAML 1.1. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit token EMS s podporou tokenu NAStavíte** příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-saml-11-token-enabled-lob-app-single-sign-on"></a>Konfigurace jednotného přihlášení k aplikaci LOB s podporou tokenu SAML 1.1

Chcete-li nakonfigurovat jednotné přihlašování na straně lob aplikací s **podporou tokenů SAML 1.1 token,** musíte odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z portálu Azure na tým podpory lob aplikací s podporou tokenů SAML 1.1 token. Toto nastavení nastaví tak, aby bylo připojení s přizasazené k samovazbě SAML správně nastaveno na obou stranách.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k TOKENU SAML 1.1 token povoleno LOB App.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte OBCHODNÍ **APLIKACI s podporou tokenu SAML 1.1 .**

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **OBCHODNÍ APLIKACI s podporou tokenu SAML 1.1 Token**.

    ![Odkaz OBCHODNÍ APLIKACE s podporou tokenu SAML 1.1 v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>Vytvořit uživatele testu LOB aplikace s podporou tokenu SAML 1.1

V této části vytvoříte uživatele s názvem Britta Simon v SAML 1.1 Token povoleno LOB App. Práce s SAML 1.1 Token povoleno LOB App tým podpory přidat uživatele v SAML 1.1 Token povoleno LOB App platformy. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici OBCHODNÍ APLIKACE s podporou tokenu SAML 1.1 na přístupovém panelu byste měli být automaticky přihlášeni k obchodní aplikaci s podporou tokenu SAML 1.1, pro kterou nastavíte služby Připřipojení k internetu. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

