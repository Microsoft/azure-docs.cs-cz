---
title: 'Kurz: Integrace služby Azure Active Directory s jira samlem přiřazuje microsoft (V5.2) | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a jednotném přihlašování JIRA SAML od Microsoftu (V5.2).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3b304bb35ea69906fc9576f45733134387be1b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099517"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Kurz: Integrace služby Azure Active Directory s jira saml sso od Microsoftu (V5.2)

V tomto kurzu se dozvíte, jak integrovat JIRA SAML SSO od Microsoftu (V5.2) s Azure Active Directory (Azure AD).
Integrace jira saml sso microsoftem (V5.2) s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k JIRA SAML sso od Microsoftu (V5.2).
* Můžete povolit, aby se uživatelé automaticky přihlašovali k jednotnému přihlašování JIRA SAML od Microsoftu (V5.2) (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="description"></a>Popis

K povolení jednotného přihlášení použijte účet Služby Microsoft Azure Active Directory se serverem Atlassian JIRA. Tímto způsobem všichni uživatelé vaší organizace můžete použít přihlašovací údaje Azure AD k přihlášení do aplikace JIRA. Tento plugin používá SAML 2.0 pro federaci.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s JIRA SAML SSO od Microsoftu (V5.2), budete potřebovat následující položky:

- Předplatné Azure AD
- Jira Core a Software 5.2 by měly být nainstalovány a nakonfigurovány v 64bitové verzi systému Windows
- Jira server je https povolen
- Všimněte si, že podporované verze pro JIRA Plugin jsou uvedeny v níže uvedené části.
- Jira server je dostupný na internetu, zejména na stránku Přihlášení do Azure AD pro ověřování a měl by být schopen přijímat token z Azure AD
- Pověření správce jsou nastavena v JIRA
- WebSudo je zakázáno v JIRA
- Testovací uživatel vytvořený v aplikaci serveru JIRA

> [!NOTE]
> Chcete-li otestovat kroky v tomto kurzu, nedoporučujeme používat produkční prostředí JIRA. Otestujte integraci nejprve ve vývojovém nebo přípravném prostředí aplikace a potom použijte produkční prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkční prostředí, pokud to není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete získat měsíční zkušební verzi zde: [Zkušební nabídka](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Podporované verze JIRA

* Jádra a software JIRA: 5.2
* JIRA také podporuje 6,0 až 7,12. Další podrobnosti zobrazíte klepnutím na [položku JIRA SAML SSO od společnosti Microsoft.](jiramicrosoft-tutorial.md)

> [!NOTE]
> Vezměte prosím na vědomí, že náš PLUGIN JIRA funguje také na Ubuntu verze 16.04

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Jira SAML SSO od Microsoft (V5.2) podporuje **SP** iniciované sso

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Přidání sso Jira SAML od microsoftu (V5.2) z galerie

Chcete-li nakonfigurovat integraci jira SAML sso microsoftem (V5.2) do Azure AD, musíte přidat JIRA SAML SSO od Microsoftu (V5.2) z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat jira saml sso od společnosti Microsoft (V5.2) z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **jira saml sso od společnosti Microsoft (V5.2)**, vyberte **JIRA SAML SSO od Microsoft (V5.2)** z panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

    ![Jira SAML SSO od Microsoftu (V5.2) v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování JIRA SAML od microsoftu (V5.2) na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v jednotném přihlašování JIRA SAML od Microsoftu (V5.2).

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování JIRA SAML od Microsoftu (V5.2), musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování JIRA SAML od společnosti Microsoft (V5.2) Jednotné přihlašování](#configure-jira-saml-sso-by-microsoft-v52-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte jira saml sso podle Microsoft (V5.2) testovací uživatel](#create-jira-saml-sso-by-microsoft-v52-test-user)** – mít protějšek Britta Simon v JIRA SAML SSO od Microsoftu (V5.2), který je propojen s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování JIRA SAML od Microsoftu (V5.2), proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **JIRA SAML od Microsoftu (V5.2)** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Jednotné přihlašování JIRA SAML od společnosti Microsoft (V5.2) Informace o doméně a adresách URL pro jednotné přihlášení](common/sp-identifier-reply.png)

    a. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<domain:port>/plugins/servlet/saml/auth`

    b. Do pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<domain:port>/`

    c. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a adresou URL pro přihlášení. Port je volitelný v případě, že se jedná o pojmenovanou adresu URL. Tyto hodnoty jsou přijímány během konfigurace pluginu Jira, který je vysvětlen dále v tutoriálu.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na tlačítko Kopírovat, chcete-li zkopírovat **adresu URL metadat federace aplikací** a uložit ji do počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-jira-saml-sso-by-microsoft-v52-single-sign-on"></a>Konfigurace jednotného přihlašování JIRA SAML od společnosti Microsoft (V5.2) jednotného přihlašování

1. V jiném okně webového prohlížeče se přihlaste k instanci JIRA jako správce.

2. Najeďte přes kolečko a klikněte **na doplňky**.

    ![Konfigurace jednotného přihlašování](./media/jira52microsoft-tutorial/addon1.png)

3. V části Karta Doplňky klikněte na **Spravovat doplňky**.

    ![Konfigurace jednotného přihlašování](./media/jira52microsoft-tutorial/addon7.png)

4. Stáhněte si plugin ze [služby Stažení softwaru společnosti Microsoft](https://www.microsoft.com/download/details.aspx?id=56521). Ručně nahrajte plugin poskytovaný společností Microsoft pomocí nabídky **Nahrát doplněk.** Na stahování pluginů se vztahuje [smlouva o poskytování služeb společnosti Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Konfigurace jednotného přihlašování](./media/jira52microsoft-tutorial/addon12.png)

5. Jakmile je plugin nainstalován, zobrazí se v části **Doplňky nainstalované uživatelem.** Chcete-li nakonfigurovat nový plugin, klepněte na **tlačítko Konfigurovat.**

    ![Konfigurace jednotného přihlašování](./media/jira52microsoft-tutorial/addon13.png)

6. Na konfigurační stránce proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Ujistěte se, že je pouze jeden certifikát mapován proti aplikaci tak, aby nedošlo k žádné chybě při řešení metadat. Pokud existuje více certifikátů, po vyřešení metadat, správce dostane chybu.

    a. V textovém poli **Url metadat** vložte hodnotu url metadat **federace aplikace,** kterou jste zkopírovali z portálu Azure, a klikněte na tlačítko **Přeložit.** Přečte adresu URL metadat idp a naplní všechny informace o polích.

    b. Zkopírujte hodnoty **adresy URL identifikátor, odpověď url a přihlásit se** a vložte je do textových polí **Identifikátor, Adresa URL a Podepsat adresu URL** v části Základní konfigurace **SAML** na webu Azure Portal.

    c. Do **pole Název tlačítka přihlášení** zadejte název tlačítka, které chce vaše organizace zobrazit na přihlašovací obrazovce.

    d. V **umístění chodu uživatele SAML** vyberte buď **ID uživatele v nameidentifier elementu Subject prohlášení** nebo **ID uživatele je v elementu Attribute**.  Toto ID musí být ID uživatele JIRA. Pokud ID uživatele není spárováno, systém nepovolí uživatelům přihlášení.

    > [!Note]
    > Výchozí umístění ID uživatele SAML je Identifikátor názvu. Můžete to změnit na možnost atributu a zadat příslušný název atributu.

    e. Pokud vyberete **ID uživatele v možnosti Element atributu,** pak do textového pole **název atributu** zadejte název atributu, kde se očekává ID uživatele. 

    f. Pokud používáte federovovovnou doménu (například ADFS atd.) s Azure AD, klikněte na možnost **Povolit zjišťování domovské sféry** a nakonfigurujte **název domény**.

    g. Do **pole Domain Name** zadejte název domény zde v případě přihlášení založeného na službě ADFS.

    h. Zaškrtněte **políčko Povolit jednotné odhlášení,** pokud se chcete odhlásit z Azure AD, když se uživatel odhlásí z JIRA. 

    i. Kliknutím na **tlačítko Uložit** nastavení uložte.

    > [!NOTE]
    > Další informace o instalaci a řešení potíží naleznete v [Průvodci správcem konektorů MS JIRA SSO connector](../ms-confluence-jira-plugin-adminguide.md) a k dispozici jsou také [nejčastější dotazy.](../ms-confluence-jira-plugin-faq.md)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** `brittasimon\@yourcompanydomain.extension`jméno typ pole . Například, BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k JIRA SAML jednotného přihlašování od Microsoftu (V5.2).

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **JIRA SAML SSO od Microsoftu (V5.2).**

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **JIRA SAML SSO podle společnosti Microsoft (V5.2)**.

    ![Odkaz JIRA SAML SSO od společnosti Microsoft (V5.2) v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>Vytvoření uživatelského rozhraní s testováním JIRA SAML od microsoftu (V5.2)

Aby se uživatelé Azure AD mohli přihlásit k místnímu serveru JIRA, musí být zřízeni do místního serveru JIRA.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k místnímu serveru JIRA jako správce.

2. Najeďte přes kolečko a klikněte na **správu uživatelů**.

    ![Přidat zaměstnance](./media/jira52microsoft-tutorial/user1.png)

3. Budete přesměrováni na stránku Přístup správce, abyste zadali **heslo** a klepněte na tlačítko **Potvrdit.**

    ![Přidat zaměstnance](./media/jira52microsoft-tutorial/user2.png)

4. V části **Karta Správa uživatelů** klikněte na vytvořit **uživatele**.

    ![Přidat zaměstnance](./media/jira52microsoft-tutorial/user3.png) 

5. Na stránce dialogového okna **Vytvořit nového uživatele** proveďte následující kroky:

    ![Přidat zaměstnance](./media/jira52microsoft-tutorial/user4.png)

    a. Do textového pole **E-mailová adresa** Brittasimon@contoso.comzadejte e-mailovou adresu uživatele jako .

    b. Do textového pole **Celé jméno** zadejte celé jméno uživatele, jako je Britta Simon.

    c. Do textového pole **Uživatelské jméno** zadejte Brittasimon@contoso.come-mail uživatele jako .

    d. Do textového pole **Heslo** zadejte heslo uživatele.

    e. Klepněte na **tlačítko Vytvořit uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici JIRA SAML SSO od společnosti Microsoft (V5.2) na přístupovém panelu, měli byste být automaticky přihlášeni k JIRA SAML SSO společností Microsoft (V5.2), pro které nastavíte sso. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
