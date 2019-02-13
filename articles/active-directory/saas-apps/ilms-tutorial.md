---
title: 'Kurz: Integrace Azure Active Directory s iLMS | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf68f76f6ba451ff5f3e81b3aaabf3825155ba15
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201529"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Kurz: Integrace Azure Active Directory s iLMS

V tomto kurzu se dozvíte, jak integrovat iLMS s Azure Active Directory (Azure AD).

ILMS integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k iLMS
- Můžete povolit uživatelům, aby automaticky získat přihlášení k iLMS (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s iLMS, potřebujete následující položky:

- Předplatné Azure AD
- ILMS jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Produkčním prostředí byste neměli používat, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání iLMS z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-ilms-from-the-gallery"></a>Přidání iLMS z Galerie
Konfigurace integrace iLMS do služby Azure AD, budete muset přidat iLMS z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat iLMS z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **iLMS**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/ilms-tutorial/tutorial_ilms_search.png)

1. Na panelu výsledků vyberte **iLMS**, pak klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí iLMS podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v iLMS je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v iLMS potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v iLMS.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s iLMS, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytváří se testovací uživatelské jméno iLMS](#creating-an-ilms-test-user)**  – Pokud chcete mít v iLMS, který je propojený s Azure AD reprezentace jí protějšek Britta Simon.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci iLMS.

**Ke konfiguraci Azure AD jednotné přihlašování s iLMS, proveďte následující kroky:**

1. Na webu Azure Portal na **iLMS** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/tutorial_ilms_samlbase.png)

