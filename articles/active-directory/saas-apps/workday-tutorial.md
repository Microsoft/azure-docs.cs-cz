---
title: 'Kurz: Integrace Azure Active Directory s aplikací Workday | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aba2d3bab7d709b4bb9ac18e4a9c6ed052a5fb83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086966"
---
# <a name="tutorial-integrate-workday-with-azure-active-directory"></a>Kurz: Integrace Workday s Azure Active Directory

V tomto kurzu se dozvíte, jak zajistit integraci Workday s Azure Active Directory (Azure AD). Při integraci Workday s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Workday.
* Aby uživatelé mohli být automaticky přihlášeni k Workday pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* WORKDAY jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje WORKDAY **SP** jednotné přihlašování zahájené.

## <a name="adding-workday-from-the-gallery"></a>Přidání Workday z Galerie

Pokud chcete nakonfigurovat integraci Workday do služby Azure AD, budete muset přidat Workday z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **Workday** do vyhledávacího pole.
1. Vyberte **Workday** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s aplikací Workday pomocí testovacího uživatele volá **Britta Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatelské ve Workday.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s aplikací Workday, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Workday](#configure-workday)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Workday](#create-workday-test-user)**  mít protějšek Britta Simon ve Workday, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Workday** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** stránky, zadejte hodnoty pro následující pole:

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://www.workday.com`

    c. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a adresy URL odpovědi. Vaše adresa URL odpovědi musí mít například subdomény: www wd2, wd3, wd3 impl, wd5, wd5 impl).
    > Pomocí příkazu podobného tomuto `http://www.myworkday.com` funguje, ale `http://myworkday.com` tak není. Kontakt [tým podpory klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Vaše aplikace Workday očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, přičemž **nameidentifier** je namapována na žádnou **user.userprincipalname**. Pracovní aplikace očekává **nameidentifier** namapovat s **user.mail**, **UPN**a tak dále, takže budete muset upravit mapování atributů kliknutím na  **Upravit** ikonu a změnit mapování atributů.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Tady jsme namapované ID názvu s hlavní název uživatele (user.userprincipalname) jako výchozí. Je nutné ID názvu se skutečné ID uživatele ve vašem účtu Workday (e-mailu, hlavního názvu uživatele, atd.) pro úspěšné fungování jednotného přihlašování.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Base64)** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. Chcete-li změnit **podepisování** možnosti podle vašich požadavků, klikněte na tlačítko **upravit** tlačítko Otevřít **podpisový certifikát SAML** dialogové okno.

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Vyberte **přihlašování SAML odpověď a kontrolní výraz** pro **podepisování možnost**.

    b. Klikněte na **Uložit**.

1. Na **nastavení Workday** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-workday"></a>Konfigurovat pracovní den

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Workday jako správce.

2. V **vyhledávacího pole** hledání s názvem **upravit nastavení Tenanta – zabezpečení** na horní levé straně domovské stránky.

    ![Upravit klienta zabezpečení](./media/workday-tutorial/IC782925.png "upravit klienta zabezpečení")

3. V **adresy URL přesměrování** části, proveďte následující kroky:

    ![Adresy URL přesměrování](./media/workday-tutorial/IC7829581.png "adresy URL přesměrování")

    a. Klikněte na tlačítko **přidat řádek**.

    b. V **přihlašovací adresa URL pro přesměrování**, **adresy URL přesměrování při vypršení časového limitu** a **adresy URL přesměrování Mobile** vložit do textového pole **přihlašovací adresa URL** který jste zkopírovali z **nastavení Workday** části webu Azure portal.

    c. V **adresy URL přesměrování při odhlášení** vložit do textového pole **odhlašovací adresa URL** zkopírovanou z **nastavení Workday** části webu Azure portal.

    d. V **používá pro prostředí** textové pole, vyberte název prostředí.  

   > [!NOTE]
   > Hodnota atributu prostředí se váže na hodnotu adresy URL tenanta:  
   > -Pokud název domény adresy URL klienta Workday začíná impl například: *https:\//impl.workday.com/\<tenanta\>/login-saml2.flex*), **prostředí**atribut musí být nastaven na implementaci.  
   > – Pokud je název domény začíná znakem jiný, budete muset požádat [tým podpory klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) zobrazíte odpovídající **prostředí** hodnotu.

