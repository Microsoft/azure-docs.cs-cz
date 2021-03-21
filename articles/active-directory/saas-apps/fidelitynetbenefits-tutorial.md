---
title: 'Kurz: Azure Active Directory integrace se NetBenefitsou přesnosti | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a věrnou NetBenefits.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: ac41bf89a3c2997fe0c179ef1a64787b5fb8d617
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453582"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Kurz: Azure Active Directory integrace s NetBenefitsmi věrných

V tomto kurzu se naučíte integrovat věrnou NetBenefits s Azure Active Directory (Azure AD).
Integrace věrných NetBenefits se službou Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup ke věrnému NetBenefits.
* Uživatelům můžete povolit, aby se automaticky přihlásili ke věrnému NetBenefits (jednotné přihlašování) s účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s využitím věrných NetBenefits potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným jednotným přihlašováním NetBenefits

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Věrnost NetBenefits podporuje **IDP** iniciované jednotné přihlašování.

* NetBenefits přesnosti podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Přidání věrnosti NetBenefits z Galerie

Pokud chcete nakonfigurovat integraci věrnosti NetBenefits do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat věrné NetBenefits z galerie.

**Pokud chcete přidat věrnou NetBenefits z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **věrné NetBenefits**, vyberte možnost **věrnost NetBenefits** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![NetBenefits věrnosti v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí věrné NetBenefits na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v NetBenefits.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s přesností NetBenefits, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte NetBenefits jednotné přihlašování](#configure-fidelity-netbenefits-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte věrného uživatele NetBenefits test](#create-fidelity-netbenefits-test-user)** -abyste měli protějšek Britta Simon v přesnosti NetBenefits, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí věrných NetBenefits, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **věrnost aplikace NetBenefits** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    ![Informace o jednotném přihlašování v NetBenefits a doméně a adresách URL přesnosti](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    Pro testovací prostředí:  `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    V produkčním prostředí:  `urn:sp:fidelity:geninbndnbparts20`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL, která se má poskytnout věrnost v době implementace, nebo se obraťte na přiřazené Service Manager klienta věrnosti.

5. Aplikace NetBenefits přesnosti očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName**. Aplikace věrnosti NetBenefits očekává, že **NameIdentifier** má být mapován pomocí **ČísloZaměstnance** nebo jakékoli jiné deklarace identity, která je pro vaši organizaci platná jako **NameIdentifier**, takže je nutné upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![image](common/edit-attribute.png)

    >[!Note]
    >NetBenefits věrnosti podporuje statickou a dynamickou federaci. Static znamená, že nebude používat technologii SAML podle času zřizování uživatelů a dynamickou cestou, kterou podporuje při zřizování uživatelů jenom včas. Pro použití zřizování založeného na prostředí JIT musí zákazníci přidat další deklarace identity v Azure AD, jako je třeba datum narození atd. Tyto podrobnosti jsou k dispozici pro klienta s přiřazenou **věrností Service Manager** a musí tuto dynamickou federaci povolit pro vaši instanci.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **nastavení přesnosti NetBenefits** zkopírujte příslušné adresy URL podle vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-fidelity-netbenefits-single-sign-on"></a>Konfigurace NetBenefits s přesností na jedno Sign-On

Chcete-li nakonfigurovat jednotné přihlašování na straně **přesnosti NetBenefits** , je třeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory NetBenefits](mailto:SSOMaintenance@fmr.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k věrnému NetBenefits.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte možnost **věrnost NetBenefits**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **věrnost NetBenefits**.

    ![Odkaz na věrnost NetBenefits v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-fidelity-netbenefits-test-user"></a>Vytvořit věrného uživatele testu NetBenefits

V této části vytvoříte uživatele s názvem Britta Simon na věrni NetBenefits. Pokud vytváříte statickou federaci, spolupracujte s přiřazenou **Service Manager klienta věrnosti** a vytvářejte uživatele na platformě NetBenefits. Tito uživatelé musí být před použitím jednotného přihlašování vytvořeni a aktivováni.

Pro dynamickou federaci se uživatelé vytvářejí pomocí zřizování uživatelů jenom v čase. Pro použití zřizování založeného na prostředí JIT musí zákazníci přidat další deklarace identity v Azure AD, jako je třeba datum narození atd. Tyto podrobnosti jsou k dispozici pro klienta s přiřazenou **věrností Service Manager** a musí tuto dynamickou federaci povolit pro vaši instanci.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici NetBenefits, měli byste se automaticky přihlásit ke věrnosti NetBenefits, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)