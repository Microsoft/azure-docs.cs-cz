---
title: 'Kurz: Integrace Azure Active Directory se Zscalerem ZSCloud | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscalerem ZSCloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeedes
ms.openlocfilehash: 311cf4ad22f8e7fdcee976e0cb4b26b9dd2a13a5
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794262"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Kurz: Integrace Azure Active Directory s Zscaler ZSCloud

V tomto kurzu se dozvíte, jak integrovat ZSCloud Zscalerem se službou Azure Active Directory (Azure AD).

Integrace Zscalerem ZSCloud s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Zscalerem ZSCloud.
- Uživatele, aby automaticky získat přihlášení k Zscalerem ZSCloud (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se Zscalerem ZSCloud, potřebujete následující položky:

- Předplatné Azure AD
- Zscalerem ZSCloud jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zscalerem ZSCloud z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Přidání Zscalerem ZSCloud z Galerie

Ke konfiguraci integrace Zscalerem ZSCloud do služby Azure AD, budete muset přidat Zscalerem ZSCloud z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání Zscalerem ZSCloud z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Zscalerem ZSCloud**vyberte **Zscalerem ZSCloud** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Zscalerem ZSCloud v seznamu výsledků](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotného přihlašování se Zscalerem ZSCloud podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Zscalerem ZSCloud je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Zscalerem ZSCloud potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se Zscalerem ZSCloud, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Zscaler ZSCloud](#creating-a-zscaler-zscloud-test-user)**  – Pokud chcete mít protějšek Britta Simon Zscalerem ZSCloud, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování jednotného přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Zscalerem ZSCloud.

**Ke konfiguraci Azure AD jednotného přihlašování se Zscalerem ZSCloud, proveďte následující kroky:**

1. Na webu Azure Portal na **Zscalerem ZSCloud** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial_general_301.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Zscalerem ZSCloud domény a adresy URL jednotného přihlašování – informace](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_url.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL použít vaši uživatelé k přihlašování do aplikace Zscalerem ZSCloud.

    > [!NOTE] 
    > Budete muset aktualizovat tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory dva klientské Zscalerem](https://help.zscaler.com/) k získání těchto hodnot.

5. Zscalerem ZSCloud aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele a deklarace identity** části na stránce aplikací pro integraci. Na **sadě si jednotné přihlašování pomocí SAML, stránce**, klikněte na tlačítko **upravit** tlačítko Otevřít **atributy uživatele a deklarace identity** dialogového okna.

    ![Odkaz atributu](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_attribute.png)

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

    ![Odkaz ke stažení certifikátu](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_certificate.png) 

8. Na **nastavení Zscalerem ZSCloud** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

    ![Konfigurace Zscaler ZSCloud](common/configuresection.png)

9. V okně jiné webové prohlížeče Přihlaste se k serveru vaší společnosti Zscalerem ZSCloud jako správce.

10. Přejděte na **správy > ověřování > Nastavení ověřování** a proveďte následující kroky:
   
    ![Správa](./media/zscaler-zscloud-tutorial/ic800206.png "správy")

    a. V části typ ověřování, zvolte **SAML**.

    b. Klikněte na tlačítko **konfigurace SAML**.

11. Na **upravit SAML** okno, postupujte takto: a klikněte na Uložit.  
            
    ![Správa uživatelů a ověřování](./media/zscaler-zscloud-tutorial/ic800208.png "správu uživatelů a ověřování")
    
    a. V **adresa URL portálu SAML** vložit do textového pole **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    b. V **atribut Name přihlášení** textového pole zadejte **NameID**.

    c. Klikněte na tlačítko **nahrát**, k nahrání podpisového certifikátu Azure SAML, který jste si stáhli z webu Azure portal v **veřejný certifikát SSL**.

    d. Přepnout **povolení automatického zřizování SAML**.

    e. V **atribut uživatelského jména. zobrazení** textového pole zadejte **displayName** Pokud chcete povolit SAML automatického zřizování pro atributy displayName.

    f. V **atribut název skupiny** textového pole zadejte **memberOf** Pokud chcete povolit SAML automatického zřizování pro memberOf atributy.

    g. V **atributem název oddělení** Enter **oddělení** Pokud chcete povolit SAML automatického zřizování pro oddělení atributy.

    i. Klikněte na **Uložit**.

12. Na **konfiguraci ověření uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Správa](./media/zscaler-zscloud-tutorial/ic800207.png)

    a. Najeďte myší **aktivace** nabídky vlevo dole.

    b. Klikněte na tlačítko **aktivovat**.

## <a name="configuring-proxy-settings"></a>Konfigurace nastavení proxy serveru
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurace nastavení proxy serveru v aplikaci Internet Explorer

1. Spustit **aplikace Internet Explorer**.

1. Vyberte **Možnosti Internetu** z **nástroje** nabídku otevřít **Možnosti Internetu** dialogového okna.   
    
     ![Možnosti Internetu](./media/zscaler-zscloud-tutorial/ic769492.png "Možnosti Internetu")

1. Klikněte na tlačítko **připojení** kartu.   
  
     ![Připojení](./media/zscaler-zscloud-tutorial/ic769493.png "připojení")

1. Klikněte na tlačítko **nastavení místní sítě** otevřít **nastavení místní sítě** dialogového okna.

1. V části Proxy server proveďte následující kroky:   
   
    ![Proxy server](./media/zscaler-zscloud-tutorial/ic769494.png "Proxy serveru")

    a. Vyberte **používat proxy server pro síť LAN**.

    b. Do textového pole Adresa zadejte **brány. Zscalerem ZSCloud.net**.

    c. V textovém poli portu zadejte **80**.

    d. Vyberte **obejít proxy server pro místní adresy**.

    e. Klikněte na tlačítko **OK** zavřete **nastavení místní sítě (LAN)** dialogového okna.

1. Klikněte na tlačítko **OK** zavřete **Možnosti Internetu** dialogového okna.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

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

### <a name="creating-a-zscaler-zscloud-test-user"></a>Vytvoření zkušebního uživatele Zscaler ZSCloud

Cílem této části je vytvořte uživatele Britta Simon v Zscalerem ZSCloud. Zscalerem ZSCloud podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Zscalerem ZSCloud, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Zscalerem ZSCloud](https://help.zscaler.com/).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Zscalerem ZSCloud.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Zscalerem ZSCloud**.

    ![Konfigurace jednotného přihlašování](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko a pak vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogovém okně vyberte uživatele, jako jsou **Britta Simon** ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_users.png)

6. Z **vybrat roli** dialogového okna zvolte příslušné role uživatele v seznamu a potom klikněte na tlačítko **vyberte** tlačítko v dolní části obrazovky.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_roles.png)

7. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_assign.png)

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Zscalerem ZSCloud na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Zscalerem ZSCloud.
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
