---
title: 'Kurz: Integrace Azure Active Directory s BitaBIZ | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a BitaBIZ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: 3a338e882a909bca01baa461e0609f23e308b08d
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000928"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Kurz: Integrace Azure Active Directory s BitaBIZ

V tomto kurzu se dozvíte, jak integrovat BitaBIZ s Azure Active Directory (Azure AD).
BitaBIZ integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k BitaBIZ.
* Můžete povolit uživatelům být automaticky přihlášeni k BitaBIZ (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s BitaBIZ, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* BitaBIZ jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje BitaBIZ **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-bitabiz-from-the-gallery"></a>Přidání BitaBIZ z Galerie

Konfigurace integrace BitaBIZ do služby Azure AD, budete muset přidat BitaBIZ z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat BitaBIZ z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **BitaBIZ**vyberte **BitaBIZ** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![BitaBIZ v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí BitaBIZ podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v BitaBIZ.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s BitaBIZ, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace BitaBIZ Single Sign-On](#configure-bitabiz-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele BitaBIZ](#create-bitabiz-test-user)**  – Pokud chcete mít protějšek Britta Simon BitaBIZ, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s BitaBIZ, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **BitaBIZ** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, postupujte následovně:

    ![BitaBIZ domény a adresy URL jednotného přihlašování – informace](common/idp-identifier.png)

    V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://www.bitabiz.com/<instanceId>`

    > [!NOTE]
    > Hodnota v výše zobrazenou adresu URL je pouze ukázku. Aktualizujte hodnotu s skutečné identifikátor, který je vysvětlen později v tomto kurzu.

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![image](common/both-preintegrated-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL:  `https://www.bitabiz.com/dashboard`

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. Na **nastavení BitaBIZ** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-bitabiz-single-sign-on"></a>Konfigurace BitaBIZ jednotné přihlašování

1. V okně prohlížeče jiných webových přihlašování k vašemu tenantovi BitaBIZ jako správce.

2. Klikněte na **nastavení správce**.

    ![Konfigurace BitaBIZ](./media/bitabiz-tutorial/settings1.png)

3. Klikněte na **integrace Microsoft** pod **vyšší zhodnocení** oddílu.

    ![Konfigurace BitaBIZ](./media/bitabiz-tutorial/settings2.png)

4. Přejděte dolů do části **Microsoft Azure AD (povolit jednotné přihlašování)** a proveďte následující kroky:

    ![Konfigurace BitaBIZ](./media/bitabiz-tutorial/settings3.png)

    a. Zkopírujte hodnotu z **ID Entity ("identifikátor" ve službě Azure AD)** textového pole a vložte ho do **identifikátor** textového pole na **základní konfiguraci SAML** části webu Azure Portal . 

    b. V **Azure AD jednotné přihlašování – adresa URL služby** vložit do textového pole **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    c. V **ID Entity Azure AD SAML** vložit do textového pole **Azure Ad identifikátor**, který jste zkopírovali z portálu Azure portal.

    d. Otevřete váš stažené **Certificate(Base64)** v poznámkovém bloku soubor, zkopírujte obsah ho do schránky a vložte ho do **Azure AD podpisový certifikát (základ64 kódovaný)** textového pole.

    e. Přidání e-mailové domény firmy název tedy mycompany.com v **název domény** textové pole, které chcete přiřadit uživatelům ve vaší společnosti pomocí tohoto e-mailovou doménu jednotné přihlašování (nepovinné).

    f. Označit **povoleno jednotné přihlašování** BitaBIZ účtu.

    g. Klikněte na tlačítko **uložit konfiguraci Azure AD** uložit a aktivovat Konfigurace jednotného přihlašování.

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

V této části je povolit Britta Simon k udělení přístupu k BitaBIZ použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **BitaBIZ**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **BitaBIZ**.

    ![Odkaz BitaBIZ v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-bitabiz-test-user"></a>Vytvoření BitaBIZ testovacího uživatele

Přihlaste se k BitaBIZ Azure AD uživatelům umožnit, musí být poskytnuty do BitaBIZ.  
V případě BitaBIZ zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se na web společnosti BitaBIZ jako správce.

2. Klikněte na **nastavení správce**.

    ![BitaBIZ přidat uživatele](./media/bitabiz-tutorial/settings1.png)

3. Klikněte na **přidat uživatele** pod **organizace** oddílu.

    ![BitaBIZ přidat uživatele](./media/bitabiz-tutorial/user1.png)

4. Klikněte na tlačítko **přidat nového zaměstnance**.

    ![BitaBIZ přidat uživatele](./media/bitabiz-tutorial/user2.png)

5. Na **přidat nového zaměstnance** dialogového okna stránky, proveďte následující kroky:

    ![BitaBIZ přidat uživatele](./media/bitabiz-tutorial/user3.png)

    a. V **křestní jméno** textového pole Název typu prvního uživatele, jako je Britta.

    b. V **příjmení** textového pole zadejte příjmení uživatele, jako je Simon.

    c. V **e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.

    d. Vyberte datum v **datum zaměstnání**.

    e. Existují ostatní atributy uživatele nepovinné, které lze nastavit pro daného uživatele. Najdete [zaměstnance nastavení Doc](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) další podrobnosti.

    f. Klikněte na tlačítko **uložit zaměstnance**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mailu a řídí se odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici BitaBIZ na přístupovém panelu, můžete by měl být automaticky přihlášeni k BitaBIZ, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)