---
title: 'Kurz: Integrace Azure Active Directory se Salesforce | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 64b94baeaede9b05e953b69324648c63d97cea8e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045434"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Kurz: Integrace Azure Active Directory se Salesforce

V tomto kurzu se dozvíte, jak integrovat služby Salesforce se službou Azure Active Directory (Azure AD).

Salesforce integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Salesforce.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Salesforce (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí služby Salesforce, potřebujete následující položky:

- S předplatným služby Azure AD
- Salesforce jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Salesforce z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-salesforce-from-the-gallery"></a>Přidání Salesforce z Galerie
Pokud chcete nakonfigurovat integraci Salesforce do služby Azure AD, budete muset přidat Salesforce z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat z Galerie služby Salesforce, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Salesforce**vyberte **Salesforce** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Salesforce v seznamu výsledků](./media/salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí služby Salesforce podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Salesforce je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Salesforce.

V Salesforce, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí služby Salesforce, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Salesforce](#create-a-salesforce-test-user)**  – Pokud chcete mít protějšek Britta Simon v Salesforce, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Salesforce.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí služby Salesforce, proveďte následující kroky:**

1. Na webu Azure Portal na **Salesforce** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. Na **Salesforce domény a adresy URL** části, proveďte následující kroky:

    ![Salesforce domény a adresy URL jednotného přihlašování – informace](./media/salesforce-tutorial/tutorial_salesforce_url.png)
    
    a. V **přihlašovací adresa URL** textového pole zadejte hodnotu pomocí následujícího vzorce:
    
    Účet organizace: `https://<subdomain>.my.salesforce.com`

    Vývojářský účet: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    b. V **identifikátor** textového pole zadejte hodnotu pomocí následujícího vzorce:
    
    Účet organizace: `https://<subdomain>.my.salesforce.com`

    Vývojářský účet: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory klient Salesforce](https://help.salesforce.com/support) k získání těchto hodnot.

4. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/salesforce-tutorial/tutorial_general_400.png)

6. Na **Salesforce konfigurace** klikněte na tlačítko **konfigurace Salesforce** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace služby Salesforce](./media/salesforce-tutorial/tutorial_salesforce_configure.png) 

7. Otevře nová karta prohlížeče a přihlaste se k vašemu účtu správce služby Salesforce.

8. Klikněte na **nastavení** pod **ikona nastavení** v pravém horním rohu stránky.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/configure1.png)

9. Přejděte dolů k položce **nastavení** v navigačním podokně klikněte na tlačítko **Identity** tím rozbalíte související. Pak klikněte na tlačítko **nastavení jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-admin-sso.png)

10. Na **nastavení jednotného přihlašování** stránky, klikněte na tlačítko **upravit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-admin-sso-edit.png)
    
    > [!NOTE]
    > Pokud nemůžete povolit nastavení jednotného přihlašování pro váš účet Salesforce, budete muset kontaktovat [tým podpory klient Salesforce](https://help.salesforce.com/support). 

11. Vyberte **povoleno SAML**a potom klikněte na tlačítko **Uložit**.

      ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-enable-saml.png)
12. Konfigurace SAML jednotné přihlašování – nastavení, klikněte na tlačítko **nový**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-admin-sso-new.png)

13. Na **SAML jednotné přihlašování – nastavení upravit** stránce, proveďte následující konfigurace:

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-saml-config.png)

    a. Pro **název** zadejte popisný název pro tuto konfiguraci. Zadání hodnoty pro **název** se automaticky vyplní **název rozhraní API** textového pole.

    b. V **vystavitele** pole, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal.

    c. V **textového pole Entity Id**, zadejte název domény vaší služby Salesforce pomocí následujícího vzorce:
      
      * Účet organizace: `https://<subdomain>.my.salesforce.com`
      * Vývojářský účet: `https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Nahrát **certifikát poskytovatele Identity**, klikněte na tlačítko **zvolit soubor** Procházet a vyberte soubor certifikátu, který jste si stáhli z webu Azure portal.

    e. Jako **typ Identity SAML**, zvolte jednu z následujících možností:
    
      * Vyberte **kontrolní výraz obsahuje uživatelské jméno uživatele Salesforce**, pokud uživatelské jméno Salesforce uživatele se předává v kontrolní výraz SAML

      * Vyberte **kontrolní výraz obsahuje ID federace z objektu uživatele**, pokud je předávána federace z objektu uživatele v kontrolní výraz SAML

      * Vyberte **kontrolní výraz obsahuje ID použití z objektu uživatele**v případě, že ID uživatele z objektu uživatele je předávaný kontrolní výraz SAML

    f. Pro **umístění Identity SAML**vyberte **identita je v elementu NameIdentifier příkazu subjektu**.

    g. Pro **poskytovatele inicioval žádost o vazbu služby**vyberte **přesměrování protokolu HTTP**.

    h. V **přihlašovací adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal
    
    i. Nakonec klikněte na tlačítko **Uložit** SAML jednotné přihlašování – nastavení použijte.

14. Na levém navigačním podokně v Salesforce, klikněte na tlačítko **nastavení společnosti** související rozbalíte, a pak klikněte na **Moje doména**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-my-domain.png)

15. Přejděte dolů k položce **konfigurace ověřování** a klikněte **upravit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-edit-auth-config.png)

16. V **konfigurace ověřování** části, zkontrolujte **přihlašovací stránku** jako **ověřování Provisioning** Konfigurace jednotného přihlašování SAML, a pak klikněte na tlačítko  **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Pokud je vybrán více než jedna služba ověřování, uživatelé vyzváni k výběru které ověřovací službu, jako jsou se přihlásit pomocí při inicializaci jednotné přihlašování do prostředí Salesforce. Pokud nechcete, aby bylo možné, měli byste **nechte nezaškrtnuté všechny ostatní služby ověřování**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/salesforce-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/salesforce-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/salesforce-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/salesforce-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-salesforce-test-user"></a>Vytvoření zkušebního uživatele Salesforce

V této části se v Salesforce vytvoří uživatelské volá Britta Simon. Salesforce podporuje just-in-time zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel ještě neexistuje v Salesforce, vytvoří se nový při pokusu o přístup k Salesforce. Salesforce také podporuje automatické zřizování uživatelů, další podrobnosti můžete najít [tady](salesforce-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k Salesforce.

![Přiřazení role uživatele][200]

**Britta Simon přiřadit k Salesforce, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Salesforce**.

    ![Propojení služby Salesforce v seznamu aplikací](./media/salesforce-tutorial/tutorial_salesforce_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici služby Salesforce na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Salesforce.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-tutorial/tutorial_general_203.png
