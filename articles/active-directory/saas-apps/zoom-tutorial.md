---
title: 'Kurz: Integrace Azure Active Directory s přiblížení | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a přiblížení.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/05/2019
ms.author: jeedes
ms.openlocfilehash: 7dcf77a34179f6ab71e1b48e088d364393258e5a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998327"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Kurz: Integrace Azure Active Directory s přiblížení

V tomto kurzu se dozvíte, jak integrovat přiblížení s Azure Active Directory (Azure AD).
Přiblížení integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup ke zvětšení.
* Můžete povolit uživatelům, aby se automaticky přihlášeni přiblížení (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s přiblížení, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Přiblížení jednotné přihlášení povolený předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Přiblížení podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-zoom-from-the-gallery"></a>Přidání přiblížení z Galerie

Ke konfiguraci integrace přiblížení či oddálení do služby Azure AD, budete muset přidat přiblížení na váš seznam spravovaných aplikací SaaS z galerie.

**Přiblížení přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **přiblížení**vyberte **přiblížení** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Lupa v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotného přihlašování se podle testu uživateli přiblížení **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v přiblížení.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s zvětšení, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace přiblížení Single Sign-On](#configure-zoom-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele přiblížení](#create-zoom-test-user)**  – Pokud chcete mít protějšek Britta Simon zvětšení, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s přiblížení, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **přiblížení** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Přiblížení domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.zoom.us`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `<companyname>.zoom.us`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory přiblížení klienta](https://support.zoom.us/hc/en-us) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Přiblížení aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název | Obor názvů  |  Zdrojový atribut|
    | ---------------| --------------- | --------- |
    | E-mailová adresa  | user.mail  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail |
    | Jméno  | user.givenname  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname |
    | Příjmení  | user.surname  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname |
    | Telefonní číslo  | user.telephonenumber  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone |
    | Oddělení  | user.department  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department |
    | role |    user.assignedrole |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/role |

    > [!NOTE]
    > Po klepnutí na [tady](https://docs.microsoft.com/en-gb/azure/role-based-access-control/role-assignments-portal) vědět, jak nakonfigurovat Role ve službě Azure AD

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Vyberte zdroj jako **atribut**.

    d. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    e. Klikněte na tlačítko **Ok**

    f. Klikněte na **Uložit**.

    > [!NOTE]
    > Přiblížení může očekávat deklarace skupiny v datové části SAML, takže pokud jste vytvořili libovolná skupina, kontaktujte [tým podpory přiblížení klienta](https://support.zoom.us/hc/en-us) s informace o skupinách tak, aby se můžete nakonfigurovat informace o této skupině také na jeho konci. Je také potřeba zadat ID objektu [tým podpory přiblížení klienta](https://support.zoom.us/hc/en-us) tak, že můžete nakonfigurovat na jeho konci. Postupujte prosím podle [dokumentu](https://support.zoom.us/hc/en-us/articles/115005887566) k získání ID objektu.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. Na **nastavení přiblížení** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-zoom-single-sign-on"></a>Konfigurace přiblížení jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti přiblížení jako správce.

2. Klikněte na tlačítko **Single Sign-On** kartu.

    ![Karta přihlašování](./media/zoom-tutorial/ic784700.png "jednotného přihlašování")

3. Klikněte na tlačítko **řízení zabezpečení** kartu a potom přejděte **Single Sign-On** nastavení.

4. V části jednotného přihlašování proveďte následující kroky:

    ![Jednotné přihlašování – část](./media/zoom-tutorial/ic784701.png "jednotného přihlašování")

    a. V **přihlašovací adresa URL stránky** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    b. V **adresy URL odhlašovací stránky** textového pole vložte hodnotu **odhlašovací adresa URL** zkopírovanou z webu Azure portal.

    c. V poznámkovém bloku otevřete certifikát kódováním base-64, zkopírujte obsah ho do schránky a a vložte ho do **certifikát poskytovatele Identity** textového pole.

    d. V **vystavitele** textového pole vložte hodnotu **Azure Ad identifikátor** zkopírovanou z webu Azure portal. 

    e. Klikněte na **Uložit**.

    > [!NOTE]
    > Další informace najdete v dokumentaci k přiblížení [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu přiblížit.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **přiblížení**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **přiblížení**.

    ![Přiblížení odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-zoom-test-user"></a>Vytvořit testovacího uživatele přiblížení

Chcete-li povolit Azure AD uživatelům přihlášení přiblížit, musí být poskytnuty do přiblížení. V případě přiblížení zřizování se ruční úlohy.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>K poskytnutí uživatelského účtu, postupujte následovně:

1. Přihlaste se k vaší **přiblížení** společnosti serveru jako správce.

2. Klikněte na tlačítko **správu účtů** kartu a potom klikněte na tlačítko **Správa uživatelů**.

3. V části Správa uživatelů, klikněte na tlačítko **přidat uživatele**.

    ![Správa uživatelů](./media/zoom-tutorial/ic784703.png "Správa uživatelů")

4. Na **přidat uživatele** stránce, proveďte následující kroky:

    ![Přidání uživatelů](./media/zoom-tutorial/ic784704.png "přidat uživatele")

    a. Jako **typ uživatele**vyberte **základní**.

    b. V **e-mailů** textové pole, zadejte e-mailovou adresu platnou Azure AD účtu, který jste ke zřízení.

    c. Klikněte na tlačítko **Add** (Přidat).

> [!NOTE]
> Můžete použít jakékoli jiné přiblížení uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných přiblížení ke zřízení služby Azure Active Directory uživatelské účty.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici přiblížení na přístupovém panelu, vám by měl být automaticky přihlášeni přiblížit, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)