---
title: 'Kurz: Integrace Azure Active Directory s Moxtra | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Moxtra.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f613c7768b92ce97dcc16a757a066dd5796879b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180587"
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Kurz: Integrace Azure Active Directory s Moxtra

V tomto kurzu se dozvíte, jak integrovat Moxtra s Azure Active Directory (Azure AD).

Moxtra integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Moxtra
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Moxtra (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Moxtra, potřebujete následující položky:

- Předplatné Azure AD
- Moxtra jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Moxtra z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-moxtra-from-the-gallery"></a>Přidání Moxtra z Galerie
Konfigurace integrace Moxtra do služby Azure AD, budete muset přidat Moxtra z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Moxtra z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Moxtra**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/moxtra-tutorial/tutorial_moxtra_search.png)

1. Na panelu výsledků vyberte **Moxtra**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/moxtra-tutorial/tutorial_moxtra_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Moxtra podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Moxtra je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Moxtra potřeba navázat.

V Moxtra, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Moxtra, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Moxtra](#creating-a-moxtra-test-user)**  – Pokud chcete mít protějšek Britta Simon Moxtra, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Moxtra.

**Ke konfiguraci Azure AD jednotné přihlašování s Moxtra, proveďte následující kroky:**

1. Na webu Azure Portal na **Moxtra** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/moxtra-tutorial/tutorial_moxtra_samlbase.png)

1. Na **Moxtra domény a adresy URL** části, postupujte následovně:

    ![Konfigurace jednotného přihlašování](./media/moxtra-tutorial/tutorial_moxtra_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL jako: `https://www.moxtra.com/service/#login`

1. Moxtra aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto konfiguraci. 

    ![Konfigurace jednotného přihlašování](./media/moxtra-tutorial/tutorial_moxtra_attributes.png)
    
1. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |    
    | jméno | user.givenname |
    | Příjmení | user.surname |
    | idpid    | < SAML Entity ID > 

    > [!Note]
    > Hodnota **idpid** atribut není skutečný. Můžete získat skutečnou hodnotu z **Stručná referenční příručka** části **Moxtra konfigurace**.
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/moxtra-tutorial/tutorial_attribute_04.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    ![Konfigurace jednotného přihlašování](./media/moxtra-tutorial/tutorial_attribute_05.png)

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Klikněte na tlačítko **OK**.
    
1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/moxtra-tutorial/tutorial_moxtra_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/moxtra-tutorial/tutorial_general_400.png)

1. Na **Moxtra konfigurace** klikněte na tlačítko **nakonfigurovat Moxtra** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/moxtra-tutorial/tutorial_moxtra_configure.png) 

1. V jiném okně prohlížeče Přihlaste se k webu společnosti Moxtra jako správce.

1. Na panelu nástrojů na levé straně klikněte na tlačítko **konzoly pro správu > SAML jednotného přihlašování**a potom klikněte na tlačítko **nový**.
   
    ![Konfigurace jednotného přihlašování](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

1. Na **SAML** stránce, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. V **název** textového pole zadejte název pro vaši konfiguraci (například: *SAML*). 
  
    b. V **IdP Entity ID** textového pole vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal. 
 
    c. V **přihlašovací adresa URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal. 
 
    d. V **AuthnContextClassRef** textové pole, typ **urn: oasis: názvy: tc: SAML:2.0:ac:classes:Password**. 
 
    e. V **formátem** textové pole, typ **urn: oasis: názvy: tc: SAML:1.1:nameid – formát: emailAddress**. 
 
    f. Otevřete certifikát, který jste si stáhli z webu Azure portal v programu Poznámkový blok, zkopírujte obsah a vložte jej do **certifikát** textového pole.    
 
    g. V textovém poli SAML e-mailové doméně zadejte e-mailovou doménu vašeho SAML.    
  
    >[!NOTE]
    >Pokud chcete zobrazit postup pro ověření domény, klikněte "**můžu**" níže.

    h. Klikněte na tlačítko **aktualizace**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/moxtra-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/moxtra-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/moxtra-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/moxtra-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-moxtra-test-user"></a>Vytvoření zkušebního uživatele Moxtra

Cílem této části je vytvořte uživatele Britta Simon v Moxtra.

**Vytvořte uživatele v Moxtra jako Britta Simon, proveďte následující kroky:**

1. Přihlaste se k webu společnosti Moxtra jako správce.

1. Na panelu nástrojů na levé straně klikněte na tlačítko **konzolu správce > Správa uživatelů**a potom **přidat uživatele**.
   
    ![Konfigurace jednotného přihlašování](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. Na **přidat uživatele** dialogového okna, proveďte následující kroky:
  
    a. V **křestní jméno** textové pole, typ **Britta**.
  
    b. V **příjmení** textové pole, typ **Simon**.
  
    c. V **e-mailu** textové pole, typ Haniny e-mailová adresa stejné jako na portálu Azure portal.
  
    d. V **dělení** textové pole, typ **Dev**.
  
    e. V **oddělení** textové pole, typ **IT**.
  
    f. Vyberte **správce**.
  
    g. Klikněte na tlačítko **Add** (Přidat).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Moxtra použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Moxtra Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Moxtra**.

    ![Konfigurace jednotného přihlašování](./media/moxtra-tutorial/tutorial_moxtra_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Moxtra na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Moxtra.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/moxtra-tutorial/tutorial_general_01.png
[2]: ./media/moxtra-tutorial/tutorial_general_02.png
[3]: ./media/moxtra-tutorial/tutorial_general_03.png
[4]: ./media/moxtra-tutorial/tutorial_general_04.png

[100]: ./media/moxtra-tutorial/tutorial_general_100.png

[200]: ./media/moxtra-tutorial/tutorial_general_200.png
[201]: ./media/moxtra-tutorial/tutorial_general_201.png
[202]: ./media/moxtra-tutorial/tutorial_general_202.png
[203]: ./media/moxtra-tutorial/tutorial_general_203.png

