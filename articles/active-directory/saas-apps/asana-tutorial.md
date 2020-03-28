---
title: 'Kurz: Integrace služby Azure Active Directory s Asanou | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Asanou.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5531a7c1a95e472239c639e3307623fc4ccedd37
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157868"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Kurz: Integrace služby Azure Active Directory s Asanou

V tomto kurzu se dozvíte, jak integrovat Asana s Azure Active Directory (Azure AD).
Integrace Asana s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Asana.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k Asaně (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Asana, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Asana povolení jednotného přihlášení předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Asana podporuje **SP** inicioval SSO

* Asana podporuje [ **automatické** zřizování uživatelů](asana-provisioning-tutorial.md)

## <a name="adding-asana-from-the-gallery"></a>Přidání Asany z galerie

Chcete-li nakonfigurovat integraci Asany do služby Azure AD, musíte přidat Asanu z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Asanu z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Asana**, z panelu výsledků vyberte **Asana** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![Asana v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s Asana na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Asaně.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí asany, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování Asana](#configure-asana-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu Asana](#create-asana-test-user)** – chcete-li mít protějšek Britta Simon v Asana, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí aplikace Asana, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Asana** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Asana Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL:`https://app.asana.com/`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL:`https://app.asana.com/`

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit Asanu** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-asana-single-sign-on"></a>Konfigurace jednotného přihlašování Asana

1. V jiném okně prohlížeče se přihlaste k aplikaci Asana. Chcete-li nakonfigurovat přihlašování k obecnému zabezpečení v Asaně, přejděte k nastavení pracovního prostoru kliknutím na název pracovního prostoru v pravém horním rohu obrazovky. Poté ** \<klikněte na název\> pracovního prostoru Nastavení**.

    ![Nastavení asana sso](./media/asana-tutorial/tutorial_asana_09.png)

2. V okně **Nastavení organizace** klepněte na položku **Správa**. Potom klepněte na tlačítko **Členové musí přihlásit přes SAML** povolit konfiguraci přihlašování. Proveďte následující kroky:

    ![Konfigurace nastavení organizace jednotného přihlášení](./media/asana-tutorial/tutorial_asana_10.png)  

    a. Do textového pole **Adresa URL přihlašovací stránky** vložte přihlašovací adresu **URL**.

    b. Klikněte pravým tlačítkem myši na certifikát stažený z webu Azure Portal a potom otevřete soubor certifikátu pomocí programu Poznámkový blok nebo upřednostňovaného textového editoru. Zkopírujte obsah mezi názvem počátečního a koncového certifikátu a vložte jej do textového pole **Certifikát X.509.**

3. Klikněte na **Uložit**. Pokud potřebujete další pomoc, přejděte na [průvodce Asana pro nastavení sazí.](https://asana.com/guide/help/premium/authentication#gl-saml)

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Asana.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Asana**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Možnost Asana**.

    ![Odkaz Asana v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-asana-test-user"></a>Vytvořit testovacího uživatele Asany

Cílem této sekce je vytvořit uživatele s názvem Britta Simon v Asana. Asana podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povoleno. Další podrobnosti najdete [zde](asana-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud potřebujete vytvořit uživatele ručně, proveďte následující kroky:**

V této části vytvoříte uživatele s názvem Britta Simon v Asana.

1. Na **Asaně**přejděte do sekce **Týmy** na levém panelu. Klikněte na tlačítko znaménko plus.

    ![Vytvoření testovacího uživatele Azure AD](./media/asana-tutorial/tutorial_asana_12.png)

2. Do textového pole zadejte e-mail uživatele, jako **je britta.simon\@contoso.com,** a pak vyberte **Pozvat**.

3. Klikněte na **Odeslat pozvánku**. Nový uživatel obdrží e-mail na svůj e-mailový účet. uživatel bude muset vytvořit a ověřit účet.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Asana na přístupovém panelu, můžete by měl být automaticky přihlášeni k Asana, u kterého nastavíte přihlašující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurace zřizování uživatelů](asana-provisioning-tutorial.md)
