---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Kantega pro bambus | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování Kantega pro bambus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e238b574-9e9b-43b7-ab98-d2a87ff89d48
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 8c951d7f5f1629447b1b5c1fc6e8a1c202246d74
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099105"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bamboo"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Kantega pro bambus

V tomto kurzu se dozvíte, jak integrovat Kantega jednotné přihlašování pro bambus s Azure Active Directory (Azure AD).
Integrace Kantega jednotné přihlašování pro bambus s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Kantega jednotného přihlašování pro bambus.
* Uživatelům se automaticky přihlášeni k Kantega jednotné přihlašování pro bambus (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním Kantega pro bambus, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Kantega jednotné přihlašování pro bambus jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje jednotné přihlašování Kantega pro bambus **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-kantega-sso-for-bamboo-from-the-gallery"></a>Přidání Kantega jednotné přihlašování pro bambus z Galerie

Konfigurace integrace Kantega jednotné přihlašování pro bambus do služby Azure AD, budete muset přidat Kantega jednotné přihlašování pro bambus z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Kantega jednotné přihlašování pro bambus z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Kantega jednotné přihlašování pro bambus**vyberte **Kantega jednotné přihlašování pro bambus** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Kantega jednotné přihlašování pro bambus v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním Kantega bambus podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Kantega jednotné přihlašování pro bambus.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním Kantega pro bambus, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace jednotného přihlašování Kantega pro bambus Single Sign-On](#configure-kantega-sso-for-bamboo-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření Kantega jednotného přihlašování pro uživatele test bambus](#create-kantega-sso-for-bamboo-test-user)**  – Pokud chcete mít protějšek Britta Simon Kantega jednotné přihlašování pro bambus, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním Kantega pro bambus, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Kantega jednotné přihlašování pro bambus** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Jednotné přihlašování Kantega bambus domény a adresy URL jednotné přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Jednotné přihlašování Kantega bambus domény a adresy URL jednotné přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Tyto hodnoty jsou přijímány během konfigurace bambus modulu plug-in, který je vysvětlen později v tomto kurzu.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. Na **nastavit jednotné přihlašování Kantega pro bambus** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-kantega-sso-for-bamboo-single-sign-on"></a>Nakonfigurovat jednotné přihlašování Kantega pro bambus jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se na váš místní server bambus jako správce.

1. Najeďte myší na ikonu a klikněte na tlačítko **doplňky**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon1.png)

1. Karta části doplňků, klikněte na tlačítko **najít nové doplňky**. Hledání **Kantega jednotné přihlašování pro bambus (SAML & Kerberos)** a klikněte na tlačítko **nainstalovat** tlačítko k instalaci nové zásuvný modul SAML.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon2.png)

1. Spustí se instalace modulu plug-in.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon21.png)

1. Po dokončení instalace. Klikněte na **Zavřít**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon33.png)

1. Klikněte na **Manage** (Spravovat).

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon34.png)

1. Klikněte na tlačítko **konfigurovat** konfigurace nového modulu plug-in.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon3.png)

1. V **SAML** oddílu. Vyberte **Azure Active Directory (Azure AD)** z **přidat zprostředkovatele identity** rozevíracího seznamu.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon4.png)

1. Vyberte úroveň předplatného jako **základní**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon5.png)

1. Na **vlastnosti aplikace** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon6.png)

    a. Kopírovat **identifikátor ID URI aplikace** hodnoty a použít ho jako **identifikátor, adresa URL odpovědi a přihlašovací adresa URL** na **základní konfiguraci SAML** části webu Azure Portal.

    b. Klikněte na **Další**.

1. Na **import metadat** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon7.png)

    a. Vyberte **soubor metadat v mém počítači**a nahrát soubor metadat, který jste si stáhli z webu Azure portal.

    b. Klikněte na **Další**.

1. Na **název a jednotného přihlašování k umístění** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon8.png)

    a. Přidání názvu zprostředkovatele Identity v **název zprostředkovatele identit** textového pole (např. Azure AD).

    b. Klikněte na **Další**.

1. Ověřte podpisového certifikátu a klikněte na tlačítko **Další**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon9.png)

1. Na **bambus uživatelské účty** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon10.png)

    a. Vyberte **v případě potřeby vytvořte uživatele v adresáři společnosti bambus interní** a zadejte odpovídající název skupiny pro uživatele (může být více č. ze skupiny, oddělené čárkami).

    b. Klikněte na **Další**.

1. Klikněte na **Dokončit**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon11.png)

1. Na **známé domény pro službu Azure AD** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon12.png)

    a. Vyberte **známé domén** na levém panelu na stránce.

    b. Zadejte název domény **známé domén** textového pole.

    c. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Kantega jednotné přihlašování pro bambus.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Kantega jednotné přihlašování pro bambus**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Kantega jednotné přihlašování pro bambus**.

    ![Kantega jednotné přihlašování pro bambus odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-kantega-sso-for-bamboo-test-user"></a>Vytvoření Kantega jednotného přihlašování pro bambus testovacího uživatele

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k bambus, musí být poskytnuty do bambus. V případě Kantega jednotného přihlašování pro bambus zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se na váš místní server bambus jako správce.

1. Najeďte myší na ikonu a klikněte na tlačítko **Správa uživatelů**.

    ![Přidat zaměstnance](./media/kantegassoforbamboo-tutorial/user1.png)

1. Klikněte na tlačítko **uživatelé**. V části **přidat uživatele** části, proveďte následující kroky:

    ![Přidat zaměstnance](./media/kantegassoforbamboo-tutorial/user2.png)

    a. V **uživatelské jméno** , jako je textové pole, typ e-mailu uživatele Brittasimon@contoso.com.

    b. V **heslo** textového pole zadejte heslo uživatele.

    c. V **potvrzení hesla** textového pole zadejte znovu heslo uživatele.

    d. V **jméno a příjmení** textového pole zadejte celé jméno uživatele jako Britta Simon.

    e. V **e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.

    f. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na Kantega jednotné přihlašování pro bambus dlaždici na přístupovém panelu, můžete by měl být automaticky přihlášeni k Kantega jednotné přihlašování pro bambus, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
