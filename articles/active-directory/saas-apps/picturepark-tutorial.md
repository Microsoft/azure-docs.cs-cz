---
title: 'Kurz: Integrace Azure Active Directory s Picturepark | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: jeedes
ms.openlocfilehash: 87b910b627b6d9549348851fe98e2037e87317c0
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821596"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Kurz: Integrace Azure Active Directory s Picturepark

V tomto kurzu se dozvíte, jak integrovat Picturepark s Azure Active Directory (Azure AD).

Picturepark integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Picturepark
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Picturepark (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Picturepark, potřebujete následující položky:

- Předplatné Azure AD
- Picturepark jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Picturepark z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-picturepark-from-the-gallery"></a>Přidání Picturepark z Galerie
Konfigurace integrace Picturepark do služby Azure AD, budete muset přidat Picturepark z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Picturepark z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Picturepark**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/picturepark-tutorial/tutorial_picturepark_search.png)

1. Na panelu výsledků vyberte **Picturepark**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/picturepark-tutorial/tutorial_picturepark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Picturepark podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Picturepark je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Picturepark potřeba navázat.

V Picturepark, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Picturepark, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Picturepark](#creating-a-picturepark-test-user)**  – Pokud chcete mít protějšek Britta Simon Picturepark, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Picturepark.

**Ke konfiguraci Azure AD jednotné přihlašování s Picturepark, proveďte následující kroky:**

1. Na webu Azure Portal na **Picturepark** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/picturepark-tutorial/tutorial_picturepark_samlbase.png)

1. Na **Picturepark domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/picturepark-tutorial/tutorial_picturepark_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.picturepark.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: 
    
    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Picturepark klienta](https://picturepark.com/about/contact/) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Konfigurace jednotného přihlašování](./media/picturepark-tutorial/tutorial_picturepark_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/picturepark-tutorial/tutorial_general_400.png)

1. Na **Picturepark konfigurace** klikněte na tlačítko **nakonfigurovat Picturepark** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/picturepark-tutorial/tutorial_picturepark_configure.png) 

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Picturepark.

1. Na panelu nástrojů v horní části klikněte na tlačítko **nástroje pro správu**a potom klikněte na tlačítko **Konzola pro správu**.
   
    ![Konzola pro správu](./media/picturepark-tutorial/ic795062.png "Konzola pro správu")

1. Klikněte na tlačítko **ověřování**a potom klikněte na tlačítko **zprostředkovatelé Identity**.
   
    ![Ověřování](./media/picturepark-tutorial/ic795063.png "ověřování")

1. V **konfigurace zprostředkovatele Identity** části, proveďte následující kroky:
   
    ![Konfigurace zprostředkovatele identity](./media/picturepark-tutorial/ic795064.png "konfigurace zprostředkovatele Identity")
   
    a. Klikněte na tlačítko **Add** (Přidat).
  
    b. Zadejte název pro vaši konfiguraci.
   
    c. Vyberte **nastavit jako výchozí**.
   
    d. V **identifikátor URI vystavitele** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.
   
    e. V **důvěryhodného vystavitele tisk miniatury** textového pole vložte hodnotu **kryptografický otisk** zkopírovanou z **podpisový certifikát SAML** oddílu. 

1. Klikněte na tlačítko **JoinDefaultUsersGroup**.

1. Nastavit **Emailaddress** atribut **deklarace identity** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` a klikněte na tlačítko **Uložit**.

      ![Konfigurace](./media/picturepark-tutorial/ic795065.png "konfigurace")

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/picturepark-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/picturepark-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/picturepark-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/picturepark-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-picturepark-test-user"></a>Vytvoření zkušebního uživatele Picturepark

Chcete-li povolit uživatele Azure AD k přihlášení do Picturepark, musí být poskytnuty do Picturepark. V případě Picturepark zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **Picturepark** tenanta.

1. Na panelu nástrojů v horní části klikněte na tlačítko **nástroje pro správu**a potom klikněte na tlačítko **uživatelé**.
   
    ![Uživatelé](./media/picturepark-tutorial/ic795067.png "uživatelů")

1. V **přehled uživatelů** klikněte na tlačítko **nový**.
   
    ![Správa uživatelů](./media/picturepark-tutorial/ic795068.png "Správa uživatelů")

1. Na **vytvořit uživatele** dialogového okna, proveďte následující kroky platného Azure Active Directory uživatele chcete ke zřízení:
   
    ![Vytvoření uživatele](./media/picturepark-tutorial/ic795069.png "vytvoření uživatele")
   
    a. V **e-mailovou adresu** textové pole, typ **e-mailová adresa** uživatele **BrittaSimon@contoso.com**.  
   
    b. V **heslo** a **potvrzení hesla** textová pole, typ **heslo** z BrittaSimon. 
   
    c. V **křestní jméno** textové pole, typ **křestní jméno** uživatele **Britta**. 
   
    d. V **příjmení** textové pole, typ **příjmení** uživatele **Simon**.
   
    e. V **společnosti** textové pole, typ **název společnosti** uživatele. 
   
    f. V **země** textové pole, vyberte **země** uživatele.
  
    g. V **ZIP** textové pole, typ **PSČ** města.
   
    h. V **Město** textové pole, typ **název města** uživatele.

    i. Vyberte **jazyka**.
   
    j. Klikněte na možnost **Vytvořit**.

>[!NOTE]
>Můžete použít jakékoli jiné Picturepark uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Picturepark zřízení uživatelských účtů služby Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Picturepark použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Picturepark Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Picturepark**.

    ![Konfigurace jednotného přihlašování](./media/picturepark-tutorial/tutorial_picturepark_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Picturepark na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Picturepark. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/picturepark-tutorial/tutorial_general_01.png
[2]: ./media/picturepark-tutorial/tutorial_general_02.png
[3]: ./media/picturepark-tutorial/tutorial_general_03.png
[4]: ./media/picturepark-tutorial/tutorial_general_04.png

[100]: ./media/picturepark-tutorial/tutorial_general_100.png

[200]: ./media/picturepark-tutorial/tutorial_general_200.png
[201]: ./media/picturepark-tutorial/tutorial_general_201.png
[202]: ./media/picturepark-tutorial/tutorial_general_202.png
[203]: ./media/picturepark-tutorial/tutorial_general_203.png

