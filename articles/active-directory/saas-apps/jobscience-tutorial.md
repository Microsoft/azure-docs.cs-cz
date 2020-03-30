---
title: 'Kurz: Integrace služby Azure Active Directory s jobscience | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Jobscience.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f0ea5b922b2c958aabf5be3a6123bb81a8f0234
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048508"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Kurz: Integrace Azure Active Directory s Jobscience

V tomto kurzu se dozvíte, jak integrovat Jobscience s Azure Active Directory (Azure AD).

Integrace Jobscience s Azure AD vám poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Jobscience
- Uživatelům můžete povolit automatické přihlášení k Jobscience (jednotné přihlašování) pomocí jejich účtů Azure AD.
- Své účty můžete spravovat na jednom centrálním místě – na portálu Azure

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, podívejte se, [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Jobscience, budete potřebovat následující položky:

- Předplatné Azure AD
- Předplatné s povoleným jedním přihlášením Jobscience

> [!NOTE]
> Chcete-li otestovat kroky v tomto kurzu, nedoporučujeme používat produkční prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkční prostředí, pokud to není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete získat měsíční zkušební verzi zde: [Zkušební nabídka](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu otestujete jednotné přihlašování Azure AD v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Jobscience z galerie
1. Konfigurace a testování jednotného přihlašování azure ad

## <a name="adding-jobscience-from-the-gallery"></a>Přidání Jobscience z galerie
Chcete-li nakonfigurovat integraci Jobscience do Azure AD, musíte přidat Jobscience z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Jobscience z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.** 

    ![Active Directory][1]

1. Přejděte k **podnikovým aplikacím**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Jobscience**.

    ![Vytvoření testovacího uživatele Azure AD](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. V panelu výsledků vyberte **Jobscience**a pak klepnutím na **tlačítko Přidat** přidejte aplikaci.

    ![Vytvoření testovacího uživatele Azure AD](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování azure ad
V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Jobscience na základě testovacího uživatele s názvem "Britta Simon.".

Aby jednotné přihlašování fungovalo, Azure AD potřebuje vědět, co je protějšek uživatele jobscience pro uživatele ve službě Azure AD. Jinými slovy vztah propojení mezi uživatelem Azure AD a související uživatel v Jobscience musí být vytvořen.

V Jobscience přiřaďte hodnotu **uživatelského jména** ve službě Azure AD jako hodnotu **uživatelského jména** k vytvoření vztahu propojení.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Jobscience, musíte dokončit následující stavební bloky:

1. **[Konfigurace jednotného přihlašování Azure AD](#configuring-azure-ad-single-sign-on)** – k tomu, aby uživatelé mohli tuto funkci používat.
1. **[Vytvoření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)** – k testování jednotného přihlašování Azure AD s Brittou Simonovou.
1. **[Vytvoření jobscience test uživatele](#creating-a-jobscience-test-user)** – mít protějšek Britta Simon v Jobscience, který je propojen s reprezentaci Azure AD uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)** – chcete-li britta Simon používat Azure AD jednotné přihlašování.
1. **[Testování jednotného přihlášení](#testing-single-sign-on)** - k ověření, zda konfigurace funguje.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal a nakonfigurujete jednotné přihlašování ve vaší aplikaci Jobscience.

**Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí jobscience, proveďte následující kroky:**

1. Na portálu Azure klikněte na stránce Integrace aplikací **Jobscience** na **Jedno přihlášení**.

    ![Konfigurace jednotného přihlašování][4]

1. V **dialogovém okně Jednotné přihlašování** vyberte **Režim** jako **přihlašování na základě SAML,** abyste povolili jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. V části **Jobscience Domain and URL** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Tato hodnota není skutečná. Aktualizujte tuto hodnotu skutečnou přihlašovací adresou URL. Získejte tuto hodnotu [týmem podpory jobscience klienta](https://www.jobscience.com/support) nebo z profilu steso, které vytvoříte, který je vysvětleno dále v kurzu. 
 
1. V části **Podpisový certifikát SAML** klikněte na **Certifikát (Base64)** a uložte soubor certifikátu do počítače.

    ![Konfigurace jednotného přihlašování](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Klikněte na **tlačítko Uložit.**

    ![Konfigurace jednotného přihlašování](./media/jobscience-tutorial/tutorial_general_400.png)

1. V části **Konfigurace jobscience** klikněte na **Konfigurovat Jobscience** a otevřete okno **Konfigurace přihlášení.** Zkopírujte **adresu URL pro odhlášení, ID entity SAML a adresu URL služby jednotného přihlašovacího systému SAML** z části Stručný **přehled.**

    ![Konfigurace jednotného přihlašování](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Přihlaste se na web společnosti Jobscience jako správce.

1. Přejděte na **instalační program**.
   
   ![Nastavení](./media/jobscience-tutorial/IC784358.png "Nastavení")

1. V levém navigačním podokně klikněte v části **Správa** na **položku Správa domény** a rozbalte související oddíl a potom kliknutím na **Moje doména** otevřete stránku **Moje doména.** 
   
   ![Moje doména](./media/jobscience-tutorial/ic767825.png "Moje doména")

1. Chcete-li ověřit, zda byla vaše doména správně nastavena, zkontrolujte, zda je v kroku**4 nasazeném uživatelům,** a zkontrolujte**nastavení domény**.

    ![Doména nasazená pro uživatele](./media/jobscience-tutorial/ic784377.png "Doména nasazená pro uživatele")

1. Na webu společnosti Jobscience klepněte na **položku Bezpečnostní ovládací prvky**a potom klepněte na **položku Nastavení jednotného přihlášení**.
    
    ![Zabezpečovací mechanismy](./media/jobscience-tutorial/ic784364.png "Zabezpečovací mechanismy")

1. V části **Nastavení jednotného přihlášení** proveďte následující kroky:
    
    ![Nastavení jednotného přihlášení](./media/jobscience-tutorial/ic781026.png "Nastavení jednotného přihlášení")
    
    a. Vyberte **možnost SAML Povolená**.

    b. Klepněte na tlačítko **Nový**.

1. V dialogovém **okně Úpravy nastavení jednotného přihlášení SAML** proveďte následující kroky:
    
    ![Saml nastavení jednotného přihlášení](./media/jobscience-tutorial/ic784365.png "Saml nastavení jednotného přihlášení")
    
    a. Do textového pole **Název** zadejte název konfigurace.

    b. V textovém poli **Vystavitena** vložte hodnotu **ID entity SAML**, kterou jste zkopírovali z webu Azure Portal.

    c. Do textového pole **Id entity** zadejte`https://salesforce-jobscience.com`

    d. Kliknutím na **Procházet** nahrajte certifikát Azure AD.

    e. Jako **typ identity SAML**vyberte **assertion obsahuje ID federace z objektu User**.

    f. Jako **umístění identity SAML**je vyberte **Identita v elementu NameIdentfier příkazu Subject**.

    g. V textovém poli **Přihlašovací adresa URL zprostředkovatele identity** vložte hodnotu **adresy URL služby jednotného přihlašování SAML**, kterou jste zkopírovali z webu Azure Portal.

    h. V textovém poli **URL odhlášení zprostředkovatele identity** vložte hodnotu **adresy URL pro odhlášení**, kterou jste zkopírovali z webu Azure Portal.

    i. Klikněte na **Uložit**.

1. V levém navigačním podokně klikněte v části **Správa** na **položku Správa domény** a rozbalte související oddíl a potom kliknutím na **Moje doména** otevřete stránku **Moje doména.** 
    
    ![Moje doména](./media/jobscience-tutorial/ic767825.png "Moje doména")

1. Na stránce **Moje doména** klikněte v části **Branding přihlašovací stránky** na **Upravit**.
    
    ![Přihlašovací stránka značky](./media/jobscience-tutorial/ic767826.png "Přihlašovací stránka značky")

1. Na stránce **Branding přihlašovací stránky** se v části Ověřovací **služba** zobrazí název **nastavení přihlašování saml.** Vyberte ji a klepněte na tlačítko **Uložit**.
    
    ![Přihlašovací stránka značky](./media/jobscience-tutorial/ic784366.png "Přihlašovací stránka značky")

1. Chcete-li získat SP inicioval jednotného přihlášení URL klikněte na **nastavení jednotného přihlášení** v sekci **Bezpečnostní ovládací prvky.**

    ![Zabezpečovací mechanismy](./media/jobscience-tutorial/ic784368.png "Zabezpečovací mechanismy")
    
    Klikněte na profil přistupovače, který jste vytvořili ve výše uvedeném kroku. Na této stránce se zobrazuje adresa URL jednotného přihlášení pro vaši společnost (například `https://companyname.my.salesforce.com?so=companyid`.    

> [!TIP]
> Teď si můžete přečíst stručnou verzi těchto pokynů uvnitř [portálu Azure](https://portal.azure.com), zatímco nastavujete aplikaci!  Po přidání této aplikace z oddílu **Active Directory > Enterprise Applications** jednoduše klikněte na kartu Jednotné **přihlášení** a získejte přístup k vložené dokumentaci prostřednictvím části **Konfigurace** v dolní části. Další informace o funkci vložené dokumentace najdete zde: [Integrovaná dokumentace azure ad]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD
Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na **webu Azure Portal**klikněte v levém navigačním podokně na ikonu **Služby Azure Active Directory.**

    ![Vytvoření testovacího uživatele Azure AD](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **položku Uživatelé a skupiny** a klepněte na tlačítko **Všichni uživatelé**.
    
    ![Vytvoření testovacího uživatele Azure AD](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít dialogové **okno Uživatel,** klepněte v horní části dialogového okna na **Přidat.**
 
    ![Vytvoření testovacího uživatele Azure AD](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Na stránce **uživatelského** dialogového okna proveďte následující kroky:
 
    ![Vytvoření testovacího uživatele Azure AD](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. Do textového pole **Název** zadejte **příkaz BrittaSimon**.

    b. Do textového pole **Uživatelské jméno** zadejte **e-mailovou adresu** BrittaSimon.

    c. Vyberte **Zobrazit heslo** a poznamenejte si hodnotu **hesla**.

    d. Klikněte na **Vytvořit**.
 
### <a name="creating-a-jobscience-test-user"></a>Vytvoření testovacího uživatele Jobscience

Aby bylo možné uživatelům Azure AD přihlásit se k Jobscience, musí být zřízena do Jobscience. V případě Jobscience zřizování je ruční úlohy.

>[!NOTE]
>Ke zřizování uživatelských účtů Azure Active Directory můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Jobscience nebo rozhraní API poskytovaná jobscienceem.
>  
        
**Chcete-li konfigurovat zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se na web společnosti **Jobscience** jako správce.

1. Přejděte do instalačního programu.
   
   ![Nastavení](./media/jobscience-tutorial/ic784358.png "Nastavení")
1. Přejděte na **spravovat uživatele \> uživatelů**.
   
   ![Uživatelé](./media/jobscience-tutorial/ic784369.png "Uživatelé")
1. Klepněte na **položku Nový uživatel**.
   
   ![Všichni uživatelé](./media/jobscience-tutorial/ic784370.png "Všichni uživatelé")
1. V dialogovém okně **Upravit uživatele** proveďte následující kroky:
   
   ![Úpravy uživatele](./media/jobscience-tutorial/ic784371.png "Úpravy uživatele")
   
   a. Do textového pole **Křestní jméno** zadejte křestní jméno uživatele, jako je Britta.
   
   b. Do textového pole **Příjmení** zadejte příjmení uživatele, jako je Simon.
   
   c. Do textového pole **Alias** zadejte alias uživatele jako brittas.

   d. Do textového pole **E-mail** zadejte Brittasimon@contoso.come-mailovou adresu uživatele, který se líbí .

   e. Do textového pole **Uživatelské jméno** zadejte Brittasimon@contoso.comuživatelské jméno uživatele, jako se mi líbí .

   f. Do textového pole **Nick Název** zadejte přezdívku uživatele, jako je Simon.

   g. Klikněte na **Uložit**.

    
> [!NOTE]
> Držitel účtu Azure Active Directory obdrží e-mail a následuje odkaz pro potvrzení svého účtu před tím, než se stane aktivním.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu jobscience.

![Přiřadit uživatele][200] 

**Chcete-li brittu Simon přiřadit společnosti Jobscience, proveďte následující kroky:**

1. Na webu Azure Portal otevřete zobrazení aplikací a přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** a klikněte na **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Jobscience**.

    ![Konfigurace jednotného přihlašování](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. V nabídce vlevo klikněte na **Uživatelé a skupiny**.

    ![Přiřadit uživatele][202] 

1. Klikněte na **tlačítko Přidat.** V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Přiřadit uživatele][203]

1. V dialogovém **okně Uživatelé a skupiny** vyberte **britta Simon** v seznamu Uživatelé.

1. V dialogovém okně **Uživatelé a skupiny** klikněte na **tlačítko Vybrat.**

1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **Přiřadit.**
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Jobscience na přístupovém panelu byste se měli automaticky přihlásit ke své žádosti Jobscience.
Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje

* [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png

