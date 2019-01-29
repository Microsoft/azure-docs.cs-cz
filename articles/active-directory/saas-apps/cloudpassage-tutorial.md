---
title: 'Kurz: Integrace Azure Active Directory s CloudPassage | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: d8bddc309db3ede67586a2067fcf57fbede7d6b3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152786"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Kurz: Integrace Azure Active Directory s CloudPassage

V tomto kurzu se dozvíte, jak integrovat CloudPassage s Azure Active Directory (Azure AD).

CloudPassage integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k CloudPassage
- Můžete povolit uživatelům, aby automaticky získat přihlášení k CloudPassage (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s CloudPassage, potřebujete následující položky:

- Předplatné Azure AD
- CloudPassage jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání CloudPassage z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-cloudpassage-from-the-gallery"></a>Přidání CloudPassage z Galerie
Konfigurace integrace CloudPassage do služby Azure AD, budete muset přidat CloudPassage z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat CloudPassage z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **CloudPassage**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/cloudpassage-tutorial/tutorial_cloudpassage_search.png)

1. Na panelu výsledků vyberte **CloudPassage**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/cloudpassage-tutorial/tutorial_cloudpassage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s CloudPassage podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v CloudPassage je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v CloudPassage potřeba navázat.

V CloudPassage, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s CloudPassage, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele CloudPassage](#creating-a-cloudpassage-test-user)**  – Pokud chcete mít protějšek Britta Simon CloudPassage, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci CloudPassage.

**Ke konfiguraci Azure AD jednotné přihlašování s CloudPassage, proveďte následující kroky:**

1. Na webu Azure Portal na **CloudPassage** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/cloudpassage-tutorial/tutorial_cloudpassage_samlbase.png)

1. Na **CloudPassage domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/cloudpassage-tutorial/tutorial_cloudpassage_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://portal.cloudpassage.com/saml/init/accountid`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://portal.cloudpassage.com/saml/consume/accountid`. Kliknutím můžete získat hodnotu pro tento atribut **nastavení jednotného přihlašování k dokumentaci** v **nastavení jednotného přihlašování** části CloudPassage portálu.

    ![Konfigurace jednotného přihlašování](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečná adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory CloudPassage klienta](https://www.cloudpassage.com/company/contact/) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/cloudpassage-tutorial/tutorial_cloudpassage_certificate.png) 

1. Vaše aplikace CloudPassage očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tuto.
   
   ![Konfigurace jednotného přihlašování](./media/cloudpassage-tutorial/tutorial_cloudpassage_25.png) 

1. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název atributu | Hodnota atributu |
    | --- | --- |
    | jméno |user.givenname |
    | Příjmení |user.surname |
    | e-mail |user.mail |
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/cloudpassage-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurace jednotného přihlašování](./media/cloudpassage-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **OK**.

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/cloudpassage-tutorial/tutorial_general_400.png)
    
1. Na **CloudPassage konfigurace** klikněte na tlačítko **nakonfigurovat CloudPassage** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/cloudpassage-tutorial/tutorial_cloudpassage_configure.png) 

1. V jiném okně prohlížeče přihlašování k webu společnosti CloudPassage jako správce.

1. V nabídce v horní části klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **Správa webu**. 
   
    ![Konfigurace jednotného přihlašování][12]

1. Klikněte na tlačítko **nastavení ověřování** kartu. 
   
    ![Konfigurace jednotného přihlašování][13]

1. V **nastavení jednotného přihlašování** části, proveďte následující kroky: 
   
    ![Konfigurace jednotného přihlašování][14]

    a. Vyberte **povolit jednotné sign-on(SSO) (dokumentace instalace jednotné přihlašování)** zaškrtávací políčko.
    
    b. Vložit **SAML Entity ID** do **URL vystavitele SAML** textového pole.
  
    c. Vložit **SAML jednotné přihlašování – adresa URL služby** do **adresu URL koncového bodu SAML** textového pole.
  
    d. Vložit **odhlašování URL** do **cílová stránka odhlášení** textového pole.
  
    e. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte obsah staženého certifikátu do schránky a vložte jej do **x 509 certifikát** textového pole.
  
    f. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/cloudpassage-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/cloudpassage-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/cloudpassage-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/cloudpassage-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-cloudpassage-test-user"></a>Vytvoření zkušebního uživatele CloudPassage

Cílem této části je vytvořte uživatele Britta Simon v CloudPassage.

**Vytvořte uživatele v CloudPassage jako Britta Simon, proveďte následující kroky:**

1. Přihlášení k vaší **CloudPassage** společnosti serveru jako správce. 

1. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **Správa webu**. 
   
   ![Vytvoření zkušebního uživatele CloudPassage][22] 

1. Klikněte na tlačítko **uživatelé** kartu a potom klikněte na tlačítko **Add New User**. 
   
   ![Vytvoření zkušebního uživatele CloudPassage][23]

1. V **Add New User** části, proveďte následující kroky: 
   
   ![Vytvoření zkušebního uživatele CloudPassage][24]
    
    a. V **křestní jméno** textového pole zadejte Britta. 
  
    b. V **příjmení** textového pole zadejte Simon.
  
    c. V **uživatelské jméno** textového pole **e-mailu** textového pole a **znovu zadejte e-mailu** textového pole zadejte Haniny uživatelské jméno ve službě Azure AD.
  
    d. Jako **typ přístupu**vyberte **povolit přístup z portálu Haló**.
  
    e. Klikněte na tlačítko **Add** (Přidat).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k CloudPassage použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit CloudPassage Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **CloudPassage**.

    ![Konfigurace jednotného přihlašování](./media/cloudpassage-tutorial/tutorial_cloudpassage_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici CloudPassage na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci CloudPassage.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/cloudpassage-tutorial/tutorial_general_04.png
[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

[100]: ./media/cloudpassage-tutorial/tutorial_general_100.png

[200]: ./media/cloudpassage-tutorial/tutorial_general_200.png
[201]: ./media/cloudpassage-tutorial/tutorial_general_201.png
[202]: ./media/cloudpassage-tutorial/tutorial_general_202.png
[203]: ./media/cloudpassage-tutorial/tutorial_general_203.png

