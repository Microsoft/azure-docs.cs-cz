---
title: 'Kurz: Azure Active Directory integrace s LinkedIn prodej Navigátor | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LinkedInSalesNavigator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: 2bc4f42b0a22ce804db9290728944575888f9c72
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287442"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Kurz: Azure Active Directory integrace s LinkedIn prodej Navigátor

V tomto kurzu zjistěte, jak integrovat LinkedIn prodej Navigátor s Azure Active Directory (Azure AD).

Integrace LinkedIn prodej Navigátor s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k LinkedIn prodej Navigátor
- Můžete povolit uživatelům, aby automaticky získat přihlášení k LinkedIn prodej Navigátor (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, Procházet [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Navigátor prodej LinkedIn, potřebujete následující položky:

- Předplatné služby Azure AD
- Organizační jednotky prodej Navigátor LinkedIn jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Pokud to není nezbytné, vyhněte se použití produkční prostředí.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání LinkedIn prodej Navigátor z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Přidání LinkedIn prodej Navigátor z Galerie
Při konfiguraci integrace LinkedIn prodej Navigátor do služby Azure AD potřebujete přidat LinkedIn prodej Navigátor z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat LinkedIn prodej Navigátor z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **LinkedIn prodej Navigátor**.

    ![Vytváření testovacího uživatele Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_search.png)

5. Na panelu výsledků vyberte **LinkedIn prodej Navigátor**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Navigátor LinkedIn prodeje podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v LinkedIn prodej navigátoru je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v LinkedIn prodej Navigátor musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Navigátor prodej LinkedIn.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Navigátor LinkedIn prodeje, musíte dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele LinkedIn prodej Navigátor](#creating-a-linkedin-sales-navigator-test-user)**  – Pokud chcete mít protějšek Britta Simon v Navigátor LinkedIn prodej, propojené služby Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Navigátor prodej LinkedIn.

**Ke konfiguraci Azure AD jednotné přihlašování s Navigátor prodej LinkedIn, proveďte následující kroky:**

1. Na portálu Azure na **LinkedIn prodej Navigátor** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogové okno, v **režimu** vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_samlbase.png)

3. V okně prohlížeče jiný web, přihlašování k vaší **LinkedIn prodej Navigátor** webu jako správce.

4. V **centra účtů**, klikněte na tlačítko **globální nastavení** pod **nastavení**. Kromě toho **prodej Navigátor** z rozevíracího seznamu.

    ![Konfigurovat jednotné přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

5. Klikněte na tlačítko **nebo klikněte na tlačítko sem můžete načíst a zkopírujte jednotlivých polí z formuláře** a zkopírujte **Entity Id** a **adresu Url pro přístup k příjemce Assertion (ACS)**.

    ![Konfigurovat jednotné přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

6. Na portálu Azure v části **LinkedIn prodej Navigátor domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu.

    ![Konfigurovat jednotné přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url1.png)

    a. V **identifikátor** textovému poli, zadejte **Entity ID** zkopírovat z portálu LinkedIn 

    b. V **adresa URL odpovědi** textovému poli, zadejte **adresu Url pro přístup k příjemce Assertion (ACS)** zkopírovat z portálu LinkedIn

7. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu.

    ![Konfigurovat jednotné přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url2.png)

    V **přihlašovací adresa URL** textovému poli, zadejte hodnotu pomocí následujícího vzorce: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

8. Vaše **LinkedIn prodej Navigátor** aplikace očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad. Výchozí hodnota **uživatelský identifikátor** je **user.userprincipalname** ale LinkedIn prodej Navigátor očekává, že nejde mapovat pomocí e-mailovou adresu uživatele. Můžete použít **user.mail** atribut ze seznamu, nebo použijte hodnotu odpovídajícího atributu na základě konfigurace vaší organizace. 

    ![Konfigurovat jednotné přihlašování](./media/linkedinsalesnavigator-tutorial/updateusermail.png)
    
9. V **uživatelské atributy** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** a nastavit atributy. Uživatel musí přidat čtyři deklarace identity s názvem **e-mailu**, **oddělení**, **firstname**, a **lastname** a hodnota má být namapována na **user.mail**, **user.department**, **user.givenname**, a **user.surname** v uvedeném pořadí

    | Název atributu | Hodnota atributu |
    | --- | --- |    
    | e-mail| User.Mail |
    | oddělení| User.Department |
    | FirstName| user.givenname |
    | Příjmení| user.surname |
    
    ![Vytváření testovacího uživatele Azure AD](./media/linkedinsalesnavigator-tutorial/userattribute.png)
    
    a. Klikněte na **přidat atribut** tím otevřete dialogové okno atribut.
    
    ![Vytváření testovacího uživatele Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_04.png)
    
    ![Vytváření testovacího uživatele Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_05.png)
   
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **Ok**

10. Proveďte následující kroky na **název** atribut -

    a. Klikněte na atribut, který se otevře **Upravit atribut** okno.

    ![Konfigurovat jednotné přihlašování](./media/linkedinsalesnavigator-tutorial/url_update.png)

    b. Odstranit hodnotu adresy URL **obor názvů**.
    
    c. Klikněte na tlačítko **Ok** uložte nastavení.

11. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_certificate.png) 

12. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_general_400.png)

13. Přejděte na **nastavení správce LinkedIn** části. Klikněte na tlačítko **soubor nahrát XML** nahrát soubor XML s metadaty soubor, který jste si stáhli z portálu Azure.

    ![Konfigurovat jednotné přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

14. Klikněte na tlačítko **na** umožňující jednotného přihlašování. Jednotné přihlašování stav se změní z **Nepřipojeno** k **připojeno**

    ![Konfigurovat jednotné přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)


> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na tlačítko **přidat** otevřete **uživatele** dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-linkedin-sales-navigator-test-user"></a>Vytvoření zkušebního uživatele LinkedIn prodej Navigátor

Propojené aplikace Navigátor prodej podporuje pouze v zřizování uživatelů čas (JIT) a po ověření uživatelé jsou automaticky vytvořené v aplikaci. Aktivovat **automaticky přiřadit licence** přiřadit licence pro uživatele.
   
   ![Vytváření testovacího uživatele Azure AD](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Navigátor prodej LinkedIn.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Navigátor prodej LinkedIn, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **LinkedIn prodej Navigátor**.

    ![Konfigurovat jednotné přihlašování](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici LinkedIn prodej Navigátor na přístupovém panelu, přesměrovat na organizační stránku, kde je nutné zadat osobní podrobnosti o účtu LinkedIn. Ho propojí s vaším účtem obchodní LinkedIn svůj osobní účet. Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_01.png
[2]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_02.png
[3]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_03.png
[4]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_04.png

[100]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_100.png

[200]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_200.png
[201]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_201.png
[202]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_202.png
[203]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_203.png

