---
title: 'Kurz: Integrace Azure Active Directory s iPass SmartConnect | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 9417d7b957d69dc802ecb2f9f78723eb7aba08ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099856"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Kurz: Integrace Azure Active Directory s iPass SmartConnect

V tomto kurzu se dozvíte, jak integrovat iPass SmartConnect s Azure Active Directory (Azure AD).
IPass SmartConnect integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k iPass SmartConnect.
* Můžete povolit uživatelům být automaticky přihlášeni k iPass SmartConnect (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s iPass SmartConnect, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* iPass SmartConnect jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* iPass SmartConnect podporuje **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí
* iPass SmartConnect podporuje **JIT** zřizování uživatelů

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Přidání iPass SmartConnect z Galerie

Konfigurace integrace iPass SmartConnect do služby Azure AD, budete muset přidat iPass SmartConnect z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat iPass SmartConnect z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **iPass SmartConnect**vyberte **iPass SmartConnect** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![iPass SmartConnect v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfiguraci a testování Azure AD jednotné přihlašování s iPass SmartConnect podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci vztah odkazu mezi uživatele služby Azure AD a související uživatelské v iPass SmartConnect potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s iPass SmartConnect, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace iPass SmartConnect Single Sign-On](#configure-ipass-smartconnect-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření iPass SmartConnect testovacího uživatele](#create-ipass-smartconnect-test-user)**  – Pokud chcete mít protějšek Britta Simon v iPass SmartConnect, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s iPass SmartConnect, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **iPass SmartConnect** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** oddílu, uživatel nebude muset provést libovolný krok, protože aplikace je už předem integrováno s Azure.

    ![iPass SmartConnect domény a adresy URL jednotného přihlašování – informace](common/preintegrated.png)

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![iPass SmartConnect domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL:  `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

6. iPass SmartConnect aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

7. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název |  Zdrojový atribut|
    | ---------------| ----------|
    | Jméno | user.givenname |
    | Příjmení | user.surname |
    | email | user.userprincipalname |
    | username jméno | user.userprincipalname |
    | | |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

9. Na **nastavení iPass SmartConnect** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-ipass-smartconnect-single-sign-on"></a>Konfigurace iPass SmartConnect jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **iPass SmartConnect** straně, je nutné odeslat na stažený **kód XML metadat federace** a vhodné zkopírovaný adresy URL z webu Azure portal [iPass SmartConnect tým podpory](mailto:help@ipass.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k iPass SmartConnect.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **iPass SmartConnect**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **iPass SmartConnect**.

    ![IPass SmartConnect odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-ipass-smartconnect-test-user"></a>Vytvoření iPass SmartConnect testovacího uživatele

V této části vytvoříte uživatele v iPass SmartConnect jako Britta Simon. Práce s [tým podpory iPass SmartConnect](mailto:help@ipass.com) přidat uživatele nebo doménu, která musí být přidaný do seznamu povolených položek pro platformu SmartConnect iPass. Pokud je doména přidána týmem, bude získat uživatelé automaticky přiřazeni k platformě SmartConnect iPass. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

**K otestování aplikace ve službě flow iniciované SP, proveďte následující kroky:**

a. Stáhněte si windows iPass SmartConnect klienta [tady](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![IPass SmartConnect odkaz v seznamu aplikací](./media/ipasssmartconnect-tutorial/testing3.png)

b. Instalace klienta a spuštění.

c. Klikněte na **Začínáme**.

![IPass SmartConnect odkaz v seznamu aplikací](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Zadejte jméno uživatele Azure s doménou. Klikněte na **pokračovat**. Budete přesměrováni na stránku pro přihlášení k Azure

![IPass SmartConnect odkaz v seznamu aplikací](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Po úspěšném ověření se spustí aktivace klienta. Klient bude aktivovat.

**K otestování aplikace ve službě flow zahájené pomocí IdP, proveďte následující kroky:**

a. Přihlaste se k [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

b. Klikněte na iPass SmartConnect aplikace.

c. Spustí SSA stránky, klikněte na **stáhnout aplikaci pro Windows** instalace iPass SmartConnect klienta.

![IPass SmartConnect odkaz v seznamu aplikací](./media/ipasssmartconnect-tutorial/testing4.png)

d. Po instalaci klienta při prvním spuštění se automaticky spustí aktivaci po přijetí podmínek a ujednání.

e. Pokud se aktivace nepodaří spustit, klikněte na tlačítko aktivovat na stránce SSA zahájí aktivaci.

f. Klient bude aktivovat.

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)