---
title: 'Kurz: Integrace služby Azure Active Directory s xMatters OnDemand | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi službou Azure Active Directory a xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: e8ae31122d59238ac104d7d873cf56f32977c9af
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086504"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Kurz: Integrace služby Azure Active Directory s xMatters OnDemand

V tomto kurzu se dozvíte, jak integrovat xMatters OnDemand s Azure Active Directory (Azure AD).
Integrace xMatters OnDemand s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k xMatters OnDemand.
* Můžete povolit uživatelům, aby se automaticky přihlásili ke službě xMatters OnDemand (Jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s xMatters OnDemand, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* xMatters OnDemand jednopřihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* xMatters OnDemand podporuje iniciované služby přihlašované do služby ASO iniciované iniciací **IDP**

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Přidání xMatters OnDemand z galerie

Chcete-li nakonfigurovat integraci xMatters OnDemand do Azure AD, je třeba přidat xMatters OnDemand z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat xMatters OnDemand z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **xMatters OnDemand**, vyberte **xMatters OnDemand** z panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![xMatters OnDemand v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí xMatters OnDemand na základě testovacího uživatele s názvem **Britta Simon**.
Pro jednotné přihlašování do práce, propojení vztah mezi uživatelem Azure AD a související uživatel v xMatters OnDemand musí být vytvořena.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí xMatters OnDemand, je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace xMatters OnDemand Single Sign-On](#configure-xmatters-ondemand-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte xMatters OnDemand testovací ho uživatele](#create-xmatters-ondemand-test-user)** – mít protějšek Britta Simon v xMatters OnDemand, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí služby xMatters OnDemand, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **xMatters OnDemand** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** proveďte následující kroky:

    ![xMatters OnDemand Domény a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|
    | |

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|
    | |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou adresou URL identifikátoru a odpovědi. Obraťte se [na tým podpory klienta xMatters OnDemand,](https://www.xmatters.com/company/contact-us/) abyste získali tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

    > [!IMPORTANT]
    > Certifikát je třeba předat [týmu podpory xMatters OnDemand](https://www.xmatters.com/company/contact-us/). Certifikát musí být odeslán týmem podpory xMatters před dokončením konfigurace jednotného přihlášení.

6. V **části Nastavit xMatters OnDemand** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-xmatters-ondemand-single-sign-on"></a>Konfigurace jednotného přihlašování xMatters OnDemand

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti XMatters OnDemand jako správce.

2. Na panelu nástrojů nahoře klikněte na **Správce**a potom na navigačním panelu na levé straně klikněte na **Podrobnosti společnosti.**

    ![Správce](./media/xmatters-ondemand-tutorial/IC776795.png "Správce")

3. Na stránce **Konfigurace SAML proveďte** následující kroky:

    ![Konfigurace SAML](./media/xmatters-ondemand-tutorial/IC776796.png "Konfigurace SAML")

    a. Vyberte **povolit saml**.

    b. V textovém poli **ID zprostředkovatele identity** vložte hodnotu **identifikátoru Azure AD,** kterou jste zkopírovali z webu Azure Portal.

    c. Do textového pole **Adresa URL jednotného přihlášení** vložte hodnotu přihlašovací adresy **URL,** kterou jste zkopírovali z portálu Azure.

    d. Do textového pole **Adresa URL jednotného odhlášení** vložte **adresu URL odhlášení**, kterou jste zkopírovali z webu Azure Portal.

    e. Na stránce Podrobnosti společnosti klikněte nahoře na **Uložit změny**.

    ![Údaje o společnosti](./media/xmatters-ondemand-tutorial/IC776797.png "Údaje o společnosti")

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** brittasimon@yourcompanydomain.extensionjméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k xMatters OnDemand.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **xMatters OnDemand**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **xMatters OnDemand**.

    ![Odkaz xMatters OnDemand v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-xmatters-ondemand-test-user"></a>Vytvořit uživatele testu xMatters OnDemand

Cílem této části je vytvořit uživatele s názvem Britta Simon v xMatters OnDemand.

**Pokud potřebujete vytvořit uživatele ručně, proveďte následující kroky:**

1. Přihlaste se k tenantovi **XMatters OnDemand.**

2. Klikněte na kartu **Uživatelé.** a potom klikněte na **Přidat uživatele**.

    ![Uživatelé](./media/xmatters-ondemand-tutorial/IC781048.png "Uživatelé")

3. V části **Přidat uživatele** proveďte následující kroky:

    ![Přidání uživatele](./media/xmatters-ondemand-tutorial/IC781049.png "Přidání uživatele")

    a. Vyberte **Možnost Aktivní**.

    b. Do textového pole **ID uživatele** zadejte id uživatele jako Brittasimon@contoso.com.

    c. Do textového pole **Křestní jméno** zadejte křestní jméno uživatele, jako je Britta.

    d. Do textového pole **Příjmení** zadejte příjmení uživatele, jako je Simon.

    e. V textovém poli **Web** zadejte platný web platného účtu Azure AD, který chcete zřídit.

    f. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici xMatters OnDemand na přístupovém panelu, by měl být automaticky přihlášeni k xMatters OnDemand, pro které nastavíte přihlašující služby. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

