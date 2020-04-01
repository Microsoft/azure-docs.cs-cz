---
title: 'Kurz: Integrace služby Azure Active Directory s malými vylepšeními | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a malými vylepšeními.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: d2d0bbc7a6e1c680434041d1b9d55e39a96b6f44
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090362"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Kurz: Integrace služby Azure Active Directory s malými vylepšeními

V tomto kurzu se dozvíte, jak integrovat malá vylepšení s Azure Active Directory (Azure AD).
Integrace malých vylepšení s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k malé vylepšení.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k malým vylepšením (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s malými vylepšeními, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Malé vylepšení jednotné přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Malá vylepšení podporuje **sp** inicioval sso

## <a name="adding-small-improvements-from-the-gallery"></a>Přidání drobných vylepšení z galerie

Chcete-li nakonfigurovat integraci malých vylepšení do Azure AD, je potřeba přidat malá vylepšení z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li z galerie přidat malá vylepšení, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Malá vylepšení**, z panelu výsledků vyberte **Malá vylepšení** a klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![Malá vylepšení v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s malými vylepšeními na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v malých vylepšeních.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD s malými vylepšeními, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace malých vylepšení jednotného přihlašování](#configure-small-improvements-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit malé vylepšení testovacího uživatele](#create-small-improvements-test-user)** – mít protějšek Britta Simon v malé vylepšení, která je propojena s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování azure ad s malými vylepšeními, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **s malými vylepšeními** vyberte **možnost Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Malé vylepšení Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.small-improvements.com`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte se na [tým podpory klienta s malými vylepšeními](mailto:support@small-improvements.com) a získejte tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit malá vylepšení** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-small-improvements-single-sign-on"></a>Konfigurace malých vylepšení jednotného přihlašování

1. V jiném okně prohlížeče se přihlaste na web společnosti Small Improvements jako správce.

1. Na hlavní stránce řídicího panelu klikněte vlevo na tlačítko **Správa.**

    ![Konfigurace jednotného přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Klikněte na tlačítko **saml sso** z části **Integrace.**

    ![Konfigurace jednotného přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Na stránce Nastavení přistaň provedete následující kroky:

    ![Konfigurace jednotného přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. Do textového pole **HTTP Endpoint** vložte hodnotu **přihlašovací adresy URL**, kterou jste zkopírovali z webu Azure Portal.

    b. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte obsah a vložte ho do textového pole **certifikátu x509.** 

    c. Pokud chcete mít možnost ověřování pomocí služby SSO a přihlašovací formulář k dispozici pro uživatele, pak **zkontrolujte povolit přístup přes přihlašovací jméno / heslo příliš** možnost.  

    d. Zadejte příslušnou hodnotu pro pojmenování tlačítka Přihlášení při přihlašovacím zařízení SSO do textového pole **Výzva SAML.**  

    e. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k malé vylepšení.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte Malá **vylepšení**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **malá vylepšení**.

    ![Odkaz Malá zlepšení v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-small-improvements-test-user"></a>Vytvořit malé vylepšení testovacího uživatele

Chcete-li povolit uživatelům Azure AD k přihlášení k malé vylepšení, musí být zřízena do malé vylepšení. V případě malých vylepšení zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu společnosti Small Improvements company jako správce.

1. Na domovské stránce přejděte do nabídky vlevo a klikněte na **Správa**.

1. Klepněte na tlačítko **Adresář uživatelů** v části Správa uživatelů.

    ![Vytvoření testovacího uživatele Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Klikněte na **Přidat uživatele**.

    ![Vytvoření testovacího uživatele Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. V dialogovém okně **Přidat uživatele** proveďte následující kroky: 

    ![Vytvoření testovacího uživatele Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Zadejte **křestní jméno** uživatele, jako je **Britta**.

    b. Zadejte **příjmení** uživatele, jako je **Simon**.

    c. Zadejte **e-mail** uživatele jako **brittasimon@contoso.com**.

    d. Můžete také zadat osobní zprávu do **e-mailové schránky Odeslat oznámení.** Pokud si nepřejete odeslat oznámení, zaškrtněte toto políčko.

    e. Klepněte na **tlačítko Vytvořit uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici malá vylepšení na přístupovém panelu, můžete by měl být automaticky přihlášeni k malé vylepšení, pro které nastavíte přistupující ho. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)