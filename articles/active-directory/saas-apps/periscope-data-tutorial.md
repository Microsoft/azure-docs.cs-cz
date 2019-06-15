---
title: 'Kurz: Integrace Azure Active Directory s daty přes aplikaci Periscope | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Data přes aplikaci Periscope.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f378edb-9ac9-494d-a84a-03357b923ee1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: efde1f1dafc62576398c5225ad1c652438fc0c31
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094505"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>Kurz: Integrace Azure Active Directory s daty přes aplikaci Periscope

V tomto kurzu se dozvíte, jak integrovat Data přes aplikaci Periscope s Azure Active Directory (Azure AD).
Integrace dat přes aplikaci Periscope s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k datům přes aplikaci Periscope.
* Uživatelům se automaticky přihlášeni k datům přes aplikaci Periscope (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s daty přes aplikaci Periscope, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Data přes aplikaci periscope jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Data přes aplikaci periscope **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-periscope-data-from-the-gallery"></a>Přidání dat přes aplikaci Periscope z Galerie

Konfigurace integrace přes aplikaci Periscope Data do služby Azure AD, budete muset přidat Data přes aplikaci Periscope z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Data přes aplikaci Periscope z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Data přes aplikaci Periscope**vyberte **Data přes aplikaci Periscope** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Data přes aplikaci periscope v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotného přihlašování přes aplikaci Periscope dat na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v datech přes aplikaci Periscope.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování přes aplikaci Periscope daty, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurovat přes aplikaci Periscope Data Single Sign-On](#configure-periscope-data-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Data přes aplikaci Periscope](#create-periscope-data-test-user)**  – Pokud chcete mít protějšek Britta Simon přes aplikaci Periscope dat, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s daty přes aplikaci Periscope, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Data přes aplikaci Periscope** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Přes aplikaci periscope dat domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte některý z adresy URL:
    
    | |
    |--|
    | `https://app.periscopedata.com/` |
    | `https://app.periscopedata.com/app/<SITENAME>` |

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > Přihlašovací adresa URL není skutečný. Aktualizujte hodnoty skutečné přihlašovací adresu URL. Kontakt [tým podpory přes aplikaci Periscope dat klienta](mailto:support@periscopedata.com) získat tuto hodnotu a hodnotu identifikátoru budou optimálnímu **konfigurovat přes aplikaci Periscope Data jednotného přihlašování** oddílu, který je vysvětlen později v tomto kurzu. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Konfigurovat přes aplikaci Periscope Data jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k datům přes aplikaci Periscope jako správce.

2. Otevřete nabídku ozubeného kolečka v levém dolním a otevřete **fakturace** > **zabezpečení** nabídky a proveďte následující kroky. Pouze správci mají k těmto nastavením přístup.

    ![Informace o konfiguraci dat přes aplikaci periscope](./media/periscope-data-tutorial/configure01.png)

    a. Kopírovat **adresa URL federačních metadat aplikace** z kroku #5 **podpisový certifikát SAML** a otevřít ji v prohlížeči. Tím se otevře dokument XML.

    b. V **Single Sign-On** textové pole, vyberte **Azure Active Directory**.

    c. Požadovanou značku **SingleSignOnService** a vložte **umístění** hodnotu **adresu URL jednotného přihlašování** textového pole.

    d. Požadovanou značku **SingleLogoutService** a vložte **umístění** hodnotu **adresa URL cíle na úrovni služby** textového pole.

    e. Kopírovat **identifikátor** hodnotu pro vaši instanci a vložte ji **identifikátor (Entity ID)** textové pole z **základní konfiguraci SAML** části na webu Azure portal.

    f. První značku souboru XML, zkopírujte hodnotu **entityID** a vložte ji **vystavitele** textového pole.

    g. Požadovanou značku **IDPSSODescriptor** pomocí protokolu SAML. V této části najdete značky **KeyDescriptor** s **použít = podepisování**. Zkopírujte hodnotu **certifikátu x 509** a vložte ji **certifikát** textového pole.

    h. Servery s více prostory můžete zvolit místo výchozí z **výchozí místo** rozevírací seznam. Bude jím místo, které noví uživatelé přidají do při přihlášení k datům přes aplikaci Periscope poprvé a jsou zřízené prostřednictvím Active Directory jednotného přihlašování.

    i. Nakonec klikněte na tlačítko **Uložit** a **potvrďte** změnit nastavení jednotného přihlašování tak, že zadáte **odhlášení**.

    ![Informace o konfiguraci dat přes aplikaci periscope](./media/periscope-data-tutorial/configure02.png)

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k datům přes aplikaci Periscope.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Data přes aplikaci Periscope**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Data přes aplikaci Periscope**.

    ![Propojení dat přes aplikaci Periscope v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-periscope-data-test-user"></a>Vytvořit testovacího uživatele Data přes aplikaci Periscope

Přihlaste se k datům přes aplikaci Periscope Azure AD uživatelům umožnit, musí být zřízená data přes aplikaci Periscope. V datech přes aplikaci Periscope zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k datům přes aplikaci Periscope jako správce.

2. Klikněte na **nastavení** ikony v levé dolní části nabídky a přejděte do **oprávnění**.

    ![Informace o konfiguraci dat přes aplikaci periscope](./media/periscope-data-tutorial/configure03.png)

3. Klikněte na **přidat uživatele** a proveďte následující kroky:

      ![Informace o konfiguraci dat přes aplikaci periscope](./media/periscope-data-tutorial/configure04.png)

    a. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je **Britta**.

    b. V **příjmení** textové pole, zadejte příjmení uživatele, jako je **Simon**.

    c. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je **brittasimon\@contoso.com**.

    d. Klikněte na tlačítko **přidat**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici přes aplikaci Periscope Data na přístupovém panelu, vám by měl být automaticky přihlášeni k datům přes aplikaci Periscope, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

