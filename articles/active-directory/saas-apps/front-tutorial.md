---
title: 'Kurz: Integrace služby Azure Active Directory s frontem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Front.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 33ed2f98ba66704072e8139654dfbc4cde8ab3be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73155641"
---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Kurz: Integrace služby Azure Active Directory s frontem

V tomto kurzu se dozvíte, jak integrovat Front s Azure Active Directory (Azure AD).
Integrace fronty s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k front.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k frontě (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s front, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Přední podporuje **IDP** iniciované spoje

## <a name="adding-front-from-the-gallery"></a>Přidání fronty z galerie

Chcete-li nakonfigurovat integraci fronty do Azure AD, musíte přidat Front z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat frontu z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Přední**, z panelu výsledků vyberte **Přední** a pak klepněte na **tlačítko Přidat** a přidejte aplikaci.

     ![Přední strana v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí fronty na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v aplikaci Front.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí aplikace Front, je třeba provést následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování vpředu](#configure-front-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit front test uživatele](#create-front-test-user)** – mít protějšek Britta Simon v přední, který je propojený s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí aplikace Front, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce Integrace aplikací **front** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** proveďte následující kroky:

    ![Informace o jednotném přihlášení přední domény a adresy URL](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.frontapp.com`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.frontapp.com/sso/saml/callback`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou adresou URL identifikátoru a odpovědi. Obraťte [se na tým podpory předního klienta](mailto:support@frontapp.com) a získejte tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit vpředu** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-front-single-sign-on"></a>Konfigurace jednotného přihlašování ze předu

1. Přihlaste se k přednímu tenantovi jako správce.

2. Přejděte na **Nastavení (ikona ostrosti v dolní části levého postranního panelu) > předvolby**.
   
    ![Konfigurace jednotného přihlašování na straně aplikace](./media/front-tutorial/tutorial_front_000.png)

3. Klikněte na odkaz **Jednotné přihlášení.**
   
    ![Konfigurace jednotného přihlašování na straně aplikace](./media/front-tutorial/tutorial_front_001.png)

4. V rozevíracím seznamu **jednotného přihlášení**vyberte **možnost SAML** .
   
    ![Konfigurace jednotného přihlašování na straně aplikace](./media/front-tutorial/tutorial_front_002.png)

5. V textovém poli **Vstupní bod** zadejte hodnotu přihlašovací **adresy URL** z průvodce konfigurací aplikace Azure AD.
    
    ![Konfigurace jednotného přihlašování na straně aplikace](./media/front-tutorial/tutorial_front_003.png)

6. Otevřete stažený soubor **certifikátu (Base64)** v poznámkovém bloku, zkopírujte jeho obsah do schránky a vložte ho do textového pole **Podpisový certifikát.**
    
    ![Konfigurace jednotného přihlašování na straně aplikace](./media/front-tutorial/tutorial_front_004.png)

7. V části **Nastavení poskytovatele služeb** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/front-tutorial/tutorial_front_005.png)

    a. Zkopírujte hodnotu **ID entity** a vložte ji do textového pole **Identifikátor** v části **Přední doména a adresy URL** na webu Azure Portal.

    b. Zkopírujte hodnotu **adresy URL služby ACS** a vložte ji do textového pole **Adresa URL odpovědi** v části Přední **doména a adresy URL** na webu Azure Portal.
    
8. Klikněte na **tlačítko Uložit.**

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu front.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Front**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **přední položku**.

    ![Odkaz Přední strana v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-front-test-user"></a>Vytvořit uživatele frontového testu

V této části vytvoříte uživatele s názvem Britta Simon vpředu. Spolupracujte s [týmem podpory front klienta](mailto:support@frontapp.com) a přidejte uživatele na platformu Front. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na přední dlaždici na přístupovém panelu, měli byste být automaticky přihlášeni k frontě, pro kterou nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

