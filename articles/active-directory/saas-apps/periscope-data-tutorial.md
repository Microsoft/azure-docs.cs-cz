---
title: 'Kurz: Azure Active Directory integrace s daty Periscope | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Periscope daty.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: deac4dcde51049d744706b7479f5b5f3bbb4d9c0
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520851"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>Kurz: Azure Active Directory integrace s daty Periscope

V tomto kurzu se naučíte, jak integrovat data Periscope pomocí Azure Active Directory (Azure AD).
Integrace dat Periscope s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Periscope datům.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Periscope datům (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Periscope daty potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné Periscope data s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Data Periscope podporují jednotné přihlašování v **SP** .

## <a name="adding-periscope-data-from-the-gallery"></a>Přidání dat Periscope z Galerie

Pokud chcete nakonfigurovat integraci dat Periscope do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Periscope data z galerie.

**Chcete-li přidat Periscope data z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Periscope data** , vyberte **Periscope data** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Periscope data v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Periscope dat na základě testovacího uživatele s názvem **Britta Simon** .
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Periscope datech.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Periscope dat, je potřeba, abyste dokončili tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Periscope data](#configure-periscope-data-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte Periscope data Test User](#create-periscope-data-test-user)** -to, abyste měli protějšek Britta Simon v datech Periscope, který se odkazuje na reprezentaci uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Periscope dat, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **Periscope data** Application Integration vyberte **jednotné přihlašování** .

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování Periscope dat v doméně a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte libovolnou z těchto adres URL:
    
    ```https
    https://app.periscopedata.com/
    https://app.periscopedata.com/app/<SITENAME>
    ```

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > Hodnota adresy URL pro přihlášení není v reálném čase. Aktualizujte hodnoty pomocí skutečné přihlašovací adresy URL. Obraťte se na [tým podpory Periscope data Client](mailto:support@periscopedata.com) , aby získal tuto hodnotu a hodnotu identifikátoru z oddílu **Konfigurace Periscope dat pro jednotné přihlašování** , které se vysvětlí později v tomto kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Konfigurovat Periscope data Single Sign-On

1. V jiném okně webového prohlížeče se přihlaste k datům Periscope jako správce.

2. Otevřete nabídku ozubeného kolečka v levém dolním rohu **Billing** a otevřete  >  nabídku **zabezpečení** fakturace a proveďte následující kroky. K těmto nastavením mají přístup jenom správci.

    ![Snímek obrazovky, který zobrazuje dialogové okno zabezpečení s vybraným nastavením.](./media/periscope-data-tutorial/configure01.png)

    a. Zkopírujte **adresu URL federačních metadat aplikace** z kroku #5 **podpisový certifikát SAML** a otevřete ho v prohlížeči. Tím se otevře dokument XML.

    b. V textovém poli **jednotného přihlašování** vyberte **Azure Active Directory** .

    c. Vyhledejte tag **SingleSignOnService** a vložte hodnotu **umístění** do textového pole **URL jednotného přihlašování** .

    d. Vyhledejte tag **SingleLogoutService** a vložte hodnotu **umístění** do textového pole **URL objektu slo** .

    e. Zkopírujte hodnotu **identifikátoru** instance a vložte ji do textového pole **identifikátor (ID entity)** **základního konfiguračního oddílu SAML** na Azure Portal.

    f. Vyhledejte první značku souboru XML, zkopírujte hodnotu **entityID** a vložte ji do textového pole **vystavitele** .

    například Vyhledejte tag **IDPSSODescriptor** s protokolem SAML. V této části Najděte **popisovač** klíč značek pomocí **Use = Signing** . Zkopírujte hodnotu **certifikátu x509** a vložte ji do textového pole **certifikátu** .

    h. Lokality s více mezerami můžou zvolit výchozí prostor z rozevíracího seznamu **výchozí prostor** . Toto je místo, kde se nové uživatele přidají, když se přihlásí k Periscope dat poprvé a zřídí se pomocí jednotného přihlašování služby Active Directory.

    i. Nakonec klikněte na **Uložit** a **potvrďte** změnu nastavení jednotného přihlašování, a to zadáním **odhlášení** .

    ![Snímek obrazovky, který zobrazuje dialogové okno aktualizace s S konfigurací, se kterým se v textovém poli zadal typ odhlášení a vybrané tlačítko Potvrdit](./media/periscope-data-tutorial/configure02.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory** , vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé** .

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon** .
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit** .

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k datům Periscope.

1. V Azure Portal vyberte možnost **podnikové aplikace** , vyberte možnost **všechny aplikace** a pak vyberte **Periscope data** .

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Periscope data** .

    ![Odkaz na data Periscope v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny** .

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-periscope-data-test-user"></a>Vytvořit uživatele Periscope data test

Aby se uživatelé Azure AD mohli přihlašovat k Periscope datům, musí se zřídit do Periscope dat. V Periscope data je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k datům Periscope jako správce.

2. Klikněte na ikonu **Nastavení** v levém dolním rohu nabídky a přejděte na **oprávnění** .

    ![Snímek obrazovky zobrazující, že je v nabídce nastavení vybraná možnost oprávnění](./media/periscope-data-tutorial/configure03.png)

3. Klikněte na **Přidat uživatele** a proveďte následující kroky:

      ![Informace o konfiguraci Periscope dat](./media/periscope-data-tutorial/configure04.png)

    a. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako například **Britta** .

    b. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon** .

    c. Do textového pole **e-mail** zadejte e-maily uživatele, jako je **brittasimon \@ contoso.com** .

    d. Klikněte na tlačítko **Přidat** .

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici data Periscope na přístupovém panelu, měli byste se automaticky přihlásit k Periscope datům, pro která jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)