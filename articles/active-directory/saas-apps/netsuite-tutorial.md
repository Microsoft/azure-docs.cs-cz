---
title: 'Kurz: Integrace Azure Active Directory se službou NetSuite | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: jeedes
ms.openlocfilehash: 511fdcf587d16a59ff2bb11dfc55504b2218a569
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431401"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Kurz: Integrace Azure Active Directory se službou NetSuite

V tomto kurzu se dozvíte, jak integrovat NetSuite s Azure Active Directory (Azure AD).

NetSuite integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k NetSuite
- Můžete povolit uživatelům, aby automaticky získat přihlášení k NetSuite (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s NetSuite, potřebujete následující položky:

- S předplatným služby Azure AD
- NetSuite jediného přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání NetSuite z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-netsuite-from-the-gallery"></a>Přidání NetSuite z Galerie
Konfigurace integrace NetSuite do služby Azure AD, budete muset přidat NetSuite z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat NetSuite z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]

1. Klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **NetSuite**vyberte **NetSuite** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![NetSuite v seznamu výsledků](./media/netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s NetSuite podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v NetSuite je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v NetSuite je potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v NetSuite.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s NetSuite, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele NetSuite](#creating-a-netsuite-test-user)**  – Pokud chcete mít protějšek Britta Simon NetSuite, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci NetSuite.

**Ke konfiguraci Azure AD jednotné přihlašování s NetSuite, proveďte následující kroky:**

1. Na webu Azure Portal na **NetSuite** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/tutorial_NetSuite_samlbase.png)

1. Na **NetSuite domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/tutorial_NetSuite_url.png)

    V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`
    
    > [!NOTE]
    > Ty nejsou skutečné hodnoty. Tyto hodnoty aktualizujte pomocí příkazu skutečnou adresu URL odpovědi. Kontakt [tým podpory NetSuite](http://www.NetSuite.com/portal/services/support.shtml) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/tutorial_NetSuite_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/tutorial_general_400.png)

1. Na **NetSuite konfigurace** klikněte na tlačítko **nakonfigurovat NetSuite** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/tutorial_NetSuite_configure.png)

1. Otevření nové záložky v prohlížeči a podepsat vaše společnost web NetSuite jako správce.

1. Na panelu nástrojů v horní části stránky klikněte na tlačítko **nastavení**, přejděte na **společnosti** a klikněte na tlačítko **povolit funkce**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setupsaml.png)

1. Na panelu nástrojů na druhé stránce klikněte na tlačítko **SuiteCloud**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-suitecloud.png)

1. V části **spravovat ověřování** vyberte **SAML jednotné přihlašování** možnost SAML jednotné přihlašování v NetSuite povolit.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-ticksaml.png)

1. Na panelu nástrojů v horní části stránky klikněte na tlačítko **nastavení**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setup.png)

1. Z **ÚLOH její úvodního nastavení** klikněte na možnost **integrace**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-integration.png)

1. V **SPRAVOVAT ověřování** klikněte na tlačítko **SAML jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-saml.png)

1. Na **nastavení SAML** stránce v části **NetSuite konfigurace** části proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Vyberte **primární metoda ověřování**.

    b. Pro pole s popiskem **METADAT zprostředkovatele IDENTITY SAMLV2**vyberte **NAHRÁT soubor METADAT zprostředkovatele identity**. Pak klikněte na tlačítko **Procházet** nahrát soubor metadat, který jste si stáhli z webu Azure portal.

    c. Klikněte na tlačítko **odeslat**.

1. Ve službě Azure AD, klikněte na **zobrazit a upravit všechny ostatní atributy uživatele** zaškrtávací políčko a přidáním atributu.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-attributes.png)

1. Pro **název atributu** pole, zadejte v `account`. Pro **hodnota atributu** pole, zadejte své ID účtu NetSuite. Tato hodnota je konstantní a změně účtu. Pokyny o tom, jak najít ID vašeho účtu jsou uvedeny níže:

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-add-attribute.png)

    a. V NetSuite, klikněte na tlačítko **nastavení** přejděte na **společnosti** a klikněte na tlačítko **informace o společnosti** v horní navigační nabídce.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-com.png)

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-account-id.png)

    b. V **informace o společnosti** stránky v pravém sloupci kopii **ID účtu**.

    c. Vložit **ID účtu** který jste zkopírovali z účtu NetSuite je do **hodnota atributu** pole ve službě Azure AD. 

1. Předtím, než uživatel může provádět jednotné přihlašování do NetSuite, je třeba nejprve je přiřadit v NetSuite příslušná oprávnění. Postupujte podle pokynů níže toto přiřazení oprávnění.

    a. V horní navigační nabídce klikněte na tlačítko **nastavení**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setup.png)

    b. V levé navigační nabídce vyberte **uživatele/role**, pak klikněte na tlačítko **spravovat role**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Klikněte na tlačítko **novou roli**.

    d. Zadejte **název** pro novou roli.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-new-role.png)

    e. Klikněte na **Uložit**.

    f. V nabídce v horní části klikněte na tlačítko **oprávnění**. Pak klikněte na tlačítko **nastavení**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-sso.png)

    g. Vyberte **SAML jednotného přihlašování**a potom klikněte na tlačítko **přidat**.

    h. Klikněte na **Uložit**.

    i. V horní navigační nabídce klikněte na tlačítko **nastavení**, pak klikněte na tlačítko **Správce instalace**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setup.png)

    j. V levé navigační nabídce vyberte **uživatele/role**, pak klikněte na tlačítko **spravovat uživatele**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Vyberte testovací uživatel. Pak klikněte na tlačítko **upravit** a přejděte na **přístup** kartu.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-edit-user.png)

    l. V dialogovém okně role přiřaďte odpovídající roli, kterou jste vytvořili.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-add-role.png)

    m. Klikněte na **Uložit**.
 
### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/NetSuite-tutorial/create_aaduser_01.png) 

1.  Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/NetSuite-tutorial/create_aaduser_02.png) 

1. V horní části dialogového okna, klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/NetSuite-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/NetSuite-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**. 

### <a name="creating-a-netsuite-test-user"></a>Vytvoření zkušebního uživatele NetSuite

V této části se vytvoří uživateli Britta Simon v NetSuite. NetSuite podporuje just-in-time zřizování, který je ve výchozím nastavení povolené.
Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi NetSuite, vytvoří se při pokusu o přístup k NetSuite nový.


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k NetSuite použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit NetSuite Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **NetSuite**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/tutorial_NetSuite_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Otestovat jednotné přihlašování – nastavení, otevřete na přístupovém panelu na [ https://myapps.microsoft.com ](https://myapps.microsoft.com/), přihlaste se do testovací účet a klikněte na tlačítko **NetSuite**.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](NetSuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/NetSuite-tutorial/tutorial_general_01.png
[2]: ./media/NetSuite-tutorial/tutorial_general_02.png
[3]: ./media/NetSuite-tutorial/tutorial_general_03.png
[4]: ./media/NetSuite-tutorial/tutorial_general_04.png

[100]: ./media/NetSuite-tutorial/tutorial_general_100.png

[200]: ./media/NetSuite-tutorial/tutorial_general_200.png
[201]: ./media/NetSuite-tutorial/tutorial_general_201.png
[202]: ./media/NetSuite-tutorial/tutorial_general_202.png
[203]: ./media/NetSuite-tutorial/tutorial_general_203.png

