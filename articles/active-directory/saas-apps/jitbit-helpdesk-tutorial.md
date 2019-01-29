---
title: 'Kurz: Integrace Azure Active Directory s Jitbit helpdesku | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jitbit Helpdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: e240efe98e04195947829b1821e0f6b6d92c2be6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173441"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Kurz: Integrace Azure Active Directory s Jitbit helpdesku

V tomto kurzu se dozvíte, jak integrovat Jitbit helpdesku se službou Azure Active Directory (Azure AD).

Integrace s Azure AD Jitbit helpdesku poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k helpdesku Jitbit
- Uživatele, aby automaticky získat přihlášení k helpdesku Jitbit (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Jitbit technickou podporu, potřebujete následující položky:

- Předplatné Azure AD
- Jitbit Helpdesk jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Jitbit helpdesku z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Přidání Jitbit helpdesku z Galerie
Ke konfiguraci integrace Jitbit helpdesku do služby Azure AD, budete muset přidat Jitbit helpdesku na váš seznam spravovaných aplikací SaaS z galerie.

**Přidat technickou podporu Jitbit z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Jitbit helpdesku**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_search.png)

1. Na panelu výsledků vyberte **Jitbit helpdesku**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a test Azure AD jednotné přihlašování s Jitbit helpdesku na základě testu uživatelem nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Jitbit Helpdesk je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Jitbit Helpdesk je potřeba navázat.

V helpdesku Jitbit přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Jitbit technické podpory, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Jitbit helpdesku](#creating-a-jitbit-helpdesk-test-user)**  – Pokud chcete mít protějšek Britta Simon Jitbit technické podpory, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Jitbit helpdesku.

**Ke konfiguraci Azure AD jednotné přihlašování s Jitbit helpdesku, proveďte následující kroky:**

1. Na webu Azure Portal na **Jitbit helpdesku** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_samlbase.png)

1. Na **domény Jitbit technickou podporu a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: 
    | |     
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory helpdesku klienta Jitbit](https://www.jitbit.com/support/) tuto výhodu získáte. 
    
    b.  V **identifikátor** textového pole zadejte adresu URL následujícím způsobem: `https://www.jitbit.com/web-helpdesk/`

    
 


1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/jitbit-helpdesk-tutorial/tutorial_general_400.png)

1. Na **Jitbit helpdesku konfigurace** klikněte na tlačítko **nakonfigurovat helpdesku Jitbit** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_configure.png) 

1. V okně jiný webový prohlížeč přihlaste jako správce webu Jitbit technickou podporu společnosti.

1. Na panelu nástrojů v horní části klikněte na tlačítko **správu**.
   
    ![Správa](./media/jitbit-helpdesk-tutorial/ic777681.png "správy")

1. Klikněte na tlačítko **obecné nastavení**.
   
    ![Uživatelé, společností a oprávnění](./media/jitbit-helpdesk-tutorial/ic777680.png "uživatele, společností a oprávnění")

1. V **nastavení ověřování** konfigurační oddíl, proveďte následující kroky:
   
    ![Nastavení ověřování](./media/jitbit-helpdesk-tutorial/ic777683.png "nastavení ověřování")
    
    a. Vyberte **povolit SAML 2.0 jednotné přihlášení**, přihlaste se pomocí jednotné přihlašování (SSO), s **OneLogin**.

    b. V **adresu URL koncového bodu** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    c. Otevřete váš **base-64** kódování certifikátu v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte ho do **certifikát X.509** textové pole

    d. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/jitbit-helpdesk-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/jitbit-helpdesk-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/jitbit-helpdesk-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/jitbit-helpdesk-tutorial/create_aaduser_04.png) 

    a. V **název** textového pole Název zadejte jako **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-jitbit-helpdesk-test-user"></a>Vytvoření zkušebního uživatele Jitbit helpdesku

Chcete-li povolit uživatele Azure AD k přihlášení do Jitbit technickou podporu, musí být poskytnuty do Jitbit Helpdesk.  V případě Jitbit helpdesku zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **Jitbit helpdesku** tenanta.

1. V nabídce v horní části klikněte na tlačítko **správu**.
   
    ![Správa](./media/jitbit-helpdesk-tutorial/ic777681.png "správy")

1. Klikněte na tlačítko **uživatelů, firmy a oprávnění**.
   
    ![Uživatelé, společností a oprávnění](./media/jitbit-helpdesk-tutorial/ic777682.png "uživatele, společností a oprávnění")

1. Klikněte na **Přidat uživatele**.
   
    ![Přidat uživatele](./media/jitbit-helpdesk-tutorial/ic777685.png "přidat uživatele")
   
1. V části Vytvoření zadejte údaje účtu služby Azure AD, které chcete zřídit následujícím způsobem:

    ![Vytvoření](./media/jitbit-helpdesk-tutorial/ic777686.png "vytvořit")
   
   a. V **uživatelské jméno** textové pole, typ **BrittaSimon**, uživatelské jméno jako na webu Azure portal.

   b. V **e-mailu** , jako je textové pole, typ e-mailu uživatele **BrittaSimon@contoso.com**.

   c. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.

   d. V **příjmení** textového pole zadejte příjmení uživatele, jako je **Simon**.
   
   e. Klikněte na možnost **Vytvořit**.

>[!NOTE]
>Další nástroje pro tvorbu účtu uživatele Jitbit helpdesku nebo rozhraní API poskytovaných Jitbit helpdesku můžete použít ke zřízení uživatelských účtů služby Azure AD.
> 
        

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Jitbit Helpdesk.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Jitbit helpdesku, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Jitbit helpdesku**.

    ![Konfigurace jednotného přihlašování](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Jitbit helpdesku na přístupovém panelu, měli byste obdržet přihlašovací stránku aplikace Jitbit helpdesku.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/jitbit-helpdesk-tutorial/tutorial_general_01.png
[2]: ./media/jitbit-helpdesk-tutorial/tutorial_general_02.png
[3]: ./media/jitbit-helpdesk-tutorial/tutorial_general_03.png
[4]: ./media/jitbit-helpdesk-tutorial/tutorial_general_04.png

[100]: ./media/jitbit-helpdesk-tutorial/tutorial_general_100.png

[200]: ./media/jitbit-helpdesk-tutorial/tutorial_general_200.png
[201]: ./media/jitbit-helpdesk-tutorial/tutorial_general_201.png
[202]: ./media/jitbit-helpdesk-tutorial/tutorial_general_202.png
[203]: ./media/jitbit-helpdesk-tutorial/tutorial_general_203.png

