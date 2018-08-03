---
title: 'Kurz: Integrace Azure Active Directory s Igloo softwarem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Igloo softwaru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: d49a08c6f57f5248f17539cd9d0467d132f7a63d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447403"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Kurz: Integrace Azure Active Directory s Igloo softwaru

V tomto kurzu se dozvíte, jak integrovat Igloo Software s Azure Active Directory (Azure AD).

Integrace softwaru Igloo s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k softwaru Igloo
- Uživatele, aby automaticky získat přihlášení k softwaru Igloo (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Igloo softwaru, potřebujete následující položky:

- S předplatným služby Azure AD
- Softwaru Igloo jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Igloo softwaru z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-igloo-software-from-the-gallery"></a>Přidání Igloo softwaru z Galerie
Konfigurace integrace Igloo softwaru do služby Azure AD, budete muset přidat Igloo Software z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Igloo softwaru z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Igloo softwaru**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/igloo-software-tutorial/tutorial_igloosoftware_search.png)

1. Na panelu výsledků vyberte **Igloo softwaru**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Igloo Software založený na uživateli test "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek Igloo softwaru je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské Igloo softwaru je potřeba navázat.

V softwaru Igloo přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Igloo softwaru, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytváří se testovací uživatelské jméno Igloo softwaru](#creating-an-igloo-software-test-user)**  – Pokud chcete mít protějšek Britta Simon Igloo Software, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Igloo softwaru.

**Ke konfiguraci Azure AD jednotné přihlašování s Igloo softwaru, proveďte následující kroky:**

1. Na webu Azure Portal na **Igloo softwaru** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

1. Na **Igloo softwaru domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company name>.igloocommmunities.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company name>.igloocommmunities.com/saml.digest`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory Igloo softwarového klienta](https://www.igloosoftware.com/services/support) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/igloo-software-tutorial/tutorial_general_400.png)
    
1. Na **konfigurace softwaru Igloo** klikněte na tlačítko **konfigurace softwaru Igloo** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k serveru vaší společnosti Igloo softwaru jako správce.

1. Přejděte **ovládací panely**.
   
     ![Ovládací panely](./media/igloo-software-tutorial/ic799949.png "ovládací panely")

1. V **členství** klikněte na tlačítko **přihlašování v nastavení**.
   
    ![Nastavení přihlášení](./media/igloo-software-tutorial/ic783968.png "nastavení přihlášení")

1. V části Konfigurace SAML klikněte na tlačítko **konfigurace ověřování SAML**.
   
    ![Konfigurace SAML](./media/igloo-software-tutorial/ic783969.png "konfigurace SAML")
   
1. V **obecné konfigurace** části, proveďte následující kroky:
   
    ![Obecná konfigurace](./media/igloo-software-tutorial/ic783970.png "Obecná konfigurace")

    a. V **název připojení** textového pole zadejte vlastní název pro vaši konfiguraci.
   
    b. V **přihlašovací adresa URL zprostředkovatele identity** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.
   
    c. V **odhlašovací adresa URL zprostředkovatele identity** textového pole vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.
    
    d. Vyberte **odpovědi na odhlášení a typ požadavku HTTP** jako **příspěvek**.
   
    e. Otevřete váš **base-64** kódovaného certifikátu v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a vložte ho do **veřejný certifikát** textového pole.
    
1. V **odpovědi a konfigurace ověřování**, proveďte následující kroky:
    
    ![Odpovědi a konfigurace ověřování](./media/igloo-software-tutorial/IC783971.png "odpovědi a konfigurace ověřování")
  
      a. Jako **zprostředkovatele Identity**vyberte **Microsoft ADFS**.
      
      b. Jako **typ identifikátoru**vyberte **e-mailovou adresu**. 

      c. V **atribut e-mailové** textové pole, typ **emailaddress**.

      d. V **křestní jméno atributu** textové pole, typ **givenname**.

      e. V **poslední název atributu** textové pole, typ **příjmení**.

1. Proveďte následující kroky k dokončení konfigurace:
    
    ![Vytvoření uživatele na přihlašovací](./media/igloo-software-tutorial/IC783972.png "vytvoření uživatele na přihlašovací") 

     a. Jako **vytvoření uživatele na přihlašovací**vyberte **vytvořte nového uživatele ve vaší lokalitě při přihlášení**.

     b. Jako **nastavení přihlášení**vyberte **SAML použijte tlačítko na obrazovce "Sign in"**.

     c. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/igloo-software-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/igloo-software-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/igloo-software-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/igloo-software-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-igloo-software-test-user"></a>Vytvoření Igloo Software testovacího uživatele

Neexistuje žádná položka akce konfigurace zřizování uživatelů pro Igloo softwaru.  

Přiřazený uživatel se pokusí přihlásit k softwaru Igloo pomocí přístupového panelu, Igloo Software kontroluje, zda uživatel existuje.  Pokud není žádný uživatelský účet k dispozici dosud, vytvoří se automaticky Igloo softwarem.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k softwaru Igloo.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Igloo softwaru, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Igloo softwaru**.

    ![Konfigurace jednotného přihlašování](./media/igloo-software-tutorial/tutorial_igloosoftware_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Igloo softwaru na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Igloo softwaru.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/igloo-software-tutorial/tutorial_general_01.png
[2]: ./media/igloo-software-tutorial/tutorial_general_02.png
[3]: ./media/igloo-software-tutorial/tutorial_general_03.png
[4]: ./media/igloo-software-tutorial/tutorial_general_04.png

[100]: ./media/igloo-software-tutorial/tutorial_general_100.png

[200]: ./media/igloo-software-tutorial/tutorial_general_200.png
[201]: ./media/igloo-software-tutorial/tutorial_general_201.png
[202]: ./media/igloo-software-tutorial/tutorial_general_202.png
[203]: ./media/igloo-software-tutorial/tutorial_general_203.png

