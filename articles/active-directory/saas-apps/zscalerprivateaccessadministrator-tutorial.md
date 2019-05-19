---
title: 'Kurz: Integrace s Azure Active Directory se Zscalerem privátní přístup správce | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscalerem privátní přístup správce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce75431de24886c038cd2eb4ee7db02d2b6cde31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "65864885"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Kurz: Integrace s Azure Active Directory se Zscalerem privátní přístup správce

V tomto kurzu se dozvíte, jak integrovat Zscalerem privátní přístup správce Azure Active Directory (Azure AD).
Integrace Zscalerem privátní přístup správce s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Zscalerem privátní přístup správce.
* Uživatelům se automaticky přihlášeni k Zscalerem privátní přístup správce (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se Zscalerem privátní přístup správce, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Zscalerem privátní přístup správce jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Zscalerem privátní přístup správce **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Přidání Zscalerem privátní přístup správce z Galerie

Ke konfiguraci integrace správce přístupu Zscalerem privátní do služby Azure AD, budete muset přidat Zscalerem privátní přístup správce v galerii na váš seznam spravovaných aplikací SaaS.

**Pro přidání správce Zscalerem privátní přístup z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscalerem privátní přístup správce**vyberte **Zscalerem privátní přístup správce** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Zscalerem privátní přístup správce v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestovat Azure AD jednotné přihlašování s Zscalerem privátní přístup správce na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Zscalerem privátní přístup správce.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se Zscalerem privátní přístup správce, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Zscalerem privátní přístup správce jednotného přihlašování](#configure-zscaler-private-access-administrator-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Zscalerem privátní přístup správce](#create-zscaler-private-access-administrator-test-user)**  – Pokud chcete mít protějšek Britta Simon v privátní Zscalerem správce přístupu, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotného přihlašování se Zscalerem privátní přístup správce, postupujte následovně:

1. V [webu Azure portal](https://portal.azure.com/)na **Zscalerem privátní přístup správce** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Zscalerem privátní přístup správce domény a adresy URL jednotného přihlašování – informace](common/idp-relay.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Klikněte na tlačítko **nastavit další adresy URL**.

    d. V **stav přenosu** textové pole, zadejte adresu URL: `idpadminsso`

5.  Pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu, postupujte následovně:

    ![Zscalerem privátní přístup správce domény a adresy URL jednotného přihlašování – informace](common/both-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL identifikátor, adresa URL odpovědi a přihlašování. Kontakt [tým podpory Zscalerem privátní přístup správce klienta](https://help.zscaler.com/zpa-submit-ticket) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. Na **nastaven se Zscalerem privátní přístup správce** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Konfigurace přihlašování jedním privátní přístup správce Zscaler

1. V okně prohlížeče jiných webových přihlášení jako správce k Zscalerem privátní přístup správce.

2. V horní části klikněte na **správu** a přejděte do **ověřování** klikněte na **konfigurace zprostředkovatele identity**.

    ![Správce Zscalerem privátní přístup správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. V pravém horním rohu, klikněte na tlačítko **přidat konfigurace zprostředkovatele identity**. 

    ![Addidp Zscalerem privátní přístup správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Na **přidat konfigurace zprostředkovatele identity** stránku, proveďte následující kroky:
 
    ![Idpselect Zscalerem privátní přístup správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Klikněte na tlačítko **vybrat soubor** nahrát stažený soubor metadat z Azure AD **nahrát soubor metadat zprostředkovatele identity** pole.

    b. Načte **metadat zprostředkovatele identity** ze služby Azure AD a naplní všechny informace o polích, jak je znázorněno níže.

    ![Idpconfig Zscalerem privátní přístup správce](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Vyberte **jednotné přihlašování** jako **správce**.

    d. Zadejte název domény z **domén** pole.
    
    e. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Zscalerem privátní přístup správce.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Zscalerem privátní přístup správce**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zscalerem privátní přístup správce**.

    ![Odkaz Zscalerem privátní přístup správce v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Vytvořit testovacího uživatele Zscalerem privátní přístup správce

Povolení služby Azure AD uživatelům umožní přihlásit k Zscalerem privátní přístup správce, musí být poskytnuty do Zscalerem privátní přístup správce. V případě Zscalerem privátní přístup správce, zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k webu Zscalerem privátní přístup správce společnosti jako správce.

2. V horní části klikněte na **správu** a přejděte do **ověřování** klikněte na **konfigurace zprostředkovatele identity**.

    ![Správce Zscalerem privátní přístup správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klikněte na tlačítko **správci** z levé nabídky.

    ![Správce Zscalerem privátní přístup správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. V pravém horním rohu, klikněte na tlačítko **přidat správce**:

    ![Zscalerem privátní přístup správce přidat správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. V **přidat správce** stránce, proveďte následující kroky:

    ![Správce uživatelů Zscalerem privátní přístup správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. V **uživatelské jméno** textového pole zadejte e-mailu uživatele, jako je BrittaSimon@contoso.com.

    b. V **heslo** textového pole zadejte heslo.

    c. V **potvrzení hesla** textového pole zadejte heslo.

    d. Vyberte **Role** jako **Zscalerem privátní přístup správce**.

    e. V **e-mailu** textového pole zadejte e-mailu uživatele, jako je BrittaSimon@contoso.com.

    f. V **Phone** textového pole zadejte telefonní číslo.

    g. V **časové pásmo** textové pole, vyberte časové pásmo.

    h. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Zscalerem privátní přístup správce na přístupovém panelu, můžete by měl být automaticky přihlášeni k Zscalerem privátní přístup správce, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

