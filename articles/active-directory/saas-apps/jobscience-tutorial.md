---
title: 'Kurz: Azure Active Directory integrace s Jobscience | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jobscience.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 65fc899c73e127750935e3cd8c24a488870ac0ca
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899182"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Kurz: Azure Active Directory integrace s Jobscience

V tomto kurzu zjistěte, jak integrovat Jobscience s Azure Active Directory (Azure AD).

Integrace Jobscience s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Jobscience
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Jobscience (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Jobscience, potřebujete následující položky:

- Předplatné služby Azure AD
- Jobscience jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat a jeden měsíc zkušební: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Jobscience z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-jobscience-from-the-gallery"></a>Přidání Jobscience z Galerie
Při konfiguraci integrace Jobscience do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Jobscience z galerie.

**Pokud chcete přidat Jobscience z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Jobscience**.

    ![Vytváření testovacího uživatele Azure AD](./media/jobscience-tutorial/tutorial_jobscience_search.png)

5. Na panelu výsledků vyberte **Jobscience**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Jobscience podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Jobscience je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Jobscience musí navázat.

V Jobscience, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Jobscience, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Jobscience](#creating-a-jobscience-test-user)**  – Pokud chcete mít protějšek Britta Simon v Jobscience propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Jobscience.

**Ke konfiguraci Azure AD jednotné přihlašování s Jobscience, proveďte následující kroky:**

1. Na portálu Azure na **Jobscience** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

3. Na **Jobscience domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce:  `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Tato hodnota získat [tým podpory Jobscience klienta](https://www.jobscience.com/support) nebo z profilu jednotné přihlašování bude vytvořena, což je vysvětleno později v tomto kurzu. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/jobscience-tutorial/tutorial_general_400.png)

6. Na **Jobscience konfigurace** klikněte na tlačítko **konfigurace Jobscience** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

7. Přihlaste se k serveru vaší společnosti Jobscience jako správce.

8. Přejděte na **instalační program**.
   
   ![Instalační program](./media/jobscience-tutorial/IC784358.png "instalační program")

9. V levém navigačním podokně v **Správa** klikněte na tlačítko **Správa domén** rozbalte související část, a potom klikněte na **Moje domény** otevřete **Moje domény** stránky. 
   
   ![Moje doména](./media/jobscience-tutorial/ic767825.png "Moje doména")

10. Chcete-li ověřit, zda vaše doména byla nastavena správně, ujistěte se, že je v "**krok 4 nasazení uživatelům**" a zkontrolovat vaše "**Moje nastavení domény**".

    ![Domény uživatele nejsou nasazené](./media/jobscience-tutorial/ic784377.png "doménou nasazení pro uživatele")

11. Na webu společnosti Jobscience klikněte na tlačítko **ovládacích prvků zabezpečení**a potom klikněte na **nastavení jednotného přihlašování**.
    
    ![Ovládací prvky zabezpečení](./media/jobscience-tutorial/ic784364.png "kontrolních mechanismů pro zabezpečení")

12. V **nastavení jednotného přihlašování** část, proveďte následující kroky:
    
    ![Jednotné přihlašování v nastavení](./media/jobscience-tutorial/ic781026.png "jednotné přihlašování v nastavení")
    
    a. Vyberte **povoleno SAML**.

    b. Klikněte na možnost **Nové**.

13. Na **SAML jeden přihlašování nastavení upravit** dialogové okno, proveďte následující kroky:
    
    ![SAML jeden přihlašování nastavení](./media/jobscience-tutorial/ic784365.png "SAML jeden přihlašování nastavení")
    
    a. V **název** textovému poli, zadejte název pro svou konfiguraci.

    b. V **vystavitele** textovému poli, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure.

    c. V **Entity Id** textovému poli, typu `https://salesforce-jobscience.com`

    d. Klikněte na tlačítko **Procházet** odeslání vašeho certifikátu Azure AD.

    e. Jako **typ Identity SAML**, vyberte **kontrolní výraz obsahuje ID federace z objektu uživatele**.

    f. Jako **umístění Identity SAML**, vyberte **identita je v elementu NameIdentfier příkaz subjektu**.

    g. V **adresu URL pro přihlášení zprostředkovatele Identity** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    h. V **adresa URL odhlašovací zprostředkovatele Identity** textovému poli, vložte hodnotu **Sign-Out URL**, který jste zkopírovali z portálu Azure.

    i. Klikněte na **Uložit**.

14. V levém navigačním podokně v **Správa** klikněte na tlačítko **Správa domén** rozbalte související část, a potom klikněte na **Moje domény** otevřete **Moje domény** stránky. 
    
    ![Moje doména](./media/jobscience-tutorial/ic767825.png "Moje doména")

15. Na **Moje domény** stránky v **Branding přihlašovací stránky** klikněte na tlačítko **upravit**.
    
    ![Branding přihlašovací stránky](./media/jobscience-tutorial/ic767826.png "Branding přihlašovací stránky")

16. Na **Branding přihlašovací stránky** stránky v **ověřovací služby** tématu, názvu vaší **nastavení jednotného přihlašování SAML** se zobrazí. Vyberte ji a pak klikněte na tlačítko **Uložit**.
    
    ![Branding přihlašovací stránky](./media/jobscience-tutorial/ic784366.png "Branding přihlašovací stránky")

17. Chcete-li získat SP iniciované jednotného přihlašování na klikněte na adresu URL pro přihlášení na **nastavení jednotného přihlašování** v **kontrolních mechanismů pro zabezpečení** části nabídky.

    ![Ovládací prvky zabezpečení](./media/jobscience-tutorial/ic784368.png "kontrolních mechanismů pro zabezpečení")
    
    Klikněte na tlačítko jednotného přihlašování k profilu, který jste vytvořili v předchozích krocích. Tato stránka zobrazuje jednotného přihlašování na adrese URL vaší společnosti (například [ https://companyname.my.salesforce.com?so=companyid ](https://companyname.my.salesforce.com?so=companyid).    

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/jobscience-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/jobscience-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/jobscience-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-jobscience-test-user"></a>Vytvoření zkušebního uživatele Jobscience

Pokud chcete povolit uživatelům Azure AD přihlášení k Jobscience, musí být zřízená do Jobscience. V případě Jobscience zřizování je ruční úloha.

>[!NOTE]
>Můžete použít všechny ostatní Jobscience uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Jobscience zřídit služby Azure Active Directory uživatelské účty.
>  
        
**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **Jobscience** společnosti lokality jako správce.

2. Přejděte do instalačního programu.
   
   ![Instalační program](./media/jobscience-tutorial/ic784358.png "instalační program")
3. Přejděte na **Správa uživatelů \> uživatelé**.
   
   ![Uživatelé](./media/jobscience-tutorial/ic784369.png "uživatelů")
4. Klikněte na tlačítko **nového uživatele**.
   
   ![Všichni uživatelé](./media/jobscience-tutorial/ic784370.png "všichni uživatelé")
5. Na **upravit uživatele** dialogové okno, proveďte následující kroky:
   
   ![Úprava uživatele](./media/jobscience-tutorial/ic784371.png "Úprava uživatele")
   
   a. V **křestní jméno** textovému poli, zadejte jméno uživatele, jako je Britta.
   
   b. V **příjmení** textovému poli, zadejte příjmení uživatele jako Simon.
   
   c. V **Alias** textovému poli, zadejte název aliasu uživatele jako brittas.

   d. V **e-mailu** jako typ e-mailovou adresu uživatele k textovému poli, Brittasimon@contoso.com.

   e. V **uživatelské jméno** textové pole, zadejte uživatelské jméno uživatele jako Brittasimon@contoso.com.

   f. V **Přezdívka** textovému poli, zadejte název nick uživatele jako Simon.

   g. Klikněte na **Uložit**.

    
> [!NOTE]
> Držitel účtu Azure Active Directory obdrží e-mailu a dodržuje odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Jobscience.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Jobscience, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Jobscience**.

    ![Konfigurovat jednotné přihlašování](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Jobscience na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Jobscience.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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

