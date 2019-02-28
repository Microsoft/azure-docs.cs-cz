---
title: 'Kurz: Integrace Azure Active Directory s Image Relay | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a přenosu bitové kopie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b9675289ab14b00d565a5863600845d42b55030
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985950"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Kurz: Integrace Azure Active Directory s Image Relay

V tomto kurzu se dozvíte, jak integrovat Image Relay s Azure Active Directory (Azure AD).
Obrázek Relay integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k předávání obrázků.
* Uživatelům se automaticky přihlášeni k předávání obrázků (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Relay bitové kopie, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Obrázek Relay jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Obrázek Relay podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-image-relay-from-the-gallery"></a>Přidání propojení Image z Galerie

Ke konfiguraci integrace služby Image Relay do služby Azure AD, budete muset přidat Image Relay z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Image Relay z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Image Relay**vyberte **Image Relay** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Obrázek Relay v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Relay Image na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské Image Relay.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Relay bitové kopie, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Image předávání přes jednotné přihlašování](#configure-image-relay-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření Image Relay testovacího uživatele](#create-image-relay-test-user)**  – Pokud chcete mít protějšek Britta Simon Image Relay, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Image Relay, postupujte následovně:

1. V [webu Azure portal](https://portal.azure.com/)na **Image Relay** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Obrázek Relay domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.imagerelay.com/`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory Image přenosový klient](http://support.imagerelay.com/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavit obrázek Relay** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-image-relay-single-sign-on"></a>Konfigurace Image předávání přes jednotné přihlašování

1. V jiném okně prohlížeče Přihlaste se na web společnosti Image Relay jako správce.

2. Na panelu nástrojů v horní části klikněte na tlačítko **uživatelů a oprávnění** pracovního vytížení.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

3. Klikněte na tlačítko **vytvořit nové oprávnění**.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

4. V **nastavení jednotného přihlašování** úloh, vyberte **tato skupina může pouze přihlášení pomocí jednotného přihlašování** zaškrtněte políčko a potom klikněte na tlačítko **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

5. Přejděte na **nastavení účtu**.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

6. Přejděte **nastavení jednotného přihlašování** pracovního vytížení.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

7. Na **nastavení SAML** dialogového okna, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. V **přihlašovací adresa URL** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    b. V **odhlašovací adresa URL** textového pole vložte hodnotu **odhlašovací adresa URL** zkopírovanou z webu Azure portal.

    c. Jako **formát Id názvu**vyberte **urn: oasis: názvy: tc: SAML:1.1:nameid – formát: emailAddress**.

    d. Jako **vazby možnosti pro žádosti od poskytovatele služeb (přenosu Image)** vyberte **příspěvek vazby**.

    e. V části **x.509 Certificate**, klikněte na tlačítko **aktualizace certifikátu**.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte jeho obsah a vložte jej do **x.509 Certificate** textového pole.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. V **zřizování uživatelů za běhu** vyberte **povolit zřizování uživatelů za běhu**.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Vyberte skupinu oprávnění (například **jednotného přihlašování základní**) povolený pro přihlášení jenom prostřednictvím jednotného přihlašování.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k předávání obrázků.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Image Relay**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Image Relay**.

    ![Odkaz obrázku Relay v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-image-relay-test-user"></a>Vytvoření Image Relay testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon Relay bitové kopie.

**K vytvoření uživateli Britta Simon Image Relay, postupujte následovně:**

1. Přihlašování k webu společnosti Image Relay jako správce.

2. Přejděte na **uživatelů a oprávnění** a vyberte **vytvořit jednotné přihlašování uživatele**.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Zadejte **e-mailu**, **křestní jméno**, **příjmení**, a **společnosti** chcete zřídit a vyberte skupinu oprávnění (pro uživatele Příklad: základní jednotné přihlašování) která je skupina, která mohou přihlašovat pouze pomocí jednotného přihlašování.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_22.png)

4. Klikněte na možnost **Vytvořit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Relay bitové kopie na přístupovém panelu, vám by měl být automaticky přihlášeni do služby Relay bitové kopie, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)