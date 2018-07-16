---
title: 'Kurz: Integrace Azure Active Directory s Salesforce Sandbox | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sandboxu služby Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 1c87acb427a4d31a6eee1e215d43a601adbc17ec
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050898"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Kurz: Integrace Azure Active Directory s Salesforce Sandbox

V tomto kurzu se dozvíte, jak integrovat Sandboxu služby Salesforce se službou Azure Active Directory (Azure AD).

Integrace s Azure AD Sandboxu služby Salesforce vám poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Salesforce izolovaného prostoru.
- Uživatele, aby automaticky získat přihlášení k Salesforce izolovaného prostoru (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Sandboxu služby Salesforce, potřebujete následující položky:

- S předplatným služby Azure AD
- Izolovaného prostoru Salesforce jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Sandboxu Salesforce z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Přidání Sandboxu Salesforce z Galerie
Pokud chcete nakonfigurovat integraci izolovaného prostoru Salesforce do služby Azure AD, budete muset přidat Sandboxu služby Salesforce v galerii na váš seznam spravovaných aplikací SaaS.

**Salesforce izolovaného prostoru přidat z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Salesforce Sandboxu**vyberte **Salesforce Sandboxu** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Salesforce izolovaného prostoru v seznamu výsledků](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování s Salesforce Sandbox podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Sandboxu služby Salesforce je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Sandboxu služby Salesforce.

V Sandboxu služby Salesforce, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Sandboxu služby Salesforce, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Salesforce Sandboxu](#create-a-salesforce-sandbox-test-user)**  – Pokud chcete mít protějšek Britta Simon v Salesforce Sandboxu, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Salesforce izolovaného prostoru.

**Ke konfiguraci Azure AD jednotné přihlašování s Sandboxu služby Salesforce, proveďte následující kroky:**

1. Na webu Azure Portal na **Salesforce Sandboxu** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. Na **Salesforce izolovaného prostoru domény a adresy URL** části, proveďte následující kroky:

    ![Salesforce izolovaného prostoru domény a adresy URL jednotného přihlašování – informace](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte hodnotu pomocí následujícího vzorce: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    b. V **identifikátor** textového pole zadejte hodnotu pomocí následujícího vzorce: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`
    
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory klient Salesforce](https://help.salesforce.com/support) k získání těchto hodnot.

4. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/salesforce-sandbox-tutorial/tutorial_general_400.png)

6. Na **Salesforce izolovaného prostoru konfigurace** klikněte na tlačítko **konfigurace Sandboxu Salesforce** otevřete **nakonfigurovat přihlašování** okno. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png) 

7. Otevření nové záložky v prohlížeči a přihlaste se k vašemu účtu správce izolovaného prostoru služby Salesforce.

8. Klikněte na **nastavení** pod **ikona nastavení** v pravém horním rohu stránky.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/configure1.png)

9. Přejděte dolů k položce **nastavení** v navigačním podokně klikněte na tlačítko **Identity** tím rozbalíte související. Pak klikněte na tlačítko **nastavení jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Vyberte **povoleno SAML**a potom klikněte na tlačítko **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

11. Konfigurace SAML jednotné přihlašování – nastavení, klikněte na tlačítko **nový**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

12. V části SAML jednotné přihlašování – nastavení proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

    a. V **název** textového pole zadejte název konfigurace (například: *SPSSOWAAD_Test*). 

    b. V **vystavitele** pole, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal

    c. V **Entity Id** textové pole, typ `https://<instancename>--Sandbox.<entityid>.my.salesforce.com` Pokud je první instance Salesforce izolovaného prostoru, který přidáváte do svého adresáře. Pokud jste už přidali instance Sandboxu služby Salesforce, pak pro **Entity ID** zadejte **přihlašovací adresa URL**, který by měl být v tomto formátu: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    d. Nahrát **certifikát poskytovatele Identity**, klikněte na tlačítko **zvolit soubor** Procházet a vyberte soubor certifikátu, který jste si stáhli z webu Azure portal.

    e. Jako **typ Identity SAML**, zvolte jednu z následujících možností:

      * Vyberte **kontrolní výraz obsahuje uživatelské jméno uživatele Salesforce**, pokud uživatelské jméno Salesforce uživatele se předává v kontrolní výraz SAML

      * Vyberte **kontrolní výraz obsahuje ID federace z objektu uživatele**, pokud je předávána federace z objektu uživatele v kontrolní výraz SAML

      * Vyberte **kontrolní výraz obsahuje ID použití z objektu uživatele**v případě, že ID uživatele z objektu uživatele je předávaný kontrolní výraz SAML

    f. Jako **umístění Identity SAML**vyberte **identita je v elementu NameIdentifier příkazu subjektu**.

    g. Jako **poskytovatele inicioval žádost o vazbu služby**vyberte **HTTP POST**.

    h. V **přihlašovací adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    i. SFDC nepodporuje odhlašování SAML.  Jako alternativní řešení, vložte `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` do **odhlašovací adresa URL zprostředkovatele Identity** textového pole.

    j. Klikněte na **Uložit**.

### <a name="enable-your-domain"></a>Povolit vaší domény

V této části se předpokládá, že jste již vytvořili domény.  Další informace najdete v tématu [definování váš název domény](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Povolit vaší domény, proveďte následující kroky:**

1. Na levém navigačním podokně v Salesforce, klikněte na tlačítko **nastavení společnosti** související rozbalíte, a pak klikněte na **Moje doména**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

   >[!NOTE]
   >Ujistěte se prosím, že vaše doména se už nakonfigurovala správně.

2. V **konfigurace ověřování** klikněte na **upravit**, bude časem **ověřovací službu**, vyberte z předchozí název SAML jednotné přihlašování – nastavení části a nakonec klikněte na tlačítko **Uložit**.

   ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

Jakmile budete mít nakonfigurované domény, vaši uživatelé používali domény adresy URL pro přihlášení k Salesforce izolovaného prostoru.

Pokud chcete získat hodnotu adresy URL, klikněte na profil jednotného přihlašování, který jste vytvořili v předchozí části.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/salesforce-sandbox-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/salesforce-sandbox-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/salesforce-sandbox-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-salesforce-sandbox-test-user"></a>Vytvoření zkušebního uživatele Sandboxu služby Salesforce

V této části se vytvoří uživateli Britta Simon v Sandboxu služby Salesforce. Salesforce Sandbox podporuje zřizování just-in-time je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel ještě neexistuje v Sandboxu služby Salesforce, je vytvořen nový při pokusu o přístup k Salesforce izolovaného prostoru. Salesforce Sandbox také podporuje automatické zřizování uživatelů, další podrobnosti můžete najít [tady](salesforce-sandbox-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do izolovaného prostoru služby Salesforce.

![Přiřazení role uživatele][200]

**Přiřadit Britta Simon do izolovaného prostoru služby Salesforce, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Salesforce Sandboxu**.

    ![Odkaz Sandboxu služby Salesforce v seznamu aplikací](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici služby Salesforce izolovaného prostoru na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Salesforce izolovaného prostoru.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](salesforce-sandbox-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png
