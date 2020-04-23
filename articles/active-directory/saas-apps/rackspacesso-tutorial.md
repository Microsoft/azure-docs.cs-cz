---
title: 'Kurz: Integrace služby Azure Active Directory s rackspace spřimicrosoft | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a jednotném přihlašování Rackspace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 36b398be-2f7e-4ce8-9031-53587299bc4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 98a160b361e316c87e61855825c2cc36fdbb06d1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870299"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Kurz: Integrace služby Azure Active Directory s přiřazovat služby Při vadí mši Rackspace

V tomto kurzu se dozvíte, jak integrovat rackspace přistupující služby s Azure Active Directory (Azure AD).
Integrace přistřešení služby Přistaň Rackspace s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Rackspace sem a so.
* Můžete povolit uživatelům, aby se automaticky přihlásili k Rackspace Jednotné přihlašování (jednotné přihlašování) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadované součásti

Chcete-li nakonfigurovat integraci Azure AD pomocí služby SSO Rackspace, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Rackspace Jednotné přihlašování s povoleným předplatným

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Rackspace SSO podporuje **SP** iniciované sso

## <a name="adding-rackspace-sso-from-the-gallery"></a>Přidání semanového pojetí Rackspace z galerie

Chcete-li nakonfigurovat integraci přiřazuje k rackspace do Azure AD, je potřeba přidat Rackspace SSO z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat semanové přiznané místo rackspace z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Rackspace SSO**, vyberte **Rackspace SSO** z panelu výsledků a pak klikněte na **tlačítko Přidat** a přidejte aplikaci.

     ![Rackspace SSO v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování Rackspace na základě testovacího uživatele s názvem **Britta Simon**.
Při použití jednotného přihlášení s Rackspace budou uživatelé Rackspace automaticky vytvořeni při prvním přihlášení k portálu Rackspace. 

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí služby Přihlašování k rackspace, je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace rackspace Jednotné přihlašování](#configure-rackspace-sso-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
1. **[Nastavte mapování atributů v ovládacím panelu Rackspace](#set-up-attribute-mapping-in-the-rackspace-control-panel)** – chcete-li přiřadit role Rackspace uživatelům Azure AD.
1. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Rackspace, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Rackspace SSO** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** nahrajte **soubor metadat poskytovatele služeb,** který si můžete stáhnout z [adresy URL,](https://login.rackspace.com/federate/sp.xml) a proveďte následující kroky:

    a. Klikněte na **Nahrát soubor metadat**.

    ![image](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klepněte na tlačítko **Nahrát**.

    ![image](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se automaticky automaticky vyplní potřebné adresy URL.

    d. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL:`https://login.rackspace.com/federate/`

    ![Rackspace Jednotné ho so domény a adresy URL jednotné přihlášení informace](common/sp-signonurl.png)   

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

Tento soubor bude odeslán do rackspace k naplnění požadované nastavení konfigurace Federace identity.

### <a name="configure-rackspace-sso-single-sign-on"></a>Konfigurace jednotného přihlašování do rackového prostoru

Konfigurace jednotného přihlašování na straně **sjednotného přihlašování Rackspace:**

1. Podívejte se na dokumentaci k [přidat poskytovatele identity do ovládacího panelu](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. To vás provede kroky k:
    1. Vytvoření nového zprostředkovatele identity
    1. Zadejte e-mailovou doménu, kterou budou uživatelé používat k identifikaci vaší společnosti při přihlášení.
    1. Nahrajte **XML metadat federace,** který byl dříve stažen z ovládacího panelu Azure.

Tím se správně nakonfiguruje základní nastavení přizpůsobování připojení, které je potřeba pro připojení Azure a Rackspace.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** `brittasimon@yourcompanydomain.extension`jméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Rackspace jednotného přihlašování.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Rackspace SSO**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Rackspace SSO**.

    ![Odkaz SSO Rackspace v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Nastavení mapování atributů v ovládacím panelu Rackspace

Rackspace používá **zásady mapování atributů** k přiřazení rolí rackspace a skupin uživatelům s jedním přihlášením. **Zásady mapování atributů** převádí deklarace identity Azure AD SAML do polí konfigurace uživatele, která Rackspace vyžaduje. Další dokumentaci naleznete v [dokumentaci Základy mapování atributů](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)Rackspace . Některé úvahy:

* Pokud chcete přiřadit různé úrovně přístupu Rackspace pomocí skupin Azure AD, budete muset povolit deklaraci skupiny v nastavení jednotného přihlašování Azure **Rackspace Jednotné** přihlašování. **Zásady mapování atributů** se pak použijí k přizpůsobení těchto skupin požadovaným rolím a skupinám Rackspace:

    ![Nastavení deklarací skupin](common/sso-groups-claim.png)

* Ve výchozím nastavení Azure AD odešle UID skupin Azure AD ve deklaraci SAML versus název skupiny. Pokud však synchronizujete místní službu Active Directory do služby Azure AD, máte možnost odeslat skutečné názvy skupin:

    ![Nastavení názvu deklarace skupin](common/sso-groups-claims-names.png)

Následující příklad **zásady mapování atributů** ukazuje:
1. Nastavení jména uživatele Rackspace na `user.name` deklaraci SAML. Lze použít jakoukoli deklaraci, ale nejběžnější je nastavení na pole obsahující e-mailovou adresu uživatele.
1. Nastavení role `admin` Rackspace `billing:admin` a na uživatele odpovídající Skupiny Azure AD, buď název skupiny nebo skupiny UID. *Nahrazení* `"{0}"` v `roles` poli se používá a bude nahrazeno výsledky `remote` výrazů pravidla.
1. `"{D}"` Pomocí *výchozí nahrazení,* aby Rackspace načíst další saml pole hledáním standardní a známé saml deklarace identity ve výměně SAML.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> Ujistěte se, že používáte textový editor, který ověřuje syntaxi YAML při úpravách souboru zásad.

Další příklady naleznete v dokumentaci k [mapování atributů](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/) Rackspace Basics.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Rackspace spři na přístupovém panelu, můžete by měl být automaticky přihlášeni k rackspace sem a to, pro které nastavíte přispojené správě. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Můžete také použít **tlačítko Ověřit** v nastavení jednotného přihlašování **Rackspace** Jednotné přihlašování:

   ![Tlačítko Ověření při přisávací ho stesá](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

