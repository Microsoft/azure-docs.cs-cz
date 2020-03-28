---
title: 'Kurz: Integrace služby Azure Active Directory s absorpčním LMS | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Absorbovat LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 936de76d1117c56f5a9dec48b51f33b9afa15351
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67107503"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Kurz: Integrace služby Azure Active Directory s absorpčním LMS

V tomto kurzu se dozvíte, jak integrovat absorbovat LMS s Azure Active Directory (Azure AD).
Integrace Absorb LMS s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Absorb LMS.
* Můžete povolit uživatelům, aby se automaticky přihlásili k absorpci LMS (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí absorbovat LMS, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Absorpční předplatné s povoleným jedním přihlášením LMS

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Absorb LMS podporuje **idp** iniciované sso

## <a name="adding-absorb-lms-from-the-gallery"></a>Přidání Absorb LMS z galerie

Chcete-li nakonfigurovat integraci Absorb LMS do Azure AD, musíte přidat Absorb LMS z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat absorbovat LMS z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Absorbovat LMS**, z panelu výsledků vyberte **Absorbovat LMS** a pak klepněte na **tlačítko Přidat** a přidejte aplikaci.

     ![Absorbovat LMS v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí absorbování LMS na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v absorbovat LMS.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí technologie Absorb LMS, je třeba provést následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace absorbovat LMS jednotné přihlášení](#configure-absorb-lms-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte absorpční lms testovací uživatel](#create-absorb-lms-test-user)** – mít protějšek Britta Simon v absorbovat LMS, který je propojený s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí technologie Absorb LMS, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce Integrace aplikací **Absorbovat LMS** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na tlačítko **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Absorpce informací o doméně LMS a adresURL s jedním přihlášením](common/idp-intiated.png)

    Pokud používáte **absorbovat 5 - uI** použijte následující konfiguraci:

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://company.myabsorb.com/account/saml`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://company.myabsorb.com/account/saml`

    Pokud používáte **absorbovat 5 - Nové zkušenosti se studentem** použijte následující konfiguraci:

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://company.myabsorb.com/api/rest/v2/authentication/saml`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://company.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou adresou URL identifikátoru a odpovědi. Obraťte [se na tým podpory klienta LMS](https://support.absorblms.com/hc/) absorbovat, abyste získali tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Následující snímek obrazovky zobrazuje seznam výchozích atributů, kde jako **nameidentifier** je mapován s **user.userprincipalname**.

    ![image](common/edit-attribute.png)

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V části **Nastavit absorbovat LMS** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-absorb-lms-single-sign-on"></a>Konfigurace jednorázového přihlášení absorbovat LMS

1. V novém okně webového prohlížeče se přihlaste k webu společnosti Absorb LMS jako správce.

2. Vpravém horním rohu vyberte tlačítko **Účet.**

    ![Tlačítko Účet](./media/absorblms-tutorial/1.png)

3. V podokně Účet vyberte **Nastavení portálu**.

    ![Odkaz Nastavení portálu](./media/absorblms-tutorial/2.png)

4. Vyberte kartu **Spravovat nastavení připřipoutání k siřízení zabezpečení.**

    ![Karta Uživatelé](./media/absorblms-tutorial/managesso.png)

5. Na stránce **Spravovat nastavení jednotného přihlašování** postupujte takto:

    ![Konfigurační stránka jednotného přihlášení](./media/absorblms-tutorial/settings.png)

    a. Do textového pole **Název** zadejte název, jako je azure ad marketplace přisáchu.

    b. Vyberte **SAML** jako **metodu**.

    c. V poznámkovém bloku otevřete certifikát, který jste stáhli z webu Azure Portal. Odeberte značky **---BEGIN CERTIFICATE---** a **---END CERTIFICATE---.** Potom do pole **Klíč** vložte zbývající obsah.

    d. V poli **Mode** vyberte **Identity Provider Initiated**.

    e. V poli **Vlastnost Id** vyberte atribut, který jste nakonfigurovali jako identifikátor uživatele ve službě Azure AD. Například pokud *nameidentifier* je vybraná ve službě Azure AD, vyberte **Uživatelské jméno**.

    f. Vyberte **Sha256** jako **typ podpisu**.

    g. Do pole **Přihlašovací adresa URL** vložte adresu URL pro přístup **uživatelů** ze stránky **Vlastnosti** aplikace na webu Azure Portal.

    h. V **adrese URL odhlášení**vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z okna **Konfigurovat přihlášení na** webu Azure Portal.

    i. Přepínat **automatické přesměrování** **na Zapnuto**.

6. Vyberte **Uložit.**

    ![Pouze povolit přihlášení při hlásek přihlašování](./media/absorblms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** typ pole`brittasimon\@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Absorb LMS.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Absorbovat LMS**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **Absorbovat LMS**.

    ![Odkaz Absorbovat LMS v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-absorb-lms-test-user"></a>Vytvořit testovacího uživatele Absorb LMS

Pro uživatele Azure AD k přihlášení k absorbovat LMS, musí být nastavena v Absorb LMS. V případě absorbovat LMS zřizování je ruční úlohy.

**Chcete-li konfigurovat zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k webu společnosti Absorb LMS jako správce.

2. V podokně **Uživatelé** vyberte **možnost Uživatelé**.

    ![Odkaz Uživatelé](./media/absorblms-tutorial/absorblms_userssub.png)

3. Vyberte kartu **Uživatel.**

    ![Rozevírací seznam Přidat nový](./media/absorblms-tutorial/absorblms_createuser.png)

4. Na stránce **Přidat uživatele** postupujte takto:

    ![Stránka Přidat uživatele](./media/absorblms-tutorial/user.png)

    a. Do pole **Křestní jméno** zadejte křestní jméno, například **Britta**.

    b. Do pole **Příjmení** zadejte příjmení, například **Simon**.

    c. Do pole **Uživatelské jméno** zadejte celé jméno, například **Britta Simon**.

    d. Do pole **Heslo** zadejte uživatelské heslo.

    e. Do pole **Potvrdit heslo** zadejte heslo znovu.

    f. Nastavte přepínač **Je aktivní** na **Aktivní**.

5. Vyberte **Uložit.**

    ![Pouze povolit přihlášení při hlásek přihlašování](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Ve výchozím nastavení zřizování uživatelů není povoleno v přiřazování služeb při řadání. Pokud chce zákazník tuto funkci povolit, musí ji nastavit tak, jak je uvedeno v [této](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) dokumentaci. Upozorňujeme také, že uživatelská provisioing je k dispozici pouze na **absorb5 - New Learner Experience** s Adresou URL ACS-`https://company.myabsorb.com/api/rest/v2/authentication/saml`

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Absorbovat LMS na přístupovém panelu, by měl být automaticky přihlášeni k absorbovat LMS, pro které nastavíte přihlašování k řízení přihlašování. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
