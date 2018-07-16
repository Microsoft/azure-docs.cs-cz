---
title: 'Kurz: Integrace Azure Active Directory se službou Tableau Server | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 4c88d6a8b22e4c5c19112c30e15bc960bc8ed1b5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045219"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Kurz: Integrace Azure Active Directory se službou Tableau Server

V tomto kurzu se dozvíte, jak integrovat Tableau Server se službou Azure Active Directory (Azure AD).

Tableau Server integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Tableau Server
- Uživatele, aby automaticky získat přihlášení k serveru Tableau (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Tableau Server, potřebujete následující položky:

- S předplatným služby Azure AD
- Tableau Server jediného přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Tableau serveru z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-tableau-server-from-the-gallery"></a>Přidání Tableau serveru z Galerie
Konfigurace integrace Tableau Server do služby Azure AD, budete muset přidat Tableau serveru v galerii na váš seznam spravovaných aplikací SaaS.

**Tableau Server přidat z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Tableau Server**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/tableauserver-tutorial/tutorial_tableauserver_search.png)

5. Na panelu výsledků vyberte **Tableau Server**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/tableauserver-tutorial/tutorial_tableauserver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Tableau Server vytvořený podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Tableau Server je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Tableau Server je potřeba navázat.

Tableau server přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Tableau Server, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Tableau Server](#creating-a-tableau-server-test-user)**  – Pokud chcete mít protějšek Britta Simon v Tableau Server, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Tableau Server.

**Ke konfiguraci Azure AD jednotné přihlašování s Tableau Server, proveďte následující kroky:**

1. Na webu Azure Portal na **Tableau Server** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial_tableauserver_samlbase.png)

3. Na **Tableau Server domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial_tableauserver_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://azure.<domain name>.link`
    
    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://azure.<domain name>.link`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://azure.<domain name>.link/wg/saml/SSO/index.html`
     
    > [!NOTE] 
    > Předchozí hodnoty nejsou skutečné hodnoty. Později aktualizujte hodnoty s skutečnou adresu URL a identifikátorem na stránce konfigurace Tableau Server. 

4. Tableau Server aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **"Atributy uživatele"** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro stejné.
    
    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/3.png)
    
5. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |    
    | uživatelské jméno | *user.mailnickname* |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial_officespace_04.png)

    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial_officespace_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **Ok**


6. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial_tableauserver_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial_general_400.png)
<CS>
8. Pokud chcete získat jednotné přihlašování nakonfigurované pro vaši aplikaci, budete muset přihlašování k vašemu tenantovi Tableau Server jako správce.
   
   a. V konfiguraci Tableau serveru, klikněte **SAML** kartu.
  
    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial_tableauserver_001.png) 
  
   b. Zaškrtněte políčko z **pomocí SAML pro jednotné přihlašování**.
   
   c. Tableau Server návratová adresa URL – adresa URL, Tableau Server uživatelé budou přistupovat k, jako například http://tableau_server. Pomocí http://localhost se nedoporučuje. Pomocí adresy URL s koncovým lomítkem (například http://tableau_server/) se nepodporuje. Kopírování **Tableau Server návratová adresa URL** a vložte ho do služby Azure AD **přihlašovací adresa URL** textového pole v **Tableau Server domény a adresy URL** oddílu.
   
   d. SAML entity ID – entity ID jednoznačně identifikuje Tableau Server instalace pro zprostředkovatele identity. Můžete zadat adresu URL svého Tableau Server znovu sem, pokud chcete, můžete, ale nemusí být vaše adresa URL serveru Tableau. Kopírování **SAML entity ID** a vložte ho do služby Azure AD **identifikátor** textového pole v **Tableau Server domény a adresy URL** oddílu.
     
   e. Klikněte na tlačítko **exportovat soubor metadat** a otevřít ji v aplikaci textového editoru. Vyhledejte adresu URL služby příjemce kontrolního výrazu s Http Post a indexu 0 a zkopírujte adresu URL. Nyní jej vložte do služby Azure AD **adresy URL odpovědi** textového pole v **Tableau Server domény a adresy URL** oddílu.
   
   f. Vyhledejte federačních metadat soubor stažený z webu Azure portal a pak ho nahrajte **soubor metadat SAML zprostředkovatele identity**.
   
   g. Klikněte na tlačítko **OK** tlačítko na stránce konfigurace serveru Tableau.
   
    >[!NOTE] 
    >Zákazník muset nahrát libovolný certifikát v konfiguraci jednotného přihlašování SAML Tableau serveru a bude získat ignorovat v toku jednotného přihlašování.
    >Pokud potřebujete pomoct konfiguraci SAML na Tableau serveru, najdete v tomto článku [konfigurace SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).
    >
<CE>

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/tableauserver-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/tableauserver-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/tableauserver-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/tableauserver-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-tableau-server-test-user"></a>Vytvoření zkušebního uživatele Tableau Server

Cílem této části je vytvořte uživatele Britta Simon v Tableau Server. Budete muset zřídit všechny uživatele v Tableau server. 

Tímto uživatelským jménem uživatele by měl odpovídat hodnotě, které jste nakonfigurovali v vlastní atribut Azure AD **uživatelské jméno**. Pomocí správné mapování by mělo fungovat integrace [konfigurace služby Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, budete muset kontaktovat správce Tableau serveru ve vaší organizaci.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k serveru Tableau.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit k serveru Tableau, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Tableau Server**.

    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial_tableauserver_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Tableau Server na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Tableau Server.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/tableauserver-tutorial/tutorial_general_04.png

[100]: ./media/tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/tableauserver-tutorial/tutorial_general_201.png
[202]: ./media/tableauserver-tutorial/tutorial_general_202.png
[203]: ./media/tableauserver-tutorial/tutorial_general_203.png

