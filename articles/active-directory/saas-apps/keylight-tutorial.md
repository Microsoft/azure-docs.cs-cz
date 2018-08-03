---
title: 'Kurz: Integrace Azure Active Directory se službou LockPath Keylight | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LockPath Keylight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 18fbcc785491ca8a0631f54750412bc0f12254c1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421443"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Kurz: Integrace Azure Active Directory se službou LockPath Keylight

V tomto kurzu se dozvíte, jak integrovat LockPath Keylight s Azure Active Directory (Azure AD).

LockPath Keylight integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k LockPath Keylight
- Uživatele, aby automaticky získat přihlášení k LockPath Keylight (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s LockPath Keylight, potřebujete následující položky:

- S předplatným služby Azure AD
- LockPath Keylight jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání LockPath Keylight z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Přidání LockPath Keylight z Galerie
Konfigurace integrace LockPath Keylight do služby Azure AD, budete muset přidat LockPath Keylight z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat LockPath Keylight z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **LockPath Keylight**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/keylight-tutorial/tutorial_keylight_search.png)

1. Na panelu výsledků vyberte **LockPath Keylight**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a test Azure AD jednotné přihlašování s LockPath Keylight podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v LockPath Keylight je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v LockPath Keylight potřeba navázat.

V LockPath Keylight přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s LockPath Keylight, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele LockPath Keylight](#creating-a-lockpath-keylight-test-user)**  – Pokud chcete mít protějšek Britta Simon LockPath Keylight, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci LockPath Keylight.

**Ke konfiguraci Azure AD jednotné přihlašování s LockPath Keylight, proveďte následující kroky:**

1. Na webu Azure Portal na **LockPath Keylight** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/tutorial_keylight_samlbase.png)

1. Na **LockPath Keylight domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/tutorial_keylight_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company name>.keylightgrc.com/`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company name>.keylightgrc.com`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company name>.keylightgrc.com/Login.aspx`
    
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory LockPath Keylight klienta](https://www.lockpath.com/contact/) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Raw)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/tutorial_keylight_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/tutorial_general_400.png)
    
1. Na **LockPath Keylight konfigurace** klikněte na tlačítko **nakonfigurovat LockPath Keylight** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/tutorial_keylight_configure.png) 

1. Pokud chcete povolit jednotné přihlašování v LockPath Keylight, proveďte následující kroky:
   
    a. Přihlášení ke svému účtu LockPath Keylight jako správce.
    
    b. V nabídce v horní části klikněte na tlačítko **osoba**a vyberte **Keylight nastavení**.
   
    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/401.png) 

    c. Ve stromovém zobrazení na levé straně klikněte na tlačítko **SAML**.
   
    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/402.png) 

    d. Na **nastavení SAML** dialogového okna, klikněte na tlačítko **upravit**.
   
    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/404.png) 

1. Na **upravit nastavení SAML** dialogového okna stránky, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/405.png) 
   
    a. Nastavte **ověřování SAML** k **aktivní**.

    b. Vložit **SAML jednotné přihlašování – adresa URL služby** hodnotu, která jste zkopírovali z portálu Azure portal do **přihlašovací adresa URL zprostředkovatele Identity** textového pole.

    c. Vložit **adresu URL jednotného odhlašování služby** hodnotu, která jste zkopírovali z portálu Azure portal do **odhlašovací adresa URL zprostředkovatele Identity** textového pole.

    d. Klikněte na tlačítko **zvolit soubor** vyberte stažený certifikát LockPath Keylight a potom klikněte na **otevřít** na kterou certifikát nahrajete.

    e. Nastavte **místo Id uživatele SAML** k **NameIdentifier element příkazu subjektu**.
    
    f. Zadejte **poskytovatele služeb Keylight** pomocí následujícímu vzoru: **https://&lt;CompanyName&gt;. keylightgrc.com**.
    
    g. Nastavte **automatické zřizování uživatelů** k **aktivní**.

    h. Nastavte **typ účtu automatické zřizování** k **úplné uživatelské**.

    i. Nastavte **role zabezpečení automatické zřizování**vyberte **standardního uživatele pomocí SAML**.
    
    j. Nastavte **konfigurace zabezpečení automatické zřizování**vyberte **standardní konfigurace uživatele**.
     
    k. V **atribut e-mailové** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    l. V **křestní jméno atributu** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    m. V **poslední název atributu** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    
    n. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/keylight-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/keylight-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/keylight-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/keylight-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-lockpath-keylight-test-user"></a>Vytvoření zkušebního uživatele LockPath Keylight

V této části vytvoříte uživatele v LockPath Keylight jako Britta Simon. LockPath Keylight podporuje zřizování just-in-time je ve výchozím nastavení povolená.

Neexistuje žádná položka akce pro vás v této části. Vytvoření nového uživatele při přístupu k LockPath Keylight, pokud uživatel ještě neexistuje. 

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory LockPath Keylight klienta](https://www.lockpath.com/contact/). 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k LockPath Keylight.

![Přiřadit uživatele][200] 

**Přiřadit LockPath Keylight Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **LockPath Keylight**.

    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/tutorial_keylight_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici LockPath Keylight na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci LockPath Keylight. 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/keylight-tutorial/tutorial_general_01.png
[2]: ./media/keylight-tutorial/tutorial_general_02.png
[3]: ./media/keylight-tutorial/tutorial_general_03.png
[4]: ./media/keylight-tutorial/tutorial_general_04.png

[100]: ./media/keylight-tutorial/tutorial_general_100.png

[200]: ./media/keylight-tutorial/tutorial_general_200.png
[201]: ./media/keylight-tutorial/tutorial_general_201.png
[202]: ./media/keylight-tutorial/tutorial_general_202.png
[203]: ./media/keylight-tutorial/tutorial_general_203.png

