---
title: 'Kurz: Integrace Azure Active Directory se službou DocuSign | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 05ec113db5fbdc0f2ea7d1f176c9be654f53a946
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39053338"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Kurz: Integrace Azure Active Directory se službou DocuSign

V tomto kurzu se dozvíte, jak integrovat DocuSign s Azure Active Directory (Azure AD).

Integrace DocuSign s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k DocuSign
- Můžete povolit uživatelům, aby automaticky získat přihlášení k DocuSign (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD službou DocuSign, potřebujete následující položky:

- S předplatným služby Azure AD
- DocuSign jediného přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání DocuSign z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-docusign-from-the-gallery"></a>Přidání DocuSign z Galerie
Konfigurace integrace DocuSign do služby Azure AD, budete muset přidat DocuSign z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat DocuSign z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **DocuSign**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/docusign-tutorial/tutorial_docusign_search.png)

5. Na panelu výsledků vyberte **DocuSign**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí DocuSign podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v DocuSign je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v DocuSign.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v DocuSign.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování službou DocuSign, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele DocuSign](#creating-a-docusign-test-user)**  – Pokud chcete mít protějšek Britta Simon v DocuSign, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci DocuSign.

**Ke konfiguraci Azure AD jednotného přihlašování službou DocuSign, proveďte následující kroky:**

1. Na webu Azure Portal na **DocuSign** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/docusign-tutorial/tutorial_docusign_samlbase.png)

3. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base 64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

4. Na **DocuSign konfigurace** části webu Azure portal, klikněte na tlačítko **nakonfigurovat DocuSign** konfigurovat přihlašování – okno. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**
    
    ![Konfigurace jednotného přihlašování](./media/docusign-tutorial/tutorial_docusign_configure.png)

5. V okně jiné webové prohlížeče, přihlaste se k vaší **portál pro správu DocuSign** jako správce.

6. V navigační nabídce na levé straně klikněte na tlačítko **domén**.
   
    ![Konfiguruje se jednotné přihlašování.][51]

7. V pravém podokně klikněte na tlačítko **domény deklaraci identity**.
   
    ![Konfiguruje se jednotné přihlašování.][52]

8. Na **deklarace identity domény** dialogového okna v **název domény** textového pole zadejte doménu vaší společnosti a potom klikněte na tlačítko **deklarace identity**. Ujistěte se, že ověření domény a je ve stavu aktivní.
   
    ![Konfiguruje se jednotné přihlašování.][53]

9. V nabídce na levé straně klikněte na tlačítko **zprostředkovatelů Identity**  
   
    ![Konfiguruje se jednotné přihlašování.][54]
10. V pravém podokně klikněte na tlačítko **přidat zprostředkovatele Identity**. 
   
    ![Konfiguruje se jednotné přihlašování.][55]

11. Na **nastavení zprostředkovatele Identity** stránce, proveďte následující kroky:
   
    ![Konfiguruje se jednotné přihlašování.][56]

    a. V **název** textového pole zadejte jedinečný název pro vaši konfiguraci. Nepoužívejte mezery.

    b. Vložit **SAML Entity ID** do **Vystavitel zprostředkovatele Identity** textového pole.

    c. Vložit **SAML jednotné přihlašování – adresa URL služby** do **přihlašovací adresa URL zprostředkovatele Identity** textového pole.

    d. Vložit **odhlašování URL** do **odhlašovací adresa URL zprostředkovatele Identity** textového pole.

    e. Vyberte **podepsat ověřovací požadavek**.

    f. Jako **žádost odeslat ověřovací**vyberte **příspěvek**.

    g. Jako **žádost odeslat odhlášení**vyberte **získat**.

12. V **mapování vlastního atributu** zvolte pole, které chcete propojit s deklarací identity Azure AD. V tomto příkladu **emailaddress** deklarací identity je namapována na žádnou hodnotu **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Je výchozí název deklarace identity z Azure AD pro deklarace identity e-mailu. 
   
    > [!NOTE]
    > Použít příslušné **identifikátor uživatele** mapovat uživatele ze služby Azure AD pro mapování uživatele DocuSign. Vyberte správné pole a zadejte příslušnou hodnotu na základě svého nastavení organizace.
          
    ![Konfiguruje se jednotné přihlašování.][57]

13. V **certifikát poskytovatele Identity** klikněte na tlačítko **přidat certifikát**a pak nahrajte certifikát, který jste si stáhli z portálu Azure AD.   
   
    ![Konfiguruje se jednotné přihlašování.][58]

14. Klikněte na **Uložit**.

15. V **zprostředkovatelé Identity** klikněte na tlačítko **akce**a potom klikněte na tlačítko **koncové body**.   
   
    ![Konfiguruje se jednotné přihlašování.][59]
 
16. V **zobrazit SAML 2.0 koncové body** části na **portál pro správu DocuSign**, proveďte následující kroky:
   
    ![Konfiguruje se jednotné přihlašování.][60]
   
    a. Kopírovat **URL vystavitele poskytovatele služby**a vložte do **identifikátor** textovým polem na **DocuSign domény a adresy URL** tématu na webu Azure portal následující vzor: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>`.
   
    b. Kopírování **přihlašovací adresa URL služby zprostředkovatele**a vložte do **přihlašovací adresa URL** textovým polem na **DocuSign domény a adresy URL** tématu na webu Azure portal následující vzor: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/` .

    ![Konfigurace jednotného přihlašování](./media/docusign-tutorial/tutorial_docusign_url.png)
      
    c.  Klikněte na tlačítko **zavřít**
    
17. Na webu Azure portal, klikněte na tlačítko **Uložit**.
    
    ![Konfigurace jednotného přihlašování](./media/docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/docusign-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/docusign-tutorial/create_aaduser_02.png) 

3. V horní části dialogového okna, klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/docusign-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/docusign-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-docusign-test-user"></a>Vytvoření zkušebního uživatele DocuSign

Aplikace podporuje **čas zřizování uživatelů podle potřeby** a poté, co se v aplikaci automaticky vytvoří uživatele s ověřováním.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k DocuSign.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon DocuSign, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **DocuSign**.

    ![Konfigurace jednotného přihlašování](./media/docusign-tutorial/tutorial_docusign_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici DocuSign na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci DocuSign.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/docusign-tutorial/tutorial_general_01.png
[2]: ./media/docusign-tutorial/tutorial_general_02.png
[3]: ./media/docusign-tutorial/tutorial_general_03.png
[4]: ./media/docusign-tutorial/tutorial_general_04.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[100]: ./media/docusign-tutorial/tutorial_general_100.png

[200]: ./media/docusign-tutorial/tutorial_general_200.png
[201]: ./media/docusign-tutorial/tutorial_general_201.png
[202]: ./media/docusign-tutorial/tutorial_general_202.png
[203]: ./media/docusign-tutorial/tutorial_general_203.png

