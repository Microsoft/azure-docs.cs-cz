---
title: 'Kurz: Integrace Azure Active Directory s Egnyte | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 2/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b95c3dbbe08a152ed5d9265f04a5ec989e55275b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167809"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Kurz: Integrace Azure Active Directory s Egnyte

V tomto kurzu se dozvíte, jak integrovat Egnyte s Azure Active Directory (Azure AD).
Egnyte integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Egnyte.
* Můžete povolit uživatelům být automaticky přihlášeni k Egnyte (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Egnyte, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Egnyte jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Egnyte **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-egnyte-from-the-gallery"></a>Přidání Egnyte z Galerie

Konfigurace integrace Egnyte do služby Azure AD, budete muset přidat Egnyte z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Egnyte z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Egnyte**vyberte **Egnyte** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Egnyte v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Egnyte podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Egnyte.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Egnyte, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Egnyte Single Sign-On](#configure-egnyte-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Egnyte](#create-egnyte-test-user)**  – Pokud chcete mít protějšek Britta Simon Egnyte, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Egnyte, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Egnyte** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Egnyte domény a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<companyname>.egnyte.com`

    > [!NOTE]
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Egnyte klienta](https://www.egnyte.com/corp/contact_egnyte.html) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

5. Na **nastavení Egnyte** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-egnyte-single-sign-on"></a>Konfigurace Egnyte jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Egnyte jako správce.

2. Klikněte na tlačítko **nastavení**.
   
    ![Nastavení](./media/egnyte-tutorial/ic787819.png "nastavení")

3. V nabídce klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/egnyte-tutorial/ic787820.png "nastavení")

4. Klikněte na tlačítko **konfigurace** kartu a potom klikněte na tlačítko **zabezpečení**.

    ![Zabezpečení](./media/egnyte-tutorial/ic787821.png "zabezpečení")

5. V **ověření jednotného přihlašování** části, proveďte následující kroky:

    ![Jednotné přihlašování na ověřování](./media/egnyte-tutorial/ic787822.png "jednotné přihlašování na ověřování")   
    
    a. Jako **ověření jednotného přihlašování**vyberte **SAML 2.0**.
   
    b. Jako **zprostředkovatele Identity**vyberte **AzureAD**.
   
    c. Vložit **přihlašovací adresa URL** zkopírovanými z webu Azure portal do **přihlašovací adresa URL zprostředkovatele Identity** textového pole.
   
    d. Vložit **Azure AD identifikátor** zkopírovanou z webu Azure portal do **ID entity zprostředkovatele Identity** textového pole.
      
    e. Otevřete váš certifikát base-64 kódovaných v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a a vložte ho do **certifikát poskytovatele Identity** textového pole.
   
    f. Jako **výchozí mapování uživatele**vyberte **e-mailová adresa**.
   
    g. Jako **použijte hodnotu issuer specifického pro doménu**vyberte **zakázané**.
   
    h. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon k udělení přístupu k Egnyte použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Egnyte**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Egnyte**.

    ![Odkaz Egnyte v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-egnyte-test-user"></a>Vytvoření Egnyte testovacího uživatele

Přihlaste se k Egnyte Azure AD uživatelům umožnit, musí být poskytnuty do Egnyte. V případě Egnyte zřizování se ruční úlohy.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se k vaší **Egnyte** společnosti serveru jako správce.

2. Přejděte na **nastavení \> uživatelů a skupin**.

3. Klikněte na tlačítko **Add New User**a pak vyberte typ uživatele, které chcete přidat.
   
    ![Uživatelé](./media/egnyte-tutorial/ic787824.png "uživatelů")

4. V **nové skupiny Power Users** části, proveďte následující kroky:
    
    ![Nového standardního uživatele](./media/egnyte-tutorial/ic787825.png "nového standardního uživatele")   

    a. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je **Brittasimon@contoso.com**.

    b. V **uživatelské jméno** textové pole, zadejte uživatelské jméno uživatele, jako je **Brittasimon**.

    c. Vyberte **jednotného přihlašování** jako **typ ověřování**.
   
    d. Klikněte na **Uložit**.
    
    >[!NOTE]
    >Držitel účtu Azure Active Directory obdrží e-mailové oznámení.
    >

>[!NOTE]
>Můžete použít jakékoli jiné Egnyte uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Egnyte uživatelským účtům, zřídit AAD.
>

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Egnyte na přístupovém panelu, můžete by měl být automaticky přihlášeni k Egnyte, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

