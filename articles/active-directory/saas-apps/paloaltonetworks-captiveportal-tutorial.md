---
title: 'Kurz: Integrace Azure Active Directory pomocí portálu vnitropodnikové sítě Palo Alto | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a portálu vnitropodnikové Palo Alto sítě.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 1888ac8098773d6110e5510a3dd6c0ca1220f5d7
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892486"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Kurz: Integrace Azure Active Directory pomocí portálu vnitropodnikové Palo Alto sítě

V tomto kurzu se dozvíte, jak integrovat Palo Alto sítě vnitropodnikové portálu Azure Active Directory (Azure AD).

Při integraci portálu vnitropodnikové Palo Alto sítě s využitím Azure AD, získáte následující výhody:

* Ve službě Azure AD můžete řídit, kdo má přístup k portálu vnitropodnikové Palo Alto sítě.
* Uživatelé portálu Palo Alto sítě vnitropodnikové (jednotné přihlašování) můžete automaticky přihlásit pomocí účtů uživatelů Azure AD.
* Můžete spravovat své účty v jednom, centrálním místě na webu Azure portal.

Další informace o softwaru jako integraci služby (SaaS) aplikací s Azure AD, najdete v článku [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Integrovat Azure AD Portal vnitropodnikové Palo Alto sítě, potřebujete následující položky:

* Předplatné Azure Active Directory. Pokud nemáte Azure AD, můžete získat [zkušební verze na jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).
* Portálu vnitropodnikové sítě Palo Alto jednotné přihlašování (SSO)-povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

Palo Alto sítě vnitropodnikové portál podporuje tyto scénáře:

* **Zahájené pomocí IDP jednotného přihlašování**
* **Zřizování uživatelů just-in-time**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Přidejte portál vnitropodnikové Palo Alto sítě z Galerie

Pokud chcete začít, v galerii, přidejte si na seznam spravovaných aplikací SaaS Palo Alto sítě vnitropodnikové portálu:

1. V [webu Azure portal](https://portal.azure.com), v nabídce vlevo vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte **podnikové aplikace** > **všechny aplikace**.

    ![Možnost podnikových aplikací v nabídce](common/enterprise-applications.png)

3. Vyberte **Nová aplikace**.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Palo Alto sítě vnitropodnikové portál**. Ve výsledcích hledání vyberte **Palo Alto Networks - vnitropodnikové portál**a pak vyberte **přidat**.

     ![Palo Alto Networks - vnitropodnikové portálu v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a testování Azure AD jednotné přihlašování pomocí portálu vnitropodnikové Palo Alto sítě podle testovacího uživatele s názvem *Britta Simon*. Pro jednotné přihlašování pro práci je potřeba vytvořit vztah mezi uživatele služby Azure AD a stejného uživatele portálu Palo Alto sítě další zpracování. 

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí portálu vnitropodnikové Palo Alto sítě, proveďte následující úkoly:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**: Povolte uživateli tuto funkci používat.
2. **[Konfigurace portálu vnitropodnikové sítě Palo Alto jednotného přihlašování](#configure-palo-alto-networks-captive-portal-single-sign-on)**: Konfigurovat nastavení jednotného přihlašování v aplikaci.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**: Testování Azure AD jednotné přihlašování s uživatelem *Britta Simon*.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**: Nastavení Britta Simon pro použití služby Azure AD jednotného přihlašování.
5. **Vytvoření zkušebního uživatele Palo Alto sítě vnitropodnikové portál**: Vytvoření uživatele protějšek *Britta Simon* portálu Palo Alto sítě další zpracování, který je propojený s uživatele Azure AD.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**: Zkontrolujte, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

Nejprve povolte Azure AD jednotného přihlašování na portálu Azure portal:

1. V [webu Azure portal](https://portal.azure.com/)na **Palo Alto Networks - vnitropodnikové portál** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** podokně, vyberte **SAML**.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. V **nastavte si jednotné přihlašování pomocí SAML** podokně, vyberte tužku **upravit** ikonu.

    ![Penciled ikona pro úpravy](common/edit-urls.png)

4. V **základní konfiguraci SAML** podokno, proveďte následující kroky:

    ![Podokno Palo Alto sítě vnitropodnikové základní SAML konfigurace portálu](common/idp-intiated.png)

    1. Pro **identifikátor**, zadejte adresu URL ve tvaru `https://<customer_firewall_host_name>/SAML20/SP`.

    2. Pro **adresy URL odpovědi**, zadejte adresu URL ve tvaru `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

    > [!NOTE]
    > Aktualizujte hodnoty zástupných symbolů v tomto kroku s identifikátorem skutečné a adresy URL odpovědí. Abyste získali skutečné hodnoty, kontaktujte [tým podpory Palo Alto sítě vnitropodnikové portál klienta](https://support.paloaltonetworks.com/support).

5. V **podpisový certifikát SAML** části vedle **kód XML metadat federace**vyberte **Stáhnout**. Uložte stažený soubor ve vašem počítači.

    ![Kód XML metadat federace odkaz ke stažení](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Konfigurace portálu vnitropodnikové sítě Palo Alto jednotného přihlašování

Potom si nastavte jednotného přihlašování na portálu vnitropodnikové Palo Alto sítě:

1. V jiném okně prohlížeče Přihlaste se k webu Palo Alto Networks jako správce.

2. Vyberte **zařízení** kartu.

    ![Na kartě zařízení Palo Alto Networks webu](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. V nabídce vyberte **zprostředkovatele Identity SAML**a pak vyberte **Import**.

    ![Tlačítko Import](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. V **Import profilu serveru zprostředkovatele Identity SAML** dialogové okno pole, proveďte následující kroky:

    ![Konfigurace Palo Alto Networks jednotného přihlašování](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. Pro **název profilu**, zadejte název, jako je třeba **AzureAD CaptivePortal**.
    
    2. Vedle položky **metadat zprostředkovatele Identity**vyberte **Procházet**. Na webu Azure Portal vyberte stažený soubor metadata.xml.
    
    3. Vyberte **OK**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Dále vytvořte testovacího uživatele s názvem *Britta Simon* na webu Azure Portal:

1. Na webu Azure Portal, vyberte **Azure Active Directory** > **uživatelé** > **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele**.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. V **uživatele** podokno, proveďte následující kroky:

    ![Dialogové okno uživatele](common/user-properties.png)

    1. Pro **název**, zadejte **BrittaSimon**.
  
    2. Pro **uživatelské jméno**, zadejte **BrittaSimon @\<your_company_domain\>**. Příklad: **BrittaSimon@contoso.com**.

    3. Pro **heslo**, zadejte heslo. Doporučujeme, abyste záznam o heslo, které zadáte. Můžete vybrat **zobrazit heslo** políčka zobrazíte heslo.

    4. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V dalším kroku udělte přístup k portálu vnitropodnikové Palo Alto sítí tak Britta Simon můžete použít Azure jednotné přihlašování:

1. Na webu Azure Portal, vyberte **podnikové aplikace** > **všechny aplikace**.

    ![V podokně podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace zadejte **Palo Alto Networks - vnitropodnikové portál**a pak vyberte aplikaci.

    ![Palo Alto sítě – portál vnitropodnikové odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Vyberte **přidat uživatele**. Potom v **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** podokno v **uživatelé** seznamu vyberte **Britta Simon**. Vyberte **vyberte**.

6. Přidat roli hodnotu pro kontrolní výraz SAML v **vybrat roli** podokně, vyberte odpovídající roli pro uživatele. Vyberte **vyberte**.

7. V **přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Vytvoření zkušebního uživatele Palo Alto sítě vnitropodnikové portálu

V dalším kroku vytvoření uživatele s názvem *Britta Simon* Palo Alto sítě vnitropodnikové portálu. Palo Alto sítě vnitropodnikové portál podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Není nutné k dokončení všech úkolů v této části. Pokud uživatel portálu Palo Alto sítě vnitropodnikové ještě neexistuje, vytvoří se nový po ověření.

> [!NOTE]
> Pokud chcete ručně vytvořit uživatele, obraťte se [tým podpory Palo Alto sítě vnitropodnikové portál klienta](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

Za branou firewall na virtuálním počítači s Windows je nainstalován portál vnitropodnikové Palo Alto sítě. Pokud chcete otestovat jednotné přihlašování na portálu vnitropodnikové Palo Alto sítě, přihlaste se k virtuálnímu počítači Windows pomocí protokolu RDP (Remote Desktop). V této relaci RDP otevřete prohlížeč a přejděte na libovolném webu. Otevře adresu URL jednotného přihlašování a zobrazí se výzva k ověření. Po dokončení ověření můžete přístup k webům.

## <a name="additional-resources"></a>Další materiály

Další informace najdete v těchto článcích:

- [Výukové programy o integraci aplikací SaaS se službou Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Podmíněný přístup ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

