---
title: 'Kurz: Integrace Azure Active Directory s Wdesk | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 0894b1ccf976cfa1a932b76f6ed06e1bd45ee1b7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180904"
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Kurz: Integrace Azure Active Directory s Wdesk

V tomto kurzu se dozvíte, jak integrovat Wdesk s Azure Active Directory (Azure AD).

Wdesk integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Wdesk
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Wdesk (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma. [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Wdesk, potřebujete následující položky:

- Předplatné Azure AD
- Wdesk jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Wdesk z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-wdesk-from-the-gallery"></a>Přidání Wdesk z Galerie
Konfigurace integrace Wdesk do služby Azure AD, budete muset přidat Wdesk z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Wdesk z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Wdesk**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/wdesk-tutorial/tutorial_wdesk_search.png)

1. Na panelu výsledků vyberte **Wdesk**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s Wdesk podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Wdesk je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Wdesk potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Wdesk.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Wdesk, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Wdesk](#creating-a-wdesk-test-user)**  – Pokud chcete mít protějšek Britta Simon Wdesk, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Wdesk.

**Ke konfiguraci Azure AD jednotné přihlašování s Wdesk, proveďte následující kroky:**

1. Na webu Azure Portal na **Wdesk** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_samlbase.png)

1. Na **Wdesk domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

1. Zkontrolujte **zobrazit pokročilé nastavení URL**. Pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu, postupujte následovně:

    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Tyto hodnoty získat z portálu WDesk při konfiguraci jednotné přihlašování. 
  
1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_general_400.png)
    
1. V jiné okno webového prohlížeče, přihlaste se k Wdesk jako správce zabezpečení.

1. Vlevo dole, klikněte na tlačítko **správce** a zvolte **správce účtu**:
 
     ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. V Wdesk správce, přejděte na **zabezpečení**, pak **SAML** > **nastavení SAML**:

    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. V části **obecné nastavení**, zkontrolujte **povolit SAML jednotného přihlašování**:

    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

1. V části **podrobnosti o službě Zprostředkovatel**, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Kopírovat **přihlašovací adresa URL** a vložte ji **přihlašovací adresa Url** textovým polem na webu Azure portal.
   
      b. Kopírovat **adresa Url metadat** a vložte ji **identifikátor** textovým polem na webu Azure portal.
       
      c. Kopírovat **příjemce url** a vložte ji **adresy Url odpovědi** textovým polem na webu Azure portal.
   
      d. Klikněte na tlačítko **Uložit** na portálu Azure portal a uložte změny.      

1. Klikněte na tlačítko **konfigurovat nastavení zprostředkovatele identity** otevřete **upravit nastavení zprostředkovatele identity** dialogového okna. Klikněte na tlačítko **zvolit soubor** vyhledejte **Metadata.xml** souborů, které jste si uložili z webu Azure portal, pak ho nahrajte.
    
    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
1. Klikněte na tlačítko **uložit změny**.

    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/wdesk-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/wdesk-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/wdesk-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/wdesk-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-wdesk-test-user"></a>Vytvoření zkušebního uživatele Wdesk

Přihlaste se k Wdesk Azure AD uživatelům umožnit, musí být poskytnuty do Wdesk. Zřizování v Wdesk, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k Wdesk jako správce zabezpečení.
1. Přejděte do **správce** > **účet správce**.

     ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. Klikněte na tlačítko **členy** pod **lidé**.

1. Nyní klikejte na příkaz **přidat člen** otevřete **přidat člen** dialogové okno. 
   
    ![Vytváří se testovací uživatele služby Azure AD](./media/wdesk-tutorial/createuser1.png)  

1. V **uživatele** textové pole, zadejte uživatelské jméno uživatele, jako je **brittasimon@contoso.com** a klikněte na tlačítko **pokračovat** tlačítko.

    ![Vytváří se testovací uživatele služby Azure AD](./media/wdesk-tutorial/createuser3.png)

1.  Zadejte podrobnosti, jak je znázorněno níže:
  
    ![Vytváří se testovací uživatele služby Azure AD](./media/wdesk-tutorial/createuser4.png)
 
    a. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.

    b. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je **Britta**.

    c. V **příjmení** textové pole, zadejte příjmení uživatele, jako je **Simon**.

1. Klikněte na tlačítko **uložit člen** tlačítko.  

    ![Vytváří se testovací uživatele služby Azure AD](./media/wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Wdesk použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Wdesk Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Wdesk**.

    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Wdesk na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Wdesk.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/wdesk-tutorial/tutorial_general_01.png
[2]: ./media/wdesk-tutorial/tutorial_general_02.png
[3]: ./media/wdesk-tutorial/tutorial_general_03.png
[4]: ./media/wdesk-tutorial/tutorial_general_04.png

[100]: ./media/wdesk-tutorial/tutorial_general_100.png

[200]: ./media/wdesk-tutorial/tutorial_general_200.png
[201]: ./media/wdesk-tutorial/tutorial_general_201.png
[202]: ./media/wdesk-tutorial/tutorial_general_202.png
[203]: ./media/wdesk-tutorial/tutorial_general_203.png

