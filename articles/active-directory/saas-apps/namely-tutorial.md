---
title: 'Kurz: Integrace služby Azure Active Directory s konkrétně | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Namely.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: a9ec54ce27b4d058938e688ec671709e09391cce
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160365"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Kurz: Integrace Azure Active Directory s Namely

V tomto kurzu se dozvíte, jak integrovat Konkrétně s Azure Active Directory (Azure AD).
Integrace Konkrétně s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Namely.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k Namely (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Namely, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Konkrétně jedno přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Konkrétně podporuje **SP** inicioval SSO

## <a name="adding-namely-from-the-gallery"></a>Přidání Konkrétně z galerie

Chcete-li nakonfigurovat integraci Namely do Azure AD, musíte přidat Namely z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Konkrétně z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Ve vyhledávacím poli zadejte **Namely**, vyberte **Namely** z panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![Konkrétně v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s Namely na základě testovacího uživatele s názvem **Britta Simon**.
Pro jednotné přihlašování do práce, vztah propojení mezi uživatelem Azure AD a související uživatel v Namely musí být vytvořen.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD s Namely, je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace Namely Single Sign-On](#configure-namely-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit Konkrétně test uživatele](#create-namely-test-user)** – mít protějšek Britta Simon v Konkrétně, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí Namely, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce **Namely** application integration vyberte **Single sign-on**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Konkrétně domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.namely.com`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Kontakt [Jmenovitě tým podpory klienta](https://www.namely.com/contact/) získat tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit, konkrétně,** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-namely-single-sign-on"></a>Konfigurovat konkrétně jednotné přihlašování

1. V jiném okně prohlížeče se přihlaste na web společnosti Administrátor.

2. Na panelu nástrojů nahoře klikněte na **Společnost**.
   
    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_06.png) 

3. Klikněte na kartu **Nastavení**.
   
    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_07.png) 

4. Klepněte na tlačítko **SAML**.
   
    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_08.png) 

5. Na stránce **Nastavení SAML** proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_09.png)
 
    a. Klepněte na **tlačítko Povolit funkci SAML**. 

    b. V textovém poli **url adresy Identity zprostředkovatele identity** vložte hodnotu **přihlašovací adresy URL**, kterou jste zkopírovali z webu Azure Portal.
    
    c. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte obsah a vložte ho do textového pole **certifikátu poskytovatele identity.**
     
    d. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu namely.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Namely**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Konkrétně**.

    ![Odkaz A to v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-namely-test-user"></a>Vytvořit konkrétně test uživatele

Cílem této části je vytvořit uživatele s názvem Britta Simon v Namely.

**Chcete-li vytvořit uživatele s názvem Britta Simon v Namely, proveďte následující kroky:**

1. Přihlaste se k webu společnosti, který je již správcem.

2. Na panelu nástrojů nahoře klikněte na **Lidé**.
   
    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_10.png) 

3. Klikněte na kartu **Adresář.**
   
    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_11.png) 

4. Klikněte na **Přidat novou osobu**.

    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_12.png)

5. V dialogovém okně **Přidat novou osobu** proveďte následující kroky:

    a. Do textového pole **Jméno** zadejte **Britta**.

    b. Do textového pole **Příjmení** zadejte **Simon**.

    c. Do textového pole **E-mail** zadejte **e-mailovou adresu** BrittaSimon.

    d. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Konkrétně na přístupovém panelu, měli byste být automaticky přihlášeni k Namely, pro které nastavíte přivytváření. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

