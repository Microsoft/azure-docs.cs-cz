---
title: 'Kurz: Integrace azure active directory se softwarem Cezanne HR | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a softwarem Cezanne HR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa689e6138f8d965e59f7cfa7a85e0835301086c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158695"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Kurz: Integrace Azure Active Directory se softwarem Cezanne HR

V tomto kurzu se dozvíte, jak integrovat Cezanne HR software s Azure Active Directory (Azure AD).
Integrace softwaru Cezanne HR s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Softwaru pro lidské zdroje Cezanne.
* Uživatelům můžete povolit automatické přihlášení k softwaru Cezanne HR Software (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD se softwarem Cezanne HR, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením společnosti Cezanne HR Software

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cezanne HR Software podporuje **SP** inicioval SSO

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Přidání Cezanne HR Software z galerie

Chcete-li nakonfigurovat integraci softwaru Cezanne HR software do Azure AD, je třeba přidat Cezanne HR Software z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat software Cezanne HR z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Ve vyhledávacím poli zadejte **Cezanne HR Software**, z panelu výsledků vyberte **Cezanne HR Software** a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Cezanne HR Software v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí softwaru Cezanne HR na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v softwaru Cezanne HR.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí softwaru Cezanne HR, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte jednopřihlásáčené připojení cezannhrového softwaru](#configure-cezanne-hr-software-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu Cezanne HR Software](#create-cezanne-hr-software-test-user)** – chcete-li mít protějšek Britta Simon v Cezanne HR software, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí softwaru Cezanne HR, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Cezanne HR Software** vyberte Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Cezanne HR Software Domain a url jednotné přihlašovací informace](common/sp-identifier-reply.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL:`https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. Do textového pole **Adresa URL pro odpověď** zadejte adresu URL pomocí následujícího vzoru:`https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a adresou URL pro odpověď. Obraťte se na [tým podpory cezanského hr softwarového klienta](https://cezannehr.com/services/support/) a získejte tyto hodnoty.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit Cezanne HR Software** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Konfigurace jednotného přihlašování softwaru Cezanne HR

1. V jiném okně webového prohlížeče se přihlaste ke svému tenantovi Cezanne HR Software jako správce.

2. V levém navigačním podokně klepněte na **položku Nastavení systému**. Přejděte na **nastavení zabezpečení**. Potom přejděte na **konfiguraci jednotného přihlášení**.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. V poli **Povolit uživatelům přihlášení pomocí následujícího panelu služby jednotného přihlašování (SSO)** zaškrtněte políčko **SAML 2.0** a vyberte možnost **Upřesnit konfiguraci.**

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Klikněte na **tlačítko Přidat nový.**

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. Proveďte následující kroky v části **SAML 2.0 IDENTITY PROVIDERS.**

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Jako **zobrazované jméno**zadejte jméno svého poskytovatele identity .

    b. V textovém poli **Identifikátor entity** vložte hodnotu **identifikátoru Azure Ad,** kterou jste zkopírovali z portálu Azure.

    c. Změňte **vazbu SAML** na POST.

    d. V textovém poli Koncového bodu **služby tokenu zabezpečení** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure.

    e. Do textového pole Název atributu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`ID uživatele zadejte .

    f. Kliknutím na ikonu **Nahrát** nahrajete stažený certifikát z webu Azure Portal.

    g. Klikněte na tlačítko **Ok.**

6. Klikněte na **tlačítko Uložit.**

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Cezanne HR software.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Cezanne HR Software**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **možnost Cezanne HR Software**.

    ![Odkaz Cezanne HR Software v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-cezanne-hr-software-test-user"></a>Vytvořit uživatele testu cezanne HR software

Aby se uživatelé Azure AD mohli přihlásit k softwaru Cezanne HR, musí být zřízeni do softwaru Cezanne HR. V případě Cezanne HR Software zřizování je ruční úloha.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se na web společnosti Cezanne HR Software jako správce.

2. V levém navigačním podokně klepněte na **položku Nastavení systému**. Přejděte na **Spravovat uživatele**. Potom přejděte na **Přidat nového uživatele**.

    ![Nový uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nový uživatel")

3. V části **PODROBNOSTI O OSOBĚ** proveďte následující kroky:

    ![Nový uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nový uživatel")

    a. Nastavte **interního uživatele** jako VYPNUTO.

    b. Do textového pole **Křestní jméno** zadejte křestní jméno uživatele, jako **je Britta**.  

    c. Do textového pole **Příjmení** zadejte příjmení uživatele, jako **je Simon**.

    d. Do textové schránky **E-mail** zadejte e-mailovou adresu uživatele jako Brittasimon@contoso.com.

4. V části **Informace o účtu** proveďte následující kroky:

    ![Nový uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nový uživatel")

    a. Do textového pole **Uživatelské jméno** zadejte Brittasimon@contoso.come-mail uživatele jako .

    b. Do textového pole **Heslo** zadejte heslo uživatele.

    c. Vyberte **hr professional** jako **roli zabezpečení**.

    d. Klikněte na tlačítko **OK**.

5. Přejděte na kartu **Jednotné přihlášení** a v oblasti **Identifikátory SAML 2.0** vyberte **Přidat nový.**

    ![Uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Uživatel")

6. Vyberte poskytovatele identity pro **poskytovatele identity** a do textového pole **Identifikátor uživatele**zadejte e-mailovou adresu účtu Britta Simon.

    ![Uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Uživatel")

7. Klikněte na **tlačítko Uložit.**

    ![Uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Uživatel")

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Cezanne HR software na přístupovém panelu, měli byste být automaticky přihlášeni k Cezanne HR software, pro které nastavíte přihlašovací služby. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
