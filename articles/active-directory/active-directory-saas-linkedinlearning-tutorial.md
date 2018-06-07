---
title: 'Kurz: Azure Active Directory integrace s LinkedIn Learning | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: b0db212b215d2d53b32437d703394148bcc4ac92
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590272"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Kurz: Azure Active Directory integrace s LinkedIn Learning

V tomto kurzu zjistěte, jak integrovat LinkedIn Learning s Azure Active Directory (Azure AD).

Integrace LinkedIn Learning s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k LinkedIn Learning
- Můžete povolit uživatelům, aby automaticky získat přihlášení k LinkedIn Learning (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s LinkedIn Learning, potřebujete následující položky:

- Předplatné služby Azure AD
- LinkedIn Learning jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání LinkedIn Learning z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-linkedin-learning-from-the-gallery"></a>Přidání LinkedIn Learning z Galerie
Při konfiguraci integrace LinkedIn Learning do služby Azure AD potřebujete přidat LinkedIn Learning z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat LinkedIn Learning z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **přidat** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **LinkedIn Learning**. Na panelu výsledků klikněte na **LinkedIn Learning** tuto aplikaci přidat.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s LinkedIn Learning podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v LinkedIn Learning je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v LinkedIn Learning musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v LinkedIn Learning.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s LinkedIn Learning, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele LinkedIn Learning](#creating-a-linkedin-learning-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci LinkedIn Learning.

**Ke konfiguraci Azure AD jednotné přihlašování s LinkedIn Learning, proveďte následující kroky:**

1. Na portálu Azure na **LinkedIn Learning** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedin_01.png)

3. V okně prohlížeče jiný web přihlášení jako správce klienta LinkedIn učení.

4. V **centra účtů**, klikněte na tlačítko **globální nastavení** pod **nastavení**. Kromě toho **učení – výchozí** z rozevíracího seznamu.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

5. Klikněte na tlačítko **nebo klikněte na tlačítko sem můžete načíst a zkopírujte jednotlivých polí z formuláře** a zkopírujte **Entity Id** a **adresu Url pro přístup k příjemce Assertion (ACS)**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

6. Na portálu Azure v části **LinkedIn Learning domény a adresy URL**, proveďte následující kroky, pokud chcete nakonfigurovat jednotné přihlašování v **IdP iniciované** režimu

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_signon_01.png)

    a. V **identifikátor** textovému poli, zadejte **Entity ID** zkopírovat z portálu LinkedIn 

    b. V **adresa URL odpovědi** textovému poli, zadejte **adresu Url pro přístup k příjemce Assertion (ACS)** zkopírovat z portálu LinkedIn

7. Pokud chcete nakonfigurovat jednotné přihlašování v **iniciované SP**, klikněte na možnost zobrazit Advanced adresy URL v části Konfigurace a nakonfigurovat následující vzor adresy URL přihlašování:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_signon_02.png)   
    
8. Aplikace LinkedIn Learning očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tento. Výchozí hodnota **uživatelský identifikátor** je **user.userprincipalname** ale LinkedIn Learning očekává to nejde mapovat pomocí e-mailovou adresu uživatele. K tomu můžete použít **user.mail** atribut ze seznamu, nebo použijte hodnotu odpovídajícího atributu na základě konfigurace vaší organizace. 

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinlearning-tutorial/updateusermail.png)
    
9. V **uživatelské atributy** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** a nastavit atributy. Uživatel musí přidat čtyři deklarace identity s názvem **e-mailu**, **oddělení**, **firstname**, a **lastname** a hodnota má být namapována na **user.mail**, **user.department**, **user.givenname**, a **user.surname** v uvedeném pořadí

    | Název atributu | Hodnota atributu |
    | --- | --- |
    | e-mail| User.Mail |    
    | oddělení| User.Department |
    | FirstName| user.givenname |
    | Příjmení| user.surname |
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/userattribute.png)
    
    a. Klikněte na tlačítko **přidat atribut** tím otevřete dialogové okno atribut.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinLearning-tutorial/tutorial_attribute_04.png)

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinLearning-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **Ok**

10. Proveďte následující kroky na **název** atribut -

    a. Klikněte na atribut, který se otevře **Upravit atribut** okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinLearning-tutorial/url_update.png)

    b. Odstranit hodnotu adresy URL **obor názvů**.
    
    c. Klikněte na tlačítko **Ok** uložte nastavení.

11. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_certificate.png)

12. Klikněte na **Uložit**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_400.png)

13. Přejděte na **nastavení správce LinkedIn** části. Nahrajte soubor XML, který jste si stáhli z portálu Azure klikněte na možnost soubor nahrát XML.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

14. Klikněte na tlačítko **na** umožňující jednotného přihlašování. Jednotné přihlašování stav se změní z **Nepřipojeno** k **připojeno**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-linkedin-learning-test-user"></a>Vytvoření zkušebního uživatele LinkedIn Learning

LinkedIn Learning aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatelé jsou automaticky vytvořené v aplikaci. Nastavení na správce stránky na portálu překlopit LinkedIn Learning přepínač **automaticky přiřadit licence** na aktivní těsně v čase zřizování a to bude také licenci uživateli přiřadit. LinkedIn Learning podporuje také zřizování automatické uživatelů, můžete najít další podrobnosti o [sem](active-directory-saas-linkedinlearning-provisioning-tutorial.md) na tom, jak nakonfigurovat uživatele automatické zřizování.

   ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinLearning-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu LinkedIn Learning.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon LinkedIn Learning, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **LinkedIn Learning**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_0001.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici LinkedIn Learning na přístupovém panelu, měli byste obdržet stránku Azure přihlášení a na po úspěšném přihlášení, měli byste obdržet do své aplikace LinkedIn Learning.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfiguraci zřizování uživatelů](active-directory-saas-linkedinlearning-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_203.png