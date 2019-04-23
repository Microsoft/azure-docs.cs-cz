---
title: 'Kurz: Integrace Azure Active Directory s LogicMonitor | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LogicMonitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a953ae52f37b8d48f9e2e0566325691dbb08307
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60257765"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Kurz: Integrace Azure Active Directory s LogicMonitor

V tomto kurzu se dozvíte, jak integrovat LogicMonitor s Azure Active Directory (Azure AD).
LogicMonitor integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k LogicMonitor.
* Můžete povolit uživatelům být automaticky přihlášeni k LogicMonitor (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s LogicMonitor, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* LogicMonitor jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje LogicMonitor **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-logicmonitor-from-the-gallery"></a>Přidání LogicMonitor z Galerie

Konfigurace integrace LogicMonitor do služby Azure AD, budete muset přidat LogicMonitor z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat LogicMonitor z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **LogicMonitor**vyberte **LogicMonitor** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![LogicMonitor v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí LogicMonitor podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v LogicMonitor.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s LogicMonitor, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace LogicMonitor Single Sign-On](#configure-logicmonitor-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele LogicMonitor](#create-logicmonitor-test-user)**  – Pokud chcete mít protějšek Britta Simon LogicMonitor, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s LogicMonitor, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **LogicMonitor** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![LogicMonitor domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.logicmonitor.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.logicmonitor.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory LogicMonitor klienta](https://www.logicmonitor.com/contact/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení LogicMonitor** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-logicmonitor-single-sign-on"></a>Konfigurace LogicMonitor jednotné přihlašování

1. Přihlaste se k vaší **LogicMonitor** společnosti serveru jako správce.

2. V nabídce v horní části klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/logicmonitor-tutorial/ic790052.png "nastavení")

3. V navigačním bat na levé straně, klikněte na tlačítko **jednotného přihlašování**

    ![Jednotné přihlašování](./media/logicmonitor-tutorial/ic790053.png "jednotného přihlašování")

4. V **nastavení jednotného přihlašování (SSO)** části, proveďte následující kroky:

    ![Jednotné přihlašování – nastavení](./media/logicmonitor-tutorial/ic790054.png "jednotné přihlašování – nastavení")

    a. Vyberte **povolit jednotné přihlašování**.

    b. Jako **výchozí přiřazení Role**vyberte **jen pro čtení**.

    c. Otevřete soubor stažený metadat v programu Poznámkový blok a vložte obsah souboru do **metadat zprostředkovatele Identity** textového pole.

    d. Klikněte na tlačítko **uložit změny**.

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

V této části je povolit Britta Simon k udělení přístupu k LogicMonitor použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **LogicMonitor**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **LogicMonitor**.

    ![Odkaz LogicMonitor v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-logicmonitor-test-user"></a>Vytvoření LogicMonitor testovacího uživatele

Pro uživatele Azure AD bude moct přihlásit musí být zřízená LogicMonitor aplikace pomocí jejich uživatelská jména Azure Active Directory.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se na web společnosti LogicMonitor jako správce.

2. V nabídce v horní části klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **rolí a uživatelů**.

    ![Rolí a uživatelů](./media/logicmonitor-tutorial/ic790056.png "rolí a uživatelů")

3. Klikněte na tlačítko **Add** (Přidat).

4. V **přidat účet** části, proveďte následující kroky:

    ![Přidat účet](./media/logicmonitor-tutorial/ic790057.png "přidat účet")

    a. Typ **uživatelské jméno**, **e-mailu**, **heslo**, a **zadejte znovu heslo** hodnoty uživatele Azure Active Directory, které chcete zřídit do související textových polí.

    b. Vyberte **role**, **zobrazit oprávnění**a **stav**.

    c. Klikněte na **Submit** (Odeslat).

> [!NOTE]
> Můžete použít jakékoli jiné LogicMonitor uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných LogicMonitor ke zřízení služby Azure Active Directory uživatelské účty.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici LogicMonitor na přístupovém panelu, můžete by měl být automaticky přihlášeni k LogicMonitor, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

