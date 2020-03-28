---
title: 'Kurz: Integrace služby Azure Active Directory s fidelity NetBenefits | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi službou Azure Active Directory a Fidelity NetBenefits.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 77dc8a98-c0e7-4129-ab88-28e7643e432a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40c1e9d493b0fb7bbf1e484799d56e91da1d0833
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73156323"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Kurz: Integrace služby Azure Active Directory s fidelity NetBenefits

V tomto kurzu se dozvíte, jak integrovat Fidelity NetBenefits s Azure Active Directory (Azure AD).
Integrace Fidelity NetBenefits s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Fidelity NetBenefits.
* Můžete povolit uživatelům, aby se automaticky přihlásili k Fidelity NetBenefits (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Fidelity NetBenefits, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením Fidelity NetBenefits

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Fidelity NetBenefits podporuje iniciované přihlašované při hlášce **IDP**

* Fidelity NetBenefits podporuje zřizování uživatelů **just in time**

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Přidání fidelity netvýhody z galerie

Chcete-li nakonfigurovat integraci Fidelity NetBenefits do Azure AD, musíte přidat Fidelity NetBenefits z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat věrnostní síťové výhody z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Příkaz Fidelity NetBenefits**, vyberte **Fidelity NetBenefits** z panelu výsledků a klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![Fidelity NetBenefits v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí fidelity NetBenefits na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Služby Azure AD a souvisejícím uživatelem v programu Fidelity NetBenefits.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby Fidelity NetBenefits, je třeba provést následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte fidelity NetBenefits Single Sign-On](#configure-fidelity-netbenefits-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte fidelity NetBenefits testovacího uživatele](#create-fidelity-netbenefits-test-user)** – mít protějšek Britta Simon v Fidelity NetBenefits, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí služby Fidelity NetBenefits, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Fidelity NetBenefits** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** proveďte následující kroky:

    ![Fidelity NetBenefits Domény a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    Pro testovací prostředí:`urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Pro produkční prostředí:`urn:sp:fidelity:geninbndnbparts20`

    b. Do textového pole **Adresa URL pro odpověď** zadejte adresu URL, kterou má služba Fidelity poskytnout v době implementace, nebo se obraťte na přiřazenou službu Fidelity Client Service Manager.

5. Aplikace Fidelity NetBenefits očekává kontrolní výrazy SAML v určitém formátu, který vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky zobrazuje seznam výchozích atributů, kde jako **nameidentifier** je mapován s **user.userprincipalname**. Aplikace Fidelity NetBenefits očekává, že **identifikátor nameidentifier** bude mapován pomocí **employeeid** nebo jakékoli jiné deklarace, která je použitelná pro vaši organizaci jako **nameidentifier**, takže je třeba upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![image](common/edit-attribute.png)

    >[!Note]
    >Fidelity NetBenefits podporuje statickou a dynamickou federaci. Statické znamená, že nebude používat SAML založené právě v čase zřizování uživatelů a dynamické znamená, že podporuje právě v čase zřizování uživatelů. Pro použití zřizování na základě JIT zákazníci musí přidat některé další deklarace ve službě Azure AD, jako je datum narození uživatele atd. Tyto podrobnosti jsou poskytovány přiřazeným **správcem klientských služeb Fidelity** a musí povolit tuto dynamickou federaci pro vaši instanci.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V části **Nastavit věrnost netbenefits** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-fidelity-netbenefits-single-sign-on"></a>Konfigurace jednotného přihlašování Fidelity NetBenefits

Chcete-li nakonfigurovat jednotné přihlašování na straně **Fidelity NetBenefits,** musíte odeslat stažený **xml metadat federace** a příslušné zkopírované adresy URL z portálu Azure do týmu podpory [Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com). Toto nastavení nastaví tak, aby bylo připojení s přizasazené k samovazbě SAML správně nastaveno na obou stranách.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Fidelity NetBenefits.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Fidelity NetBenefits**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **možnost Fidelity NetBenefits**.

    ![Odkaz Fidelity NetBenefits v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-fidelity-netbenefits-test-user"></a>Vytvořit testovacího uživatele Fidelity NetBenefits

V této části vytvoříte uživatele s názvem Britta Simon v Fidelity NetBenefits. Pokud vytváříte statickou federaci, ve skutých **klientských službách Fidelity** ve správě klientských služeb ve zdravotnictví vytvořte uživatele na platformě Fidelity NetBenefits. Tito uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

Pro dynamické federace uživatelé jsou vytvořeny pomocí just in time zřizování uživatelů. Pro použití zřizování na základě JIT zákazníci musí přidat některé další deklarace ve službě Azure AD, jako je datum narození uživatele atd. Tyto podrobnosti jsou poskytovány přiřazeným **správcem klientských služeb Fidelity** a musí povolit tuto dynamickou federaci pro vaši instanci.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Fidelity NetBenefits na přístupovém panelu, měli byste být automaticky přihlášeni k Fidelity NetBenefits, pro které nastavíte přistupující smlouvy. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

