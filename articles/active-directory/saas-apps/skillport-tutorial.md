---
title: 'Kurz: Integrace Azure Active Directory s Skillport | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Skillport.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4df349b2-a73f-4b88-a077-ec0fbfc26527
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: d6b80855fa301da5d2e5be37b1b316e5a0af81d1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171962"
---
# <a name="tutorial-azure-active-directory-integration-with-skillport"></a>Kurz: Integrace Azure Active Directory s Skillport

V tomto kurzu se dozvíte, jak integrovat Skillport s Azure Active Directory (Azure AD).

Skillport integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Skillport
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Skillport (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Skillport, potřebujete následující položky:

- Předplatné Azure AD
- Skillport jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Skillport z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-skillport-from-the-gallery"></a>Přidání Skillport z Galerie
Konfigurace integrace Skillport do služby Azure AD, budete muset přidat Skillport z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Skillport z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Skillport**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/skillport-tutorial/tutorial_skillport_search.png)

1. Na panelu výsledků vyberte **Skillport**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/skillport-tutorial/tutorial_skillport_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Skillport podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Skillport je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Skillport potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Skillport.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Skillport, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Skillport](#creating-a-skillport-test-user)**  – Pokud chcete mít protějšek Britta Simon Skillport, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Skillport.

**Ke konfiguraci Azure AD jednotné přihlašování s Skillport, proveďte následující kroky:**

1. Na webu Azure Portal na **Skillport** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/skillport-tutorial/tutorial_skillport_samlbase.png)

1. Na **Skillport domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/skillport-tutorial/tutorial_skillport_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL:
      
      Datové centrum Evropa: `https://adfs.skillport.eu`
   
      Datacentra v USA: `https://sso.skillport.com`

    b. V **identifikátor** textového pole zadejte adresu URL:
      
      Datové centrum Evropa: `http://adfs.skillport.eu/adfs/services/trust`
   
      Datacentra v USA: `https://sso.skillport.com`
   
    c. V **adresy URL odpovědi** textového pole zadejte adresu URL:
    
      Datové centrum Evropa: ` https://adfs.skillport.eu/adfs/ls/`
    
      Datacentra v USA: `https://sso.skillport.com/sp/ACS.saml2`
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/skillport-tutorial/tutorial_skillport_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/skillport-tutorial/tutorial_general_400.png)

1. Ke konfiguraci jednotného přihlašování na **Skillport** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory Skillport](https://www.skillsoft.com/contact.asp). To bude nastavené jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/skillport-tutorial/create_aaduser_01.png) 

1. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/skillport-tutorial/create_aaduser_02.png) 

1. V horní části dialogového okna, klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/skillport-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/skillport-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-skillport-test-user"></a>Vytvoření zkušebního uživatele Skillport

Chcete-li vytvořit testovacího uživatele Skillport, budete muset požádat [tým podpory Skillport](https://www.skillsoft.com/contact.asp) mají více obchodních scénářů podle požadavku koncového uživatele. Nakonfiguruje ho po diskuze s uživateli.  

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Skillport použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Skillport Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Skillport**.

    ![Konfigurace jednotného přihlašování](./media/skillport-tutorial/tutorial_skillport_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Skillport na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Skillport.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skillport-tutorial/tutorial_general_01.png
[2]: ./media/skillport-tutorial/tutorial_general_02.png
[3]: ./media/skillport-tutorial/tutorial_general_03.png
[4]: ./media/skillport-tutorial/tutorial_general_04.png

[100]: ./media/skillport-tutorial/tutorial_general_100.png

[200]: ./media/skillport-tutorial/tutorial_general_200.png
[201]: ./media/skillport-tutorial/tutorial_general_201.png
[202]: ./media/skillport-tutorial/tutorial_general_202.png
[203]: ./media/skillport-tutorial/tutorial_general_203.png

