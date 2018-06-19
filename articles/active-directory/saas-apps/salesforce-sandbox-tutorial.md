---
title: 'Kurz: Azure Active Directory integrace s izolovaného prostoru Salesforce | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a izolovaného prostoru služby Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 0aa7c2e7a025414f4bee348a90b0daa87b51c579
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899071"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Kurz: Azure Active Directory integrace s izolovaného prostoru Salesforce

V tomto kurzu zjistěte, jak integrovat Salesforce izolovaného prostoru se službou Azure Active Directory (Azure AD).

Integrace služby Salesforce izolovaného prostoru s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k izolovanému prostoru služby Salesforce.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Salesforce izolovaného prostoru (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Salesforce izolovaného prostoru, potřebujete následující položky:

- Předplatné služby Azure AD
- Izolovaného prostoru Salesforce jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání izolovaného prostoru Salesforce z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Přidání izolovaného prostoru Salesforce z Galerie
Při konfiguraci integrace služby Salesforce izolovaného prostoru do služby Azure AD potřebujete přidat izolovaného prostoru Salesforce z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat izolovaného prostoru Salesforce z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **izolovaného prostoru Salesforce**, vyberte **izolovaného prostoru Salesforce** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Salesforce izolovaného prostoru v seznamu výsledků](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat, testovací Azure AD jednotné přihlašování s izolovaného prostoru Salesforce podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Salesforce izolovaného prostoru je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v izolovaném prostoru Salesforce musí navázat.

V karanténě Salesforce přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Salesforce izolovaného prostoru, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele izolovaného prostoru Salesforce](#create-a-salesforce-sandbox-test-user)**  – Pokud chcete mít protějšek Britta Simon v Salesforce izolovaný prostor, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Salesforce izolovaného prostoru.

**Ke konfiguraci Azure AD jednotné přihlašování s Salesforce izolovaného prostoru, proveďte následující kroky:**

1. Na portálu Azure na **izolovaného prostoru Salesforce** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. Na **Salesforce izolovaného prostoru domény a adresy URL** část, proveďte následující kroky:

    ![Salesforce izolovaného prostoru domény a adresy URL jednotné přihlašování informace](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte hodnotu pomocí následujícího vzorce: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    b. V **identifikátor** textovému poli, zadejte hodnotu pomocí následujícího vzorce: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`
    
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory služby Salesforce klienta](https://help.salesforce.com/support) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikát** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/salesforce-sandbox-tutorial/tutorial_general_400.png)

6. Na **Salesforce izolovaného prostoru konfigurace** klikněte na tlačítko **konfigurace izolovaného prostoru Salesforce** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresu URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png) 

7. V prohlížeči otevřete novou kartu a přihlaste se k účtu správce izolovaného prostoru služby Salesforce.

8. Klikněte na **instalační program** pod **ikonu nastavení** v pravém horním rohu stránky.

    ![Konfigurovat jednotné přihlašování](./media/salesforce-sandbox-tutorial/configure1.png)

9. Přejděte dolů k položce **nastavení** v navigačním podokně klikněte na tlačítko **Identity** rozbalte související část. Pak klikněte na tlačítko **nastavení jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Vyberte **povoleno SAML**a potom klikněte na **Uložit**.

    ![Konfigurovat jednotné přihlašování](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

11. Chcete-li nakonfigurovat SAML jeden přihlašování nastavení, klikněte na tlačítko **nový**.

    ![Konfigurovat jednotné přihlašování](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

12. V části SAML jeden přihlašování nastavení proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

    a. V **název** textovému poli, zadejte název konfigurace (například: *SPSSOWAAD_Test*). 

    b. V **vystavitele** pole, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure

    c. V **Entity Id** textovému poli, typ `https://<instancename>--Sandbox.<entityid>.my.salesforce.com` Pokud je první instance Salesforce izolovaného prostoru, který chcete přidat do vašeho adresáře. Pokud jste již přidali instance Salesforce izolovaného prostoru, pak pro **Entity ID** zadejte **přihlašovací adresa URL**, což by mělo být v tomto formátu: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    d. Nahrát **certifikát zprostředkovatele Identity**, klikněte na tlačítko **zvolit soubor** a procházet a vyberte soubor certifikátu, který jste si stáhli z portálu Azure.

    e. Jako **typ Identity SAML**, vyberte jednu z následujících možností:

      * Vyberte **kontrolní výraz obsahuje uživatelské jméno Salesforce**, pokud uživatelské jméno uživatele Salesforce je předávána v kontrolního výrazu SAML

      * Vyberte **kontrolní výraz obsahuje ID federace z objektu uživatele**, pokud je předávána federace z objektu uživatele v kontrolního výrazu SAML

      * Vyberte **kontrolní výraz obsahuje ID použití z objektu uživatele**, pokud ID uživatele z objektu uživatele je předávána v kontrolního výrazu SAML

    f. Jako **umístění Identity SAML**, vyberte **identita je v elementu NameIdentifier příkaz subjektu**.

    g. Jako **zprostředkovatele iniciované žádosti vazby služby**, vyberte **HTTP POST**.

    h. V **adresu URL pro přihlášení zprostředkovatele Identity** textovému poli, vložte hodnotu **jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    i. SFDC nepodporuje SAML odhlášení.  Jako alternativní řešení, vložte `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` ji do **adresa URL odhlašovací zprostředkovatele Identity** textové pole.

    j. Klikněte na **Uložit**.

### <a name="enable-your-domain"></a>Povolit doménu

V této části se předpokládá, že jste již vytvořili domény.  Další informace najdete v tématu [definování váš název domény](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Pokud chcete povolit doménu, proveďte následující kroky:**

1. V levém navigačním podokně v Salesforce, klikněte na tlačítko **nastavení společnosti** rozbalte související část, a potom klikněte na **Moje domény**.

    ![Konfigurovat jednotné přihlašování](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

   >[!NOTE]
   >Přesvědčte se, že doménu je správně nakonfigurováno.

2. V **konfiguraci ověřování** klikněte na tlačítko **upravit**, pak jako **ověřovací služby**, vyberte název SAML jeden přihlašování nastavení z předchozí část a nakonec klikněte na tlačítko **Uložit**.

   ![Konfigurovat jednotné přihlašování](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

Jakmile máte doménu nakonfigurovat, uživatelé měli používat adresa URL domény k přihlášení k izolovanému prostoru služby Salesforce.

Chcete-li získat hodnotu adresy URL, klikněte na tlačítko jednotného přihlašování k profilu, který jste vytvořili v předchozí části.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/salesforce-sandbox-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/salesforce-sandbox-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/salesforce-sandbox-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-salesforce-sandbox-test-user"></a>Vytvoření zkušebního uživatele izolovaného prostoru Salesforce

V této části se uživatel volá Britta Simon vytvoří v izolovaného prostoru služby Salesforce. Izolovaný prostor Salesforce podporuje zřizování za běhu, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel v izolovaném prostoru Salesforce ještě neexistuje, vytvoří se nový při pokusu o přístup k izolovanému prostoru služby Salesforce. Izolovaný prostor Salesforce podporuje také zřizování automatické uživatelů, můžete najít další podrobnosti o [sem](salesforce-sandbox-provisioning-tutorial.md) na tom, jak nakonfigurovat uživatele automatické zřizování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup k izolovanému prostoru služby Salesforce.

![Přiřadit role uživatele][200]

**Pokud chcete přiřadit Britta Simon Salesforce izolovaného prostoru, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **izolovaného prostoru Salesforce**.

    ![Odkaz Salesforce izolovaného prostoru v seznamu aplikací](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici služby Salesforce izolovaného prostoru na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Salesforce izolovaného prostoru.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfiguraci zřizování uživatelů](salesforce-sandbox-provisioning-tutorial.md)


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
