---
title: 'Kurz: Integrace Azure Active Directory s Beta Zscalerem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscalerem Beta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07341c1ad30f1242bdff430826fdc82c45e09dac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086073"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Kurz: Integrace Azure Active Directory s Zscaler Beta

V tomto kurzu se dozvíte, jak integrovat Beta Zscalerem se službou Azure Active Directory (Azure AD).
Při integraci Zscalerem Beta s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Zscalerem Beta.
* Umožní uživatelům být automaticky přihlášeni k Zscalerem Beta pomocí jejich účtů služby Azure AD. Toto řízení přístupu se nazývá jednotné přihlašování (SSO).
* Správa účtů na jednom místě pomocí webu Azure portal.

Další informace o softwaru jako integraci služby (SaaS) aplikací s Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se Zscalerem Beta, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Beta verze Zscalerem odběr, který používá jednotné přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Beta Zscalerem podporuje jednotné přihlašování iniciovaného Zprostředkovatelem přihlašování.
* Beta verze Zscalerem podporuje zřizování uživatelů just-in-time.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Přidat Zscalerem Beta na webu Azure Marketplace

Pokud chcete nakonfigurovat integraci Zscalerem Beta do služby Azure AD, přidejte Zscalerem Beta z Azure Marketplace si na seznam spravovaných aplikací SaaS.

