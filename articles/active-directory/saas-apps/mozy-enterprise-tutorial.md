---
title: 'Kurz: Integrace Azure Active Directory Mozy Enterprise | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 4e7570afc5d359877f677e692678b09036da9ad3
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240454"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Kurz: Integrace Azure Active Directory Mozy Enterprise

V tomto kurzu se dozvíte, jak integrovat Mozy organizace Azure Active Directory (Azure AD).
Mozy podnikové integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup do firemní sítě Mozy.
* Uživatelům se automaticky přihlášeni k Mozy Enterprise (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Mozy Enterprise, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Mozy podnikové jednotné přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje organizace Mozy **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Přidání organizace Mozy z Galerie

Ke konfiguraci integrace Mozy organizace do služby Azure AD, budete muset přidat Mozy Enterprise na váš seznam spravovaných aplikací SaaS z galerie.

**Přidání organizace Mozy z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Mozy Enterprise**vyberte **Mozy Enterprise** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Mozy Enterprise v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a test Azure AD jednotné přihlašování s Mozy Enterprise podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Mozy Enterprise.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Mozy organizace, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Mozy Enterprise Single Sign-On](#configure-mozy-enterprise-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele organizace Mozy](#create-mozy-enterprise-test-user)**  – Pokud chcete mít protějšek Britta Simon Mozy Enterprise, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Mozy organizace, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Mozy Enterprise** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Mozy podnikové domény a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Mozy Enterprise Client](http://support.mozy.com/) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Mozy Enterprise** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-mozy-enterprise-single-sign-on"></a>Konfigurace Mozy Enterprise Single Sign-On

1. V okně jiný webový prohlížeč přihlaste jako správce serveru vaší společnosti Mozy Enterprise.

2. V **konfigurace** klikněte na tlačítko **zásady ověřování**.
   
    ![Zásady ověřování](./media/mozy-enterprise-tutorial/ic777314.png "zásady ověřování")

3. Na **zásady ověřování** části, proveďte následující kroky:
   
    ![Zásady ověřování](./media/mozy-enterprise-tutorial/ic777315.png "zásady ověřování")
   
    a. Vyberte **adresářová služba** jako **poskytovatele**.
   
    b. Vyberte **využití nabízené instalace LDAP**.
   
    c. Klikněte na tlačítko **ověřování SAML** kartu.
   
    d. Vložit **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal do **adresy URL ověřování** textového pole.
   
    e. Vložit **Azure AD identifikátor**, který jste zkopírovali z portálu Azure portal do **koncový bod SAML** textového pole.
   
    f. Otevřete stažený certifikát kódováním base-64 v poznámkovém bloku, zkopírujte obsah ho do schránky a vložte celý certifikát do **certifikát SAML** textového pole.
   
    g. Vyberte **povolit jednotné přihlašování pro správce, které se chcete přihlásit pomocí svých přihlašovacích údajů sítě**.
   
    h. Klikněte na tlačítko **uložit změny**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do firemní sítě Mozy.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Mozy Enterprise**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Mozy Enterprise**.

    ![Odkaz Mozy Enterprise v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-mozy-enterprise-test-user"></a>Vytvořit testovacího uživatele Mozy Enterprise

Chcete-li povolit uživatele Azure AD k přihlášení do Mozy organizace, musí být poskytnuty do Mozy Enterprise. V případě Mozy Enterprise zřizování je ruční úloha.

>[!NOTE]
>Můžete použít jakékoli jiné Mozy podnikové uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Mozy Enterprise uživatelským účtům, zřídit AAD.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se k vaší **Mozy Enterprise** tenanta.

2. Klikněte na tlačítko **uživatelé**a potom klikněte na tlačítko **Add New User**.
   
    ![Uživatelé](./media/mozy-enterprise-tutorial/ic777317.png "uživatelů")
   
    >[!NOTE]
    >**Přidat nového uživatele** možnost se zobrazí pouze pouze v případě **Mozy** je zvolen jako poskytovatele v rámci **zásady ověřování**. Pokud je nakonfigurované ověřování SAML, pak uživatelé jsou přidány automaticky při jejich první přihlášení pomocí jednotného přihlašování na.
    
3. V dialogovém okně Nový uživatel proveďte následující kroky:
   
    ![Přidání uživatelů](./media/mozy-enterprise-tutorial/ic777318.png "přidat uživatele")
   
    a. Z **zvolte skupinu** vyberte skupinu, ze seznamu.
   
    b. Z **jaký typ uživatelů** vyberte typ, ze seznamu.
   
    c. V **uživatelské jméno** textového pole zadejte jméno uživatele Azure AD.
   
    d. V **e-mailu** textového pole zadejte e-mailová adresa uživatele Azure AD.
   
    e. Vyberte **odeslání e-mailu uživatele instrukce**.
   
    f. Klikněte na tlačítko **přidat uživatele**.

     >[!NOTE]
     > Po vytvoření uživatele se odešle e-mail pro uživatele Azure AD, který obsahuje odkaz pro potvrzení účtu, pak se změní na aktivní.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Mozy Enterprise na přístupovém panelu, můžete by měl být automaticky přihlášeni na Mozy Enterprise, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

