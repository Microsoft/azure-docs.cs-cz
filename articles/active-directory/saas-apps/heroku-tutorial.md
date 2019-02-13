---
title: 'Kurz: Integrace Azure Active Directory s Heroku | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Heroku.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d7d72ec6-4a60-4524-8634-26d8fbbcc833
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f42ff2fe8c3ff03019203eac7e6c02c495e5493
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191771"
---
# <a name="tutorial-azure-active-directory-integration-with-heroku"></a>Kurz: Integrace Azure Active Directory s Heroku

V tomto kurzu se dozvíte, jak integrovat Heroku s Azure Active Directory (Azure AD).

Heroku integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Heroku
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Heroku (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Heroku, potřebujete následující položky:

- Předplatné Azure AD
- Heroku jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Heroku z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-heroku-from-the-gallery"></a>Přidání Heroku z Galerie
Konfigurace integrace Heroku do služby Azure AD, budete muset přidat Heroku z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Heroku z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Heroku**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/heroku-tutorial/tutorial_heroku_search.png)

1. Na panelu výsledků vyberte **Heroku**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/heroku-tutorial/tutorial_heroku_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s Heroku podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Heroku je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Heroku.

V Heroku, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Heroku, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Heroku](#creating-a-heroku-test-user)**  – Pokud chcete mít protějšek Britta Simon Heroku, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Heroku.

**Ke konfiguraci Azure AD jednotné přihlašování s Heroku, proveďte následující kroky:**

1. Na webu Azure Portal na **Heroku** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/heroku-tutorial/tutorial_heroku_samlbase.png)

1. Na **Heroku domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/heroku-tutorial/tutorial_heroku_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:    
    `https://sso.heroku.com/saml/<company-name>/init`

    b. V **identifikátoru adresy URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:            
    `https://sso.heroku.com/saml/<company-name>`

    > [!NOTE]
    >Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Tyto hodnoty získat z týmu Heroku, která je popsaná v předchozích částech tohoto článku. 
        
1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/heroku-tutorial/tutorial_heroku_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/heroku-tutorial/tutorial_general_400.png)

1. Pokud chcete povolit jednotné přihlašování v Heroku, proveďte následující kroky:
   
    a. Přihlaste se k účtu Heroku jako správce.

    b. Klikněte na kartu **Nastavení**.

    c. Na **jediné přihlášení na stránce**, klikněte na tlačítko **nahrát metadat**.

    d. Nahrajte soubor metadat, který jste si stáhli z portálu Azure portal.

    e. Po úspěšné instalaci potvrzovací dialogové okno zobrazuje správcům a adresa URL pro přihlášení jednotného přihlašování pro koncové uživatele se zobrazí. 

    f. Kopírovat **Heroku přihlašovací adresa URL** a **Heroku Entity ID** hodnoty a vraťte se do **Heroku domény a adresy URL** části webu Azure Portal a vložte tyto hodnoty do  **Přihlašovací adresa Url** a **identifikátor** textová pole v uvedeném pořadí.

    ![Konfigurace jednotného přihlašování](./media/heroku-tutorial/tutorial_heroku_52.png) 
    
1. Klikněte na **Další**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **Active Directory podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/heroku-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/heroku-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/heroku-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/heroku-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-heroku-test-user"></a>Vytvoření zkušebního uživatele Heroku

V této části vytvořte uživatele Britta Simon v Heroku. Heroku podporuje just-in-time zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Vytvoření nového uživatele při přístupu k Heroku, pokud uživatel ještě neexistuje. Po zřízení účtu koncový uživatel obdrží e-mail pro ověření a je potřeba, klikněte na odkaz potvrzení.

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory Heroku klienta](https://www.heroku.com/support).
>  

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Heroku.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Heroku, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Heroku**.

    ![Konfigurace jednotného přihlašování](./media/heroku-tutorial/tutorial_heroku_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Heroku na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Heroku.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/heroku-tutorial/tutorial_general_01.png
[2]: ./media/heroku-tutorial/tutorial_general_02.png
[3]: ./media/heroku-tutorial/tutorial_general_03.png
[4]: ./media/heroku-tutorial/tutorial_general_04.png

[100]: ./media/heroku-tutorial/tutorial_general_100.png

[200]: ./media/heroku-tutorial/tutorial_general_200.png
[201]: ./media/heroku-tutorial/tutorial_general_201.png
[202]: ./media/heroku-tutorial/tutorial_general_202.png
[203]: ./media/heroku-tutorial/tutorial_general_203.png
