---
title: 'Kurz: Azure Active Directory integrace s autopracovním pracovištěm | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a úlohou automatického pracovního pracoviště.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2019
ms.author: jeedes
ms.openlocfilehash: 8010bf25cc62d2de1b8b6ff9c0ecdc140c02c6a3
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674001"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Kurz: Integrace Azure Active Directory s autopracovním pracovištěm

V tomto kurzu se naučíte integrovat pracovní plochu AutoTask pomocí Azure Active Directory (Azure AD).
Integrování autopracovní plochy pomocí Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k pracovním prostorům pro autoúlohu.
* Můžete uživatelům povolit, aby se automaticky přihlásili k pracovní ploše automatického úkolu (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s autopracovním pracovištěm, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné automatického přihlašování na pracovišti pro jednotné přihlašování
* Předplatné automatického úkolu samoobslužné pracovní plochy s povoleným jednorázovým přihlášením
* Musíte být správce nebo správce super na pracovišti.
* Musíte mít účet správce ve službě Azure AD.
* Uživatelé, kteří budou používat tuto funkci, musí mít účty v rámci pracoviště a Azure AD a jejich e-mailové adresy musí odpovídat.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Pracovní prostor autopracovní pracoviště podporuje **aktualizace SP a IDP, které** bylo iniciováno.

## <a name="adding-autotask-workplace-from-the-gallery"></a>Přidání pracovního prostoru AutoTask z Galerie

Pokud chcete nakonfigurovat integraci autopracovní pracoviště do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat pracovní plochu pro vzdálenou úlohu z galerie.

**Pokud chcete přidat pracovní plochu AutoTask z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte příkaz **AutoTask pracoviště**, vyberte možnost **autopracovní plocha** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Pracovní plocha AutoTask v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s využitím pracovní plochy automatického úkolu na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci automatického úkolu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí automatického pracovního pracoviště, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování pro samoobslužnou práci s úlohami](#configure-autotask-workplace-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte si uživatele s Autopracovní pracovištěm pro testování](#create-autotask-workplace-test-user)** , abyste měli protějšek Britta Simon v rámci automatického úkolu, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí automatického pracovního pracoviště, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikací automatického **pracovního pracoviště** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat identifikátor, odpovědět U R L a vybrat Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory pro pracovní plochu AutoTask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **nastavit pracovní plochu Autopracovní pracoviště** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-autotask-workplace-single-sign-on"></a>Konfigurace Sign-On na pracovišti pro práci s úlohami

1. V jiném okně webového prohlížeče se přihlaste k pracovní ploše online pomocí přihlašovacích údajů správce.

    > [!Note]
    > Při konfiguraci IdP bude nutné zadat subdoménu. Pokud chcete potvrdit správnou subdoménu, přihlaste se k pracovní ploše online. Po přihlášení si poznamenejte poddoménu v adrese URL. Subdoména je součástí mezi "https://" a ". awp.autotask.net/" a měla by být US, EU, CA nebo au.

2. Přejít na **Konfigurace**  >  **jednotného přihlašování** a proveďte následující kroky:

    ![Konfigurace automatického přihlašování k jednotnému úkolu](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)

    a. Vyberte možnost **soubor metadat XML** a poté nahrajte stažené **federační Metadata XML** z Azure Portal.

    b. Klikněte na **Povolit jednotné přihlašování**.

    ![Konfigurace automatického přihlašování k jednotnému úkolu pro schválení](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. Zaškrtněte políčko **potvrzuji, že tyto informace jsou správné a toto zaškrtávací políčko je důvěryhodné pro IDP** .

    d. Klikněte na tlačítko **schválit**.

> [!Note]
> Pokud potřebujete pomoc s konfigurací pracovního prostředí pro vzdálenou úlohu, přečtěte si prosím na [této stránce](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) , kde získáte pomoc s účtem na pracovišti.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.

    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k pracovní ploše automatického úkolu.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte možnost **pracovní plocha pro autoúlohu**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **pracovní plocha pro autoúlohu**.

    ![Odkaz na pracovní plochu AutoTask v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-autotask-workplace-test-user"></a>Vytvořit úlohu pro test uživatele autopracovní plocha

V této části vytvoříte uživatele s názvem Britta Simon na pracovišti pro autoúlohu. Pokud chcete přidat uživatele na platformě autopracovní pracoviště autopracovní plocha, obraťte se na [tým podpory](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) autopracovní pracoviště.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici automatické pracovní plochy, měli byste se automaticky přihlásit k pracovní ploše automatického úkolu, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)