---
title: 'Kurz: Azure Active Directory integrace s NetSuite | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e462197bac854004aaf2d2f0f96e121ed081581a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967238"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Kurz: Integrace Azure Active Directory s NetSuite

V tomto kurzu se dozvíte, jak integrovat NetSuite s Azure Active Directory (Azure AD).
Integrace NetSuite s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k NetSuite.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k NetSuite (jednotné přihlašování) pomocí svých účtů Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s NetSuite potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným NetSuitem jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* NetSuite podporuje jednotné přihlašování **IDP** .
* NetSuite podporuje zřizování uživatelů **jenom v čase** .
* NetSuite podporuje [automatizované zřizování uživatelů](NetSuite-provisioning-tutorial.md)

## <a name="adding-netsuite-from-the-gallery"></a>Přidání NetSuite z Galerie

Pokud chcete nakonfigurovat integraci NetSuite do služby Azure AD, musíte přidat NetSuite z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat NetSuite z Galerie, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **NetSuite**, vyberte **NetSuite** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![NetSuite v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí NetSuite na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v NetSuite.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí NetSuite, musíte dokončit tyto stavební bloky:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurujte jednotné přihlašování NetSuite](#configure-netsuite-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření NetSuite Test User](#create-netsuite-test-user)** – pro Britta Simon v NetSuite, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí NetSuite, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **NetSuite** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně NetSuite a adresách URL](common/idp-reply.png)

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou URL odpovědi. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta NetSuite](https://www.netsuite.com/portal/services/support-services/suitesupport.shtml) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. NetSuite aplikace očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na tlačítko **Upravit** a otevřete dialog **uživatelské atributy** .

    ![image](common/edit-attribute.png)

6. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** upravte deklarace pomocí **ikony upravit** nebo přidejte deklarace pomocí **Přidat novou deklaraci identity** , jak je znázorněno na obrázku výše, a proveďte následující kroky:
    
    | Name | Zdrojový atribut | 
    | ---------------| --------------- |
    | účet  | `account id` |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

    >[!NOTE]
    >Hodnota atributu Account není reálné číslo. Tuto hodnotu budete aktualizovat, což je vysvětleno později v tomto kurzu.

4. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a stáhněte **XML metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení NetSuite** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-netsuite-single-sign-on"></a>Konfigurace jednotného přihlašování NetSuite

1. V prohlížeči otevřete novou kartu a přihlaste se k webu NetSuite společnosti jako správce.

2. Na panelu nástrojů v horní části stránky klikněte na **Nastavení**, přejděte na **Společnost** a klikněte na **Povolit funkce**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na panelu nástrojů uprostřed stránky klikněte na **SuiteCloud**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-suitecloud.png)

4. V části **spravovat ověřování** vyberte **jednotné přihlašování SAML** , abyste v NETSUITE mohli povolit možnost jednotného přihlašování SAML.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na panelu nástrojů v horní části stránky klikněte na tlačítko **nastavit**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setup.png)

6. V seznamu **úlohy instalace** klikněte na **integrace**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-integration.png)

7. V části **spravovat ověřování** klikněte na **jednotné přihlašování SAML**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-saml.png)

8. Na stránce **Nastavení SAML** v části **Konfigurace NetSuite** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Vyberte **metodu primárního ověřování**.

    b. Pro pole s popiskem **metadata poskytovatele identity SAMLV2**vyberte **nahrát soubor metadat IDP**. Pak klikněte na tlačítko **Procházet** a nahrajte soubor metadat, který jste stáhli z Azure Portal.

    c. Klikněte na **Submit** (Odeslat).

9. V NetSuite klikněte na **Nastavení** , přejděte na **Společnost** a v horní navigační nabídce klikněte na **informace o společnosti** .

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-com.png)

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-account-id.png)

    b. Na stránce **informace o společnosti** v pravém SLOUPCI zkopírujte **ID účtu**.

    c. Vložte **ID účtu** , které jste zkopírovali z účtu NetSuite, do pole **hodnota atributu** ve službě Azure AD. 

10. Předtím, než mohou uživatelé provádět jednotné přihlašování do NetSuite, musí být nejprve přiřazena příslušná oprávnění v NetSuite. Pokud chcete přiřadit tato oprávnění, postupujte podle následujících pokynů.

    a. V horní navigační nabídce klikněte na **Nastavení**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setup.png)

    b. V levém navigačním panelu vyberte **Uživatelé/role**a pak klikněte na **Spravovat role**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Klikněte na **Nová role**.

    d. Zadejte **název** nové role.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-new-role.png)

    e. Klikněte na **Uložit**.

    f. V nabídce v horní části klikněte na **oprávnění**. Pak klikněte na **Nastavení**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-sso.png)

    g. Vyberte **jednotné přihlašování SAML**a pak klikněte na **Přidat**.

    h. Klikněte na **Uložit**.

    i. V horní navigační nabídce klikněte na **Nastavení**a pak na **Správce instalace**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setup.png)

    j. V levém navigačním panelu vyberte **Uživatelé/role**a pak klikněte na **Spravovat uživatele**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Vyberte testovacího uživatele. Pak klikněte na **Upravit** a pak přejděte na kartu **přístup** .

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-edit-user.png)

    l. V dialogu role přiřaďte příslušnou roli, kterou jste vytvořili.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-add-role.png)

    m. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon\@yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k NetSuite.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **NetSuite**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **NetSuite**.

    ![Odkaz NetSuite v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-netsuite-test-user"></a>Vytvořit testovacího uživatele NetSuite

V této části se v NetSuite vytvoří uživatel s názvem Britta Simon. NetSuite podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v NetSuite neexistuje, vytvoří se po ověření nový.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici NetSuite, měli byste se automaticky přihlásit k NetSuite, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurace zřizování uživatelů](NetSuite-provisioning-tutorial.md)

