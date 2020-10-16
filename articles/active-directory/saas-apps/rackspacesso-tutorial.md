---
title: 'Kurz: Azure Active Directory integrace s Rackspace SSO | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Rackspace SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 991201429bdc09a03ddc46c5beb9d0f4d6744ed5
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108622"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Kurz: Azure Active Directory integrace s Rackspace SSO

V tomto kurzu se naučíte integrovat jednotné přihlašování Rackspace pomocí služby Azure Active Directory (Azure AD).
Integrace Rackspace SSO do Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k Rackspace SSO.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Rackspace SSO (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s Rackspace SSO budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Odběr povoleného jednotného přihlašování Rackspace SSO

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Rackspace SSO podporuje jednotné přihlašování (SSO) iniciované **SP**

## <a name="adding-rackspace-sso-from-the-gallery"></a>Přidání jednotného přihlašování Rackspace z Galerie

Pokud chcete nakonfigurovat integraci Rackspace SSO do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Rackspace SSO z galerie.

**Pokud chcete přidat Rackspace SSO z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **RACKSPACE SSO**, vyberte **Rackspace SSO** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Rackspace jednotné přihlašování v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování Rackspace na základě testovacího uživatele s názvem **Britta Simon**.
Při použití jednotného přihlašování s Rackspace se uživatelé Rackspace automaticky vytvoří při prvním přihlášení k portálu Rackspace. 

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Rackspace, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování RACKSPACE SSO](#configure-rackspace-sso-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
1. **[Nastavte mapování atributů v Ovládacích panelech Rackspace](#set-up-attribute-mapping-in-the-rackspace-control-panel)** – k přiřazení rolí Rackspace uživatelům Azure AD.
1. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Rackspace, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **jednotného přihlašování Rackspace** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** nahrajte **soubor metadat poskytovatele služeb** , který si můžete stáhnout z [adresy URL](https://login.rackspace.com/federate/sp.xml) , a proveďte následující kroky:

    a. Klikněte na **nahrát soubor metadat**.

    ![Snímek obrazovky ukazuje základní konfiguraci SAML s odkazem na soubor s metadaty pro nahrání.](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![Snímek obrazovky se zobrazí dialogové okno, ve kterém můžete vybrat soubor a nahrát ho.](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat jsou potřebné adresy URL automaticky naplněny.

    d. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://login.rackspace.com/federate/`

    ![Rackspace jednotné přihlašování k doméně a adresám URL jednotného přihlašování](common/sp-signonurl.png)   

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

Tento soubor se nahraje do Rackspace, aby se naplnila požadovaná nastavení konfigurace federace identit.

### <a name="configure-rackspace-sso-single-sign-on"></a>Konfigurace jednotného Sign-On jednotného přihlašování Rackspace

Konfigurace jednotného přihlašování na straně jednotného přihlašování **Rackspace** :

1. Přečtěte si dokumentaci v tématu [Přidání poskytovatele identity do ovládacího panelu](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/) .
1. Provede vás kroky k provedení těchto kroků:
    1. Vytvořit nového zprostředkovatele identity
    1. Zadejte e-mailovou doménu, kterou budou uživatelé používat k identifikaci vaší společnosti při přihlašování.
    1. Nahrajte **federační metadata XML** , která se dřív stáhla z ovládacích panelů Azure.

Tím se správně nakonfigurují základní nastavení jednotného přihlašování potřebné pro Azure a Rackspace pro připojení.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Rackspace SSO.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **Rackspace SSO**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **RACKSPACE SSO**.

    ![Odkaz na jednotné přihlašování Rackspace v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Nastavení mapování atributů v Ovládacích panelech Rackspace

Rackspace používá **zásadu mapování atributů** k přiřazení rolí a skupin Rackspace k uživatelům s jednotným přihlašováním. **Zásada mapování atributů** překládá deklarace identity SAML služby Azure AD do polí konfigurace uživatele Rackspace vyžaduje. Další dokumentaci najdete v [dokumentaci základy mapování atributů](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)Rackspace. Některé okolnosti:

* Pokud chcete přiřazovat různé úrovně přístupu Rackspace pomocí skupin Azure AD, budete muset povolit deklaraci identity skupin v nastavení jednotného přihlašování v Azure **RACKSPACE SSO** . **Zásada mapování atributů** se pak bude používat k porovnávání těchto skupin s požadovanými Rackspace rolemi a skupinami:

    ![Nastavení deklarací skupin](common/sso-groups-claim.png)

* Ve výchozím nastavení Azure AD pošle UID skupin Azure AD v deklaracích identity SAML, a to oproti názvu skupiny. Pokud ale provádíte synchronizaci místní služby Active Directory s Azure AD, máte možnost odesílat skutečné názvy skupin:

    ![Nastavení názvu deklarace identity skupin](common/sso-groups-claims-names.png)

Následující příklad **zásad mapování atributů** ukazuje:
1. Nastavení názvu uživatele Rackspace na `user.name` deklaraci SAML. Můžete použít jakoukoli deklaraci identity, ale je nejběžnější ji nastavit na pole, které obsahuje e-mailovou adresu uživatele.
1. Nastavení rolí Rackspace `admin` a `billing:admin` na uživatele porovnáním skupiny Azure AD podle názvu skupiny nebo UID skupiny. Použije *substitution* se náhrada `"{0}"` v `roles` poli a bude nahrazena výsledky `remote` výrazů pro pravidla.
1. Pomocí `"{D}"` *výchozí substituce* umožníte Rackspace načíst další pole SAML hledáním standardních a dobře známých deklarací SAML v výměny SAML.

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

Další příklady najdete v [dokumentaci k základům mapování atributů](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/) Rackspace.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Rackspace SSO na přístupovém panelu, měli byste se automaticky přihlásit k Rackspace SSO, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Můžete také použít tlačítko **ověřit** v nastavení jednotného přihlašování **Rackspace SSO** :

   ![Tlačítko ověření jednotného přihlašování](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

