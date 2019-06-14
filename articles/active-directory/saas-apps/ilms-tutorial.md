---
title: 'Kurz: Integrace Azure Active Directory s iLMS | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b2e012a4ce8ac4a9a5afb895d545beb0a0b8946
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100621"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Kurz: Integrace iLMS s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat iLMS s Azure Active Directory (Azure AD). Když integrujete iLMS s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k iLMS.
* Aby uživatelé mohli být automaticky přihlášeni k iLMS pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).
* iLMS jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. podporuje iLMS **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-ilms-from-the-gallery"></a>Přidání iLMS z Galerie

Konfigurace integrace iLMS do služby Azure AD, budete muset přidat iLMS z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **iLMS** do vyhledávacího pole.
1. Vyberte **iLMS** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s iLMS pomocí testovacího uživatele volá **Britta Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v iLMS.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s iLMS, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace jednotného přihlašování iLMS](#configure-ilms-sso)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele iLMS](#create-ilms-test-user)**  – Pokud chcete mít protějšek Britta Simon v iLMS, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **iLMS** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** stránky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, zadejte hodnoty pro následující pole:

    a. V **identifikátor** textového pole, Vložit **identifikátor** hodnotu zkopírujte z **poskytovatele služeb** SAML nastavení portálu pro správu iLMS.

    b. V **adresy URL odpovědi** textového pole, Vložit **(adresa URL koncového bodu)** hodnotu zkopírujte z **poskytovatele služeb** SAML nastavení portálu pro správu iLMS s následující vzor `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    V **přihlašovací adresa URL** textového pole, Vložit **(adresa URL koncového bodu)** hodnotu zkopírujte z **poskytovatele služeb** SAML nastavení v portálu pro správu iLMS jako `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Pokud chcete povolit JIT zřizování, iLMS aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na tlačítko **upravit** ikonu otevřete dialogové okno atributy uživatele.

    > [!NOTE]
    > Budete muset povolit **vytvořit uživatelský účet Un-recognized** v iLMS mapování těchto atributů. Postupujte podle pokynů [tady](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) , kde získáte představu o konfiguraci atributy.

1. Kromě toho výše iLMS aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka:

    | Název | Zdrojový atribut|
    | --------|------------- |
    | Dělení | user.department |
    | oblast | user.state |
    | Oddělení | user.jobtitle |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. Na **nastavení iLMS** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-ilms-sso"></a>Konfigurace iLMS jednotného přihlašování

1. V okně jiné webové prohlížeče, přihlaste se k vaší **portál pro správu iLMS** jako správce.

2. Klikněte na tlačítko **SSO:SAML** pod **nastavení** kartě Otevřít nastavení SAML a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/1.png)

3. Rozbalte **poskytovatele služeb** části a zkopírujte **identifikátor** a **(adresa URL koncového bodu)** hodnotu.

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/2.png) 

4. V části **zprostředkovatele Identity** klikněte na tlačítko **importovat Metadata**.

5. Vyberte **federačních metadat** soubor stáhnout z webu Azure portal ze **podpisový certifikát SAML** oddílu.

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Pokud chcete povolit JIT k vytváření účtů iLMS pro zrušení zřizování-rozpoznat uživatele, postupujte podle následujících kroků:

    a. Zkontrolujte **vytvořte účet bez rozpoznán jako uživatel**.

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mapování atributů ve službě Azure AD s atributy v iLMS. Ve sloupci atributu zadejte název atributy nebo výchozí hodnotu.

    c. Přejděte na **obchodní pravidla** kartě a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/5.png)

    d. Zkontrolujte **vytvořit Un-recognized oblasti, oddělení a oddělení** vytvořit oblasti, oddělení a oddělení, které už neexistují v době jednotného přihlašování.

    e. Zkontrolujte **aktualizace uživatelského profilu při přihlášení** k určení, zda je s každou Single Sign-on aktualizovat profil uživatele.

    f. Pokud **aktualizace prázdné hodnoty pro jiné povinných polí v profilu uživatele** zaškrtnutá možnost, profil volitelné pole, která jsou prázdné po přihlášení bude také způsobit iLMS profilu uživatele tak, aby obsahovala prázdné hodnoty příslušných polí.

    g. Zkontrolujte **odeslat E-mail s oznámením o chybě** a zadejte e-mailu uživatele, kde chcete dostávat e-mailové oznámení chyby.

7. Klikněte na tlačítko **Uložit** uložte nastavení tlačítkem.

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/save.png)

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

V této části se budou moci používat jednotné přihlašování Azure tím, že udělíte přístup k iLMS Britta Simon.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **iLMS**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-ilms-test-user"></a>Vytvoření iLMS testovacího uživatele

Aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele se vytvoří automaticky v aplikaci. JIT bude fungovat, pokud jste klikli **vytvořit uživatelský účet Un-recognized** zaškrtávací políčko během SAML nastavení konfigurace na portálu pro správu iLMS.

Pokud je potřeba ručně vytvořit uživatele, postupujte podle následujících kroků:

1. Přihlaste se k webu společnosti iLMS jako správce.

2. Klikněte na tlačítko **registrovat uživatele** pod **uživatelé** karty otevřete **registrovat uživatele** stránky.

   ![Přidat zaměstnance](./media/ilms-tutorial/3.png)

3. Na **registrovat uživatele** stránce, proveďte následující kroky.

    ![Přidat zaměstnance](./media/ilms-tutorial/create_testuser_add.png)

    a. V **křestní jméno** textové pole, typ první název, například Britta.

    b. V **příjmení** textového pole zadejte příjmení jako Simon.

    c. V **ID e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele BrittaSimon@contoso.com.

    d. V **oblasti** rozevíracím seznamu vyberte hodnotu pro oblast.

    e. V **dělení** rozevíracím seznamu vyberte hodnotu pro dělení.

    f. V **oddělení** rozevíracím seznamu vyberte hodnotu pro oddělení.

    g. Klikněte na **Uložit**.

    > [!NOTE]
    > Můžete odesílat poštu registrace pro uživatele tak, že vyberete **odeslat poštu registrace** zaškrtávací políčko.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice iLMS na přístupovém panelu, můžete by měl být automaticky přihlášeni k iLMS, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
