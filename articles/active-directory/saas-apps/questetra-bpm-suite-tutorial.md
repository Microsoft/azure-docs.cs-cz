---
title: 'Kurz: Integrace služby Azure Active Directory s aplikací Questetra BPM Suite | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi službou Azure Active Directory a službou Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: f58d6cbc6ec04e51e105662dff31c60ff502584c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093363"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Kurz: Integrace služby Azure Active Directory se sadou Questetra BPM Suite

V tomto kurzu se dozvíte, jak integrovat Questetra BPM Suite s Azure Active Directory (Azure AD).
Integrace sady Questetra BPM Suite s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Questetra BPM Suite.
* Uživatelům můžete povolit automatické přihlášení k sadě Questetra BPM Suite (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí sady Questetra BPM Suite, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Questetra BPM Suite jednotné přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Questetra BPM Suite podporuje **SP** zahájila SSO

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Přidání Questetra BPM Suite z galerie

Chcete-li nakonfigurovat integraci sady Questetra BPM Suite do služby Azure AD, musíte přidat sadu Questetra BPM Suite z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Questetra BPM Suite z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Ve vyhledávacím poli zadejte **Questetra BPM Suite**, vyberte **Questetra BPM Suite** z výsledkového panelu a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![Questetra BPM Suite v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí sady Questetra BPM Suite na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v sadě Questetra BPM Suite.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí sady Questetra BPM Suite, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace Questetra BPM Suite Single Sign-On](#configure-questetra-bpm-suite-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu Questetra BPM Suite](#create-questetra-bpm-suite-test-user)** – chcete-li mít protějšek Britta Simon v Questetra BPM Suite, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí sady Questetra BPM Suite, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikace **Questetra BPM Suite** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Questetra BPM Suite Domény a ADRESY URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Tyto hodnoty můžete získat z informační sekce **SP** na stránkách společnosti **Questetra BPM Suite,** což je vysvětleno později v tutoriálu nebo se obraťte na [tým podpory klienta Questetra BPM Suite](https://www.questetra.com/contact/). Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit Questetra BPM Suite** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Konfigurace jednotného přihlášení sady Questetra BPM Suite

1. V jiném okně webového prohlížeče se přihlaste na web společnosti **Questetra BPM Suite** jako správce.

2. V horní nabídce klepněte na **položku Nastavení systému**. 
   
    ![Jednotné přihlašování azure ad][10]

3. Chcete-li otevřít stránku **SingleSignOnSAML,** klepněte na **položku Jednotné přihlašování (SAML).** 
   
    ![Jednotné přihlašování azure ad][11]

4. Na webu společnosti **Questetra BPM Suite** v části **Informace o sp** proveďte následující kroky:

    a. Zkopírujte **adresu URL služby ACS**a vložte ji do textového pole **Přihlašovat se na adresu URL** v části Základní konfigurace **SAML** z webu Azure Portal.
    
    b. Zkopírujte **ID entity**a vložte ho do textového pole **Identifikátor** v části Základní **konfigurace SAML** z webu Azure Portal.

5. Na stránkách společnosti **Questetra BPM Suite** proveďte následující kroky: 
   
    ![Konfigurace jednotného přihlašování][15]
   
    a. Vyberte **povolit jednotné přihlašování**.
   
    b. V textovém poli **ID entity** vložte hodnotu **identifikátoru Azure AD,** kterou jste zkopírovali z webu Azure Portal.
    
    c. V textovém poli Url **přihlašovací stránky** vložte hodnotu přihlašovací **adresy URL,** kterou jste zkopírovali z webu Azure Portal.
    
    d. V textovém poli Url **odhlásit stránku** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.
    
    e. Do textového pole **formátu NameID** zadejte . `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`

    f. Otevřete svůj certifikát kódovaný **base-64** v poznámkovém bloku staženém z webu Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikátu ověření.** 

    g. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Questetra BPM Suite.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Questetra BPM Suite**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Questetra BPM Suite**.

    ![Questetra BPM Suite odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-questetra-bpm-suite-test-user"></a>Vytvořit Questetra BPM Suite testovací ho uživatele

Cílem této sekce je vytvořit uživatele s názvem Britta Simon v Questetra BPM Suite.

**Chcete-li vytvořit uživatele s názvem Britta Simon v sadě Questetra BPM Suite, proveďte následující kroky:**

1. Přihlaste se na své stránky společnosti Questetra BPM Suite jako správce.

2. Přejděte do seznamu **nastavení systému > uživatele > nového uživatele**.
 
3. V dialogovém okně Nový uživatel proveďte následující kroky: 
   
    ![Vytvořit testovacího uživatele][300] 
   
    a. Do textového pole **Název** zadejte britta.simon@contoso.com **název** uživatele .
   
    b. Do textového pole **E-mail** britta.simon@contoso.comzadejte **e-mail** uživatele .
   
    c. Do textového pole **Heslo** zadejte **heslo** uživatele.
    
    d. Klepněte na tlačítko **Přidat nového uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Questetra BPM Suite na přístupovém panelu, měli byste být automaticky přihlášeni k Questetra BPM Suite, pro které nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png