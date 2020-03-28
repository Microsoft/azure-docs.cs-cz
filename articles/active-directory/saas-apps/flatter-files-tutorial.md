---
title: 'Kurz: Integrace služby Azure Active Directory s ploššími soubory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Ploššími soubory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 13012474e34af80c84b034703a3b34f0208036bc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73156270"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Kurz: Integrace Azure Active Directory s ploššími soubory

V tomto kurzu se dozvíte, jak integrovat plošší soubory s Azure Active Directory (Azure AD).
Integrace plošších souborů s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k plošší soubory.
* Můžete povolit uživatelům, aby se automaticky přihlásili k plošší soubory (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s plošší soubory, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Plošší soubory podporuje **IDP** iniciované sso

## <a name="adding-flatter-files-from-the-gallery"></a>Přidání plošších souborů z galerie

Chcete-li nakonfigurovat integraci plošších souborů do Azure AD, musíte přidat plošší soubory z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat plošší soubory z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Flatter Files**, z panelu výsledků vyberte **Flatter Files** a pak klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![Plošší soubory v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí plošších souborů na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v plošších souborech.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí plošších souborů, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace ploššísoubory jednotné hojné přihlášení](#configure-flatter-files-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit plošší soubory testovací ho uživatele](#create-flatter-files-test-user)** – mít protějšek Britta Simon v plošší soubory, která je propojena s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí plošších souborů, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Flatter Files** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** uživatel nemusí provádět žádný krok, protože aplikace je již předem integrovaná s Azure.

    ![Plošší soubory Domény a adresy URL jednotné přihlašovací informace](common/preintegrated.png)

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit plošší soubory** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-flatter-files-single-sign-on"></a>Konfigurace plošších souborů jednotného přihlášení

1. Přihlaste se k aplikaci Flatter Files jako správce.

2. Klepněte na **položku ŘÍDICÍ PANEL**. 
   
    ![Konfigurace jednotného přihlašování](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Klikněte na **Nastavení**a na kartě **Společnost** proveďte následující kroky: 
   
    ![Konfigurace jednotného přihlašování](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Vyberte **možnost Použít SAML 2.0 pro ověřování**.
    
    b. Klepněte na **tlačítko Konfigurovat službu SAML**.

4. V dialogovém okně **Konfigurace SAML** proveďte následující kroky: 
   
    ![Konfigurace jednotného přihlašování](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. Do textového pole **Doména** zadejte registrovanou doménu.
   
   > [!NOTE]
   > Pokud ještě nemáte registrovanou doménu, obraťte se [support@flatterfiles.com](mailto:support@flatterfiles.com)na tým podpory Ploššísoubory prostřednictvím aplikace . 
    
    b. V textovém poli **URL zprostředkovatele identity** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z webu Portálu Azure.
   
    c.  Otevřete svůj certifikát kódovaný base-64 v poznámkovém bloku, zkopírujte jeho obsah do schránky a vložte jej do textového pole **Certifikát u poskytovatele identity.**

    d. Klikněte na **Aktualizovat**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k plošší soubory.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Plošší soubory**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Flatter Files**.

    ![Odkaz Plošší soubory v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-flatter-files-test-user"></a>Vytvořit uživatele testu plošších souborů

Cílem této části je vytvořit uživatele s názvem Britta Simon v Plošší soubory.

**Chcete-li vytvořit uživatele s názvem Britta Simon v plošších souborech, proveďte následující kroky:**

1. Přihlaste se k webu společnosti **Flatter Files** jako správce.

2. V navigačním podokně vlevo klikněte na **Nastavení**a potom klikněte na kartu **Uživatelé.**
   
    ![Vytvoření ploššího uživatele souborů](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Klikněte na **Add User** (Přidat uživatele). 

4. V dialogovém okně **Přidat uživatele** proveďte následující kroky:
   
    ![Vytvoření ploššího uživatele souborů](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Do textového pole **Křestní jméno** zadejte **Brittu**.
   
    b. Do textového pole **Příjmení** zadejte **Simon**. 
   
    c. Do textového pole **E-mailová adresa** zadejte e-mailovou adresu Britty na webu Azure Portal.
   
    d. Klepněte na **tlačítko Odeslat**.   


### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Plošší soubory na přístupovém panelu, můžete by měl být automaticky přihlášeni k plošší soubory, pro které nastavíte přistupující přistupující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

