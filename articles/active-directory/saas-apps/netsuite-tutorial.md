---
title: 'Kurz: Integrace Azure Active Directory s NetSuite | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.openlocfilehash: 05e565d58faefbfc80815635afa38595177ad353
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807461"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Kurz: Integrace Azure Active Directory s NetSuite

V tomto kurzu se dozvíte, jak integrovat NetSuite s Azure Active Directory (Azure AD).
NetSuite integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k NetSuite.
* Můžete povolit uživatelům být automaticky přihlášeni k NetSuite (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s NetSuite, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* NetSuite jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* NetSuite podporuje **IDP** jednotné přihlašování zahájené pomocí
* NetSuite podporuje **JIT** zřizování uživatelů
* NetSuite podporuje [automatické zřizování uživatelů](NetSuite-provisioning-tutorial.md)

## <a name="adding-netsuite-from-the-gallery"></a>Přidání NetSuite z Galerie

Konfigurace integrace NetSuite do služby Azure AD, budete muset přidat NetSuite z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat NetSuite z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **NetSuite**vyberte **NetSuite** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![NetSuite v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí NetSuite podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v NetSuite.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s NetSuite, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace NetSuite Single Sign-On](#configure-netsuite-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele NetSuite](#create-netsuite-test-user)**  – Pokud chcete mít protějšek Britta Simon NetSuite, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s NetSuite, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **NetSuite** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![NetSuite domény a adresy URL jednotného přihlašování – informace](common/idp-reply.png)

    V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`

    > [!NOTE]
    > Hodnota není skutečný. Skutečná adresa URL odpovědi zaktualizujte příslušnou hodnotu. Kontakt [tým podpory NetSuite klienta](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. NetSuite aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název | Zdrojový atribut | 
    | ---------------| --------------- |
    | account  | `account id` |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

    >[!NOTE]
    >Hodnota atributu účtu není skutečný. Tato hodnota, který je vysvětlen později v tomto kurzu budete aktualizovat.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **soubor XML s metadaty**z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení NetSuite** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-netsuite-single-sign-on"></a>Konfigurace NetSuite jednotné přihlašování

1. Otevření nové záložky v prohlížeči a podepsat vaše společnost web NetSuite jako správce.

2. Na panelu nástrojů v horní části stránky klikněte na tlačítko **nastavení**, přejděte na **společnosti** a klikněte na tlačítko **povolit funkce**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na panelu nástrojů na druhé stránce klikněte na tlačítko **SuiteCloud**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-suitecloud.png)

4. V části **spravovat ověřování** vyberte **SAML jednotné přihlašování** možnost SAML jednotné přihlašování v NetSuite povolit.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na panelu nástrojů v horní části stránky klikněte na tlačítko **nastavení**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setup.png)

6. Z **ÚLOH její úvodního nastavení** klikněte na možnost **integrace**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-integration.png)

7. V **SPRAVOVAT ověřování** klikněte na tlačítko **SAML jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-saml.png)

8. Na **nastavení SAML** stránce v části **NetSuite konfigurace** části proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Vyberte **primární metoda ověřování**.

    b. Pro pole s popiskem **METADAT zprostředkovatele IDENTITY SAMLV2**vyberte **NAHRÁT soubor METADAT zprostředkovatele identity**. Pak klikněte na tlačítko **Procházet** nahrát soubor metadat, který jste si stáhli z webu Azure portal.

    c. Klikněte na **Submit** (Odeslat).

9. V NetSuite, klikněte na tlačítko **nastavení** přejděte na **společnosti** a klikněte na tlačítko **informace o společnosti** v horní navigační nabídce.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-com.png)

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-account-id.png)

    b. V **informace o společnosti** stránky v pravém sloupci kopii **ID účtu**.

    c. Vložit **ID účtu** který jste zkopírovali z účtu NetSuite je do **hodnota atributu** pole ve službě Azure AD. 

10. Předtím, než uživatel může provádět jednotné přihlašování do NetSuite, je třeba nejprve je přiřadit v NetSuite příslušná oprávnění. Postupujte podle pokynů níže toto přiřazení oprávnění.

    a. V horní navigační nabídce klikněte na tlačítko **nastavení**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setup.png)

    b. V levé navigační nabídce vyberte **uživatele/role**, pak klikněte na tlačítko **spravovat role**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Klikněte na tlačítko **novou roli**.

    d. Zadejte **název** pro novou roli.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-new-role.png)

    e. Klikněte na **Uložit**.

    f. V nabídce v horní části klikněte na tlačítko **oprávnění**. Pak klikněte na tlačítko **nastavení**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-sso.png)

    g. Vyberte **SAML jednotného přihlašování**a potom klikněte na tlačítko **přidat**.

    h. Klikněte na **Uložit**.

    i. V horní navigační nabídce klikněte na tlačítko **nastavení**, pak klikněte na tlačítko **Správce instalace**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setup.png)

    j. V levé navigační nabídce vyberte **uživatele/role**, pak klikněte na tlačítko **spravovat uživatele**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Vyberte testovací uživatel. Pak klikněte na tlačítko **upravit** a přejděte na **přístup** kartu.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-edit-user.png)

    l. V dialogovém okně role přiřaďte odpovídající roli, kterou jste vytvořili.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-add-role.png)

    m. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon k udělení přístupu k NetSuite použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **NetSuite**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **NetSuite**.

    ![NetSuite odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-netsuite-test-user"></a>Vytvoření NetSuite testovacího uživatele

V této části se vytvoří uživateli Britta Simon v NetSuite. NetSuite podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi NetSuite, vytvoří se nový po ověření.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici NetSuite na přístupovém panelu, můžete by měl být automaticky přihlášeni k NetSuite, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurace zřizování uživatelů](NetSuite-provisioning-tutorial.md)

