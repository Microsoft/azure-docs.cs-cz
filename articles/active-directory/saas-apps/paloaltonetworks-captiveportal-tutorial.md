---
title: 'Kurz: Integrace služby Azure Active Directory s portálem palo alto networks captive | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a portálem palo alto pro vlastní spotřebu.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160163"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Kurz: Integrace služby Azure Active Directory s portálem pro vlastní spotřebu sítí Palo Alto

V tomto kurzu se dozvíte, jak integrovat Palo Alto networks Captive Portal s Azure Active Directory (Azure AD).

Při integraci portálu Palo Alto Networks Captive Portal s Azure AD získáte následující výhody:

* Ve službě Azure AD můžete řídit, kdo má přístup k palo alto sítě kaptivní portál.
* Uživatele můžete automaticky přihlašovat na portálu Palo Alto Networks Captive Portal (jednotné přihlašování) pomocí uživatelských účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění, na webu Azure Portal.

Další informace o integraci aplikací softwaru jako služby (SaaS) s Azure AD najdete [v tématu Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Chcete-li integrovat Azure AD s Palo Alto sítě captive portal, budete potřebovat následující položky:

* Předplatné služby Azure Active Directory. Pokud nemáte Azure AD, můžete získat [měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Palo Alto sítě kaptivní portál s podporou jednotného přihlašování (SSO)povoleno předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

Palo Alto Networks Captive Portal podporuje tyto scénáře:

* **Jednotné přihlašování iniciované idicí IDP**
* **Zřizování uživatelů just-in-time**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Přidat Palo Alto Networks Captive Portal z galerie

Chcete-li začít, v galerii, přidejte Palo Alto Networks Captive Portal do seznamu spravovaných aplikací SaaS:

1. Na [webu Azure Portal](https://portal.azure.com)vyberte v levé nabídce **Službu Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte **podnikové aplikace** > **Všechny aplikace**.

    ![Možnost Podnikové aplikace v nabídce](common/enterprise-applications.png)

3. Vyberte **Nová aplikace**.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Palo Alto Networks Captive Portal**. Ve výsledcích hledání vyberte **Možnost Palo Alto Networks – Portál pro vlastní zajatce**a pak vyberte **Přidat**.

     ![Palo Alto Networks - Kaptivní portál v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí portálu Palo Alto Networks Captive Portal na základě testovacího uživatele s názvem *Britta Simon*. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah mezi uživatelem služby Azure AD a stejným uživatelem v portálu Palo Alto Networks Captive Portal. 

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí portálu Palo Alto Networks Captive Portal, proveďte následující úkoly:

1. **[Konfigurace jednotného přihlášení Azure AD](#configure-azure-ad-single-sign-on)**: Povolte uživateli používat tuto funkci.
2. **[Konfigurace jednotného přihlášení portálu Palo Alto Networks Captive Portal](#configure-palo-alto-networks-captive-portal-single-sign-on)**: Konfigurace nastavení jednotného přihlášení v aplikaci.
3. **[Vytvořte uživatele testu Azure AD:](#create-an-azure-ad-test-user)** Otestujte jednotné přihlašování Azure AD s uživatelem *Britta Simon*.
4. **[Přiřaďte uživateli testu Azure AD:](#assign-the-azure-ad-test-user)** Nastavte Brittu Simonovou tak, aby používala jednotné přihlašování Azure AD.
5. **Vytvoření uživatele testovacího testu portálu pro vlastní spotřebu v palo alto sítích**: Vytvořte si protějšek uživatele *Britty Simonové* v portálu palo alto sítě pro vlastní spotřebu, který je propojený s uživatelem Azure AD.
6. **[Testování jednotného přihlášení](#test-single-sign-on)**: Ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

Nejprve povolte jednotné přihlašování Azure AD na webu Azure Portal:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Palo Alto Networks – Captive Portal** vyberte Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **podokně Vybrat metodu jednotného přihlašování** vyberte **možnost SAML**.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. V podokně **Nastavit jednotné přihlašování pomocí SAML** vyberte ikonu **Upravit** tužkou.

    ![Ikona Upravit tužkou](common/edit-urls.png)

4. V podokně **Základní konfigurace SAML** proveďte následující kroky:

    ![Palo Alto Sítě pro vlastní spotřebu Základní konfigurace saml](common/idp-intiated.png)

   1. Do **pole Identifikátor**zadejte adresu `https://<customer_firewall_host_name>/SAML20/SP`URL se vzorkem .

   2. Do **pole Adresa URL pro**odpověď `https://<customer_firewall_host_name>/SAML20/SP/ACS`zadejte adresu URL, která má vzor .

      > [!NOTE]
      > Aktualizujte zástupné hodnoty v tomto kroku skutečnými adresami URL identifikátorů a odpovědí. Chcete-li získat skutečné hodnoty, obraťte se [na Palo Alto Networks Captive Portal client team](https://support.paloaltonetworks.com/support).

5. V části **Podpisový certifikát SAML** vedle **položky Federation Metadata XML**vyberte **stáhnout**. Uložte stažený soubor do počítače.

    ![Odkaz ke stažení XML metadat federace](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Konfigurace portálu pro vlastní spotřebu palo alto pro vlastní spotřebu

Dále nastavte jednotné přihlašování v Palo Alto Networks Captive Portal:

1. V jiném okně prohlížeče se přihlaste na web Palo Alto Networks jako správce.

2. Vyberte kartu **Zařízení.**

    ![Karta Zařízení na webu Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. V nabídce vyberte **zprostředkovatele identity SAML**a pak vyberte **Importovat**.

    ![Tlačítko Importovat](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. V dialogovém okně **Import profilu serveru zprostředkovatele identity SAML proveďte** následující kroky:

    ![Konfigurace jednotného přihlašování v palo alto sítích](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. Do **pole Název profilu**zadejte název, například **AzureAD-CaptivePortal**.
    
    2. Vedle **položky Metadata zprostředkovatele identity**vyberte **procházet**. Vyberte soubor metadata.xml, který jste stáhli na webu Azure Portal.
    
    3. Vyberte **OK**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Dále vytvořte testovací ho uživatele s názvem *Britta Simon* na webu Azure:

1. Na webu Azure Portal vyberte**Možnost Uživatelé Služby** >  **Azure Active Directory** > **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. Vyberte **nový uživatel**.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. V podokně **Uživatel** proveďte následující kroky:

    ![Dialogové okno Uživatel](common/user-properties.png)

    1. V **popřípadě Název**zadejte **BrittaSimon**.
  
    2. Do **pole Uživatelské jméno**zadejte **BrittaSimon\@\<your_company_domain\>**. Například **BrittaSimon\@contoso.com**.

    3. V **části Heslo**zadejte heslo. Doporučujeme uchovávat záznamy o zadávaných heslech. Chcete-li heslo zobrazit, můžete zaškrtnout **políčko Zobrazit heslo.**

    4. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

Dále udělte přístup k portálu Palo Alto Networks Captive Portal, aby Britta Simon mohla používat jednotné přihlašování Azure:

1. Na portálu Azure vyberte **Podnikové aplikace** > **Všechny aplikace**.

    ![Podokno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací zadejte **Palo Alto Networks - Captive Portal**a vyberte aplikaci.

    ![Palo Alto Networks - Captive Portal odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele**. Potom v podokně **Přidat přiřazení** vyberte **Možnost Uživatelé a skupiny**.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V podokně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** **Brittu Simonovou**. Vyberte **Vybrat**.

6. Chcete-li přidat hodnotu role do kontrolního výrazu SAML, vyberte v podokně **role Select** příslušnou roli pro uživatele. Vyberte **Vybrat**.

7. V podokně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Vytvoření uživatele testu palo alto sítí pro kaptivní portál

Dále vytvořte uživatele jménem *Britta Simon* v Palo Alto Networks Captive Portal. Palo Alto Networks Captive Portal podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části není nutné provádět žádné úkoly. Pokud uživatel ještě neexistuje v Palo Alto Networks Captive Portal, nový je vytvořen po ověření.

> [!NOTE]
> Chcete-li vytvořit uživatele ručně, obraťte se na [tým podpory klienta palo alto sítě pro kaptivní portál](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

Palo Alto Networks Captive Portal je nainstalován za bránou firewall na virtuálním počítači se systémem Windows. Chcete-li otestovat jednotné přihlašování v portálu Palo Alto Networks Captive Portal, přihlaste se k virtuálnímu počítači windows pomocí protokolu RDP (Remote Desktop Protocol). V relaci RDP otevřete prohlížeč a přejděte na libovolný web. Otevře se adresa URL služby SSO a budete vyzváni k ověření. Po dokončení ověřování můžete přistupovat k webům.

## <a name="additional-resources"></a>Další zdroje

Další informace najdete v těchto článcích:

- [Kurzy o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Podmíněný přístup ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

