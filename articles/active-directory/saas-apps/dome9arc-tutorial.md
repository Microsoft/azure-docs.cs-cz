---
title: 'Kurz: Integrace Azure Active Directory s Dome9 oblouk | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Dome9 oblouk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: jeedes
ms.openlocfilehash: d10893989bd2ace8d30397123a4339a653024d35
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003648"
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Kurz: Integrace Azure Active Directory s Dome9 oblouk

V tomto kurzu se dozvíte, jak integrovat Dome9 oblouk s Azure Active Directory (Azure AD).
Integrace Dome9 oblouk s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Dome9 oblouk.
* Uživatelům se automaticky přihlášeni na oblouk Dome9 (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Dome9 Arc, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Dome9 oblouk jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Dome9 oblouk **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-dome9-arc-from-the-gallery"></a>Přidání Dome9 oblouk z Galerie

Konfigurace integrace Dome9 oblouku do služby Azure AD, budete muset přidat Dome9 oblouk na váš seznam spravovaných aplikací SaaS z galerie.

**Přidání Dome9 oblouk z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Dome9 oblouk**vyberte **Dome9 oblouk** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Dome9 oblouk v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Dome9 oblouk na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Dome9 oblouk.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Dome9 Arc, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Dome9 oblouk Single Sign-On](#configure-dome9-arc-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření oblouku Dome9 testovacího uživatele](#create-dome9-arc-test-user)**  – Pokud chcete mít protějšek Britta Simon Dome9 Arc, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Dome9 Arc, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Dome9 oblouk** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Dome9 oblouk domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://secure.dome9.com/`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Hodnota názvu vaší společnosti vybere v dome9 portál pro správu, který je vysvětlen později v tomto kurzu.

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Dome9 oblouk domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL adresy URL odpovědi a přihlašování. Kontakt [tým podpory Dome9 oblouk klienta](mailto:support@dome9.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Dome9 oblouk aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

7. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky: 

    | Název |  Zdrojový atribut|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

9. Na **nastavení Dome9 oblouk** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-dome9-arc-single-sign-on"></a>Konfigurace Dome9 oblouk jednotného přihlašování

1. V okně jiný webový prohlížeč přihlaste jako správce serveru vaší společnosti Dome9 oblouk.

2. Klikněte na **nastavení profilu** v pravém horním rohu a pak klikněte na tlačítko **nastavení účtu**. 

    ![Konfigurace Dome9 oblouk](./media/dome9arc-tutorial/configure1.png)

3. Přejděte do **jednotného přihlašování** a potom klikněte na tlačítko **povolit**.

    ![Konfigurace Dome9 oblouk](./media/dome9arc-tutorial/configure2.png)

4. V části Konfigurace jednotného přihlašování proveďte následující kroky:

    ![Konfigurace Dome9 oblouk](./media/dome9arc-tutorial/configure3.png)

    a. Zadejte název společnosti **ID účtu** textového pole. Tato hodnota se použije v odpovědi, adresy url uvedené v části Adresa URL Azure portal.

    b. V **vystavitele** textového pole vložte hodnotu **Azure Ad identifikátor**, který jste zkopírovali z webu Azure Portal.

    c. V **adresu url koncového bodu Idp** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z webu Azure Portal.

    d. Otevřete váš stažený certifikát kódovaný v Base64 v poznámkovém bloku, zkopírujte obsah ho do schránky a a vložte ho do **certifikát X.509** textového pole.

    e. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu na oblouk Dome9.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Dome9 oblouk**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Dome9 oblouk**.

    ![Odkaz Dome9 oblouk v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-dome9-arc-test-user"></a>Vytvoření oblouku Dome9 testovacího uživatele

Pokud chcete povolit Azure AD uživatelům přihlášení na oblouk Dome9, musí být poskytnuty do aplikace. Dome9 oblouk podporuje just-in-time zřizování, ale to fungovalo správně, uživatel muset vybrat konkrétní **Role** a přiřaďte jiný uživatel.

   >[!Note]
   >Pro **Role** vytváření a další podrobnosti o kontaktu [tým podpory Dome9 oblouk klienta](https://dome9.com/about/contact-us/).

**Zřídit účet uživatele ručně, proveďte následující kroky:**

1. Přihlaste se na web společnosti Dome9 oblouk jako správce.

2. Klikněte na **uživatelů a rolí** a potom klikněte na tlačítko **uživatelé**.

    ![Přidat zaměstnance](./media/dome9arc-tutorial/user1.png)

3. Klikněte na tlačítko **přidat uživatele**.

    ![Přidat zaměstnance](./media/dome9arc-tutorial/user2.png)

4. V **vytvořit uživatele** části, proveďte následující kroky:

    ![Přidat zaměstnance](./media/dome9arc-tutorial/user3.png)

    a. V **e-mailu** , jako je textové pole, typ e-mailu uživatele Brittasimon@contoso.com.

    b. V **křestní jméno** textového pole zadejte jméno uživatele, jako je Britta.

    c. V **příjmení** textového pole zadejte příjmení uživatele, jako je Simon.

    d. Ujistěte se, **jednotného přihlašování uživatele** jako **na**.

    e. Klikněte na tlačítko **vytvořit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Dome9 oblouk na přístupovém panelu, vám by měl být automaticky přihlášeni na oblouk Dome9, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

