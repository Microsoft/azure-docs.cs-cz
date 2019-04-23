---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Rackspace | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování Rackspace.
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
ms.openlocfilehash: fd420ea3fc4faae7fe4510a72204d71acaa3549a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009921"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Rackspace

V tomto kurzu se dozvíte, jak integrovat Rackspace jednotného přihlašování s Azure Active Directory (Azure AD).
Integrace Rackspace jednotného přihlašování s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Rackspace jednotného přihlašování.
* Uživatelům se automaticky přihlášeni k Rackspace jednotné přihlašování (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním Rackspace, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Jednotné přihlašování Rackspace jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje jednotné přihlašování Rackspace **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-rackspace-sso-from-the-gallery"></a>Přidání jednotného přihlašování Rackspace z Galerie

Konfigurace integrace Rackspace jednotné přihlašování do služby Azure AD, budete muset přidat Rackspace jednotného přihlašování na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Rackspace jednotné přihlašování z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **jednotného přihlašování Rackspace**vyberte **jednotného přihlašování Rackspace** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Jednotné přihlašování Rackspace v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování s jednotným Přihlašováním Rackspace podle testovacího uživatele volá **Britta Simon**.
Pokud používáte jednotné přihlašování Rackspace, Rackspace uživatelé se automaticky vytvoří při prvním přihlášení k portálu Rackspace. 

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním Rackspace, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Rackspace jednotného přihlašování Single Sign-On](#configure-rackspace-sso-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Nastavit mapování atributů v Ovládacích panelech Rackspace](#set-up-attribute-mapping-in-the-rackspace-control-panel)**  – Pokud chcete přiřadit Rackspace role uživatelů Azure AD.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním Rackspace, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **jednotného přihlašování Rackspace** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části nahrávání **soubor metadat poskytovatele služeb** kterou si můžete stáhnout z [URL](https://login.rackspace.com/federate/sp.xml) a proveďte následující kroky:

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![image](common/upload-metadata.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![image](common/browse-upload-metadata.png)

    c. Po úspěšném odeslání souboru metadat získat potřebné adresy URL automaticky vyplní automaticky.

    d. V **přihlašovací adresa URL** textové pole, zadejte adresu URL: `https://login.rackspace.com/federate/`

    ![Rackspace jednotného přihlašování k doméně a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)   

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

Tento soubor se nahraje k Rackspace k vyplnění požadovaných nastavení konfigurace federace identit.

### <a name="configure-rackspace-sso-single-sign-on"></a>Konfigurace jednotného přihlašování Rackspace jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **jednotného přihlašování Rackspace** na straně:

1. V dokumentaci na [přidat zprostředkovatele Identity v Ovládacích panelech](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. To vás provede kroky pro:
    1. Vytvoření nového zprostředkovatele Identity
    1. Zadejte e-mailové doméně, která budou uživatelé používat k identifikaci vaší společnosti při přihlášení.
    1. Nahrát **kód XML metadat federace** dříve stažené z ovládacího panelu Azure.

To se správně nakonfigurovat základní nastavení jednotného přihlašování, které jsou potřebné pro připojení k Azure a Rackspace.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Rackspace jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **jednotného přihlašování Rackspace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **jednotného přihlašování Rackspace**.

    ![Jednotné přihlašování Rackspace odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Nastavení mapování atributů v Ovládacích panelech Rackspace

Rackspace používá **zásady mapování atributů** Rackspace rolí a skupin přiřadit uživatelům jednotné přihlašování. **Zásady mapování atributů** výrazném konfigurační pole uživatel vyžaduje Rackspace deklarace identity Azure AD SAML. Další dokumentaci najdete v Rackspace [základy mapování atributů dokumentaci](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/). Některé aspekty:

* Pokud chcete přiřadit různé úrovně přístupu Rackspace pomocí skupin Azure AD, musíte povolit ve službě Azure deklaraci skupiny **jednotného přihlašování Rackspace** nastavení jednotného přihlašování. **Zásady mapování atributů** se pak použije tak, aby odpovídaly těmto skupinám na požadovanou Rackspace role a skupiny:

    ![Deklarace nastavení skupin](common/sso-groups-claim.png)

* Ve výchozím nastavení Azure AD pošle skupiny UID v Azure AD v deklaraci identity SAML, a název skupiny. Pokud synchronizujete vaší místní služby Active Directory do služby Azure AD, ale máte možnost odeslat skutečné názvy skupin:

    ![Název nastavení deklarace skupiny](common/sso-groups-claims-names.png)

Následující příklad **zásady mapování atributů** ukazuje:
1. Nastavení Rackspace uživatelské jméno `user.name` deklarace identity SAML. Můžete použít jakýkoli nárok, ale je nejběžnější nastavit pole obsahující e-mailovou adresu uživatele.
1. Nastavení rolí Rackspace `admin` a `billing:admin` pro uživatele to provede spárováním odpovídajících skupiny Azure AD, název skupiny nebo UID skupiny. A *nahrazení* z `"{0}"` v `roles` pole se používá a budou nahrazeny výsledky `remote` pravidlo výrazy.
1. Použití `"{D}"` *výchozí nahrazení* umožňuje načíst další pole SAML tím, že hledají standardního a dobře známé deklarace identity SAML v exchangi SAML Rackspace.

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
> Ujistěte se, že používáte textový editor, který validuje syntaxi YAML při úpravách souboru zásad.

Zobrazit Rackspace [základy mapování atributů dokumentaci](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/) Další příklady.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Rackspace jednotného přihlašování na přístupovém panelu, můžete by měl být automaticky přihlášeni na jednotné přihlašování Rackspace, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Můžete také použít **ověřit** tlačítko **jednotného přihlašování Rackspace** jednotné přihlašování – nastavení:

   ![Tlačítko Ověřit jednotného přihlašování](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

