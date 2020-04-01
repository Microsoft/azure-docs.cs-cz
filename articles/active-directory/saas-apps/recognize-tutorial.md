---
title: 'Kurz: Integrace služby Azure Active Directory s rozpoznáváním | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a rozpoznat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: c0a513ad8fe772839813615d065616f2c911a031
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943338"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Kurz: Integrace služby Azure Active Directory s rozpoznáváním

V tomto kurzu se dozvíte, jak integrovat rozpoznat s Azure Active Directory (Azure AD).
Integrace rozpoznávání s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k rozpoznat.
* Můžete povolit, aby vaši uživatelé byli automaticky přihlášeni k rozpoznávání (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí funkce Rozpoznat, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Rozpoznání předplatného s povoleným přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Rozpoznat podporuje **sp** inicioval sso

## <a name="adding-recognize-from-the-gallery"></a>Přidání rozpoznávání z galerie

Chcete-li nakonfigurovat integraci rozpoznat do Azure AD, musíte přidat rozpoznat z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat rozpoznávání z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Rozpoznat**, z panelu výsledků vyberte **Rozpoznat** a pak klepnutím na **tlačítko Přidat** přidejte aplikaci.

     ![Rozpoznat v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí funkce Rozpoznat na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v aplikaci Rozpoznat.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí funkce Rozpoznat, je třeba provést následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace funkce Rozpoznat jednotné přihlašování](#configure-recognize-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit rozpoznat testovacího uživatele](#create-recognize-test-user)** – mít protějšek Britta Simon v Rozpoznat, že je propojen s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí funkce Rozpoznat, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce **Rozpoznat** integraci aplikací vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud máte **soubor metadat poskytovatele služeb**, proveďte v části Základní konfigurace **SAML** následující kroky:

    >[!NOTE]
    >Soubor metadat **poskytovatele služeb** získáte v části Konfigurovat rozpoznávání **jednotného přihlášení** v kurzu.

    a. Klikněte na **Nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klepněte na tlačítko **Nahrát**.

    ![výběr souboru metadat](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnota **identifikátoru** automaticky naplní v části Základní konfigurace SAML.

    ![Rozpoznat informace o jednotném přihlášení domény a adresy URL](common/sp-identifier.png)

     Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Pokud hodnota **identifikátoru** není automaticky naplněna, získáte hodnotu identifikátoru otevřením adresy URL metadat poskytovatele služeb z části Nastavení jednotného přihlašování, která je vysvětlena dále v části **Konfigurovat rozpoznávání jednotného přihlášení** v kurzu. Hodnota přihlašovací adresy URL není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Chcete-li získat hodnotu, obraťte se na [tým podpory klienta.](mailto:support@recognizeapp.com) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit rozpoznat** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-recognize-single-sign-on"></a>Konfigurace rozpoznávání jednotného přihlašování

1. V jiném okně webového prohlížeče se přihlaste ke svému tenantovi Rozpoznat jako správce.

2. V pravém horním rohu klikněte na **Menu**. Přejděte na **Správce společnosti**.
   
    ![Konfigurace jednotného přihlašování na straně aplikace](./media/recognize-tutorial/tutorial_recognize_000.png)

3. V levém navigačním podokně klepněte na tlačítko **Nastavení**.
   
    ![Konfigurace jednotného přihlašování na straně aplikace](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Proveďte následující kroky v části **Nastavení přistaň.**
   
    ![Konfigurace jednotného přihlašování na straně aplikace](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Při **povolit přiřazuje**přístup , vyberte **ON**.

    b. V textovém poli **ID entity ID ID ID ID ID** ID Vložte hodnotu **identifikátoru Azure AD,** který jste zkopírovali z webu Azure Portal.
    
    c. V textovém poli **cílovou adresu Url služby Sso** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z webu Azure Portal.
    
    d. V textovém poli **cílová adresa URL Slo** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z webu Azure Portal. 
    
    e. Otevřete stažený soubor **certifikátu (Base64)** v poznámkovém bloku, zkopírujte jeho obsah do schránky a vložte ho do textového pole **Certifikát.**
    
    f. Klepněte na tlačítko **Uložit nastavení.** 

5. Vedle oddílu **Nastavení přiřazování** služeb při výběru zkopírujte adresu URL v části **Url metadat poskytovatele služeb**.
   
    ![Konfigurace jednotného přihlašování na straně aplikace](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Otevřete **odkaz URL metadat** pod prázdným prohlížečem a stáhněte dokument metadat. Pak zkopírujte hodnotu EntityDescriptor (entityID) ze souboru a vložte ji do textového pole **Identifikátor** v **základní konfiguraci SAML** na webu Azure Portal.
    
    ![Konfigurace jednotného přihlašování na straně aplikace](./media/recognize-tutorial/tutorial_recognize_004.png)

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k rozpoznat.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Rozpoznat**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Rozpoznat**.

    ![Odkaz Rozpoznat v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-recognize-test-user"></a>Vytvořit testovacího uživatele rozpoznávání

Aby bylo možné povolit uživatelům Azure AD k přihlášení k rozpoznat, musí být zřízena do rozpoznat. V případě Rozpoznat zřizování je ruční úlohy.

Tato aplikace nepodporuje zřizování SCIM, ale má alternativní synchronizaci uživatelů, která zřizuje uživatele. 

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu Rozpoznat společnost jako správce.

2. V pravém horním rohu klikněte na **Menu**. Přejděte na **Správce společnosti**.

3. V levém navigačním podokně klepněte na tlačítko **Nastavení**.

4. V části **Synchronizace uživatelů** proveďte následující kroky.
   
    ![Nový uživatel](./media/recognize-tutorial/tutorial_recognize_005.png "Nový uživatel")
   
    a. Jako **povolenou synchronizaci**vyberte **možnost ZAPNUTO**.
   
    b. Jako **Zvolit poskytovatele synchronizace**vyberte **Microsoft / Office 365**.
   
    c. Klepněte na **položku Spustit synchronizaci uživatelů**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Rozpoznat na přístupovém panelu, měli byste být automaticky přihlášeni k rozpoznat, pro které nastavíte přispojené k zámesu. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