1. Na **iLMS domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/tutorial_ilms_url.png)

    a. V **identifikátor** vložit do textového pole **identifikátor** hodnotu zkopírujte z **poskytovatele služeb** SAML nastavení portálu pro správu iLMS.

    b. V **adresy URL odpovědi** vložit do textového pole **(adresa URL koncového bodu)** hodnotu zkopírujte z **poskytovatele služeb** SAML nastavení portálu pro správu iLMS s následující vzor `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

    >[!Note]
    >Tato "123456" je příkladem hodnoty identifikátoru.

1. Zkontrolujte **zobrazit pokročilé nastavení URL**, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/tutorial_ilms_url1.png)

    V **přihlašovací adresa URL** vložit do textového pole **(adresa URL koncového bodu)** hodnotu zkopírujte z **poskytovatele služeb** SAML nastavení v portálu pro správu iLMS jako `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`     

1. Pokud chcete povolit zřizování JIT, iLMS aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/4.png)
    
    Vytvoření **oddělení, oblast** a **dělení** atributy a přidejte název tyto atributy v iLMS. Tyto atributy uvedené výše jsou povinné.  

    > [!NOTE] 
    > Budete muset povolit **vytvořit uživatelský účet Un-recognized** v iLMS mapování těchto atributů. Postupujte podle pokynů [tady](http://support.inspiredelearning.com/customer/portal/articles/2204526) , kde získáte představu o konfiguraci atributy.

1. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |    
    | Dělení | user.department |
    | oblast | user.state |
    | Oddělení | user.jobtitle |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/tutorial_ilms_04.png)

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/tutorial_ilms_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **Ok**

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/tutorial_ilms_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/tutorial_general_400.png)

1. V okně jiné webové prohlížeče, přihlaste se k vaší **portál pro správu iLMS** jako správce.

1. Klikněte na tlačítko **SSO:SAML** pod **nastavení** kartě Otevřít nastavení SAML a proveďte následující kroky:
    
    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/1.png) 

    a. Rozbalte **poskytovatele služeb** části a zkopírujte **identifikátor** a **(adresa URL koncového bodu)** hodnotu.

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/2.png) 

    b. V části **zprostředkovatele Identity** klikněte na tlačítko **importovat Metadata**.
    
    c. Vyberte **metadat** soubor stáhnout z webu Azure Portal ze **podpisový certifikát SAML** oddílu.

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. Pokud chcete povolit JIT k vytváření účtů iLMS pro zrušení zřizování-rozpoznat uživatele, postupujte podle následujících kroků:
        
       - Zkontrolujte **vytvořte účet bez rozpoznán jako uživatel**.
       
       ![Configure Single Sign-On](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  Mapování atributů ve službě Azure AD s atributy v iLMS. Ve sloupci atributu zadejte název atributy nebo výchozí hodnotu.

    e. Přejděte na **obchodní pravidla** kartě a proveďte následující kroky: 
        
       ![Configure Single Sign-On](./media/ilms-tutorial/5.png)

       - Zkontrolujte **vytvořit Un-recognized oblasti, oddělení a oddělení** vytvořit oblasti, oddělení a oddělení, které už neexistují v době jednotného přihlašování.
        
       - Zkontrolujte **aktualizace uživatelského profilu při přihlášení** k určení, zda je s každou Single Sign-on aktualizovat profil uživatele. 
        
       - Pokud **"Aktualizace prázdné hodnoty pro jiné povinného pole v profilu uživatele"** zaškrtnutá možnost, profil volitelné pole, která jsou prázdné po přihlášení bude také způsobit iLMS profilu uživatele tak, aby obsahovala prázdné hodnoty příslušných polí.
        
       - Zkontrolujte **odeslat E-mail s oznámením o chybě** a zadejte e-mailu uživatele, kde chcete dostávat e-mailové oznámení chyby.

1. Klikněte na tlačítko **Uložit** uložte nastavení tlačítkem.

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/save.png)

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
    
### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/ilms-tutorial/create_aaduser_01.png) 

1. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/ilms-tutorial/create_aaduser_02.png) 

1. V horní části okna klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/ilms-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/ilms-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-ilms-test-user"></a>Vytvoření iLMS testovacího uživatele

Aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele se vytvoří automaticky v aplikaci. JIT bude fungovat, pokud jste klikli **vytvořit uživatelský účet Un-recognized** zaškrtávací políčko během SAML nastavení konfigurace na portálu pro správu iLMS.

Pokud je potřeba ručně vytvořit uživatele, postupujte podle následujících kroků:

1. Přihlaste se na web společnosti iLMS jako správce.

1. Klikněte na tlačítko **"Registrovat uživatele"** pod **uživatelé** karty otevřete **registrovat uživatele** stránky. 
   
   ![Přidat zaměstnance](./media/ilms-tutorial/3.png)

1. Na **"Registrovat uživatele"** stránce, proveďte následující kroky.

    ![Přidat zaměstnance](./media/ilms-tutorial/create_testuser_add.png)

    a. V **křestní jméno** textového pole Název typu první Britta.
   
    b. V **příjmení** textového pole zadejte příjmení Simon.

    c. V **ID e-mailu** textového pole zadejte e-mailovou adresu účtu Britta Simon.

    d. V **oblasti** rozevíracím seznamu vyberte hodnotu pro oblast.

    e. V **dělení** rozevíracím seznamu vyberte hodnotu pro dělení.

    f. V **oddělení** rozevíracím seznamu vyberte hodnotu pro oddělení.

    g. Klikněte na **Uložit**.

    > [!NOTE] 
    > Můžete odesílat poštu registrace pro uživatele tak, že vyberete **odeslat poštu registrace** zaškrtávací políčko.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k iLMS.

![Přiřadit uživatele][200] 

**Přiřadit iLMS Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **iLMS**.

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/tutorial_ilms_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici iLMS na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci iLMS.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ilms-tutorial/tutorial_general_01.png
[2]: ./media/ilms-tutorial/tutorial_general_02.png
[3]: ./media/ilms-tutorial/tutorial_general_03.png
[4]: ./media/ilms-tutorial/tutorial_general_04.png

[100]: ./media/ilms-tutorial/tutorial_general_100.png

[200]: ./media/ilms-tutorial/tutorial_general_200.png
[201]: ./media/ilms-tutorial/tutorial_general_201.png
[202]: ./media/ilms-tutorial/tutorial_general_202.png
[203]: ./media/ilms-tutorial/tutorial_general_203.png

