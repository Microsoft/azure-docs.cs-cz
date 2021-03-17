---
title: 'Kurz: Azure Active Directory integrace se softwarem pro produktivitu Wizergos | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Wizergos software pro produktivitu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: b4cddae25bbf7ff113d2ea67700e28eb81c0e7c4
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638019"
---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>Kurz: Azure Active Directory integrace se softwarem pro produktivitu Wizergos

V tomto kurzu se naučíte integrovat software Wizergos produktivity s využitím služby Azure Active Directory (Azure AD).
Integrace Wizergos produktivity softwaru se službou Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Wizergos kancelářskému softwaru.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Wizergos produktivnímu softwaru (jednotné přihlašování) s účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD se softwarem Wizergos pro zvýšení produktivity potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné Wizergos produktivity softwaru s jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Software pro Wizergos produktivity podporuje **IDP** iniciované jednotné přihlašování.

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>Přidání softwaru pro produktivitu Wizergos z Galerie

Pokud chcete nakonfigurovat integraci Wizergos produktivity softwaru do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Wizergos software pro produktivitu z galerie.

**Pokud chcete do galerie přidat Wizergos software pro zvýšení produktivity, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Wizergos software pro zvýšení produktivity** , vyberte **Wizergos software pro produktivitu** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Wizergos produktivní software v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí softwaru Wizergos pro produktivitu na základě testovacího uživatele s názvem **Britta Simon** .
Aby jednotné přihlašování fungovalo, je potřeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Wizergos kancelářském softwaru.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Wizergos kancelářského softwaru, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování pro Wizergos produktivitu softwaru](#configure-wizergos-productivity-software-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte si uživatele testovacího softwaru Wizergos pro zvýšení produktivity](#create-wizergos-productivity-software-test-user)** , abyste měli protějšek Britta Simon v softwaru Wizergos software, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Wizergos softwaru pro produktivitu, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **softwarové aplikace Wizergos** vyberte **jednotné přihlašování** .

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Wizergos produktivitu a informace o jednotném přihlašování v doméně softwaru a adrese URL](common/idp-identifier.png)

    Do textového pole **identifikátor** zadejte adresu URL:  `https://www.wizergos.net`

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavit software pro Wizergos produktivitu** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-wizergos-productivity-software-single-sign-on"></a>Konfigurace Wizergos produktivního softwaru na jednom Sign-On

1. V jiném okně webového prohlížeče se přihlaste k tenantovi Wizergos produktivity softwaru jako správce.

2. V nabídce hamburgerovou "vyberte **správce** .

    ![Snímek obrazovky se zobrazí ikona správce vybraná z nabídky.](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)

3. V nabídce na stránce pro správu na levé straně vyberte **ověřování** a klikněte na **Azure AD** .

    ![Snímek obrazovky zobrazuje Azure A D vybraný z ověřování.](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)

4. V části **ověřování** proveďte následující kroky.

    ![Snímek obrazovky se zobrazí stránka ověřování, kde můžete zadat hodnoty, které jsou popsány.](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
    
    a. Klikněte na tlačítko **nahrát** a nahrajte stažený certifikát z Azure AD.
    
    b. Do textového pole **Adresa URL vystavitele** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.
    
    c. Do textového pole **URL s jedním Sign-On** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.
    
    d. Do textového pole **URL s jedním Sign-Out** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.
    
    e. Klikněte na tlačítko **Uložit** .

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory** , vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé** .

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon** .
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit** .

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k softwaru Wizergos produktivity.

1. V Azure Portal vyberte **podnikové aplikace** , vyberte **všechny aplikace** a pak vyberte software pro **Wizergos produktivity** .

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Wizergos software pro zvýšení produktivity** .

    ![Odkaz na software pro produktivitu Wizergos v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny** .

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-wizergos-productivity-software-test-user"></a>Vytvořit testovacího uživatele Wizergos produktivity softwaru

V této části vytvoříte uživatele s názvem Britta Simon v softwaru Wizergos pro produktivitu. Pokud chcete přidat uživatele do Wizergos produktivních softwarových platforem, pracujte s [týmem podpory Wizergos produktivity softwaru](mailTo:support@wizergos.com) .

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici software pro Wizergos produktivitu na přístupovém panelu byste měli být automaticky přihlášeni k softwaru Wizergos pro zvýšení produktivity, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)