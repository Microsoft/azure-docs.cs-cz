---
title: 'Kurz: Integrace Azure Active Directory s Workfront | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Workfront.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: aab8bd2f-f9dd-42da-a18e-d707865687d7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: d6e6914002bd5be7f8fff4822f383fc5ac058f87
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814915"
---
# <a name="tutorial-azure-active-directory-integration-with-workfront"></a>Kurz: Integrace Azure Active Directory s Workfront

V tomto kurzu se dozvíte, jak integrovat Workfront s Azure Active Directory (Azure AD).

Workfront integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Workfront
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Workfront (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Workfront, potřebujete následující položky:

- Předplatné Azure AD
- Workfront jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Workfront z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-workfront-from-the-gallery"></a>Přidání Workfront z Galerie
Konfigurace integrace Workfront do služby Azure AD, budete muset přidat Workfront z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Workfront z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Workfront**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/workfront-tutorial/tutorial_workfront_search.png)

1. Na panelu výsledků vyberte **Workfront**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/workfront-tutorial/tutorial_workfront_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s Workfront podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Workfront je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Workfront potřeba navázat.

V Workfront, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Workfront, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Workfront](#creating-a-workfront-test-user)**  – Pokud chcete mít protějšek Britta Simon Workfront, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Workfront.

**Ke konfiguraci Azure AD jednotné přihlašování s Workfront, proveďte následující kroky:**

1. Na webu Azure Portal na **Workfront** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/workfront-tutorial/tutorial_workfront_samlbase.png)

1. Na **Workfront domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/workfront-tutorial/tutorial_workfront_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.attask-ondemand.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.attasksandbox.com/SAML2`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Workfront klienta](https://www.workfront.com/services-and-support) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/workfront-tutorial/tutorial_workfront_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/workfront-tutorial/tutorial_general_400.png)

1. Na **Workfront konfigurace** klikněte na tlačítko **nakonfigurovat Workfront** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/workfront-tutorial/tutorial_workfront_configure.png) 

1. Přihlašování k webu společnosti Workfront jako správce.

1. Přejděte na **jednotné přihlašování v konfiguraci**.

1. Na **Single Sign-On** dialogového okna, proveďte následující kroky
    
    ![Konfigurace jednotného přihlašování][23]
   
    a. Jako **typ**vyberte **SAML 2.0**.
   
    b. Vyberte **ID poskytovatele služby**.
   
    c. Vložit **SAML jednotné přihlašování – adresa URL služby** do **přihlašovací adresa URL portálu** textového pole.
   
    d. Vložit **adresu URL jednotného odhlašování služby** do **odhlašování URL** textového pole.
   
    e. Vložit **heslo změnit adresu URL** do **heslo změnit adresu URL** textového pole.
   
    f. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/workfront-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/workfront-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/workfront-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/workfront-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-workfront-test-user"></a>Vytvoření zkušebního uživatele Workfront

Cílem této části je vytvořte uživatele Britta Simon v Workfront.

**Vytvořte uživatele v Workfront jako Britta Simon, proveďte následující kroky:**

1. Přihlaste se k webu společnosti Workfront jako správce.
1. V nabídce v horní části klikněte na tlačítko **lidé**.
1. Klikněte na tlačítko **nové osobě**. 
1. V dialogovém okně Nový uživatel proveďte následující kroky:
   
    ![Vytvořit testovacího uživatele Workfront][21] 
   
    a. V **křestní jméno** textového pole zadejte "Britta."
   
    b. V **příjmení** textového pole zadejte "Simon."
   
    c. V **e-mailovou adresu** textového pole zadejte Britta Simon e-mailovou adresu v Azure Active Directory.
   
    d. Klikněte na tlačítko **přidat osobu**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Workfront použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Workfront Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Workfront**.

    ![Konfigurace jednotného přihlašování](./media/workfront-tutorial/tutorial_workfront_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Workfront na přístupovém panelu, měli byste obdržet přihlašovací stránku Workfront aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/workfront-tutorial/tutorial_general_01.png
[2]: ./media/workfront-tutorial/tutorial_general_02.png
[3]: ./media/workfront-tutorial/tutorial_general_03.png
[4]: ./media/workfront-tutorial/tutorial_general_04.png
[21]:./media/workfront-tutorial/tutorial_attask_08.png
[23]:./media/workfront-tutorial/tutorial_attask_06.png
[100]: ./media/workfront-tutorial/tutorial_general_100.png

[200]: ./media/workfront-tutorial/tutorial_general_200.png
[201]: ./media/workfront-tutorial/tutorial_general_201.png
[202]: ./media/workfront-tutorial/tutorial_general_202.png
[203]: ./media/workfront-tutorial/tutorial_general_203.png

