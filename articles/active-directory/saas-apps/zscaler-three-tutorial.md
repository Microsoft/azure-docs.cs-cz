---
title: 'Kurz: Integrace Azure Active Directory se Zscalerem tři | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscalerem tři.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeedes
ms.openlocfilehash: 442d7478669a206f04cb799cb86b807cfe7b0624
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790150"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>Kurz: Integrace Azure Active Directory s tři Zscaler

V tomto kurzu se dozvíte, jak integrovat tři Zscalerem se službou Azure Active Directory (Azure AD).

Integrace s Azure AD Zscalerem tři poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Zscalerem tři.
- Uživatele, aby automaticky získat přihlášení k Zscalerem tři (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se Zscalerem tři, potřebujete následující položky:

- Předplatné Azure AD
- Tři Zscalerem jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání tří Zscalerem z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zscaler-three-from-the-gallery"></a>Přidání tří Zscalerem z Galerie

Konfigurovat integraci Zscalerem tři do služby Azure AD, budete muset přidat Zscalerem tři z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Zscalerem tři z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Zscalerem tři**vyberte **Zscalerem tři** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Zscalerem tři v seznamu výsledků](./media/zscaler-three-tutorial/tutorial_zscalerthree_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Zscalerem tři podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Zscalerem tři je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Zscalerem tři potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se Zscalerem tři, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Zscalerem tři Single Sign-On](#configure-zscaler-three-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytvořte tři Zscalerem testovacího uživatele](#create-zscaler-three-test-user)**  – Pokud chcete mít protějšek Britta Simon Cisco zastřešující, který je propojený s Azure AD reprezentace uživatele.
5. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Zscalerem tři.

**Ke konfiguraci Azure AD jednotného přihlašování se Zscalerem tři, proveďte následující kroky:**

1. Na webu Azure Portal na **Zscalerem tři** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial_general_301.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Zscalerem tři domény a adresy URL jednotného přihlašování – informace](./media/zscaler-three-tutorial/tutorial_zscalerthree_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://login.zscalerthree.net/sfc_sso`

5. Zscalerem tři aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele a deklarace identity** části na stránce aplikací pro integraci. Na **sadě si jednotné přihlašování pomocí SAML, stránce**, klikněte na tlačítko **upravit** tlačítko Otevřít **atributy uživatele a deklarace identity** dialogového okna.

    ![Odkaz atributu](./media/zscaler-three-tutorial/tutorial_zscalerthree_attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název  | Zdrojový atribut  |
    | ---------| ------------ |
    | MemberOf     | user.assignedroles |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](./common/new_save_attribute.png)
    
    ![image](./common/new_attribute_details.png)

    b. Z **zdrojový atribut** seznamu selelct hodnotu atributu.

    c. Klikněte na tlačítko **OK**.

    d. Klikněte na **Uložit**.

    > [!NOTE]
    > Po klepnutí na [tady](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) vědět, jak nakonfigurovat Role ve službě Azure AD

7. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/zscaler-three-tutorial/tutorial_zscalerthree_certificate.png) 

8. Na **nastavení Zscalerem tři** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

    ![Konfigurace Zscalerem tři](common/configuresection.png)

### <a name="configure-zscaler-three-single-sign-on"></a>Konfigurace Zscalerem tři jednotného přihlašování

9. V okně jiné webové prohlížeče Přihlaste se k webu Zscalerem tři společnosti jako správce.

10. Přejděte na **správy > ověřování > Nastavení ověřování** a proveďte následující kroky:
   
    ![Správa](./media/zscaler-three-tutorial/ic800206.png "správy")

    a. V části typ ověřování, zvolte **SAML**.

    b. Klikněte na tlačítko **konfigurace SAML**.

11. Na **upravit SAML** okno, postupujte takto: a klikněte na Uložit.  
            
    ![Správa uživatelů a ověřování](./media/zscaler-three-tutorial/ic800208.png "správu uživatelů a ověřování")
    
    a. V **adresa URL portálu SAML** vložit do textového pole **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    b. V **atribut Name přihlášení** textového pole zadejte **NameID**.

    c. Klikněte na tlačítko **nahrát**, k nahrání podpisového certifikátu Azure SAML, který jste si stáhli z webu Azure portal v **veřejný certifikát SSL**.

    d. Přepnout **povolení automatického zřizování SAML**.

    e. V **atribut uživatelského jména. zobrazení** textového pole zadejte **displayName** Pokud chcete povolit SAML automatického zřizování pro atributy displayName.

    f. V **atribut název skupiny** textového pole zadejte **memberOf** Pokud chcete povolit SAML automatického zřizování pro memberOf atributy.

    g. V **atributem název oddělení** Enter **oddělení** Pokud chcete povolit SAML automatického zřizování pro oddělení atributy.

    i. Klikněte na **Uložit**.

12. Na **konfiguraci ověření uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Správa](./media/zscaler-three-tutorial/ic800207.png)

    a. Najeďte myší **aktivace** nabídky vlevo dole.

    b. Klikněte na tlačítko **aktivovat**.

## <a name="configuring-proxy-settings"></a>Konfigurace nastavení proxy serveru
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurace nastavení proxy serveru v aplikaci Internet Explorer

1. Spustit **aplikace Internet Explorer**.

1. Vyberte **Možnosti Internetu** z **nástroje** nabídku otevřít **Možnosti Internetu** dialogového okna.   
    
     ![Možnosti Internetu](./media/zscaler-three-tutorial/ic769492.png "Možnosti Internetu")

1. Klikněte na tlačítko **připojení** kartu.   
  
     ![Připojení](./media/zscaler-three-tutorial/ic769493.png "připojení")

1. Klikněte na tlačítko **nastavení místní sítě** otevřít **nastavení místní sítě** dialogového okna.

1. V části Proxy server proveďte následující kroky:   
   
    ![Proxy server](./media/zscaler-three-tutorial/ic769494.png "Proxy serveru")

    a. Vyberte **používat proxy server pro síť LAN**.

    b. Do textového pole Adresa zadejte **brány. Zscalerem Three.net**.

    c. V textovém poli portu zadejte **80**.

    d. Vyberte **obejít proxy server pro místní adresy**.

    e. Klikněte na tlačítko **OK** zavřete **nastavení místní sítě (LAN)** dialogového okna.

1. Klikněte na tlačítko **OK** zavřete **Možnosti Internetu** dialogového okna.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_02.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="create-zscaler-three-test-user"></a>Vytvořte tři Zscalerem testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v Zscalerem tři. Zscalerem tři podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Zscalerem tři, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [Zscalerem tři tým podpory](https://www.zscaler.com/company/contact).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Zscalerem tři.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Zscalerem tři**.

    ![Konfigurace jednotného přihlašování](./media/zscaler-three-tutorial/tutorial_zscalerthree_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko a pak vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogovém okně vyberte uživatele, jako jsou **Britta Simon** ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

6. Z **vybrat roli** dialogového okna zvolte příslušné role uživatele v seznamu a potom klikněte na tlačítko **vyberte** tlačítko v dolní části obrazovky.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

7. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na tři Zscalerem dlaždici na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Zscalerem tři.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
