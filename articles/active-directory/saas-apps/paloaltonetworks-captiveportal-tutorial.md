---
title: 'Kurz: Integrace Azure Active Directory s Palo Alto Networks - vnitropodnikové portálu | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Palo Alto Networks - vnitropodnikové portálu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: eff08cc17f475e2b6ad6406e463de27371bbe5b1
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064725"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>Kurz: Integrace Azure Active Directory s Palo Alto Networks - vnitropodnikové portálu

V tomto kurzu se dozvíte, jak integrovat Palo Alto Networks - vnitropodnikové portálu se službou Azure Active Directory (Azure AD).
Integrace Palo Alto Networks - vnitropodnikové portál služby Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Palo Alto Networks - vnitropodnikové portálu.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Palo Alto Networks - vnitropodnikové portálu (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Palo Alto Networks - vnitropodnikové Portal, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Palo Alto sítě – portál vnitropodnikové jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Palo Alto Networks - vnitropodnikové portál podporuje **IDP** jednotné přihlašování zahájené pomocí

* Palo Alto Networks - vnitropodnikové portál podporuje **JIT** zřizování uživatelů

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>Přidání Palo Alto Networks - vnitropodnikové portálu z Galerie

Konfigurace integrace Palo Alto Networks - vnitropodnikové Portal do služby Azure AD, budete muset přidat Palo Alto Networks - vnitropodnikové portál z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Palo Alto Networks - vnitropodnikové portál z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Palo Alto Networks - vnitropodnikové portál**vyberte **Palo Alto Networks - vnitropodnikové portál** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace .

     ![Palo Alto Networks - vnitropodnikové portálu v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, nakonfigurovat a otestovat Azure AD jednotné přihlašování s Palo Alto Networks – vnitropodnikové portál na základě testu uživatelem volá **Britta Simon**.
Pro jednotné přihlašování k práci, vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Palo Alto Networks – je potřeba navázat vnitropodnikové portálu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Palo Alto Networks - vnitropodnikové portálu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Palo Alto Networks - vnitropodnikové portál Single Sign-On](#configure-palo-alto-networks---captive-portal-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytváření sítí Palo Alto - vnitropodnikové portál testovacího uživatele](#create-palo-alto-networks---captive-portal-test-user)**  – Pokud chcete mít protějšek Britta Simon Palo Alto Networks - vnitropodnikové portál, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Palo Alto Networks - vnitropodnikové portál, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Palo Alto Networks - vnitropodnikové portál** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** dialogového okna, proveďte následující kroky:

    ![Palo Alto sítě - vnitropodnikové portál domény a adresy URL jednotné přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<Customer Firewall Hostname>/SAML20/SP`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<Customer Firewall Hostname>/SAML20/SP/ACS`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [Palo Alto Networks - tým podpory vnitropodnikové klienta portálu](https://support.paloaltonetworks.com/support) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-palo-alto-networks---captive-portal-single-sign-on"></a>Konfigurace Palo Alto Networks - vnitropodnikové portálu jednotného přihlašování

1. Jako správce v jiném okně prohlížeče otevřete web Palo Alto.

2. Klikněte na **zařízení**.

    ![Konfigurace Palo Alto jednotného přihlašování](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Vyberte **zprostředkovatele Identity SAML** na levém navigačním panelu a klikněte na tlačítko "Import" k importu souboru metadat.

    ![Konfigurace Palo Alto jednotného přihlašování](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Proveďte následující akce v okně Import

    ![Konfigurace Palo Alto jednotného přihlašování](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. V **název profilu** textového pole zadejte například název uživatelské rozhraní správce Azure AD.
    
    b. V **metadat zprostředkovatele Identity**, klikněte na tlačítko **Procházet** a vyberte soubor metadata.xml, který jste si stáhli z webu Azure portal
    
    c. Klikněte na tlačítko **OK**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Palo Alto Networks - vnitropodnikové portálu.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Palo Alto Networks - vnitropodnikové portál**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Palo Alto Networks - vnitropodnikové portál**.

    ![Palo Alto sítě – portál vnitropodnikové odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-palo-alto-networks---captive-portal-test-user"></a>Vytváření sítí Palo Alto - vnitropodnikové portál testovacího uživatele

V této části se vytvoří uživateli Britta Simon v Palo Alto Networks - vnitropodnikové portálu. Palo Alto Networks - vnitropodnikové portál podporuje **zřizování uživatelů just-in-time**, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi Palo Alto Networks - vnitropodnikové portálu, vytvoří se nový po ověření.

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [Palo Alto Networks - tým podpory vnitropodnikové klienta portálu](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

Vnitropodnikové portál je nakonfigurován za službou Brána firewall na virtuálním počítači Windows. Otestovat jednotné přihlašování na portálu pro další zpracování, přihlášení na virtuálním počítači Windows pomocí protokolu RDP. Z v rámci relace protokolu RDP, otevřete prohlížeč na libovolný web, by měl automaticky otevře adresu url jednotného přihlašování a výzva k ověření. Po dokončení ověřování byste měli moct navgiate k webovým stránkám.

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

