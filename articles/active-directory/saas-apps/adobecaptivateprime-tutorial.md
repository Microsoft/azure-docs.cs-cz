---
title: 'Kurz: Azure Active Directory integrace s Adobe Captivate | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a od Adobe Captivate.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 89f59bba856d662aea0c3ed249d5a1462297ec60
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309053"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-captivate-prime"></a>Kurz: Integrace Azure Active Directory s využitím Adobe Captivate

V tomto kurzu se dozvíte, jak integrovat Adobe Captivate s Azure Active Directory (Azure AD).
Integrace aplikace Adobe Captivate s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Captivatei společnosti Adobe.
* Uživatelům můžete povolit, aby se automaticky přihlásili k programu Adobe Captivate (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s využitím služby Adobe Captivate potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné programu Adobe Captivate s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Společnost Adobe Captivate podporuje jednotné přihlašování **IDP** .

## <a name="adding-adobe-captivate-prime-from-the-gallery"></a>Přidání základny Adobe Captivate z Galerie

Pokud chcete nakonfigurovat integraci doplňku Adobe Captivate do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat z Galerie Adobe Captivate.

**Chcete-li přidat Adobe Captivate z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Adobe Captivate**, vyberte **Adobe Captivate** z panelu výsledků a pak kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Adobe Captivate v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s využitím služby Adobe Captivate na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci Adobe Captivate.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s využitím Adobe Captivate, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Konfigurací jednotného přihlašování Adobe Captivate](#configure-adobe-captivate-prime-single-sign-on)** můžete nakonfigurovat nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte si testovacího uživatele Adobe Captivate](#create-adobe-captivate-prime-test-user)** , abyste měli protějšek Britta Simon v Adobe Captivate, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Captivate, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace se základními aplikacemi **Adobe Captivate** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    ![Informace o jednotném přihlašování k primárním doménám a adresám URL společnosti Adobe Captivate](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL: `https://captivateprime.adobe.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL: `https://captivateprime.adobe.com/saml/SSO`

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **nastavení direktivy Adobe Captivate** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

7. Přejděte na kartu **vlastnosti** , zkopírujte **adresu URL přístupu uživatele** a vložte ji do programu Poznámkový blok.

    ![Odkaz na přístup uživatele](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_appprop.png)

### <a name="configure-adobe-captivate-prime-single-sign-on"></a>Konfigurace primárního Sign-On Adobe Captivate

Chcete-li nakonfigurovat jednotné přihlašování na straně **Adobe Captivate** , je třeba odeslat stažený **kód XML federačních metadat**, zkopírovat **adresu URL přístupu uživatele** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory společnosti Adobe Captivate](mailto:captivateprimesupport@adobe.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k části Adobe Captivate.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **Adobe Captivate**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Adobe Captivate**.

    ![Odkaz na základnu Adobe Captivate v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-adobe-captivate-prime-test-user"></a>Vytvoření primárního testovacího uživatele Adobe Captivate

V této části vytvoříte uživatele s názvem Britta Simon ve Adobe Captivate. Pracujte s [týmem podpory Adobe Captivate](mailto:captivateprimesupport@adobe.com) a přidejte uživatele v základní platformě Adobe Captivate. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Captivate, měli byste se automaticky přihlásili k části Adobe Captivate, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)