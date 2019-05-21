---
title: 'Kurz: Integrace Azure Active Directory s přenosem MOVEit – integrace služby Azure AD | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a přenos MOVEit – integrace služby Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: d7020299bbd52f5e7ba22809847815cb04048cb6
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905021"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Kurz: Integrace Azure Active Directory s přenosem MOVEit – integrace služby Azure AD

V tomto kurzu se dozvíte, jak integrovat MOVEit přenosy – integrace služby Azure AD se službou Azure Active Directory (Azure AD).
Integrace MOVEit přenosy – integrace služby Azure AD službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k MOVEit přenosy – integrace služby Azure AD.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k MOVEit přenosy – integrace Azure AD (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s přenosem MOVEit – integrace služby Azure AD, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* MOVEit přenosu – Azure AD integraci jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Přenosy MOVEit – integrace služby Azure AD podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Přidání MOVEit přenosy – integrace služby Azure AD z Galerie

Konfigurace integrace MOVEit přenosy – integrace služby Azure AD do služby Azure AD, budete muset přidat MOVEit přenosy – integrace služby Azure AD z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat MOVEit přenosy – integrace služby Azure AD z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **MOVEit přenosy – integrace služby Azure AD**vyberte **MOVEit přenosy – integrace služby Azure AD** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Přenosy MOVEit – integrace služby Azure AD v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfiguraci a testování Azure AD jednotné přihlašování s přenosem MOVEit – integrace služby Azure AD na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování k práci, vztah odkazu mezi uživatele služby Azure AD a související uživatelské v MOVEit přenosy – integrace služby Azure AD je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s přenosem MOVEit – integrace služby Azure AD, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace MOVEit přenosy – integrace služby Azure AD Single Sign-On](#configure-moveit-transfer---azure-ad-integration-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření MOVEit přenosu – Azure AD integrace testovacího uživatele](#create-moveit-transfer---azure-ad-integration-test-user)**  – Pokud chcete mít protějšek Britta Simon MOVEit přenosy – integrace služby Azure AD, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

K Azure AD nakonfigurovat jednotné přihlašování s přenosem MOVEit – integrace služby Azure AD, postupujte následovně:

1. V [webu Azure portal](https://portal.azure.com/)na **MOVEit přenosy – integrace služby Azure AD** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud máte **soubor metadat poskytovatele služeb**, proveďte následující kroky:

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![Zvolte soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnotu získá automaticky vyplní v **základní konfiguraci SAML** části:

    ![Přenosy MOVEit – integrace služby Azure AD domény a adresy URL jednotné přihlašování – informace](common/sp-identifier-reply.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://contoso.com`

    > [!NOTE]
    > **Přihlašovací adresa URL** není skutečnou hodnotu. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [MOVEit přenosy – integrace služby Azure AD podpora klientů](https://community.ipswitch.com/s/support) týmu k získání hodnoty. Si můžete stáhnout **soubor metadat zprostředkovatelů služby** z **adresa URL služby zprostředkovatele metadat** je vysvětlen později v **konfigurovat přenos MOVEit – integrace služby Azure AD jednoho Přihlašování** části kurzu. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení MOVEit přenosy – integrace služby Azure AD** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Konfigurace MOVEit přenosy – integrace služby Azure AD jednotného přihlašování

1. Přihlaste se k tenantovi MOVEit přenos jako správce.

2. V levém navigačním podokně klikněte na tlačítko **nastavení**.

    ![Části v aplikaci nastavení na straně](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Klikněte na tlačítko **jednotné přihlášení** odkaz, který je v části **zásady zabezpečení -> uživatele Auth**.

    ![Zabezpečení zásady na aplikaci na straně](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Klikněte na adresu URL metadat. odkaz ke stažení dokumentů metadat.

    ![Adresa URL služby zprostředkovatele metadat](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Ověřte **entityID** odpovídá **identifikátor** v **základní konfiguraci SAML** oddílu.
   * Ověřte **AssertionConsumerService** adresa URL umístění odpovídá **adresy URL odpovědi** v **základní konfiguraci SAML** oddílu.
    
     ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Klikněte na tlačítko **přidat zprostředkovatele Identity** tlačítko pro přidání nového poskytovatele federované Identity.

    ![Přidat zprostředkovatele Identity](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Klikněte na tlačítko **Procházet...**  Pokud chcete vybrat soubor metadat, který jste si stáhli z webu Azure portal, pak klikněte na tlačítko **přidat zprostředkovatele Identity** nahrát staženého souboru.

    ![Zprostředkovatele Identity SAML](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Vyberte "**Ano**" jako **povolené** v **Upravit federované nastavení zprostředkovatele Identity...**  stránky a klikněte na tlačítko **Uložit**.

    ![Nastavení poskytovatele federované Identity](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. V **upravit Federated Identity uživatele nastavení poskytovatele** stránce, udělejte toto:
    
    ![Upravit nastavení poskytovatele federované Identity](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Vyberte **SAML NameID** jako **přihlašovací jméno**.
    
    b. Vyberte **jiných** jako **jméno a příjmení** a **název atributu** vložte hodnotu do textového pole: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Vyberte **jiných** jako **e-mailu** a **název atributu** vložte hodnotu do textového pole: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Vyberte **Ano** jako **automatické vytvoření účtu na Sign-on**.
    
    e. Klikněte na tlačítko **Uložit** tlačítko.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon\@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k MOVEit přenosy – integrace služby Azure AD.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **MOVEit přenosy – integrace služby Azure AD**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **MOVEit přenosy – integrace služby Azure AD**.

    ![Přenosy MOVEit – integrace služby Azure AD odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Vytvoření MOVEit přenosu – Azure AD integrace testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v MOVEit přenosy – integrace služby Azure AD. Přenosy MOVEit – integrace služby Azure AD podporuje just-in-time zřizování, které jste povolili. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k MOVEit přenosy – integrace služby Azure AD, pokud ještě neexistuje.

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [MOVEit přenosy – tým podpory integrace klienta služby Azure AD](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na přenosy MOVEit – dlaždice integrace Azure AD na přístupovém panelu, vám by měl být automaticky přihlášeni k přenosu MOVEit – integrace služby Azure AD, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

