---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML Confluence microsoftem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování SAML Confluence společností Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c79694353dc703ef2197ee0a26a5709d5fd0496c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181792"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML Confluence společností Microsoft

V tomto kurzu se dozvíte, jak integrovat Confluence SAML SSO, Microsoft Azure Active Directory (Azure AD).
Integrace jednotného přihlašování SAML Confluence společností Microsoft s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Confluence SAML SSO společností Microsoft.
* Uživatelům se automaticky přihlášeni k Confluence SAML SSO společností Microsoft (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="description"></a>Popis:

Pomocí účtu Microsoft Azure Active Directory se od společnosti Atlassian Confluence serverem můžete povolit jednotné přihlašování. Tímto způsobem všichni uživatelé vaší organizace můžete použít přihlašovací údaje služby Azure AD k přihlášení do aplikace Confluence. Tento modul plug-in používá protokol SAML 2.0 pro federaci.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním SAML Confluence microsoftem, potřebujete následující položky:

- Předplatné Azure AD
- Confluence serverová aplikace nainstalované na serveru Windows 64-bit (místní nebo v cloudové infrastruktuře IaaS)
- Confluence server je povolen protokol HTTPS
- Všimněte si, že podporované verze pro modul plug-in Confluence jsou uvedené v následující části.
- Confluence server je dostupný na Internetu zejména na stránku pro přihlášení k Azure AD pro ověřování a měli byste může přijímat token ze služby Azure AD
- Přihlašovací údaje správce jsou nastavené v Confluence
- WebSudo zakázaná v Confluence
- V serveru aplikaci Confluence testovacího uživatele

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkčního prostředí Confluence. Nejdřív otestovat integraci v vývojové nebo testovací prostředí aplikace a pak používat produkčním prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady: [Zkušební nabídka](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Podporované verze systémů Confluence

V současné době jsou podporovány následující verze Confluence:

- Confluence: 5.0 k 5.10
- Confluence: 6.0.1
- Confluence: 6.1.1
- Confluence: 6.2.1
- Confluence: 6.3.4
- Confluence: 6.4.0
- Confluence: 6.5.0
- Confluence: 6.6.2
- Confluence: 6.7.0
- Confluence: 6.8.1
- Confluence: 6.9.0
- Confluence: 6.10.0
- Confluence: 6.11.0
- Confluence: 6.12.0

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje jednotné přihlašování SAML confluence microsoftem **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Přidání jednotného přihlašování SAML Confluence společností Microsoft z Galerie

Pokud chcete nakonfigurovat integraci jednotného přihlašování SAML Confluence microsoftem do služby Azure AD, budete muset přidat Confluence SAML SSO společností Microsoft z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání jednotného přihlašování SAML Confluence společností Microsoft z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Confluence SAML SSO microsoftem**vyberte **Confluence SAML SSO microsoftem** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Jednotné přihlašování SAML confluence společností Microsoft v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfiguraci a testování Azure AD jednotné přihlašování s jednotným Přihlašováním SAML Confluence společností Microsoft na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Confluence SAML SSO společností Microsoft.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním SAML Confluence microsoftem, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace jednotného přihlašování SAML Confluence podle Microsoft Single Sign-On](#configure-confluence-saml-sso-by-microsoft-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit jednotné přihlašování SAML Confluence uživatelem Microsoft test](#create-confluence-saml-sso-by-microsoft-test-user)**  – Pokud chcete mít protějšek Britta Simon Confluence SAML SSO společnosti Microsoft, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním SAML Confluence microsoftem, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Confluence SAML SSO microsoftem** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Jednotné přihlašování SAML confluence Microsoft Domain a adresy URL jednotné přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<domain:port>/plugins/servlet/saml/auth`

    b. V **identifikátor** pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<domain:port>/`

    c. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Port je volitelný, v případě, že je pojmenovaný URL. Tyto hodnoty jsou přijímány během konfigurace modulu plug-in Confluence, který je vysvětlen později v tomto kurzu.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-confluence-saml-sso-by-microsoft-single-sign-on"></a>Konfigurace jednotného přihlašování SAML Confluence podle jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k vaší instanci Confluence jako správce.

2. Najeďte myší na ikonu a klikněte na tlačítko **doplňky**.

    ![Konfigurace jednotného přihlašování](./media/confluencemicrosoft-tutorial/addon1.png)

3. Stáhněte si modul plug-in z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56503). Ručně nahrát modul plug-in poskytl pomocí Microsoft **nahrát doplněk** nabídky. Stáhnout modul plug-in se vztahuje smlouva [servisní smlouvou aplikace Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Konfigurace jednotného přihlašování](./media/confluencemicrosoft-tutorial/addon12.png)

4. Když je nainstalovaný modul plug-in, zobrazí se v **uživatel nainstaloval** doplňky část **spravovat doplněk** části. Klikněte na tlačítko **konfigurovat** konfigurace nového modulu plug-in.

    ![Konfigurace jednotného přihlašování](./media/confluencemicrosoft-tutorial/addon13.png)

5. Proveďte následující kroky na stránce konfigurace:

    ![Konfigurace jednotného přihlašování](./media/confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Ujistěte se, že je pouze jeden certifikát mapují na aplikaci tak, aby se nezobrazí žádná chyba překladu metadat. Pokud existuje víc certifikátů, správce získá chybu při překladu metadat.

    a. V **adresa URL metadat** vložit do textového pole **adresa Url federačních metadat aplikace** hodnotu, která jste zkopírovali z webu Azure portal a klikněte **vyřešit** tlačítko. Načte adresu URL metadat zprostředkovatele identity a naplní všechny informace o pole.

    b. Kopírovat **identifikátor, adresa URL odpovědi a adresa URL přihlašování** hodnoty a vložit je do **identifikátor, adresa URL odpovědi a adresa URL přihlašování** textová pole v uvedeném pořadí **základní konfiguraci SAML** části na webu Azure portal.

    c. V **přihlašovací jméno tlačítko** zadejte název tlačítka, které vaše organizace chce, aby se uživatelům zobrazí na obrazovce přihlášení.

    d. V **SAML uživatelské ID umístění**, vyberte buď **ID uživatele není v elementu NameIdentifier příkazu subjektu** nebo **ID uživatele není v elementu atribut**.  Toto ID musí být Confluence id uživatele. Pokud neodpovídá id uživatele, systém nedovolí uživatelům přihlášení. 

    > [!Note]
    > Výchozí umístění SAML ID uživatele je název identifikátoru. Můžete to změnit atribut možnost a zadejte odpovídající název.
    
    e. Pokud vyberete **ID uživatele není v elementu atribut** možnost, pak v **název atributu** textového pole zadejte název atributu, kde se očekává Id uživatele. 

    f. Pokud používáte federovanou doménu (například služby AD FS atd.) pomocí služby Azure AD, potom klikněte na **povolit zjišťování domovské sféry** řádku a nakonfigurovat **název domény**.
    
    g. V **název domény** zadejte název domény zde v případě přihlášení na základě služby AD FS.

    h. Zkontrolujte **povolit jednotné přihlašování,** Pokud se chcete odhlásit z Azure AD, když uživatel odhlásí od Confluence. 

    i. Klikněte na tlačítko **Uložit** uložte nastavení tlačítkem.

    > [!NOTE]
    > Další informace o instalaci a řešení potíží s [příručky pro správce konektoru MS Confluence jednotného přihlašování](../ms-confluence-jira-plugin-adminguide.md) a k dispozici je také [nejčastější dotazy k](../ms-confluence-jira-plugin-faq.md) vaši pomoc

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Confluence SAML SSO společností Microsoft.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Confluence SAML SSO microsoftem**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Confluence SAML SSO microsoftem**.

    ![Jednotné přihlašování SAML Confluence podle propojení Microsoft v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Vytvoření Confluence jednotného přihlašování SAML podle Microsoft testovacího uživatele

Umožňuje uživatelům Azure AD se přihlaste k Confluence na místním serveru, musí být zřízená do Confluence SAML SSO společností Microsoft. Zřizování je pro jednotné přihlašování SAML Confluence microsoftem, úlohu.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k serveru v místním Confluence jako správce.

2. Najeďte myší na ikonu a klikněte na tlačítko **Správa uživatelů**.

    ![Přidat zaměstnance](./media/confluencemicrosoft-tutorial/user1.png) 

3. V sekci uživatelé klikněte na možnost **přidat uživatele** kartu. Na **přidání uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Přidat zaměstnance](./media/confluencemicrosoft-tutorial/user2.png) 

    a. V **uživatelské jméno** textového pole zadejte e-mailu uživatele, jako je Britta Simon.

    b. V **jméno a příjmení** textového pole zadejte celé jméno uživatele jako Britta Simon.

    c. V **e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.

    d. V **heslo** textového pole zadejte heslo pro Britta Simon.

    e. Klikněte na tlačítko **potvrzení hesla** znovu zadat heslo.

    f. Klikněte na tlačítko **přidat** tlačítko.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na Confluence SAML SSO podle Microsoft dlaždici na přístupovém panelu, můžete by měl být automaticky přihlášeni na Confluence SAML SSO microsoftem, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

