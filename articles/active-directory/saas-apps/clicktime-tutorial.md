---
title: 'Kurz: Integrace Azure Active Directory s ClickTime | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ClickTime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.openlocfilehash: 1330acbb18b33f8d150617b3fd8315697439d0d0
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692757"
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Kurz: Integrace Azure Active Directory s ClickTime

V tomto kurzu se dozvíte, jak integrovat ClickTime s Azure Active Directory (Azure AD).
ClickTime integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k ClickTime.
* Můžete povolit uživatelům být automaticky přihlášeni k ClickTime (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ClickTime, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* ClickTime jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje ClickTime **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-clicktime-from-the-gallery"></a>Přidání ClickTime z Galerie

Konfigurace integrace ClickTime do služby Azure AD, budete muset přidat ClickTime z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ClickTime z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ClickTime**vyberte **ClickTime** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![ClickTime v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí ClickTime podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v ClickTime.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ClickTime, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace ClickTime Single Sign-On](#configure-clicktime-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele ClickTime](#create-clicktime-test-user)**  – Pokud chcete mít protějšek Britta Simon ClickTime, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s ClickTime, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **ClickTime** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránce, proveďte následující kroky:

    ![ClickTime domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL: `https://app.clicktime.com/sp/`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce:
    | |
    |--|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení ClickTime** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-clicktime-single-sign-on"></a>Konfigurace ClickTime jednotné přihlašování

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti ClickTime.

1. Na panelu nástrojů v horní části klikněte na tlačítko **Předvolby**a potom klikněte na tlačítko **nastavení zabezpečení**.

1. V **předvolby přihlašování** konfigurační oddíl, proveďte následující kroky:
   
    ![Nastavení zabezpečení](./media/clicktime-tutorial/tic777280.png "nastavení zabezpečení")
   
    a.  Vyberte **povolit** přihlašovat pomocí jednotné přihlašování (SSO) s **Azure AD**.
   
    b. V **koncový bod poskytovatele Identity** vložit do textového pole **přihlašovací adresa URL** zkopírovanou z webu Azure portal.
   
    c.  Otevřít **certifikáty s kódováním base-64** stáhnout z webu Azure portal v **Poznámkový blok**, zkopírujte obsah a vložte jej do **certifikát X.509** textového pole.
   
    d.  Klikněte na **Uložit**.

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

V této části je povolit Britta Simon k udělení přístupu k ClickTime použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **ClickTime**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ClickTime**.

    ![Odkaz ClickTime v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-clicktime-test-user"></a>Vytvoření ClickTime testovacího uživatele

Chcete-li povolit uživatele Azure AD k přihlášení do ClickTime, musí být poskytnuty do ClickTime.  
V případě ClickTime zřizování se ruční úlohy.

> [!NOTE]
> Můžete použít jakékoli jiné ClickTime uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných ClickTime zřízení uživatelských účtů služby Azure AD.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **ClickTime** tenanta.

1. Na panelu nástrojů v horní části klikněte na tlačítko **společnosti**a potom klikněte na tlačítko **lidé**.
   
    ![Lidé](./media/clicktime-tutorial/tic777282.png "osoby")

1. Klikněte na tlačítko **přidat osobu**.
   
    ![Přidat osobu](./media/clicktime-tutorial/tic777283.png "přidat osoby")

1. V části nové osobě proveďte následující kroky:
   
    ![Lidé](./media/clicktime-tutorial/tic777284.png "osoby")
   
    a.  V **celý název** textové pole, typ celé jméno uživatele, jako je **Britta Simon**. 
  
    b.  V **e-mailová adresa** , jako je textové pole, typ e-mailu uživatele **brittasimon@contoso.com**.
       
    > [!NOTE]
    > Pokud chcete, můžete nastavit další vlastnosti nového objektu osoba.
   
    c.  Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici ClickTime na přístupovém panelu, můžete by měl být automaticky přihlášeni k ClickTime, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

