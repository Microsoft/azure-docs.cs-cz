---
title: 'Kurz: Integrace Azure Active Directory se službou Samanage | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Samanage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 118ab72c9afc13c5792f229f9c7bc61d226553d5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420570"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Kurz: Integrace Azure Active Directory se službou Samanage

V tomto kurzu se dozvíte, jak integrovat Samanage s Azure Active Directory (Azure AD).

Samanage integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Samanage
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Samanage (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Samanage, potřebujete následující položky:

- S předplatným služby Azure AD
- Samanage jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Samanage z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-samanage-from-the-gallery"></a>Přidání Samanage z Galerie
Konfigurace integrace Samanage do služby Azure AD, budete muset přidat Samanage z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Samanage z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Samanage**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/samanage-tutorial/tutorial_samanage_search.png)

1. Na panelu výsledků vyberte **Samanage**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/samanage-tutorial/tutorial_samanage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Samanage podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Samanage je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Samanage potřeba navázat.

V Samanage, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Samanage, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Samanage](#creating-a-samanage-test-user)**  – Pokud chcete mít protějšek Britta Simon Samanage, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Samanage.

**Ke konfiguraci Azure AD jednotné přihlašování s Samanage, proveďte následující kroky:**

1. Na webu Azure Portal na **Samanage** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/samanage-tutorial/tutorial_samanage_samlbase.png)

1. Na **Samanage domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/samanage-tutorial/tutorial_samanage_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor, který je vysvětlen později v tomto kurzu. Další podrobnosti získáte [tým podpory Samanage klienta](https://www.samanage.com/support).    
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/samanage-tutorial/tutorial_samanage_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/samanage-tutorial/tutorial_general_400.png)

1. Na **Samanage konfigurace** klikněte na tlačítko **nakonfigurovat Samanage** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML Entity ID** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/samanage-tutorial/tutorial_samanage_configure.png) 

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Samanage.

1. Klikněte na tlačítko **řídicí panel** a vyberte **nastavení** v levém navigačním podokně.
   
    ![Řídicí panel](./media/samanage-tutorial/tutorial_samanage_001.png "řídicího panelu")

1. Klikněte na tlačítko **jednotného přihlašování**.
   
    ![Jednotné přihlašování](./media/samanage-tutorial/tutorial_samanage_002.png "jednotného přihlašování")

1. Přejděte do **přihlášení pomocí SAML** části, proveďte následující kroky:
   
    ![Přihlášení pomocí SAML](./media/samanage-tutorial/tutorial_samanage_003.png "přihlášení pomocí SAML")
 
    a. Klikněte na tlačítko **povolit jednotné přihlašování pomocí SAML**.  
 
    b. V **adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal.    
 
    c. Potvrďte **přihlašovací adresa URL** odpovídá **přihlašovací adresa URL** z **Samanage domény a adresy URL** části webu Azure Portal.
 
    d. V **odhlašovací adresa URL** textového pole zadejte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.
 
    e. V **SAML vystavitele** textové pole, zadejte identifikátor URI id aplikace nastavení ve zprostředkovateli identity.
 
    f. Otevření certifikátu kódováním base-64 stáhnout z webu Azure portal v programu Poznámkový blok, zkopírujte obsah ho do schránky a a vložte ho do **vložte vašeho zprostředkovatele Identity x.509 Certificate níže** textového pole.
 
    g. Klikněte na tlačítko **vytvořit uživatele, pokud ještě neexistují v Samanage**.
 
    h. Klikněte na tlačítko **aktualizace**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/samanage-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/samanage-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/samanage-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/samanage-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-samanage-test-user"></a>Vytvoření zkušebního uživatele Samanage

Přihlaste se k Samanage Azure AD uživatelům umožnit, musí být poskytnuty do Samanage.  
V případě Samanage zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlášení na webu společnosti Samanage jako správce.

1. Klikněte na tlačítko **řídicí panel** a vyberte **nastavení** v levém navigačním podokně.
   
    ![Instalační program](./media/samanage-tutorial/tutorial_samanage_001.png "instalační program")

1. Klikněte na tlačítko **uživatelé** kartu
   
    ![Uživatelé](./media/samanage-tutorial/tutorial_samanage_006.png "uživatelů")

1. Klikněte na tlačítko **nového uživatele**.
   
    ![Nový uživatel](./media/samanage-tutorial/tutorial_samanage_007.png "nového uživatele")

1. Typ **název** a **e-mailovou adresu** chcete zřídit a klikněte na účtu služby Azure Active Directory **vytvořit uživatele**.
   
    ![Vytvoření uživatele](./media/samanage-tutorial/tutorial_samanage_008.png "vytvoření uživatele")
   
   >[!NOTE]
   >Držitel účtu Azure Active Directory bude dostávat e-mailu a odkaz potvrďte svůj účet, pak se změní na aktivní. Můžete použít jakékoli jiné Samanage uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Samanage ke zřízení služby Azure Active Directory uživatelské účty.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Samanage použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Samanage Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Samanage**.

    ![Konfigurace jednotného přihlašování](./media/samanage-tutorial/tutorial_samanage_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Samanage na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Samanage.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/samanage-tutorial/tutorial_general_01.png
[2]: ./media/samanage-tutorial/tutorial_general_02.png
[3]: ./media/samanage-tutorial/tutorial_general_03.png
[4]: ./media/samanage-tutorial/tutorial_general_04.png

[100]: ./media/samanage-tutorial/tutorial_general_100.png

[200]: ./media/samanage-tutorial/tutorial_general_200.png
[201]: ./media/samanage-tutorial/tutorial_general_201.png
[202]: ./media/samanage-tutorial/tutorial_general_202.png
[203]: ./media/samanage-tutorial/tutorial_general_203.png

