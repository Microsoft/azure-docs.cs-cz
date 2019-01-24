---
title: 'Kurz: Integrace Azure Active Directory s Jobscience | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jobscience.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 2e144658ed4d4e4ae1f9e1ceb8a822e2b71bc2c0
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54822889"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Kurz: Integrace Azure Active Directory s Jobscience

V tomto kurzu se dozvíte, jak integrovat Jobscience s Azure Active Directory (Azure AD).

Jobscience integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Jobscience
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Jobscience (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Jobscience, potřebujete následující položky:

- Předplatné Azure AD
- Jobscience jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady: [Zkušební nabídka](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Jobscience z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-jobscience-from-the-gallery"></a>Přidání Jobscience z Galerie
Konfigurace integrace Jobscience do služby Azure AD, budete muset přidat Jobscience z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Jobscience z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Jobscience**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. Na panelu výsledků vyberte **Jobscience**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s Jobscience podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Jobscience je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Jobscience potřeba navázat.

V Jobscience, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Jobscience, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Jobscience](#creating-a-jobscience-test-user)**  – Pokud chcete mít protějšek Britta Simon Jobscience, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Jobscience.

**Ke konfiguraci Azure AD jednotné přihlašování s Jobscience, proveďte následující kroky:**

1. Na webu Azure Portal na **Jobscience** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. Na **Jobscience domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:  `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Tuto výhodu získáte [tým podpory Jobscience klienta](https://www.jobscience.com/support) nebo z něj jednotného přihlašování bude vytvořena, což je vysvětleno dále v tomto kurzu. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/jobscience-tutorial/tutorial_general_400.png)

1. Na **Jobscience konfigurace** klikněte na tlačítko **nakonfigurovat Jobscience** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Přihlaste se na web společnosti Jobscience jako správce.

1. Přejděte na **nastavení**.
   
   ![Instalační program](./media/jobscience-tutorial/IC784358.png "instalační program")

1. V levém navigačním podokně v **spravovat** klikněte na tlačítko **Správa domén** související rozbalíte, a pak klikněte na **Moje doména** otevřete **Moje doména** stránky. 
   
   ![Moje doména](./media/jobscience-tutorial/ic767825.png "Moje doména")

1. Pokud chcete ověřit, správně nastavené vaší domény, ujistěte se, že je v "**kroku 4 nasazena u uživatelů**" a zkontrolovat vaše "**má nastavení domény**".

    ![Doména uživatele nasazena](./media/jobscience-tutorial/ic784377.png "nasadit na uživatele domény")

1. Na webu společnosti Jobscience, klikněte na tlačítko **kontrolních mechanismů pro zabezpečení**a potom klikněte na tlačítko **nastavení jednotného přihlašování**.
    
    ![Kontrolní mechanismy zabezpečení](./media/jobscience-tutorial/ic784364.png "kontrolních mechanismů pro zabezpečení")

1. V **nastavení jednotného přihlašování** části, proveďte následující kroky:
    
    ![Jednotné přihlašování – nastavení](./media/jobscience-tutorial/ic781026.png "jednotné přihlašování – nastavení")
    
    a. Vyberte **povoleno SAML**.

    b. Klikněte na možnost **Nové**.

1. Na **SAML jednotné přihlašování – nastavení upravit** dialogového okna, proveďte následující kroky:
    
    ![SAML jednotné přihlašování – nastavení](./media/jobscience-tutorial/ic784365.png "SAML jednotné přihlašování – nastavení")
    
    a. V **název** textového pole zadejte název pro vaši konfiguraci.

    b. V **vystavitele** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal.

    c. V **Entity Id** textové pole, typ `https://salesforce-jobscience.com`

    d. Klikněte na tlačítko **Procházet** chcete nahrát svůj certifikát služby Azure AD.

    e. Jako **typ Identity SAML**vyberte **kontrolní výraz obsahuje ID federace z objektu uživatele**.

    f. Jako **umístění Identity SAML**vyberte **identita je v elementu NameIdentfier příkazu subjektu**.

    g. V **přihlašovací adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    h. V **odhlašovací adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    i. Klikněte na **Uložit**.

1. V levém navigačním podokně v **spravovat** klikněte na tlačítko **Správa domén** související rozbalíte, a pak klikněte na **Moje doména** otevřete **Moje doména** stránky. 
    
    ![Moje doména](./media/jobscience-tutorial/ic767825.png "Moje doména")

1. Na **Moje doména** stránku, **Branding přihlašovací stránky** klikněte na tlačítko **upravit**.
    
    ![Branding přihlašovací stránky](./media/jobscience-tutorial/ic767826.png "Branding přihlašovací stránky")

1. Na **Branding přihlašovací stránky** stránku, **ověřovací službu** části název vaší **nastavení jednotného přihlašování SAML** se zobrazí. Vyberte ho a pak klikněte na tlačítko **Uložit**.
    
    ![Branding přihlašovací stránky](./media/jobscience-tutorial/ic784366.png "Branding přihlašovací stránky")

1. Chcete-li získat SP zahájí jednotné přihlašování na adresu URL pro přihlášení klikněte na **nastavení jednotného přihlašování** v **kontrolních mechanismů pro zabezpečení** části nabídky.

    ![Kontrolní mechanismy zabezpečení](./media/jobscience-tutorial/ic784368.png "kontrolních mechanismů pro zabezpečení")
    
    Klikněte na profil jednotného přihlašování, které jste vytvořili v předchozím kroku. Tato stránka zobrazuje jednotného přihlašování na adrese URL vaší společnosti (třeba [ https://companyname.my.salesforce.com?so=companyid ](https://companyname.my.salesforce.com?so=companyid).    

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-jobscience-test-user"></a>Vytvoření zkušebního uživatele Jobscience

Chcete-li povolit uživatele Azure AD se přihlaste k Jobscience, musí být poskytnuty do Jobscience. V případě Jobscience zřizování se ruční úlohy.

>[!NOTE]
>Můžete použít jakékoli jiné Jobscience uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Jobscience ke zřízení služby Azure Active Directory uživatelské účty.
>  
        
**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **Jobscience** společnosti serveru jako správce.

1. Přejděte do instalačního programu.
   
   ![Instalační program](./media/jobscience-tutorial/ic784358.png "instalační program")
1. Přejděte na **spravovat uživatele \> uživatelé**.
   
   ![Uživatelé](./media/jobscience-tutorial/ic784369.png "uživatelů")
1. Klikněte na tlačítko **nového uživatele**.
   
   ![Všichni uživatelé](./media/jobscience-tutorial/ic784370.png "všichni uživatelé")
1. Na **upravit uživatele** dialogového okna, proveďte následující kroky:
   
   ![Úprava uživatele](./media/jobscience-tutorial/ic784371.png "Úprava uživatele")
   
   a. V **křestní jméno** textového pole zadejte jméno uživatele, jako je Britta.
   
   b. V **příjmení** textového pole zadejte příjmení uživatele, jako je Simon.
   
   c. V **Alias** textového pole zadejte název aliasu uživatele, jako je brittas.

   d. V **e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.

   e. V **uživatelské jméno** textové pole, typ uživatelskému jménu uživatele jako Brittasimon@contoso.com.

   f. V **Přezdívka** textového pole zadejte název nick uživatele, jako je Simon.

   g. Klikněte na **Uložit**.

    
> [!NOTE]
> Držitel účtu Azure Active Directory obdrží e-mailu a řídí se odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Jobscience použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Jobscience Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Jobscience**.

    ![Konfigurace jednotného přihlašování](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Jobscience na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Jobscience.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
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

