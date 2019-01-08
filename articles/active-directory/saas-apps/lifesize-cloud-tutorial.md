---
title: 'Kurz: Integrace Azure Active Directory s cloudem Lifesize | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Lifesize cloudu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.openlocfilehash: 312d1c8a1fdeb202d137b32a92e275b42c9934ee
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064266"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Kurz: Integrace Azure Active Directory s cloudem Lifesize

V tomto kurzu se dozvíte, jak integrovat Lifesize cloudu s Azure Active Directory (Azure AD).
Integrace Lifesize cloudu s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup do cloudu Lifesize.
* Uživatelům se automaticky přihlášeni ke cloudu Lifesize (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s využitím cloudu Lifesize, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Lifesize Cloud jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Lifesize Cloud podporuje **SP** jednotné přihlašování zahájené pomocí

* Lifesize Cloud podporuje **automatizovaná** zřizování uživatelů

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Přidání cloudové Lifesize z Galerie

Ke konfiguraci integrace Lifesize Cloud do služby Azure AD, budete muset přidat Lifesize Cloud z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání Lifesize Cloud z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Lifesize cloudu**vyberte **Lifesize cloudu** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Lifesize cloudu v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování s využitím cloudu Lifesize podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské Lifesize cloudu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s využitím Lifesize cloudu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Lifesize cloudu Single Sign-On](#configure-lifesize-cloud-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Lifesize cloudu](#create-lifesize-cloud-test-user)**  – Pokud chcete mít protějšek Britta Simon Lifesize cloudu, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s využitím cloudu Lifesize, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Lifesize cloudu** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Lifesize Cloudová doména a adresy URL jednotného přihlašování – informace](common/sp-identifier-relay.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://login.lifesizecloud.com/ls/?acs`

    b. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://login.lifesizecloud.com/<companyname>`

    c. Klikněte na tlačítko **nastavit další adresy URL**.

    d. V **stav přenosu** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://webapp.lifesizecloud.com/?ent=<identifier>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a stav přenosu. Kontakt [tým podpory Lifesize cloudu klienta](https://www.lifesize.com/en/support) přihlašovací adresa URL a identifikátor hodnoty a hodnotu lze získat stav přenosu z konfigurace jednotného přihlašování, který je vysvětlen později v tomto kurzu. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení cloudu Lifesize** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-lifesize-cloud-single-sign-on"></a>Konfigurace cloudu Lifesize jednotného přihlašování

1. Chcete-li získat jednotné přihlašování nakonfigurované pro vaši aplikaci, přihlaste se k Lifesize cloudové aplikace s oprávněními správce.

2. V pravém horním rohu klikněte na své jméno a potom klikněte na **rozšířená nastavení**.

    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

3. V nastavení zálohy nyní klikněte na **Konfigurace jednotného přihlašování** odkaz. Otevře se stránka Konfigurace jednotného přihlašování pro vaši instanci.

    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

4. Teď nakonfigurujte následující hodnoty v konfiguraci jednotného přihlašování uživatelského rozhraní.

    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    a. V **Vystavitel zprostředkovatele Identity** textového pole vložte hodnotu **Azure Ad identifikátor** zkopírovanou z webu Azure portal.

    b.  V **přihlašovací adresa URL** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    c. Otevřete váš certifikát base-64 kódovaných v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a a vložte ho do **certifikát X.509** textového pole.
  
    d. V mapování atributů SAML pro název prvního textového pole zadejte hodnotu jako `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    e. V mapování atributů SAML pro **příjmení** textového pole zadejte hodnotu jako `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    f. V mapování atributů SAML pro **e-mailu** textového pole zadejte hodnotu jako `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

5. Zkontrolujte konfiguraci, můžete kliknout na **Test** tlačítko.

    >[!NOTE]
    >Pro úspěšné testování potřebujete k dokončení Průvodce konfigurací ve službě Azure AD a také poskytnout přístup na uživatele nebo skupiny, jež lze provést test.

6. Povolit jednotné přihlašování tak, že zkontrolujete na **povolit jednotné přihlašování** tlačítko.

7. Nyní klikněte na **aktualizace** tlačítko tak, aby všechna nastavení jsou uložena. Tím se vygeneruje hodnota RelayState. Kopírování RelayState hodnoty, který je generován v textovém poli, vložte ji **stav přenosu** textového pole pod **Lifesize Cloudová doména a adresy URL** části.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do cloudu Lifesize.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Lifesize cloudu**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Lifesize cloudu**.

    ![Odkaz Lifesize cloudu v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-lifesize-cloud-test-user"></a>Vytvoření cloudu Lifesize testovacího uživatele

V této části vytvořte uživatele Britta Simon Lifesize cloudu. Lifesize cloud podporuje automatické zřizování uživatelů. Po úspěšném ověření v Azure AD se automaticky zřídí uživatele v aplikaci.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Lifesize Cloud na přístupovém panelu, měli byste obdržet přihlašovací stránku Lifesize cloudové aplikace. Tady budete muset zadat svoje uživatelské jméno a poté budou přesměrováni na domovskou stránku aplikace.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)