---
title: 'Kurz: Integrace Azure Active Directory s HighGear | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a HighGear.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 55dcd2fb-96b7-46ec-9e69-eee71c535773
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: 5d7d52122f9d3e87bf8d0822adaa221059dc18aa
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402806"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Kurz: Integrace Azure Active Directory s HighGear

V tomto kurzu se dozvíte, jak integrovat HighGear s Azure Active Directory (Azure AD).
HighGear integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k HighGear.
* Můžete povolit uživatelům být automaticky přihlášeni k HighGear (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s HighGear, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [zde](https://azure.microsoft.com/pricing/free-trial/)
* HighGear systému s Enterprise nebo neomezený počet licencí

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu se dozvíte, jak nakonfigurovat a otestovat Azure AD jednotné přihlašování v testovacím prostředí.

* Podporuje HighGear **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-highgear-from-the-gallery"></a>Přidání HighGear z Galerie

Konfigurace integrace HighGear do služby Azure AD, budete muset přidat HighGear z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat HighGear z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **HighGear**vyberte **HighGear** z panelu výsledek a pak klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

     ![HighGear v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části můžete zjistěte, jak nakonfigurovat a otestovat Azure AD jednotné přihlašování do vašeho systému HighGear podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatel ve vašem systému HighGear.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování k HighGear systému, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace HighGear Single Sign-On](#configure-highgear-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace HighGear.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele HighGear](#create-highgear-test-user)**  – Pokud chcete mít protějšek Britta Simon HighGear, který je propojený s Azure AD zastoupení uživatele. 
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části se dozvíte, jak povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotného přihlašování k HighGear systému, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **HighGear** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na tlačítko **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![HighGear domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** text vložte hodnotu **ID Entity poskytovatele služby** pole, které je na stránce nastavení jednotného přihlašování ve vašem systému HighGear.

    ![Pole ID Entity poskytovatele služby](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > Budete potřebovat k přihlášení do systému HighGear přístup na stránku nastavení jednotného přihlašování. Jakmile jste přihlášeni, najeďte myší na kartě Správa v HighGear a kliknutím na položku nabídky nastavení jednotného přihlašování.
    
    ![Položka nabídky nastavení jednotného přihlašování](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. V **adresy URL odpovědi** text vložte hodnotu **Assertion Consumer Service (ACS) adresy URL** ze stránky nastavení jednotného přihlašování ve vašem systému HighGear.

    ![Do pole Adresa URL Assertion Consumer Service (ACS)](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

     ![HighGear domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

     V **přihlašovací adresa URL** text vložte hodnotu **ID Entity poskytovatele služby** pole, které je na stránce nastavení jednotného přihlašování ve vašem systému HighGear. (Toto ID Entity je také základní adresu URL HighGear systému, který se použije pro iniciovaného Zprostředkovatelem přihlašování přihlášení.)

    ![Pole ID Entity poskytovatele služby](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečnou identifikátor, adresa URL odpovědi a přihlašovací adresu URL z **nastavení jednotného přihlašování** stránku ve svém systému HighGear. Pokud potřebujete pomoc, obraťte se prosím [tým podpory HighGear](mailto:support@highgear.com).

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** a uložte ho do počítače. Budete je potřebovat v pozdějším kroku konfigurace jednotného přihlašování.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení HighGear** části, poznamenejte si umístění následující adresy URL.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Adresa URL pro přihlášení. Budete potřebovat v kroku č. 2 pod tuto hodnotu **konfigurace HighGear Single Sign-On** níže.

    b. Identifikátor služby Azure AD. Budete potřebovat tato hodnota v kroku 3 v části **konfigurace HighGear Single Sign-On** níže.

    c. Odhlašovací adresa URL. Budete potřebovat v kroku č. 4 pod tuto hodnotu **konfigurace HighGear Single Sign-On** níže.

### <a name="configure-highgear-single-sign-on"></a>Konfigurace HighGear jednotné přihlašování

Ke konfiguraci HighGear pro jednotné přihlašování, přihlaste se do vašeho systému HighGear. Jakmile jste přihlášeni, najeďte myší na kartě Správa v HighGear a kliknutím na položku nabídky nastavení jednotného přihlašování.

![Položka nabídky nastavení jednotného přihlašování](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. V **název zprostředkovatele identit**, zadejte stručný popis, který se zobrazí v jeho HighGear Single Sign-On tlačítko na přihlašovací stránce. Příklad: Azure AD

2. V **adresu URL pro jednotné přihlašování (SSO)** pole v HighGear, vložte hodnotu z **přihlašovací adresa URL** pole, které je v **nastavení HighGear** oddíl ve službě Azure.

3. V **ID Entity zprostředkovatele Identity** pole v HighGear, vložte hodnotu z **Azure AD identifikátor** pole, které je v **nastavení HighGear** oddíl ve službě Azure.

4. V **adresu URL jednotného odhlašování (SLO)** pole v HighGear, vložte hodnotu z **odhlašovací adresa URL** pole, které se **nastavení HighGear** oddíl ve službě Azure.

5. Pomocí poznámkového bloku otevřete certifikát, který jste stáhli ze služby **podpisový certifikát SAML** oddíl ve službě Azure. Jste si stáhli **certifikát (Base64)** formátu. Zkopírujte obsah certifikát z programu Poznámkový blok a vložte ho do **certifikát poskytovatele Identity** HighGear pole.

6. E-mailu [tým podpory HighGear](mailto:support@highgear.com) certifikát HighGear vyžádat. Postupujte podle pokynů, přijímat z nich k vyplnění **HighGear certifikát** a **heslo certifikátu HighGear** pole.

7. Klikněte na tlačítko **Uložit** tlačítko uložte konfiguraci HighGear Single Sign-On.

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

V této části je povolit Britta Simon k udělení přístupu k HighGear použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **HighGear**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **HighGear**.

    ![Odkaz HighGear v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-highgear-test-user"></a>Vytvoření HighGear testovacího uživatele

Vytvoření zkušebního uživatele HighGear testování konfigurace jednotného přihlašování, přihlaste se do vašeho systému HighGear.

1. Klikněte na tlačítko **vytvořit nový kontakt** tlačítko.

    ![Tlačítko Vytvořit nový kontakt](media/highgear-tutorial/create-new-contact-button.png)

    Nabídka se zobrazí, umožní vám vybrat typ kontakt, který chcete vytvořit.

2. Klikněte na tlačítko **jednotlivé** HighGear uživatele vytvořit položku nabídky.

    Podokno se vysunou na pravé straně, takže můžete zadat informace pro nového uživatele.  
    ![Formulář Nový kontakt](media/highgear-tutorial/new-contact-form.png)

3. V **název** zadejte jméno kontaktu. Příklad: Britta Simon

4. Klikněte na tlačítko **další možnosti** nabídky a vybereme **informace o účtu** položky nabídky.

    ![Kliknutím na položku nabídky informace o účtu](media/highgear-tutorial/account-info-menu-item.png)

5. Nastavte **můžete přihlásit** pole na Ano.

    **Povolit Single Sign-On** pole automaticky nastaven na hodnotu Ano také.

6. V **jednotné přihlašování – Id uživatele** zadejte id uživatele. Příklad: BrittaSimon@contoso.com

    V části informace o účtu by teď měl vypadat přibližně takto:  
    ![Dokončení oddíl informací o účtu](media/highgear-tutorial/finished-account-info-section.png)

7. Pokud chcete uložit kontaktu, klikněte na tlačítko **Uložit** tlačítko v dolní části podokna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici HighGear na přístupovém panelu, můžete by měl být automaticky přihlášeni k HighGear, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

