---
title: 'Kurz: Integrace Azure Active Directory s ověřováním identit cloudové platformy SAP | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ověřováním identit cloudové platformy SAP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: 6b5b664581a1f3da367f74318cfb6bf5564e5b39
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472877"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Kurz: Integrace Azure Active Directory s ověřováním identit cloudové platformy SAP

V tomto kurzu se dozvíte, jak integrovat ověřováním identit cloudové platformy SAP pomocí Azure Active Directory (Azure AD).
Integruje ověřováním identit cloudové platformy SAP do Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k ověřováním identit cloudové platformy SAP.
* Uživatelům se automaticky přihlášeni k ověřováním identit cloudové platformy SAP (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ověřováním identit cloudové platformy SAP, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* SAP ověřováním identit cloudové platformy jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje ověřováním identit cloudové platformy SAP **SP** a **IDP** jednotné přihlašování zahájené pomocí

Než se začtete do technických podrobností, je důležité pochopit koncepty, kterým se chcete podívat na. Ověřováním identit cloudové platformy SAP a Active Directory Federation Services umožňují implementaci jednotného přihlašování napříč aplikacemi nebo služby, které jsou chráněné službou Azure AD (jako identity) s aplikacemi SAP a služby, které jsou chráněné službou SAP cloudem Ověření Identity Platform.

V současné době ověřováním identit cloudové platformy SAP funguje jako zprostředkovatel Identity Proxy aplikací SAP. Azure Active Directory pak funguje jako přední poskytovatel Identity v tomto nastavení. 

Následující diagram znázorňuje tuto relaci:

![Vytváří se testovací uživatele služby Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

S tímto nastavením je tenant ověřováním identit cloudové platformy SAP nakonfigurovaný jako důvěryhodné aplikace v Azure Active Directory.

Všechny aplikace SAP a služby, které chcete chránit tímto způsobem jsou následně nakonfigurovat v konzole pro správu ověřováním identit cloudové platformy SAP.

Proto autorizaci k udělování přístupu k SAP aplikacím a službám musí proběhnout v ověřováním identit cloudové platformy SAP (na rozdíl od služby Azure Active Directory).

Tím, že nakonfigurujete ověřováním identit cloudové platformy SAP jako aplikaci prostřednictvím Azure Active Directory Marketplace, není nutné ke konfiguraci jednotlivých deklarací identity nebo kontrolní výrazy SAML.

> [!NOTE]
> Momentálně se podporuje jenom jednotného přihlašování k webu je testovaná obě strany. Toky, které jsou nezbytné pro aplikace API nebo komunikace rozhraní API pro rozhraní API by mělo fungovat, ale ještě nebyly testovány. Se provést test během následující aktivity.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Přidání ověřováním identit cloudové platformy SAP z Galerie

Ke konfiguraci integrace ověřováním identit cloudové platformy SAP do Azure AD, budete muset přidat ověřováním identit cloudové platformy SAP z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ověřováním identit cloudové platformy SAP z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ověřováním identit cloudové platformy SAP**vyberte **ověřováním identit cloudové platformy SAP** z panelu výsledků klikněte **přidat** tlačítko přidáte aplikaci.

     ![Ověřováním identit cloudové platformy SAP v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfiguraci a testování Azure AD jednotné přihlašování s [název aplikace] podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v [název aplikace].

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s [název aplikace], které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace SAP Cloud Platform Identity ověřování jednotného přihlašování](#configure-sap-cloud-platform-identity-authentication-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele ověřováním identit cloudové platformy SAP](#create-sap-cloud-platform-identity-authentication-test-user)**  – Pokud chcete mít protějšek Britta Simon v SAP ověřováním identit cloudové platformy, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s [název aplikace], proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **ověřováním identit cloudové platformy SAP** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat v **IDP** intiated režimu proveďte následující kroky:

    ![SAP Cloud Platform Identity ověřování domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `<IAS-tenant-id>.accounts.ondemand.com`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Obraťte se [tým podpory SAP Cloud Platform Identity ověřování klienta](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) k získání těchto hodnot. Pokud nevíte, hodnota identifikátoru, přečtěte si dokumentaci ověřováním identit cloudové platformy SAP o [konfigurace Tenanta SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![SAP Cloud Platform Identity ověřování domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Použijte prosím vaše konkrétní obchodní aplikace přihlašovací adresa URL. Obraťte se [tým podpory SAP Cloud Platform Identity ověřování klienta](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) Pokud máte jakékoli nejisté.

6. Aplikace ověřováním identit cloudové platformy SAP očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

7. Pokud vaše aplikace SAP očekává, že atribut jako **firstName**, přidejte **firstName** atribut **deklarace identity uživatelů** části na **atributy uživatele**  dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte jméno název atributu.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** vyberte user.givenname hodnotu atributu.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **soubor XML s metadaty**z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

9. Na **nastavení ověřováním identit cloudové platformy SAP** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>Konfigurace SAP Cloud Platform Identity ověřování jednotného přihlašování

1. Pokud chcete získat jednotné přihlašování nakonfigurované pro vaši aplikaci, přejděte do konzoly pro správu ověřováním identit cloudové platformy SAP. Adresa URL má následující vzor: `https://<tenant-id>.accounts.ondemand.com/admin`. Pak si můžete přečíst dokumentaci o ověřováním identit cloudové platformy SAP na [integrace s Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html).

2. Na webu Azure Portal, vyberte **Uložit** tlačítko.

3. Pokračujte s tímto pouze v případě, že chcete přidat a povolení jednotného přihlašování pro jiné aplikace SAP. Opakujte kroky v části **přidání SAP ověřováním identit cloudové platformy z Galerie**.

4. Na webu Azure Portal na **ověřováním identit cloudové platformy SAP** integrace stránce aplikace vyberte **propojené přihlášení**.

    ![Konfigurace propojené přihlašování](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Konfiguraci uložte.

> [!NOTE]
> Nová aplikace využívá konfiguraci jednotné přihlašování předtím u aplikace SAP. Ujistěte se, že používáte stejné podnikové zprostředkovatelé Identity v konzole pro správu ověřováním identit cloudové platformy SAP.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu ověřováním identit cloudové platformy SAP.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **ověřováním identit cloudové platformy SAP**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ověřováním identit cloudové platformy SAP**.

    ![V seznamu aplikací na odkaz ověřováním identit cloudové platformy SAP](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Vytvořit testovacího uživatele ověřováním identit cloudové platformy SAP

Není nutné vytvořit uživatele v ověřováním identit cloudové platformy SAP. Uživatelé, kteří jsou v úložišti uživatele Azure AD můžete použít funkci jednotného přihlašování.

Ověřováním identit cloudové platformy SAP podporuje možnost federace identit. Tato možnost umožňuje aplikace a zjistit, zda existují uživatelé, kteří jsou ověřeni zprostředkovatelem podniková identita v uživatele úložiště z SAP ověřováním identit cloudové platformy.

Ve výchozím nastavení je zakázána možnost federace identit. Pokud je povolené federace identit, jenom uživatelé, které jsou importovány do ověřováním identit cloudové platformy SAP přístup k aplikaci.

Další informace o tom, jak povolit nebo zakázat federaci identit s ověřováním identit cloudové platformy SAP, najdete v části "Povolení Identity federace s SAP ověřováním identit cloudové platformy" v [konfigurace federace identit se službou Ověření Identity uživatele Store SAP Cloud Platform](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici ověřováním identit cloudové platformy SAP na přístupovém panelu, vám by měl být automaticky přihlášeni k ověření Identity SAP Cloud v platformu, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)