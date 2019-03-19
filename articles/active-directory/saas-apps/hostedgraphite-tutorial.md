---
title: 'Kurz: Integrace Azure Active Directory s hostované grafitová | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a hostované grafitová.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49e70eab0b9824742a67e4446c5ff6f0a9a366ec
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894661"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Kurz: Integrace Azure Active Directory s hostované grafitová

V tomto kurzu se dozvíte, jak integrovat grafitová hostované službou Azure Active Directory (Azure AD).
Hostované grafitová integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k hostované grafitová.
* Uživatelům se automaticky přihlášeni k hostované grafitová (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s hostované grafitová, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Hostované grafitová jednotné přihlašování povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Hostované grafitová podporuje **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí
* Hostované grafitová podporuje **JIT** zřizování uživatelů

## <a name="adding-hosted-graphite-from-the-gallery"></a>Přidání hostované grafitová z Galerie

Pokud chcete nakonfigurovat integraci hostované grafitová do služby Azure AD, budete muset přidat hostované grafitová z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání hostované grafitová z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **hostované grafitová**vyberte **hostované grafitová** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Hostované grafitová v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí grafitová hostované na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v hostovaných grafitová.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s hostované grafitová, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace hostované grafitová Single Sign-On](#configure-hosted-graphite-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření hostovaného grafitová testovacího uživatele](#create-hosted-graphite-test-user)**  – Pokud chcete mít protějšek Britta Simon v hostovaných grafitová, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s hostované grafitová, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **hostované grafitová** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Hostované grafitová domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://www.hostedgraphite.com/metadata/<user id>`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://www.hostedgraphite.com/complete/saml/<user id>`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Hostované grafitová domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://www.hostedgraphite.com/login/saml/<user id>/`

    > [!NOTE]
    > Mějte prosím na paměti, že se nejedná skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. K získání těchto hodnot, můžete přejít na přístup -> Nastavení SAML na straně aplikace nebo kontaktujte [tým podpory hostovaných grafitová](mailto:help@hostedgraphite.com).

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. Na **nastavit hostované grafitová** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-hosted-graphite-single-sign-on"></a>Konfigurace prostředí grafitová jednotného přihlašování

1. Přihlašování k vašemu tenantovi hostované grafitová jako správce.

2. Přejděte **stránku nastavení SAML** na bočním panelu (**přístup -> Nastavení SAML**).

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

3. Potvrďte tyto adresy URL neodpovídá v konfiguraci **základní konfiguraci SAML** části webu Azure portal.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

4. V **Entity nebo ID vystavitele** a **přihlašovací adresu URL pro jednotné přihlašování** textových polí, vložte hodnotu **Azure Ad identifikátor** a **přihlašovací adresa URL** který jste zkopírovali z webu Azure portal.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

5. Vyberte **jen pro čtení** jako **výchozí Role uživatele**.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

6. Otevřete váš certifikát base-64 kódovaných v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a a vložte ho do **certifikát X.509** textového pole.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

7. Klikněte na tlačítko **Uložit** tlačítko.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k hostované grafitová.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **hostované grafitová**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **hostované grafitová**.

    ![Odkaz grafitová hostované v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-hosted-graphite-test-user"></a>Vytvoření hostovaného grafitová testovacího uživatele

V této části se vytvoří uživateli Britta Simon v hostovaných grafitová. Hostované grafitová podporuje zřizování uživatelů v čase, je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi hostované grafitová, vytvoří se nový po ověření.

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, budete muset požádat tým podpory hostovaných grafitová prostřednictvím <mailto:help@hostedgraphite.com>.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici grafitová hostované na přístupovém panelu, můžete by měl být automaticky přihlášeni k hostované grafitová, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

