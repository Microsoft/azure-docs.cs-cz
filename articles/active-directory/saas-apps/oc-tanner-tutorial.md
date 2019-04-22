---
title: 'Kurz: Integrace Azure Active Directory s O.C. Nováková - AppreciateHub | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a O.C. Nováková - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 21cbef1532928d51ba0c9f11e80304933df505b1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261747"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Kurz: Integrace Azure Active Directory s O.C. Nováková - AppreciateHub

V tomto kurzu se dozvíte, jak integrovat O.C. Nováková - AppreciateHub s Azure Active Directory (Azure AD).
Integrace O.C. Nováková - AppreciateHub s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k O.C. Nováková - AppreciateHub.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k O.C. Nováková - AppreciateHub (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s O.C. Nováková - AppreciateHub, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* O.C. Nováková - AppreciateHub jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* O.C. Podporuje AppreciateHub Nováková - **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Přidání O.C. Nováková - AppreciateHub z Galerie

Konfigurace integrace O.C. Nováková - AppreciateHub do služby Azure AD, je třeba přidat O.C. Nováková - AppreciateHub z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat O.C. Nováková - AppreciateHub z galerie, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **O.C. Nováková - AppreciateHub**vyberte **O.C. Nováková - AppreciateHub** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![O.C. Nováková - AppreciateHub v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s O.C. Nováková - AppreciateHub podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci, vztah odkazu mezi uživatele služby Azure AD a související uživatelské v O.C. Nováková - AppreciateHub musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s O.C. Nováková - AppreciateHub, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace O.C. Nováková - AppreciateHub Single Sign-On](#configure-oc-tanner---appreciatehub-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření O.C. Nováková - AppreciateHub testovacího uživatele](#create-oc-tanner---appreciatehub-test-user)**  – Pokud chcete mít protějšek Britta Simon O.C. Nováková - AppreciateHub, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s O.C. Nováková - AppreciateHub, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **O.C. Nováková - AppreciateHub** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud máte **soubor metadat poskytovatele služeb**, proveďte následující kroky:

    >[!NOTE]
    >Můžete stáhnout **soubor metadat poskytovatele služeb** z [zde](https://fed.appreciatehub.com/fed/sp/metadata)

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![Zvolte soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v části základní konfiguraci SAML.

     ![O.C. Nováková - AppreciateHub domény a adresy URL jednotné přihlašování – informace](common/idp-intiated.png)

    > [!Note]
    > Pokud **identifikátor** a **adresy URL odpovědi** hodnoty nechcete získat polulated automaticky, a potom prosím zadejte hodnoty ručně podle vašich požadavků. Kontakt [O.C. Nováková - tým podpory AppreciateHub klienta](mailto:sso@octanner.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavit O.C. Nováková - AppreciateHub** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-oc-tanner---appreciatehub-single-sign-on"></a>Konfigurace O.C. Nováková - AppreciateHub jednotné přihlašování

Ke konfiguraci jednotného přihlašování na **O.C. Nováková - AppreciateHub** straně, je nutné odeslat na stažený **kód XML metadat federace** a vhodné zkopírovaný adresy URL z webu Azure portal [O.C. Nováková - tým podpory AppreciateHub](mailto:sso@octanner.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** pole Typ uživatele jako BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k O.C. Nováková - AppreciateHub.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **O.C. Nováková - AppreciateHub**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **O.C. Nováková - AppreciateHub**.

    ![O.C. Nováková - AppreciateHub odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-oc-tanner---appreciatehub-test-user"></a>Vytvoření O.C. Nováková - AppreciateHub testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v O.C. Nováková - AppreciateHub.

**Vytvořte uživatele v O.C. jako Britta Simon Nováková - AppreciateHub, proveďte následující kroky:**

Požádejte vašeho [O.C. Nováková - tým podpory AppreciateHub](mailto:sso@octanner.com) pro vytvoření uživatele, který má jako atribut nameID stejnou hodnotu jako uživatelské jméno Britta Simon ve službě Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete O.C. Nováková - AppreciateHub dlaždici na přístupovém panelu, vám by měl být automaticky přihlášeni k O.C. Nováková - AppreciateHub, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
