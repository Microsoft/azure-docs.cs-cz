---
title: 'Kurz: Azure Active Directory integrace s Zscaler Beta | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscaler Beta.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: f342371ec065c4fb60c492c4354c6f8c717c9bb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88545994"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Kurz: Azure Active Directory integrace s Zscaler Beta

V tomto kurzu se dozvíte, jak integrovat Zscaler Beta s Azure Active Directory (Azure AD).
Když integrujete Zscaler Beta s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Zscaler Beta.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Zscaler Beta pomocí svých účtů Azure AD. Toto řízení přístupu se nazývá jednotné přihlašování (SSO).
* Spravujte své účty v jednom centrálním umístění pomocí Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Zscaler Beta budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Zscaler Beta, které používá jednotné přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zscaler Beta podporuje jednotné přihlašování iniciované v SP.
* Zscaler Beta podporuje zřizování uživatelů za běhu.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Přidat Zscaler Beta z Azure Marketplace

Pokud chcete nakonfigurovat integraci Zscaler Beta na Azure AD, přidejte Zscaler Beta z Azure Marketplace do seznamu spravovaných aplikací SaaS.

Chcete-li přidat Zscaler Beta z Azure Marketplace, postupujte podle následujících kroků.

1. V [Azure Portal](https://portal.azure.com)v levém navigačním podokně vyberte **Azure Active Directory**.

    ![Azure Active Directory – tlačítko](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte v horní části dialogového okna možnost **Nová aplikace** .

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscaler Beta**. Z panelu výsledek vyberte **Zscaler Beta** a pak vyberte **Přidat**.

     ![Zscaler Beta v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Zscaler Beta na základě testovacího uživatele Britta Simon.
Pokud chcete jednotné přihlašování pracovat, vytvořte vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zscaler Beta.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Zscaler Beta, dokončete následující stavební bloky:

- [Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on) , aby mohli vaši uživatelé používat tuto funkci.
- [Nakonfigurujte jednotné přihlašování Zscaler Beta](#configure-zscaler-beta-single-sign-on) pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
- [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pomocí Britta Simon.
- [Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) , aby mohl Britta Simon používat jednotné přihlašování Azure AD.
- [Vytvořte uživatele Zscaler Beta test](#create-a-zscaler-beta-test-user) , který bude mít protějšek Britta Simon v Zscaler Beta, který je propojený s Předprezentací Azure AD.
- [Otestujte jednotné přihlašování](#test-single-sign-on) a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Zscaler Beta, postupujte podle těchto kroků.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **Zscaler Beta** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS** , abyste mohli povolit jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** vyberte **Upravit** a otevřete tak základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** postupujte podle tohoto kroku:

    ![Informace o jednotném přihlašování v Zscaler Beta doméně a adresách URL](common/sp-intiated.png)

    - Do pole **přihlašovací adresa URL** zadejte adresu URL, kterou vaši uživatelé používají k přihlášení do aplikace Zscaler Beta.

    > [!NOTE]
    > Hodnota není skutečná. Aktualizujte hodnotu pomocí hodnoty skutečná adresa URL pro přihlášení. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta Zscaler Beta](https://www.zscaler.com/company/contact).

5. Aplikace Zscaler Beta očekává kontrolní výrazy SAML v určitém formátu. Do konfigurace atributů tokenu SAML musíte přidat mapování vlastních atributů. Následující snímek obrazovky ukazuje seznam výchozích atributů. Výběrem **Upravit** otevřete dialogové okno **atributy uživatele** .

    ![Uživatelské atributy – dialogové okno](common/edit-attribute.png)

6. Aplikace Zscaler Beta očekává, že se v odpovědi SAML vrátí několik atributů zpátky. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** pomocí následujících kroků přidejte atribut tokenu SAML, jak je znázorněno v následující tabulce.
    
    | Název | Zdrojový atribut | 
    | ---------------| --------------- |
    | memberOf  | User. assignedroles |

    a. Výběrem možnosti **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![Dialogové okno deklarace identity uživatele](common/new-save-attribute.png)

    ![Dialogové okno Spravovat deklarace identity uživatelů](common/new-attribute-details.png)

    b. Do pole **název** zadejte název atributu zobrazený pro tento řádek.

    c. Pole **obor názvů** nechejte prázdné.

    d. Jako **zdroj**vyberte **atribut**.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Vyberte **OK**.

    například Vyberte **Uložit**.

    > [!NOTE]
    > Informace o tom, jak nakonfigurovat role v Azure AD, najdete v tématu [konfigurace deklarace identity role](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. Pokud chcete stáhnout **certifikát (Base64)**, na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** vyberte **Stáhnout** . Uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

8. V části **nastavení beta verze Zscaler** zkopírujte adresy URL, které potřebujete pro vaše požadavky:

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    - Přihlašovací adresa URL
    - Identifikátor Azure AD
    - Odhlašovací adresa URL

### <a name="configure-zscaler-beta-single-sign-on"></a>Konfigurace jednotného přihlašování v Zscaler Beta verzi

1. Pokud chcete automatizovat konfiguraci v rámci Zscaler Beta, nainstalujte pomocí možnosti **nainstalovat rozšíření**možnost **zabezpečení prohlížeče zabezpečené přihlašování pro aplikace** .

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče vyberte **nastavit Zscaler Beta verze** přímo na aplikaci Zscaler Beta. Odtud zadejte přihlašovací údaje správce pro přihlášení ke službě Zscaler Beta. Rozšíření prohlížeče aplikaci automaticky nakonfiguruje za vás a automatizuje kroky 3 až 6.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit Zscaler Beta ručně, otevřete nové okno webového prohlížeče. Přihlaste se k webu Zscaler Beta společnosti jako správce a postupujte podle těchto kroků.

4. Přejděte na **Správa**  >  **Authentication**  >  **nastavení ověřování**ověřování a postupujte podle těchto kroků.
   
    ![Správa](./media/zscaler-beta-tutorial/ic800206.png "Správa")

    a. V části **typ ověřování**vyberte **SAML**.

    b. Vyberte **Konfigurovat SAML**.

5. V okně **Upravit SAML** proveďte tyto kroky: 
            
    ![Správa uživatelů & ověřování](./media/zscaler-beta-tutorial/ic800208.png "Správa uživatelů & ověřování")
    
    a. Do pole **Adresa URL portálu SAML** vložte **adresu URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. Do pole **atribut přihlašovací jméno** zadejte **NameId**.

    c. V poli **veřejný certifikát SSL** vyberte **Odeslat** a odešlete podpisový certifikát Azure SAML, který jste stáhli z Azure Portal.

    d. Přepněte na **zapnout automatické zřizování SAML**.

    e. Do pole **Zobrazovaný název uživatele** zadejte **DisplayName** , pokud chcete povolit autozřizování SAML pro atributy DisplayName.

    f. Do pole **atribut název skupiny** zadejte **memberOf** , pokud chcete povolit autozřizování SAML pro atributy memberOf.

    například Do pole **atribut název oddělení** zadejte **oddělení** , pokud chcete povolit autozřizování SAML pro atributy oddělení.

    h. Vyberte **Uložit**.

6. Na stránce **Konfigurovat ověřování uživatele** proveďte následující kroky:

    ![Nabídka aktivace a tlačítko aktivovat](./media/zscaler-beta-tutorial/ic800207.png)

    a. Najeďte myší na nabídku **Aktivace** v levém dolním rohu.

    b. Vyberte **aktivovat**.

## <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru
Pokud chcete nakonfigurovat nastavení proxy serveru v Internet Exploreru, postupujte podle těchto kroků.

1. Spusťte **aplikaci Internet Explorer**.

2. V nabídce **nástroje** vyberte **Možnosti Internetu** a otevřete tak dialogové okno **Možnosti Internetu** . 
    
     ![Dialogové okno Možnosti Internetu](./media/zscaler-beta-tutorial/ic769492.png "Možnosti Internetu")

3. Vyberte kartu **připojení** . 
  
     ![Karta Připojení](./media/zscaler-beta-tutorial/ic769493.png "Připojení")

4. Výběrem **Možnosti nastavení sítě LAN** otevřete dialogové okno **Nastavení místní sítě (LAN)** .

5. V části **proxy server** použijte následující postup: 
   
    ![Část proxy serveru](./media/zscaler-beta-tutorial/ic769494.png "Proxy server")

    a. Zaškrtněte políčko **použít proxy server pro vaši síť LAN** .

    b. Do pole **adresa** zadejte **Gateway. Zscaler Beta.net**.

    c. Do pole **port** zadejte **80**.

    d. Zaškrtněte políčko **obejít proxy server pro místní adresy** .

    e. Kliknutím na **tlačítko OK** zavřete dialogové okno **Nastavení místní sítě (LAN)** .

6. Kliknutím na **tlačítko OK** zavřete dialogové okno **Možnosti Internetu** .

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Vytvořte testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.

    ![Odkazy uživatelů a všech uživatelů](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. V dialogovém okně **uživatel** proveďte následující kroky:

    ![Uživatel – dialogové okno](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **Uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension`. Příklad: BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** . Zapište hodnotu, která se zobrazí v poli **heslo** .

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

Povolit Britta Simon pro použití jednotného přihlašování pomocí Azure udělením přístupu k Zscaler Beta verzi.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**  >  **Zscaler Beta verzi**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Zscaler Beta**.

    ![Odkaz na Zscaler Beta v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz na uživatele a skupiny](common/users-groups-blade.png)

4. Vyberte možnost **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Tlačítko pro přidání uživatele](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatele, jako je například **Britta Simon** . Pak zvolte **Vybrat** v dolní části obrazovky.

    ![Uživatelé a skupiny – dialogové okno](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. V dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli uživatele. Pak zvolte **Vybrat** v dolní části obrazovky.

    ![Dialogové okno vybrat roli](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

    ![Dialogové okno Přidat přiřazení](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Vytvořit testovacího uživatele Zscaler Beta

V této části se uživatel Britta Simon vytvoří v Zscaler Beta. Zscaler Beta podporuje **zřizování uživatelů za běhu**, což je ve výchozím nastavení povolené. V této části nemusíte nic dělat. Pokud uživatel ještě v Zscaler Beta neexistuje, vytvoří se po ověření nový.

>[!Note]
>Pokud chcete uživatele vytvořit ručně, obraťte se na [tým podpory Zscaler Beta](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

Otestujte konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Zscaler Beta, měli byste být automaticky přihlášeni ke službě Zscaler Beta, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

