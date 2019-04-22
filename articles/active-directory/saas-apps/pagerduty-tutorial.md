---
title: 'Kurz: Integrace Azure Active Directory s použitím PagerDuty | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: ded5854c5e669ab1982641169f13a9cb400d5d6d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270111"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Kurz: Integrace Azure Active Directory s použitím PagerDuty

V tomto kurzu se dozvíte, jak integrovat PagerDuty s Azure Active Directory (Azure AD).
PagerDuty integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k PagerDuty.
* Můžete povolit uživatelům být automaticky přihlášeni k PagerDuty (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s použitím PagerDuty, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* PagerDuty jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje PagerDuty **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-pagerduty-from-the-gallery"></a>Přidání PagerDuty z Galerie

Pokud chcete nakonfigurovat integraci PagerDuty do služby Azure AD, budete muset přidat PagerDuty z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat PagerDuty z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **PagerDuty**vyberte **PagerDuty** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![V seznamu výsledků PagerDuty](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, nakonfigurovat a otestovat Azure AD jednotné přihlašování s použitím PagerDuty podle testu uživateli **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v PagerDuty.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s použitím PagerDuty, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace PagerDuty Single Sign-On](#configure-pagerduty-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele PagerDuty](#create-pagerduty-test-user)**  – Pokud chcete mít protějšek Britta Simon v PagerDuty, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s použitím PagerDuty, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **PagerDuty** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![PagerDuty domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<tenant-name>.pagerduty.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory PagerDuty klienta](https://www.pagerduty.com/support/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení PagerDuty** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-pagerduty-single-sign-on"></a>Konfigurace přihlašování jedním PagerDuty

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Pagerduty.

2. V nabídce v horní části klikněte na tlačítko **nastavení účtu**.

    ![Nastavení účtu](./media/pagerduty-tutorial/ic778535.png "nastavení účtu")

3. Klikněte na tlačítko **jednotného přihlašování**.

    ![Jednotné přihlašování](./media/pagerduty-tutorial/ic778536.png "jednotného přihlašování")

4. Na **povolit jednotné přihlašování (SSO)** stránce, proveďte následující kroky:

    ![Povolit jednotné přihlašování](./media/pagerduty-tutorial/ic778537.png "povolit jednotné přihlašování")

    a. Otevření certifikátu kódováním base-64 stáhnout z webu Azure portal v programu Poznámkový blok, zkopírujte obsah ho do schránky a a vložte ho do **certifikát X.509** textové pole
  
    b. V **přihlašovací adresa URL** vložit do textového pole **přihlašovací adresa URL** zkopírovanou z webu Azure portal.
  
    c. V **odhlašovací adresa URL** vložit do textového pole **odhlašovací adresa URL** zkopírovanou z webu Azure portal.

    d. Vyberte **povolit uživatelskému jménu nebo heslu**.

    e. Vyberte **vyžadují přesné porovnání kontext ověřování** zaškrtávací políčko.

    f. Klikněte na tlačítko **uložit změny**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k PagerDuty.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **PagerDuty**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **PagerDuty**.

    ![V seznamu aplikací na odkaz PagerDuty](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-pagerduty-test-user"></a>Vytvořit testovacího uživatele PagerDuty

Povolit uživatele Azure AD se přihlaste k PagerDuty, musí být poskytnuty do PagerDuty.  
V případě PagerDuty zřizování se ruční úlohy.

>[!NOTE]
>Můžete použít jakékoli jiné Pagerduty uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Pagerduty ke zřízení služby Azure Active Directory uživatelské účty.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **Pagerduty** tenanta.

2. V nabídce v horní části klikněte na tlačítko **uživatelé**.

3. Klikněte na tlačítko **přidat uživatele**.
   
    ![Přidání uživatelů](./media/pagerduty-tutorial/ic778539.png "přidat uživatele")

4.  Na **pozvat váš tým** dialogového okna, proveďte následující kroky:
   
    ![Pozvat váš tým](./media/pagerduty-tutorial/ic778540.png "pozvat váš tým")

    a. Typ **jméno a příjmení** uživatele, jako je **Britta Simon**. 
   
    b. Zadejte **e-mailu** adresa uživatele, jako jsou **brittasimon\@contoso.com**.
   
    c. Klikněte na tlačítko **přidat**a potom klikněte na tlačítko **odeslat vyzývá**.
   
    >[!NOTE]
    >Všechny přidaní uživatelé obdrží pozvánku k vytvoření účtu PagerDuty.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici PagerDuty na přístupovém panelu, můžete by měl být automaticky přihlášeni k PagerDuty, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