4. V **nastavení SAML** části, proveďte následující kroky:

    ![Instalační program SAML](./media/workday-tutorial/IC782926.png "nastavení SAML")

    a.  Vyberte **povolit ověřování SAML**.

    b.  Klikněte na tlačítko **přidat řádek**.

5. V **poskytovatele Identity SAML** části, proveďte následující kroky:

    ![Zprostředkovatelé Identity SAML](./media/workday-tutorial/IC7829271.png "SAML zprostředkovatele Identity")

    a. V **název zprostředkovatele identit** textového pole zadejte název zprostředkovatele (například: *SPInitiatedSSO*).

    b. Na webu Azure Portal na **nastavení Workday** tématu, zkopírujte **Azure AD identifikátor** hodnotu a vložte jej do **vystavitele** textového pole.

    ![Zprostředkovatelé Identity SAML](./media/workday-tutorial/IC7829272.png "SAML zprostředkovatele Identity")

    c. Na webu Azure Portal na **nastavení Workday** tématu, zkopírujte **odhlašovací adresa URL** hodnotu a vložte jej do **odhlašovací adresa URL odpovědi** textového pole.

    d. Na webu Azure Portal na **nastavení Workday** tématu, zkopírujte **přihlašovací adresa URL** hodnotu a vložte jej do **adresa URL zprostředkovatele identity jednotného přihlašování služby** textového pole.

    e. V **používá pro prostředí** textové pole, vyberte název prostředí.

    f. Klikněte na tlačítko **certifikát veřejného klíče zprostředkovatele Identity**a potom klikněte na tlačítko **vytvořit**.

    ![Vytvoření](./media/workday-tutorial/IC782928.png "vytvořit")

    g. Klikněte na tlačítko **vytvořit x509 veřejný klíč**.

    ![Vytvoření](./media/workday-tutorial/IC782929.png "vytvořit")

6. V **veřejný klíč zobrazit x509** části, proveďte následující kroky:

    ![Veřejný klíč zobrazit x509](./media/workday-tutorial/IC782930.png "zobrazení x509 veřejný klíč")

    a. V **název** textového pole zadejte název vašeho certifikátu (například: *PPE\_SP*).

    b. V **platné od** textového pole zadejte platnou hodnotu atributu vašeho certifikátu.

    c.  V **platný na** textového pole zadejte platnou hodnotu atributu vašeho certifikátu.

    > [!NOTE]
    > Můžete získat platný datum a platný datum, od staženého certifikátu poklepáním.  Data patří **podrobnosti** kartu.
    >
    >

    d.  Otevřete váš certifikát base-64 kódovaných v programu Poznámkový blok a zkopírujte jeho obsah.

    e.  V **certifikát** textové pole, vložte obsah schránky.

    f.  Klikněte na **OK**.

7. Proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/workday-tutorial/WorkdaySSOConfiguratio.png "Konfigurace jednotného přihlašování")

    a.  V **ID poskytovatele služby** textové pole, typ **https://www.workday.com** .

    b. Vyberte **není Deflate žádosti o ověření iniciovaného Zprostředkovatelem přihlašování**.

    c. Jako **požádat o podpis metody ověřování**vyberte **SHA256**.

    ![Ověřování podpisu požadavku](./media/workday-tutorial/WorkdaySSOConfiguration.png "ověřování podpisu požadavku") 

    d. Klikněte na **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Ujistěte se prosím, že nastavíte jednotné přihlašování správně. V případě, že můžete povolit jednotné přihlašování s nesprávné nastavení, nebudete moci zadat aplikace pomocí svých přihlašovacích údajů a získat uzamčen. V takovém případě Workday poskytuje záložní adresy url přihlašovacího tam, kde uživatelé můžou přihlásit pomocí svého obvyklého uživatelského jména a hesla v následujícím formátu: [Your Workday URL]/login.flex?redirect=n

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal volá Britta Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `Britta Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `BrittaSimon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k Workday.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Workday**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-workday-test-user"></a>Vytvoření Workday testovacího uživatele

V této části vytvořte uživatele Britta Simon ve Workday. Práce s [tým podpory klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) přidat uživatele na platformě Workday. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice Workday na přístupovém panelu, můžete by měl být automaticky přihlášeni k Workday, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
