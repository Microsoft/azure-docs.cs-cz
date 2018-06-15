---
title: 'Kurz: Azure Active Directory integrace s TeamSeer | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: b4fd5b65bf0f5ce90aa93f89aad4617976b3a490
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350844"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Kurz: Azure Active Directory integrace s TeamSeer

V tomto kurzu zjistěte, jak integrovat TeamSeer s Azure Active Directory (Azure AD).

Integrace TeamSeer s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k TeamSeer
- Můžete povolit uživatelům, aby automaticky získat přihlášení k TeamSeer (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TeamSeer, potřebujete následující položky:

- Předplatné služby Azure AD
- TeamSeer jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání TeamSeer z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-teamseer-from-the-gallery"></a>Přidání TeamSeer z Galerie
Chcete-li nakonfigurovat integraci TeamSeer v do Azure AD, přidejte TeamSeer z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat TeamSeer z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **TeamSeer**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_search.png)

5. Na panelu výsledků vyberte **TeamSeer**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s TeamSeer podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v TeamSeer je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v TeamSeer musí navázat.

V TeamSeer, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TeamSeer, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele TeamSeer](#creating-a-teamseer-test-user)**  – Pokud chcete mít protějšek Britta Simon v TeamSeer propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci TeamSeer.

**Ke konfiguraci Azure AD jednotné přihlašování s TeamSeer, proveďte následující kroky:**

1. Na portálu Azure na **TeamSeer** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_samlbase.png)

3. Na **TeamSeer domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_url.png)

     V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.teamseer.com/<companyid>`

    > [!NOTE] 
    > Hodnota není skutečné. Aktualizujte hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory TeamSeer klienta](http://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) k získání hodnoty. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-teamseer-tutorial/tutorial_general_400.png)

6. Na **TeamSeer konfigurace** klikněte na tlačítko **konfigurace TeamSeer** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_configure.png)

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti TeamSeer jako správce.

8. Přejděte na **HR správce**.
   
    ![Správce HR](./media/active-directory-saas-teamseer-tutorial/ic789634.png "HR správce")

9. Klikněte na tlačítko **instalační program**.
   
    ![Instalační program](./media/active-directory-saas-teamseer-tutorial/ic789635.png "instalační program")

10. Klikněte na tlačítko **nastavit podrobné informace o poskytovateli SAML**.
   
    ![Nastavení SAML](./media/active-directory-saas-teamseer-tutorial/ic789636.png "nastavení SAML")

11. V oddílu Podrobnosti zprostředkovatele SAML proveďte následující kroky:
   
    ![Nastavení SAML](./media/active-directory-saas-teamseer-tutorial/ic789637.png "nastavení SAML")   

    a. Vložení **jeden přihlašování adresa URL služby** hodnotu v **URL** textové pole.
          
    b. V poznámkovém bloku otevřete kódovaného certifikátu vaší kódování base-64, obsah ho zkopírujte do schránky a vložte jej do **veřejný certifikát IdP** textové pole.

12. K dokončení konfigurace zprostředkovatele SAML, proveďte následující kroky:
    
    ![Nastavení SAML](./media/active-directory-saas-teamseer-tutorial/ic789638.png "nastavení SAML") 

    a. V **testovací e-mailové adresy**, zadejte testovacího uživatele e-mailovou adresu. 
  
    b. V **vystavitele** textovému poli, zadejte adresu URL vystavitele poskytovatele služeb. 
  
    c. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-teamseer-test-user"></a>Vytvoření zkušebního uživatele TeamSeer

Pokud chcete povolit uživatelům Azure AD přihlášení k TeamSeer, se musí být zřízená v k ShiftPlanning. V případě TeamSeer zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **TeamSeer** společnosti lokality jako správce.

2. Proveďte následující kroky:
   
    ![Správce HR](./media/active-directory-saas-teamseer-tutorial/ic789640.png "HR správce")  
 
    a. Přejděte na **HR správce \> uživatelé**.
  
    b. Klikněte na tlačítko **spustit Průvodce novým uživatelem**.

3. V **podrobné informace o uživateli** část, proveďte následující kroky:
   
    ![Podrobné informace o uživateli](./media/active-directory-saas-teamseer-tutorial/ic789641.png "podrobné informace o uživateli")

    a. Typ **křestní jméno**, **Přezdívka**, **uživatelské jméno (e-mailovou adresu)** platného účtu AAD chcete zřídit v k související textových polí.
  
    b. Klikněte na **Další**.

4. Postupujte podle na obrazovce pokyny pro přidání nového uživatele a klikněte na tlačítko **Dokončit**.

>[!NOTE]
>Můžete použít všechny ostatní TeamSeer uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované TeamSeer ke zřízení uživatelských účtů Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu TeamSeer.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon TeamSeer, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **TeamSeer**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Pokud chcete testovat vaše nastavení jednotného přihlašování, otevřete Panel přístupu. Další podrobnosti o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_203.png

