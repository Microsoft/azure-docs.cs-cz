---
title: 'Kurz: Integrace Azure Active Directory s Tableau Server | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeedes
ms.openlocfilehash: 69ae32f63005d3d5ea83dce5aa349bb6c0c92e0c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818426"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Kurz: Integrace Azure Active Directory s Tableau Server

V tomto kurzu se dozvíte, jak integrovat Tableau Server se službou Azure Active Directory (Azure AD).

Tableau Server integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k serveru Tableau.
- Uživatele, aby automaticky získat přihlášení k serveru Tableau (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Tableau Server, potřebujete následující položky:

- Předplatné Azure AD
- Tableau Server jediného přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Tableau serveru z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-tableau-server-from-the-gallery"></a>Přidání Tableau serveru z Galerie

Konfigurace integrace Tableau Server do služby Azure AD, budete muset přidat Tableau serveru v galerii na váš seznam spravovaných aplikací SaaS.

**Tableau Server přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Tableau Server**vyberte **Tableau Server** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Tableau Server v seznamu výsledků](./media/tableauserver-tutorial/tutorial-tableauserver-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Tableau Server vytvořený podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Tableau Server je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Tableau Server je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Tableau Server, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Tableau Server Single Sign-On](#configure-tableau-server-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytvořit testovacího uživatele Tableau Server](#create-tableau-server-test-user)**  – Pokud chcete mít protějšek Britta Simon Cisco zastřešující, který je propojený s Azure AD reprezentace uživatele.
5. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Tableau Server.

**Ke konfiguraci Azure AD jednotné přihlašování s Tableau Server, proveďte následující kroky:**

1. Na webu Azure Portal na **Tableau Server** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial-general-301.png)

3. Tableau Server aplikace očekává vlastních deklarací identity **uživatelské jméno** který musí být definováno níže. To se používá jako identifikátor uživatele místo deklarace uživatele. jedinečný identifikátor. Můžete spravovat hodnotami těchto atributů z **atributy uživatele a deklarace identity** části na stránce aplikací pro integraci. Klikněte na tlačítko **upravit** tlačítko Otevřít **atributy uživatele a deklarace identity** dialogového okna.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-attribute.png)

4. V **deklarace identity uživatelů** části na **atributy uživatele a deklarace identity** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu | Obor názvů |
    | ---------------| --------------- | ----------- |   
    | uživatelské jméno | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-add-attribute.png)

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-manage-attribute.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Zadejte **Namespace** hodnotu.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na **Uložit**.

5. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

6. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://azure.<domain name>.link`
    
    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://azure.<domain name>.link`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-url.png)
     
    > [!NOTE] 
    > Předchozí hodnoty nejsou skutečné hodnoty. Aktualizujte hodnoty s skutečnou adresu URL a identifikátorem na stránce konfigurace Tableau Server, který je vysvětlen později v tomto kurzu.

7. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **kód XML metadat federace** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/tableauserver-tutorial/tutorial-tableauserver-certificate.png)

### <a name="configure-tableau-server-single-sign-on"></a>Konfigurace Tableau serveru jednotného přihlašování 

1. Pokud chcete získat jednotné přihlašování nakonfigurované pro vaši aplikaci, budete muset přihlašování k vašemu tenantovi Tableau Server jako správce.

2. Na **konfigurace** kartu, vyberte možnost **uživatele identita a přístup**a pak vyberte **ověřování** metoda kartu.

    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na **konfigurace** stránce, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Pro **metodu ověřování**, vyberte SAML.
    
    b. Zaškrtněte políčko z **povolit ověřování SAML pro server**.

    c. Tableau Server návratová adresa URL – adresa URL, Tableau Server uživatelé budou přistupovat k, jako například http://tableau_server. Pomocí http://localhost se nedoporučuje. Pomocí adresy URL s koncovým lomítkem (například http://tableau_server/) se nepodporuje. Kopírování **Tableau Server návratová adresa URL** a vložte ho do služby Azure AD **přihlašovací adresa URL** textového pole v **Tableau Server domény a adresy URL** oddílu.

    d. SAML entity ID – entity ID jednoznačně identifikuje Tableau Server instalace pro zprostředkovatele identity. Můžete zadat adresu URL svého Tableau Server znovu sem, pokud chcete, můžete, ale nemusí být vaše adresa URL serveru Tableau. Kopírování **SAML entity ID** a vložte ho do služby Azure AD **identifikátor** textového pole v **Tableau Server domény a adresy URL** oddílu.

    e. Klikněte na tlačítko **stáhnout soubor metadat XML** a otevřít ji v aplikaci textového editoru. Vyhledejte adresu URL služby příjemce kontrolního výrazu s Http Post a indexu 0 a zkopírujte adresu URL. Nyní jej vložte do služby Azure AD **adresy URL odpovědi** textového pole v **Tableau Server domény a adresy URL** oddílu.

    f. Vyhledejte federačních metadat soubor stažený z webu Azure portal a pak ho nahrajte **soubor metadat SAML zprostředkovatele identity**.

    g. Zadejte názvy atributů, které zprostředkovatel identity je používá pro uložení uživatelských jmen, zobrazované názvy, e-mailové adresy.

    h. Klikněte na **Uložit**.

    >[!NOTE] 
    >Zákazník muset nahrát libovolný certifikát v konfiguraci jednotného přihlašování SAML Tableau serveru a bude získat ignorovat v toku jednotného přihlašování.
    >Pokud potřebujete pomoct konfiguraci SAML na Tableau serveru, najdete v tomto článku [konfigurace SAML](https://onlinehelp.tableau.com/v2018.2/server/en-us/saml_config_steps_tsm_ui.htm).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create-aaduser-01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create-aaduser-02.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
  
### <a name="create-tableau-server-test-user"></a>Vytvořit testovacího uživatele Tableau Server

Cílem této části je vytvořte uživatele Britta Simon v Tableau Server. Budete muset zřídit všechny uživatele v Tableau server. 

Tímto uživatelským jménem uživatele by měl odpovídat hodnotě, které jste nakonfigurovali v vlastní atribut Azure AD **uživatelské jméno**. Integrace správné mapování by měl pracovat konfigurace služby Azure AD Single Sign-On.

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, budete muset kontaktovat správce Tableau serveru ve vaší organizaci.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k serveru Tableau.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Tableau Server**.

    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial-tableauserver-app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogového okna, vyberte **přiřadit** tlačítko.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Tableau Server na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Tableau Server.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
