---
title: 'Kurz: Integrace Azure Active Directory s Beta Zscalerem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscalerem Beta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39fc807e54e92a504c0bac730cd59c0223fde4b6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56164188"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Kurz: Integrace Azure Active Directory s Zscaler Beta

V tomto kurzu se dozvíte, jak integrovat Beta Zscalerem se službou Azure Active Directory (Azure AD).
Integrace Zscalerem Beta s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Zscalerem Beta.
* Uživatelům se automaticky přihlášeni k Zscalerem Beta (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se Zscalerem Beta, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Beta verze Zscalerem jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Zscalerem Beta **SP** jednotné přihlašování zahájené pomocí
* Podporuje Zscalerem Beta **JIT** zřizování uživatelů

## <a name="adding-zscaler-beta-from-the-gallery"></a>Přidání Zscalerem Beta z Galerie

Pokud chcete nakonfigurovat integraci Zscalerem Beta do služby Azure AD, budete muset přidat Zscalerem Beta z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání Zscalerem Beta z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscalerem Beta**vyberte **Zscalerem Beta** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Beta verze Zscalerem v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a test Azure AD jednotné přihlašování s Beta Zscalerem podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské ve verzi Beta Zscalerem.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se Zscalerem Beta, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Zscalerem Beta Single Sign-On](#configure-zscaler-beta-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Zscalerem Beta](#create-zscaler-beta-test-user)**  – Pokud chcete mít protějšek Britta Simon Zscalerem Beta, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotného přihlašování se Zscalerem Beta, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Zscalerem Beta** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Zscalerem beta verze domény a adresy URL jednotného přihlašování – informace](common/sp-intiated.png)

    V textovém poli přihlašovací adresa URL zadejte adresu URL používají vaši uživatelé k přihlašování do aplikace Zscalerem Beta.

    > [!NOTE]
    > Hodnota není skutečný. Skutečné UR Sign-On zaktualizujte příslušnou hodnotu. Kontakt [tým podpory Zscalerem beta verzi klienta](https://www.zscaler.com/company/contact) má být získána hodnota.

5. Beta verze Zscalerem aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název | Zdrojový atribut | 
    | ---------------| --------------- |
    | MemberOf  | user.assignedroles |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

    > [!NOTE]
    > Po klepnutí na [tady](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) vědět, jak nakonfigurovat Role ve službě Azure AD

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. Na **nastavení Zscalerem Beta** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-zscaler-beta-single-sign-on"></a>Konfigurace Zscalerem Beta jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Zscalerem Beta jako správce.

2. Přejděte na **správy > ověřování > Nastavení ověřování** a proveďte následující kroky:
   
    ![Správa](./media/zscaler-beta-tutorial/ic800206.png "správy")

    a. V části typ ověřování, zvolte **SAML**.

    b. Klikněte na tlačítko **konfigurace SAML**.

3. Na **upravit SAML** okno, postupujte takto: a klikněte na Uložit.  
            
    ![Správa uživatelů a ověřování](./media/zscaler-beta-tutorial/ic800208.png "správu uživatelů a ověřování")
    
    a. V **adresa URL portálu SAML** vložit do textového pole **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    b. V **atribut Name přihlášení** textového pole zadejte **NameID**.

    c. Klikněte na tlačítko **nahrát**, k nahrání podpisového certifikátu Azure SAML, který jste si stáhli z webu Azure portal v **veřejný certifikát SSL**.

    d. Přepnout **povolení automatického zřizování SAML**.

    e. V **atribut uživatelského jména. zobrazení** textového pole zadejte **displayName** Pokud chcete povolit SAML automatického zřizování pro atributy displayName.

    f. V **atribut název skupiny** textového pole zadejte **memberOf** Pokud chcete povolit SAML automatického zřizování pro memberOf atributy.

    g. V **atributem název oddělení** Enter **oddělení** Pokud chcete povolit SAML automatického zřizování pro oddělení atributy.

    h. Klikněte na **Uložit**.

4. Na **konfiguraci ověření uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Správa](./media/zscaler-beta-tutorial/ic800207.png)

    a. Najeďte myší **aktivace** nabídky vlevo dole.

    b. Klikněte na tlačítko **aktivovat**.

## <a name="configuring-proxy-settings"></a>Konfigurace nastavení proxy serveru
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurace nastavení proxy serveru v aplikaci Internet Explorer

1. Spustit **aplikace Internet Explorer**.

2. Vyberte **Možnosti Internetu** z **nástroje** nabídku otevřít **Možnosti Internetu** dialogového okna.   
    
     ![Možnosti Internetu](./media/zscaler-beta-tutorial/ic769492.png "Možnosti Internetu")

3. Klikněte na tlačítko **připojení** kartu.   
  
     ![Připojení](./media/zscaler-beta-tutorial/ic769493.png "připojení")

4. Klikněte na tlačítko **nastavení místní sítě** otevřít **nastavení místní sítě** dialogového okna.

5. V části Proxy server proveďte následující kroky:   
   
    ![Proxy server](./media/zscaler-beta-tutorial/ic769494.png "Proxy serveru")

    a. Vyberte **používat proxy server pro síť LAN**.

    b. Do textového pole Adresa zadejte **brány. Zscalerem Beta.net**.

    c. V textovém poli portu zadejte **80**.

    d. Vyberte **obejít proxy server pro místní adresy**.

    e. Klikněte na tlačítko **OK** zavřete **nastavení místní sítě (LAN)** dialogového okna.

6. Klikněte na tlačítko **OK** zavřete **Možnosti Internetu** dialogového okna.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Zscalerem Beta.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Zscalerem Beta**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Zscalerem Beta**.

    ![Odkaz Zscalerem Beta v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogovém okně vyberte uživatele, jako jsou **Britta Simon** ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

    ![image](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. Z **vybrat roli** dialogového okna zvolte příslušné role uživatele v seznamu a potom klikněte na tlačítko **vyberte** tlačítko v dolní části obrazovky.

    ![image](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

    ![image](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-zscaler-beta-test-user"></a>Vytvořit testovacího uživatele Zscaler Beta

V této části je uživateli Britta Simon vytvořené v betaverzi Zscalerem. Podporuje Zscalerem Beta **zřizování uživatelů just-in-time**, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi Zscalerem Beta, vytvoří se nový po ověření.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Zscalerem Beta](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Zscalerem Beta na přístupovém panelu, vám by měl být automaticky přihlášeni ke Zscalerem Beta, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