Chcete-li přidat Zscalerem Beta z Azure Marketplace, postupujte takto.

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním podokně vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscalerem Beta**. Vyberte **Zscalerem Beta** z panelu výsledek a pak vyberte **přidat**.

     ![Beta verze Zscalerem v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a test Azure AD jednotné přihlašování s Beta Zscalerem podle testovacího uživatele Britta Simon.
Pro jednotné přihlašování pro práci vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatelské ve verzi Beta Zscalerem.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se Zscalerem Beta, proveďte následující stavebních bloků:

- [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
- [Konfigurace Zscalerem Beta jednotného přihlašování](#configure-zscaler-beta-single-sign-on) ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
- [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
- [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
- [Vytvoření zkušebního uživatele Zscalerem Beta](#create-a-zscaler-beta-test-user) mít protějšek Britta Simon Zscalerem Beta, který je propojený s Azure AD zastoupení uživatele.
- [Otestovat jednotné přihlašování](#test-single-sign-on) ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotného přihlašování se Zscalerem Beta, postupujte podle těchto kroků.

1. V [webu Azure portal](https://portal.azure.com/)na **Zscalerem Beta** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogové okno, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránce **upravit** otevřít **základní konfiguraci SAML** dialogové okno.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. V **základní konfiguraci SAML** části, postupujte podle tohoto kroku:

    ![Zscalerem beta verze domény a adresy URL jednotného přihlašování – informace](common/sp-intiated.png)

    - V **přihlašovací adresa URL** zadejte adresu URL, které uživatelé používají k přihlášení do aplikace Zscalerem Beta.

    > [!NOTE]
    > Hodnota není skutečný. Skutečné přihlašování byste použili hodnotu URL zaktualizujte příslušnou hodnotu. K získání hodnoty, obraťte se [tým podpory klienta Zscalerem Beta](https://www.zscaler.com/company/contact).

5. Aplikace Zscalerem Beta očekává, že kontrolní výrazy SAML v určitém formátu. Mapování vlastního atributu je nutné přidat do vaší konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Vyberte **upravit** otevřít **atributy uživatele** dialogové okno.

    ![Dialogové okno atributů uživatele](common/edit-attribute.png)

6. Aplikace Zscalerem Beta očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** tématu **atributy uživatele** dialogové okno, postupujte podle těchto kroků přidejte atribut tokenu SAML, jak je znázorněno v následující tabulce.
    
    | Name | Zdrojový atribut | 
    | ---------------| --------------- |
    | MemberOf  | user.assignedroles |

    a. Vyberte **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogové okno.

    ![Dialogové okno deklarace identity uživatele](common/new-save-attribute.png)

    ![Správa dialogové okno deklarace identity uživatele](common/new-attribute-details.png)

    b. V **název** zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** pole prázdné.

    d. Pro **zdroj**vyberte **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Vyberte **OK**.

    g. Vyberte **Uložit**.

    > [!NOTE]
    > Další informace o konfiguraci role ve službě Azure AD, najdete v článku [konfigurace deklarace role](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **Stáhnout** ke stažení **certifikát (Base64)** . Uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. V **nastavení Zscalerem Beta** části, kopírování adresy URL je nutné pro vaše požadavky:

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    - Přihlašovací adresa URL
    - Identifikátor Azure AD
    - Adresa URL – odhlášení

### <a name="configure-zscaler-beta-single-sign-on"></a>Konfigurace Zscalerem Beta jednotného přihlašování

1. K automatizaci konfigurace v rámci Zscalerem Beta, nainstalujte **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** tak, že vyberete **nainstalovat rozšíření**.

    ![Moje aplikace rozšíření](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče, že vyberete **nastavení Zscalerem Beta** vás přesměruje na Beta Zscalerem aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení k Zscalerem Beta. Rozšíření prohlížeče automaticky nakonfiguruje aplikaci za vás a automatizuje kroky 3 až 6.

    ![Nastavení konfigurace](common/setup-sso.png)

3. Beta Zscalerem nastavit ručně, otevřete nové okno webového prohlížeče. Přihlaste se k webu Beta Zscalerem společnosti jako správce a postupujte podle těchto kroků.

4. Přejděte na **správu** > **ověřování** > **nastavení ověřování**a postupujte podle těchto kroků.
   
    ![Správa](./media/zscaler-beta-tutorial/ic800206.png "správy")

    a. V části **typ ověřování**vyberte **SAML**.

    b. Vyberte **konfigurace SAML**.

5. V **upravit SAML** okno, postupujte podle těchto kroků: 
            
    ![Správa uživatelů a ověřování](./media/zscaler-beta-tutorial/ic800208.png "správu uživatelů a ověřování")
    
    a. V **adresa URL portálu SAML** pole, vložte **přihlašovací adresa URL** , který jste zkopírovali z portálu Azure portal.

    b. V **atribut Name přihlášení** zadejte **NameID**.

    c. V **veřejný certifikát SSL** vyberte **nahrát** k nahrání podpisového certifikátu Azure SAML, který jste stáhli z portálu Azure portal.

    d. Přepnout **povolení automatického zřizování SAML**.

    e. V **atribut uživatelského jména. zobrazení** zadejte **displayName** Pokud chcete povolit autoprovisioning SAML pro atributy displayName.

    f. V **atribut název skupiny** zadejte **memberOf** Pokud chcete povolit SAML autoprovisioning memberOf atributů.

    g. V **atributem název oddělení** zadejte **oddělení** Pokud chcete povolit autoprovisioning SAML pro oddělení atributy.

    h. Vyberte **Uložit**.

6. Na **konfiguraci ověření uživatele** dialogového okna stránky, postupujte podle těchto kroků:

    ![Aktivace nabídky a tlačítko aktivovat](./media/zscaler-beta-tutorial/ic800207.png)

    a. Najeďte myší **aktivace** nabídky vlevo dole.

    b. Vyberte **aktivovat**.

## <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru
Konfigurace nastavení proxy serveru v aplikaci Internet Explorer, postupujte podle těchto kroků.

1. Spustit **aplikace Internet Explorer**.

2. Vyberte **Možnosti Internetu** z **nástroje** nabídce otevřete **Možnosti Internetu** dialogové okno. 
    
     ![Dialogové okno Možnosti Internetu](./media/zscaler-beta-tutorial/ic769492.png "Možnosti Internetu")

3. Vyberte **připojení** kartu. 
  
     ![Karta připojení](./media/zscaler-beta-tutorial/ic769493.png "připojení")

4. Vyberte **nastavení místní sítě** otevřít **nastavení místní sítě (LAN)** dialogové okno.

5. V **Proxy server** , postupujte podle těchto kroků: 
   
    ![Proxy server části](./media/zscaler-beta-tutorial/ic769494.png "Proxy serveru")

    a. Vyberte **používat proxy server pro síť LAN** zaškrtávací políčko.

    b. V **adresu** zadejte **brány. Zscalerem Beta.net**.

    c. V **Port** zadejte **80**.

    d. Vyberte **obejít proxy server pro místní adresy** zaškrtávací políčko.

    e. Vyberte **OK** zavřete **nastavení místní sítě (LAN)** dialogové okno.

6. Vyberte **OK** zavřete **Možnosti Internetu** dialogové okno.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory** > **uživatelé** > **všichni uživatelé**.

    ![Uživatelé a všechna propojení uživatelů](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. V **uživatele** dialogové okno pole, postupujte podle těchto kroků:

    ![Dialogové okno uživatelského](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension`. Příklad: BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtávací políčko. Zapište si hodnotu, která se zobrazí v **heslo** pole.

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

Povolte Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Zscalerem Beta.

1. Na webu Azure Portal, vyberte **podnikové aplikace** > **všechny aplikace** > **Zscalerem Beta**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Zscalerem Beta**.

    ![Beta verze Zscalerem odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Propojení uživatelů a skupin](common/users-groups-blade.png)

4. Vyberte **přidat uživatele**. V **přidat přiřazení** dialogu **uživatelů a skupin**.

    ![Tlačítko Přidat uživatele](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogovém okně vyberte uživatele, jako jsou **Britta Simon** ze seznamu. Klikněte na tlačítko **vyberte** v dolní části obrazovky.

    ![Dialogové okno Uživatelé a skupiny](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. V **vybrat roli** dialogového okna, vyberte příslušné role uživatele v seznamu. Klikněte na tlačítko **vyberte** v dolní části obrazovky.

    ![Dialogové okno Vybrat roli](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. V **přidat přiřazení** dialogu **přiřadit**.

    ![Přidat přiřazení – dialogové okno](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Vytvoření zkušebního uživatele Zscaler Beta

V této části je uživatel Britta Simon vytvořené v betaverzi Zscalerem. Podporuje Zscalerem Beta **zřizování uživatelů just-in-time**, který je ve výchozím nastavení povolené. Není co musíte udělat v této části. Pokud uživatel již neexistuje mezi Zscalerem Beta, vytvoří se nový po ověření.

>[!Note]
>Vytvořte uživatele ručně, obraťte se [tým podpory Zscalerem Beta](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

Testování Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když vyberete dlaždici Zscalerem Beta na přístupovém panelu, by měl být automaticky přihlásíte k Zscalerem Beta, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

