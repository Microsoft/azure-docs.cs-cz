---
title: 'Kurz: Integrace služby Azure Active Directory s LogicMonitor | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a LogicMonitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 463a8981689614d96100e03965117c9344aa5d50
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159515"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Kurz: Integrace služby Azure Active Directory s LogicMonitorem

V tomto kurzu se dozvíte, jak integrovat LogicMonitor s Azure Active Directory (Azure AD).
Integrace LogicMonitor s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k LogicMonitor.
* Můžete povolit uživatelům, aby se automaticky přihlásili k LogicMonitor (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s LogicMonitor, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením LogicMonitor

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* LogicMonitor podporuje **sp** inicioval sso

## <a name="adding-logicmonitor-from-the-gallery"></a>Přidání LogicMonitor z galerie

Chcete-li nakonfigurovat integraci LogicMonitor do Azure AD, je třeba přidat LogicMonitor z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat LogicMonitor z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **LogicMonitor**, z panelu výsledků vyberte **LogicMonitor** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![LogicMonitor v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí technologie LogicMonitor na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Služby Azure AD a souvisejícím uživatelem v LogicMonitoru.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí technologie LogicMonitor, je třeba provést následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace služby LogicMonitor Single Sign-On](#configure-logicmonitor-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte test Uživatele LogicMonitor](#create-logicmonitor-test-user)** – mít protějšek Britta Simon v LogicMonitor, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí technologie LogicMonitor, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **LogicMonitor** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![LogicMonitor Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.logicmonitor.com`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.logicmonitor.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte se na [tým podpory klienta LogicMonitor](https://www.logicmonitor.com/contact/) získat tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V **části Nastavit LogicMonitor** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-logicmonitor-single-sign-on"></a>Konfigurace jednotného přihlašování LogicMonitor

1. Přihlaste se k webu společnosti **LogicMonitor** jako správce.

2. V horní nabídce klikněte na **Nastavení**.

    ![Nastavení](./media/logicmonitor-tutorial/ic790052.png "Nastavení")

3. V navigační pálce na levé straně klikněte na **Jednotné přihlášení**

    ![Jednotné přihlašování](./media/logicmonitor-tutorial/ic790053.png "Jednotné přihlašování")

4. V části **Nastavení jednotného přihlašování (Jednotné přihlašování)** proveďte následující kroky:

    ![Nastavení jednotného přihlášení](./media/logicmonitor-tutorial/ic790054.png "Nastavení jednotného přihlášení")

    a. Vyberte **povolit jednotné přihlašování**.

    b. Jako **výchozí přiřazení role**vyberte možnost Jen pro **čtení**.

    c. Otevřete stažený soubor metadat v poznámkovém bloku a vložte obsah souboru do textového pole **Metadata zprostředkovatele identity.**

    d. Klikněte na **Save Changes** (Uložit změny).

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** zadejte **\@brittasimon vašecompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k LogicMonitor.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **LogicMonitor**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **LogicMonitor**.

    ![Odkaz LogicMonitor v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-logicmonitor-test-user"></a>Vytvořit testovacího uživatele LogicMonitor

Aby se uživatelé Azure AD mohli přihlásit, musí být zřízeni do aplikace LogicMonitor pomocí svých uživatelských jmen služby Azure Active Directory.

**Chcete-li konfigurovat zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k webu společnosti LogicMonitor jako správce.

2. V nabídce v horní části klikněte na **Nastavení**a potom klikněte na **Role a uživatelé**.

    ![Role a uživatelé](./media/logicmonitor-tutorial/ic790056.png "Role a uživatelé")

3. Klikněte na **Přidat**.

4. V části **Přidat účet** proveďte následující kroky:

    ![Přidání účtu](./media/logicmonitor-tutorial/ic790057.png "Přidání účtu")

    a. Zadejte hodnoty **uživatelského jména**, **e-mailu**, **hesla**a **znovu zadejte** hodnoty hesla uživatele služby Azure Active Directory, kterého chcete zřídit, do souvisejících textových polí.

    b. Vyberte **role**, **Zobrazit oprávnění**a **Stav**.

    c. Klepněte na **tlačítko Odeslat**.

> [!NOTE]
> Ke zřizování uživatelských účtů Služby Azure Active Directory můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů LogicMonitor nebo rozhraní API poskytovaná nástrojem LogicMonitor.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po klepnutí na dlaždici LogicMonitor na přístupovém panelu, by měl být automaticky přihlášeni k LogicMonitor, u kterého nastavíte přispojené k zámečku. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

