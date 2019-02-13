---
title: 'Kurz: Integrace Azure Active Directory s TeamSeer | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6ad70fd2aefdd0a8e626d22c0b95d83aaaf5f4c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200764"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Kurz: Integrace Azure Active Directory s TeamSeer

V tomto kurzu se dozvíte, jak integrovat TeamSeer s Azure Active Directory (Azure AD).

TeamSeer integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k TeamSeer
- Můžete povolit uživatelům, aby automaticky získat přihlášení k TeamSeer (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TeamSeer, potřebujete následující položky:

- Předplatné Azure AD
- TeamSeer jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání TeamSeer z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-teamseer-from-the-gallery"></a>Přidání TeamSeer z Galerie
Konfigurace integrace TeamSeer ve službě Azure AD, budete muset přidat TeamSeer z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat TeamSeer z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **TeamSeer**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/teamseer-tutorial/tutorial_teamseer_search.png)

1. Na panelu výsledků vyberte **TeamSeer**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/teamseer-tutorial/tutorial_teamseer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s TeamSeer podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v TeamSeer je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v TeamSeer potřeba navázat.

V TeamSeer, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TeamSeer, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele TeamSeer](#creating-a-teamseer-test-user)**  – Pokud chcete mít protějšek Britta Simon TeamSeer, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci TeamSeer.

**Ke konfiguraci Azure AD jednotné přihlašování s TeamSeer, proveďte následující kroky:**

1. Na webu Azure Portal na **TeamSeer** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/teamseer-tutorial/tutorial_teamseer_samlbase.png)

1. Na **TeamSeer domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/teamseer-tutorial/tutorial_teamseer_url.png)

     V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.teamseer.com/<companyid>`

    > [!NOTE] 
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory TeamSeer klienta](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) má být získána hodnota. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/teamseer-tutorial/tutorial_teamseer_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/teamseer-tutorial/tutorial_general_400.png)

1. Na **TeamSeer konfigurace** klikněte na tlačítko **nakonfigurovat TeamSeer** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/teamseer-tutorial/tutorial_teamseer_configure.png)

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti TeamSeer jako správce.

1. Přejděte na **HR správce**.
   
    ![Správce HR](./media/teamseer-tutorial/ic789634.png "HR správce")

1. Klikněte na tlačítko **nastavení**.
   
    ![Instalační program](./media/teamseer-tutorial/ic789635.png "instalační program")

1. Klikněte na tlačítko **nastavit SAML zprostředkovatele podrobnosti**.
   
    ![Nastavení SAML](./media/teamseer-tutorial/ic789636.png "nastavení SAML")

1. V části Podrobnosti SAML zprostředkovatele proveďte následující kroky:
   
    ![Nastavení SAML](./media/teamseer-tutorial/ic789637.png "nastavení SAML")   

    a. Vložit **jednotné přihlašování – adresa URL služby** hodnotu v **URL** textového pole.
          
    b. V poznámkovém bloku otevřete certifikát kódováním base-64, obsah ho zkopírujte do schránky a vložte ho do **veřejný certifikát IdP** textového pole.

1. K dokončení konfigurace poskytovatele SAML, proveďte následující kroky:
    
    ![Nastavení SAML](./media/teamseer-tutorial/ic789638.png "nastavení SAML") 

    a. V **testovací e-mailové adresy**, zadejte e-mailovou adresu testovacího uživatele. 
  
    b. V **vystavitele** textového pole zadejte adresu URL vystavitele poskytovatele služeb. 
  
    c. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/teamseer-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/teamseer-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/teamseer-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/teamseer-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-teamseer-test-user"></a>Vytvoření zkušebního uživatele TeamSeer

Umožňuje uživatelům Azure AD se přihlaste k TeamSeer, musí být poskytnuty v k ShiftPlanning. V případě TeamSeer zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **TeamSeer** společnosti serveru jako správce.

1. Proveďte následující kroky:
   
    ![Správce HR](./media/teamseer-tutorial/ic789640.png "HR správce")  
 
    a. Přejděte na **HR správce \> uživatelé**.
  
    b. Klikněte na tlačítko **spustit Průvodce nového uživatele**.

1. V **podrobnosti o uživateli** části, proveďte následující kroky:
   
    ![Podrobnosti o uživateli](./media/teamseer-tutorial/ic789641.png "podrobnosti o uživateli")

    a. Typ **křestní jméno**, **příjmení**, **uživatelské jméno (e-mailovou adresu)** z platný účet AAD, které chcete zřídit související textová pole v.
  
    b. Klikněte na **Další**.

1. Použijte na obrazovce pokyny pro přidání nového uživatele a klikněte na tlačítko **Dokončit**.

>[!NOTE]
>Můžete použít jakékoli jiné TeamSeer uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných TeamSeer zřízení uživatelských účtů služby Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k TeamSeer použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit TeamSeer Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **TeamSeer**.

    ![Konfigurace jednotného přihlašování](./media/teamseer-tutorial/tutorial_teamseer_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Pokud chcete otestovat jednotné přihlašování – nastavení, otevřete na přístupovém panelu. Další podrobnosti o přístupový Panel najdete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/teamseer-tutorial/tutorial_general_01.png
[2]: ./media/teamseer-tutorial/tutorial_general_02.png
[3]: ./media/teamseer-tutorial/tutorial_general_03.png
[4]: ./media/teamseer-tutorial/tutorial_general_04.png

[100]: ./media/teamseer-tutorial/tutorial_general_100.png

[200]: ./media/teamseer-tutorial/tutorial_general_200.png
[201]: ./media/teamseer-tutorial/tutorial_general_201.png
[202]: ./media/teamseer-tutorial/tutorial_general_202.png
[203]: ./media/teamseer-tutorial/tutorial_general_203.png

