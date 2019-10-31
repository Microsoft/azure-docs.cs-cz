---
title: 'Kurz: Azure Active Directory integrace s portálem Palo Alto Networks na více sítích | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Palo Alto Networks na portálu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f926741bde3bdcc69cb4ea30f54daca79606047e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160163"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Kurz: Integrace Azure Active Directory s Palo Alto Networks na portálu

V tomto kurzu se dozvíte, jak integrovat portál Palo Alto Networks na portálu s Azure Active Directory (Azure AD).

Při integraci Palo Alto Networks na portálu s Azure AD získáte následující výhody:

* V Azure AD můžete řídit, kdo má přístup k portálu Palo Alto Networks.
* Uživatele můžete automaticky přihlašovat v Palo Alto Networks na portálu (jednotné přihlašování) pomocí účtů uživatelů Azure AD.
* Účty můžete spravovat v jednom, centrálním umístění, Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Pokud chcete integrovat Azure AD s portálem Palo Alto Networks, budete potřebovat následující položky:

* Azure Active Directory předplatné. Pokud nemáte Azure AD, můžete získat měsíční [zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné s podporou jednotného přihlašování (SSO) Palo Alto Networks na portálu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

Palo Alto Networks s podporou portálu podporuje tyto scénáře:

* **Jednotné přihlašování iniciované IDP**
* **Zřizování uživatelů za běhu**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Přidání portálu Palo Alto Networks z Galerie

Začněte tím, že v galerii přidáte portál Palo Alto Networks do vašeho seznamu spravovaných aplikací SaaS:

1. V [Azure Portal](https://portal.azure.com)v nabídce vlevo vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte **podnikové aplikace** > **všechny aplikace**.

    ![Možnost podnikové aplikace v nabídce](common/enterprise-applications.png)

3. Vyberte **Nová aplikace**.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Palo Alto Networks na portálu**. Ve výsledcích hledání vyberte **Palo Alto Networks-dobrovolný portál**a pak vyberte **Přidat**.

     ![Palo Alto Networks – Samoobslužný portál v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Pomocí portálu Palo Alto Networks pro samoobslužné zpracování na základě testovacího uživatele s názvem *Britta Simon*nakonfigurujete a otestujete jednotné přihlašování Azure AD. Aby jednotné přihlašování fungovalo, musíte vytvořit relaci mezi uživatelem služby Azure AD a stejným uživatelem v Palo Alto Networks na portálu. 

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí portálu Palo Alto Networks, proveďte následující úlohy:

1. **[Nakonfigurovat jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** : Povolte uživateli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování Palo Alto Networks na portálu](#configure-palo-alto-networks-captive-portal-single-sign-on)** : Konfigurace nastavení jednotného přihlašování v aplikaci
3. **[Vytvoření testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** : Vyzkoušejte jednotné přihlašování Azure AD s uživatelem *Britta Simon*.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** : nastavte Britta Simon na používání jednotného přihlašování Azure AD.
5. **Vytvoření testovacího uživatele na portálu Palo Alto Networks na portálu**: Vytvořte uživatele s protějškem *Britta Simon* v Palo Alto Networks na portálu, který je propojený s uživatelem služby Azure AD.
6. **[Test jednotného přihlašování](#test-single-sign-on)** : Ověřte, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

Nejdřív povolte jednotné přihlašování Azure AD v Azure Portal:

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **portálu Palo Alto Networks – dobrovolný portál** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V podokně **Vyberte metodu jednotného přihlašování** vyberte **SAML**.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. V podokně **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu pro **Úpravy** tužky.

    ![Ikona tužky pro úpravy](common/edit-urls.png)

4. V podokně **základní konfigurace SAML** proveďte následující kroky:

    ![Palo Alto Networks – základní konfigurační podokno SAML na portálu](common/idp-intiated.png)

   1. V poli **identifikátor**zadejte adresu URL, která má `https://<customer_firewall_host_name>/SAML20/SP`vzoru.

   2. V poli **Adresa URL odpovědi**zadejte adresu URL, která má vzor `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > Aktualizujte zástupné hodnoty v tomto kroku skutečným identifikátorem a adresou URL pro odpovědi. Chcete-li získat skutečné hodnoty, obraťte se na [tým podpory klienta podpory Palo Alto Networks na portálu](https://support.paloaltonetworks.com/support).

5. V části **podpisový certifikát SAML** vedle **XML federačních metadat**vyberte **Stáhnout**. Uložte stažený soubor do počítače.

    ![Odkaz na stažení XML federačních metadat](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Konfigurace jednotného přihlašování v Palo Alto Networks na portálu

V dalším kroku nastavte jednotné přihlašování v Palo Alto Networks na samoobslužném portálu:

1. V jiném okně prohlížeče se přihlaste k webu Palo Alto Networks jako správce.

2. Vyberte kartu **zařízení** .

    ![Karta zařízení webu Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. V nabídce vyberte **zprostředkovatel identity SAML**a pak vyberte **importovat**.

    ![Tlačítko Import](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. V dialogovém okně **Importovat profil serveru zprostředkovatele identity SAML** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování sítí Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. Jako **název profilu**zadejte název, třeba **AzureAD-CaptivePortal**.
    
    2. Vedle pole **metadata zprostředkovatele identity**vyberte **Procházet**. Vyberte soubor metadata. XML, který jste stáhli v Azure Portal.
    
    3. Vyberte **OK**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Dále vytvořte testovacího uživatele s názvem *Britta Simon* v Azure Portal:

1. V Azure Portal vyberte **Azure Active Directory** > **uživatelů** > **všech uživatelích**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. Vyberte **Nový uživatel**.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. V podokně **uživatel** proveďte následující kroky:

    ![Uživatelský dialog](common/user-properties.png)

    1. Jako **název**zadejte **BrittaSimon**.
  
    2. Jako **uživatelské jméno**zadejte **BrittaSimon\@\<your_company_domain\>** . Například **BrittaSimon\@contoso.com**.

    3. Jako **heslo**zadejte heslo. Doporučujeme, abyste si zaznamenali záznam hesla, které zadáte. Pro zobrazení hesla můžete zaškrtnout políčko **Zobrazit heslo** .

    4. Vyberte **Create** (Vytvořit).

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

Dále udělte přístup k samoobslužnému portálu Palo Alto Networks, aby Britta Simon mohl používat jednotné přihlašování Azure:

1. V Azure Portal vyberte **podnikové aplikace** > **všech aplikacích**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte **Palo Alto Networks-dobrovolný portál**a pak vyberte aplikaci.

    ![Odkaz Palo Alto Networks-dobrovolný portál v seznamu aplikací](common/all-applications.png)

3. V nabídce vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele**. Pak v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V podokně **Uživatelé a skupiny** v seznamu **Uživatelé** vyberte možnost **Britta Simon**. Vyberte **Vybrat**.

6. Chcete-li do kontrolního výrazu SAML přidat hodnotu role, vyberte v podokně **Vybrat roli** příslušnou roli pro uživatele. Vyberte **Vybrat**.

7. V podokně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Vytvoření testovacího uživatele na portálu Palo Alto Networks pro nedobrovolný test

V dalším kroku vytvořte uživatele s názvem *Britta Simon* v Palo Alto Networks na portálu. Palo Alto Networks na samoobslužném portálu podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není nutné provádět žádné úkoly. Pokud uživatel ještě v Palo Alto Networks na portálu neexistuje, vytvoří se po ověření nový.

> [!NOTE]
> Pokud chcete uživatele vytvořit ručně, obraťte se na [tým podpory klienta podpory Palo Alto Networks na portálu](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

Palo Alto Networks Hosted Portal se nainstaluje za bránou firewall na virtuálním počítači s Windows. K otestování jednotného přihlašování v Palo Alto Networks Portalu se přihlaste k virtuálnímu počítači s Windows pomocí protokol RDP (Remote Desktop Protocol) (RDP). V relaci RDP otevřete prohlížeč a přejdete na libovolný web. Otevře se adresa URL jednotného přihlašování a budete vyzváni k ověření. Po dokončení ověřování máte přístup k webům.

## <a name="additional-resources"></a>Další zdroje informací:

Další informace najdete v těchto článcích:

- [Kurzy týkající se integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Podmíněný přístup v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

