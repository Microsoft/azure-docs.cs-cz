---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML JIRA Microsoft (V5.2) | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování SAML JIRA Microsoft (V5.2).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a57457ad8a05b5e1173ae3e7ab992dc27c32c1e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163151"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML JIRA Microsoft (V5.2)

V tomto kurzu se dozvíte, jak integrovat JIRA SAML SSO Microsoft (V5.2) se službou Azure Active Directory (Azure AD).
Integrace jednotného přihlašování SAML JIRA Microsoft (V5.2) s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k systému JIRA SAML SSO Microsoft (V5.2).
* Uživatelům se automaticky nepřihlásil k JIRA SAML SSO ve společnosti Microsoft (verze 5.2) (jednotné přihlašování) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="description"></a>Popis

Povolit jednotné přihlašování pomocí účtu Microsoft Azure Active Directory serverem Atlassian JIRA. Tímto způsobem všichni uživatelé vaší organizace můžete použít přihlašovací údaje služby Azure AD k přihlášení do aplikace systému JIRA. Tento modul plug-in používá protokol SAML 2.0 pro federaci.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním SAML JIRA Microsoft (V5.2), potřebujete následující položky:

- Předplatné Azure AD
- Základní JIRA a 5.2 softwaru by měl nainstalovaná a nakonfigurovaná na verzi Windows 64-bit
- JIRA server je povolen protokol HTTPS
- Všimněte si, že podporované verze pro modul plug-in JIRA jsou uvedeny v následující části.
- JIRA server je dostupný na Internetu zejména na stránku pro přihlášení k Azure AD pro ověřování a měli byste může přijímat token ze služby Azure AD
- Přihlašovací údaje správce jsou nastavené ve službě JIRA
- WebSudo je zakázaný ve službě JIRA
- Testovací uživatel v serveru aplikaci JIRA

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkčního prostředí JIRA. Nejdřív otestovat integraci v vývojové nebo testovací prostředí aplikace a pak používat produkčním prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady: [Zkušební nabídka](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Podporované verze systému JIRA

* Základní JIRA a Software: 5.2
* JIRA podporuje také 6.0 k 7.12. Další podrobnosti získáte kliknutím [JIRA SAML SSO společností Microsoft](jiramicrosoft-tutorial.md)

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* JIRA SAML SSO Microsoft (V5.2) podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Přidání jednotného přihlašování SAML JIRA Microsoft (V5.2) z Galerie

Pokud chcete nakonfigurovat integraci jednotného přihlašování SAML JIRA Microsoft (V5.2) do služby Azure AD, budete muset přidat JIRA SAML SSO Microsoft (V5.2) z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat JIRA SAML SSO Microsoft (V5.2) z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **JIRA SAML SSO Microsoft (V5.2)** vyberte **JIRA SAML SSO Microsoft (V5.2)** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![JIRA SAML SSO ve společnosti Microsoft (verze 5.2) v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfiguraci a testování Azure AD jednotné přihlašování s jednotným Přihlašováním SAML JIRA Microsoft (V5.2) na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelem v systému JIRA SAML SSO Microsoft (V5.2).

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním SAML JIRA Microsoft (V5.2), které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace jednotného přihlašování SAML JIRA ve společnosti Microsoft (verze 5.2) jednotného přihlašování](#configure-jira-saml-sso-by-microsoft-v52-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření JIRA SAML SSO ve společnosti Microsoft (verze 5.2) testovacího uživatele](#create-jira-saml-sso-by-microsoft-v52-test-user)**  – Pokud chcete mít protějšek Britta Simon v systému JIRA SAML SSO ve společnosti Microsoft (verze 5.2), který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním SAML JIRA Microsoft (V5.2), proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **JIRA SAML SSO Microsoft (V5.2)** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![JIRA SAML SSO ve společnosti Microsoft (verze 5.2) domény a adresy URL jednotné přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<domain:port>/plugins/servlet/saml/auth`

    b. V **identifikátor** pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<domain:port>/`

    c. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Port je volitelný, v případě, že je pojmenovaný URL. Tyto hodnoty jsou přijímány během konfigurace modulu plug-in Jira, který je vysvětlen později v tomto kurzu.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-jira-saml-sso-by-microsoft-v52-single-sign-on"></a>Konfigurace systému JIRA SAML SSO ve společnosti Microsoft (verze 5.2) jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k vaší instanci JIRA jako správce.

2. Najeďte myší na ikonu a klikněte na tlačítko **doplňky**.

    ![Konfigurace jednotného přihlašování](./media/jira52microsoft-tutorial/addon1.png)

3. Karta části doplňků, klikněte na tlačítko **spravovat doplňky**.

    ![Konfigurace jednotného přihlašování](./media/jira52microsoft-tutorial/addon7.png)

4. Stáhněte si modul plug-in z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56521). Ručně nahrát modul plug-in poskytl pomocí Microsoft **nahrát doplněk** nabídky. Stáhnout modul plug-in se vztahuje smlouva [servisní smlouvou aplikace Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Konfigurace jednotného přihlašování](./media/jira52microsoft-tutorial/addon12.png)

5. Když je nainstalovaný modul plug-in, zobrazí se v **uživatel nainstaloval** části doplňky. Klikněte na tlačítko **konfigurovat** konfigurace nového modulu plug-in.

    ![Konfigurace jednotného přihlašování](./media/jira52microsoft-tutorial/addon13.png)

6. Proveďte následující kroky na stránce konfigurace:

    ![Konfigurace jednotného přihlašování](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Ujistěte se, že je pouze jeden certifikát mapují na aplikaci tak, aby se nezobrazí žádná chyba překladu metadat. Pokud existuje víc certifikátů, po vyřešení metadata a získá správce k chybě.

    a. V **adresa URL metadat** vložit do textového pole **adresa Url federačních metadat aplikace** hodnotu, která jste zkopírovali z webu Azure portal a klikněte **vyřešit** tlačítko. Načte adresu URL metadat zprostředkovatele identity a naplní všechny informace o pole.

    b. Kopírovat **identifikátor, adresa URL odpovědi a adresa URL přihlašování** hodnoty a vložit je do **identifikátor, adresa URL odpovědi a adresa URL přihlašování** textová pole v uvedeném pořadí **základní konfiguraci SAML** části na webu Azure portal.

    c. V **přihlašovací jméno tlačítko** zadejte název tlačítka, které vaše organizace chce, aby se uživatelům zobrazí na obrazovce přihlášení.

    d. V **SAML uživatelské ID umístění** vyberte buď **ID uživatele není v elementu NameIdentifier příkazu subjektu** nebo **ID uživatele není v elementu atribut**.  Toto ID musí být id uživatele JIRA. Pokud neodpovídá id uživatele, systém nedovolí uživatelům přihlášení.

    > [!Note]
    > Výchozí umístění SAML ID uživatele je název identifikátoru. Můžete to změnit atribut možnost a zadejte odpovídající název.

    e. Pokud vyberete **ID uživatele není v elementu atribut** možnost, pak v **název atributu** textového pole zadejte název atributu, kde se očekává Id uživatele. 

    f. Pokud používáte federovanou doménu (například služby AD FS atd.) pomocí služby Azure AD, potom klikněte na **povolit zjišťování domovské sféry** řádku a nakonfigurovat **název domény**.

    g. V **název domény** zadejte název domény zde v případě přihlášení na základě služby AD FS.

    h. Zkontrolujte **povolit jednotné přihlašování,** Pokud se chcete odhlásit z Azure AD, když uživatel odhlásí ze systému JIRA. 

    i. Klikněte na tlačítko **Uložit** uložte nastavení tlačítkem.

    > [!NOTE]
    > Další informace o instalaci a řešení potíží s [příručky pro správce konektoru MS JIRA jednotného přihlašování](../ms-confluence-jira-plugin-adminguide.md) a k dispozici je také [nejčastější dotazy k](../ms-confluence-jira-plugin-faq.md) vaši pomoc

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k systému JIRA SAML SSO Microsoft (V5.2).

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **JIRA SAML SSO Microsoft (V5.2)**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **JIRA SAML SSO Microsoft (V5.2)**.

    ![Jednotné přihlašování SAML JIRA podle propojení Microsoft (verze 5.2) v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>Vytvoření JIRA SAML SSO ve společnosti Microsoft (verze 5.2) testovacího uživatele

Povolit uživatele Azure AD pro přihlášení k systému JIRA na místním serveru, musí být poskytnuty do JIRA na místním serveru.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k serveru v místním systému JIRA jako správce.

2. Najeďte myší na ikonu a klikněte na tlačítko **Správa uživatelů**.

    ![Přidat zaměstnance](./media/jira52microsoft-tutorial/user1.png)

3. Budete přesměrováni na stránku přístup správce k zadání **heslo** a klikněte na tlačítko **potvrdit** tlačítko.

    ![Přidat zaměstnance](./media/jira52microsoft-tutorial/user2.png)

4. V části **Správa uživatelů** kartě oddíl, klikněte na tlačítko **vytvořit uživatele**.

    ![Přidat zaměstnance](./media/jira52microsoft-tutorial/user3.png) 

5. Na **"Vytvořit nový uživatel"** dialogového okna stránky, proveďte následující kroky:

    ![Přidat zaměstnance](./media/jira52microsoft-tutorial/user4.png)

    a. V **e-mailová adresa** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.

    b. V **jméno a příjmení** textového pole zadejte celé jméno uživatele jako Britta Simon.

    c. V **uživatelské jméno** , jako je textové pole, typ e-mailu uživatele Brittasimon@contoso.com.

    d. V **heslo** textového pole zadejte heslo uživatele.

    e. Klikněte na tlačítko **vytvořit uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na JIRA SAML SSO ve společnosti Microsoft (verze 5.2) dlaždici na přístupovém panelu, můžete by měl být automaticky přihlášeni na JIRA SAML SSO Microsoft (V5.2) u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
