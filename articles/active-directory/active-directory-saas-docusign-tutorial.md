---
title: 'Kurz: Azure Active Directory integrace s DocuSign | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 28c46e7071841552c5bcbf3df38d9d7771b0cfe6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Kurz: Azure Active Directory integrace s DocuSign

V tomto kurzu zjistěte, jak integrovat DocuSign s Azure Active Directory (Azure AD).

Integrace DocuSign s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k DocuSign
- Můžete povolit uživatelům, aby automaticky získat přihlášení k DocuSign (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s DocuSign, potřebujete následující položky:

- Předplatné služby Azure AD
- DocuSign jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání DocuSign z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-docusign-from-the-gallery"></a>Přidání DocuSign z Galerie
Při konfiguraci integrace DocuSign do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS DocuSign z galerie.

**Pokud chcete přidat DocuSign z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **DocuSign**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_search.png)

5. Na panelu výsledků vyberte **DocuSign**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s DocuSign podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v DocuSign je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v DocuSign musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v DocuSign.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s DocuSign, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele DocuSign](#creating-a-docusign-test-user)**  – Pokud chcete mít protějšek Britta Simon v DocuSign propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci DocuSign.

**Ke konfiguraci Azure AD jednotné přihlašování s DocuSign, proveďte následující kroky:**

1. Na portálu Azure na **DocuSign** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_samlbase.png)

3. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base 64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_certificate.png) 

4. Na **DocuSign konfigurace** části portálu Azure, klikněte na tlačítko **konfigurace DocuSign** otevřete konfigurovat přihlašování okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_configure.png)

5. V okně prohlížeče jiný web a přihlášení k vaší **portál pro správu DocuSign** jako správce.

6. V navigační nabídce na levé straně, klikněte na tlačítko **domény**.
   
    ![Konfiguruje se jednotné přihlašování.][51]

7. V pravém podokně klikněte na tlačítko **deklarace identity domény**.
   
    ![Konfiguruje se jednotné přihlašování.][52]

8. Na **deklarace identity domény** dialogové okno, v **název domény** textovému poli, zadejte doménu vaší společnosti a pak klikněte na tlačítko **deklarace identity**. Ujistěte se, že ověření domény a stav je aktivní.
   
    ![Konfiguruje se jednotné přihlašování.][53]

9. V nabídce na levé straně klikněte na tlačítko **poskytovatelů identit**  
   
    ![Konfiguruje se jednotné přihlašování.][54]
10. V pravém podokně klikněte na **přidat zprostředkovatele Identity**. 
   
    ![Konfiguruje se jednotné přihlašování.][55]

11. Na **nastavení zprostředkovatele Identity** proveďte následující kroky:
   
    ![Konfiguruje se jednotné přihlašování.][56]

    a. V **název** textovému poli, zadejte jedinečný název pro svou konfiguraci. Nepoužívejte mezery.

    b. Vložení **SAML Entity ID** do **vystavitele zprostředkovatele Identity** textové pole.

    c. Vložení **SAML jeden přihlašování adresa URL služby** do **adresu URL pro přihlášení zprostředkovatele Identity** textové pole.

    d. Vložení **Sign-Out URL** do **adresa URL odhlašovací zprostředkovatele Identity** textové pole.

    e. Vyberte **přihlásit ověřovacího požadavku**.

    f. Jako **odeslání ověřovacího požadavku pomocí**, vyberte **POST**.

    g. Jako **odeslán požadavek na odhlášení podle**, vyberte **získat**.

12. V **vlastní atribut mapování** zvolte pole, které chcete propojit s Azure AD deklarací identity. V tomto příkladu **emailaddress** deklarace identity je namapována na hodnotu **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Je výchozí název deklarace identity z Azure AD pro deklarace identity e-mailu. 
   
    > [!NOTE]
    > Použít příslušné **uživatelský identifikátor** k mapování uživatelů ze služby Azure AD pro mapování uživatele DocuSign. Vyberte správné pole a zadejte odpovídající hodnotu na základě nastavení vaší organizace.
          
    ![Konfiguruje se jednotné přihlašování.][57]

13. V **certifikát zprostředkovatele Identity** klikněte na tlačítko **přidat certifikát**a pak nahrajte certifikát, který jste si stáhli z portálu Azure AD.   
   
    ![Konfiguruje se jednotné přihlašování.][58]

14. Klikněte na **Uložit**.

15. V **zprostředkovatelů Identity** klikněte na tlačítko **akce**a potom klikněte na **koncové body**.   
   
    ![Konfiguruje se jednotné přihlašování.][59]
 
16. V **zobrazit SAML 2.0 koncové body** části na **portál pro správu DocuSign**, proveďte následující kroky:
   
    ![Konfiguruje se jednotné přihlašování.][60]
   
    a. Kopírování **adresa URL vystavitele pro zprostředkovatele služby**a vložte do **identifikátor** textové pole na **DocuSign domény a adresy URL** části portálu Azure následující vzoru: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>`.
   
    b. Kopírování **adresa URL služby zprostředkovatele přihlášení**a vložte do **přihlašovací adresa URL** textové pole na **DocuSign domény a adresy URL** části portálu Azure následující vzoru: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/`.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_url.png)
      
    c.  Klikněte na tlačítko **zavřít**
    
17. Na portálu Azure klikněte na tlačítko **Uložit**.
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na položku **přidat** otevřete **uživatele** dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-docusign-test-user"></a>Vytvoření zkušebního uživatele DocuSign

Aplikace podporuje **těsně v zřizování uživatelů čas** a po ověření uživatelé jsou automaticky vytvořené v aplikaci.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup k DocuSign Azure jednotné přihlašování.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon DocuSign, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **DocuSign**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici DocuSign na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci DocuSign.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfiguraci zřizování uživatelů](active-directory-saas-docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png
[100]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_203.png

