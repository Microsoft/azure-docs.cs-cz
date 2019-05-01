---
title: 'Kurz: Integrace Azure Active Directory s oblouk | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a oblouk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: 7f6e6b262dccd5b68c5a55c3d96a894205df8933
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695891"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Kurz: Integrace Azure Active Directory s oblouk

V tomto kurzu se dozvíte, jak integrovat oblouk s Azure Active Directory (Azure AD).
Integrace oblouk s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k oblouk.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k oblouk (Single Sign-On) pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s oblouk, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Oblouk jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje oblouk **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-rollbar-from-the-gallery"></a>Přidání oblouk z Galerie

Konfigurace integrace oblouk do služby Azure AD, budete muset přidat oblouk na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat oblouk z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **oblouk**vyberte **oblouk** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![V seznamu výsledků oblouk](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování služby Azure AD jednotné přihlašování s oblouk na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v oblouk.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s oblouk, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace oblouk Single Sign-On](#configure-rollbar-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele oblouk](#create-rollbar-test-user)**  – Pokud chcete mít protějšek Britta Simon oblouk, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s oblouk, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **oblouk** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Oblouk domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL: `https://saml.rollbar.com`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://rollbar.com/<accountname>/saml/sso/azure/`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Oblouk domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://rollbar.com/<accountname>/saml/login/azure/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečná adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory oblouk klienta](mailto:support@rollbar.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. Na **nastavení oblouk** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-rollbar-single-sign-on"></a>Konfigurace přihlašování jedním oblouk

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti oblouk jako správce.

1. Klikněte na **nastavení profilu** v pravém horním rohu a pak klikněte na tlačítko **nastavení název účtu**.

    ![Konfigurace](./media/rollbar-tutorial/general.png)

1. Klikněte na tlačítko **zprostředkovatele Identity** v části zabezpečení.

    ![Konfigurace](./media/rollbar-tutorial/configure1.png)

1. V **zprostředkovatele Identity SAML** části, proveďte následující kroky:

    ![Konfigurace](./media/rollbar-tutorial/configure2.png)

    a. Vyberte **AZURE** z **zprostředkovatele Identity SAML** rozevíracího seznamu.

    b. V poznámkovém bloku otevřete soubor metadat, zkopírujte obsah ho do schránky a vložte ho do **metadat SAML** textového pole.

    c. Klikněte na **Uložit**.

1. Po kliknutí na toto tlačítko, na obrazovce bude takto:

    ![Konfigurace](./media/rollbar-tutorial/configure3.png)

    > [!NOTE]
    > Aby bylo možné dokončit následující krok, sami nejprve musíte přidat jako uživatel na oblouk aplikaci v Azure.
    >

    a. Pokud chcete, aby všichni uživatelé k ověřování prostřednictvím Azure a pak klikněte na **přihlásit pomocí vašeho zprostředkovatele identity** opakované ověření přes Azure.  

    b.  Jakmile se vrátíte na obrazovku, vyberte **vyžadují přihlášení pomocí zprostředkovatele Identity SAML** zaškrtávací políčko.

    b. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k oblouk.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **oblouk**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **oblouk**.

    ![V seznamu aplikací na odkaz oblouk](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-rollbar-test-user"></a>Vytvořit testovacího uživatele oblouk

Povolení služby Azure AD uživatelům umožní přihlásit k oblouk musí být poskytnuty do oblouk. V případě oblouk zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k webu společnosti oblouk jako správce.

1. Klikněte na **nastavení profilu** v pravém horním rohu a pak klikněte na tlačítko **nastavení název účtu**.

    ![Uživatel](./media/rollbar-tutorial/general.png)

1. Klikněte na tlačítko **uživatelé**.

    ![Přidat zaměstnance](./media/rollbar-tutorial/user1.png)

1. Klikněte na tlačítko **Přizvěte členy týmu**.

    ![Pozvat](./media/rollbar-tutorial/user2.png)

1. V textovém poli zadejte jméno uživatele, jako je **brittasimon\@contoso.com** a kliknutím na **Add/pozvání**.

    ![Pozvat](./media/rollbar-tutorial/user3.png)

1. Uživatel dostane pozvánku a po jeho přijetí učitelského vytvořené v systému.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici oblouk na přístupovém panelu, vám by měl být automaticky přihlášeni na oblouk, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

