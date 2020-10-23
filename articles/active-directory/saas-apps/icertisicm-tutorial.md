---
title: 'Kurz: Azure Active Directory integrace s platformou správy kontraktů Icertis | Microsoft Docs'
description: Naučte se konfigurovat jednotné přihlašování mezi Azure Active Directory a Icertisou platformou pro správu smluv.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: f6b1ea00684495333ba7b04dbf146aa6104fa589
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460508"
---
# <a name="tutorial-azure-active-directory-integration-with-icertis-contract-management-platform"></a>Kurz: Azure Active Directory integrace s platformou správy kontraktů Icertis

V tomto kurzu se dozvíte, jak integrovat platformu správy kontraktů Icertis s Azure Active Directory (Azure AD).
Integrace platformy pro správu kontraktů Icertis s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k platformě správy kontraktů Icertis.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k platformě správy kontraktů Icertis (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s platformou správy smluv Icertis potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Odběr povoleného jednotného přihlašování pro platformu správy kontraktů Icertis

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Platforma pro správu smluv Icertis podporuje jednotné přihlašování (SSO) s podporou **SP**

## <a name="adding-icertis-contract-management-platform-from-the-gallery"></a>Přidání platformy pro správu kontraktů Icertis z Galerie

Pokud chcete nakonfigurovat integraci platformy pro správu smluv Icertis do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Icertis platformu pro správu kontraktů z galerie.

**Pokud chcete přidat platformu správy kontraktů Icertis z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Icertis kontrakt Management Platform**, vyberte možnost **Icertis Správa kontraktů** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Icertisá platforma pro správu smluv v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s platformou správy kontraktů Icertis na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci platformy správy kontraktů Icertis.

Pokud chcete konfigurovat a testovat jednotné přihlašování Azure AD s platformou správy kontraktů Icertis, je potřeba, abyste dokončili tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování pro platformu Icertis Management Platform](#configure-icertis-contract-management-platform-single-sign-on)** – pro konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořit testovacího uživatele platformy pro správu kontraktů Icertis](#create-icertis-contract-management-platform-test-user)** – abyste měli protějšek Britta Simon v rámci platformy pro správu smluv Icertis, která je propojená s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD s platformou správy kontraktů Icertis, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **pro správu kontraktů Icertis** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování pro doménu platformy správy kontraktů Icertis a adresy URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<company name>.icertis.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<company name>.icertis.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory Icertis smlouvy pro správu smluv Management Platform](https://www.icertis.com/company/contact/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení platformy pro správu smluv Icertis** zkopírujte příslušné adresy URL podle vašich požadavků. Pro **přihlašovací adresu URL**použijte hodnotu s následujícím vzorem: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ je ID tenanta předplatného služby Azure AD.

    ![Kopírovat adresy URL konfigurace](media/icertisicm-tutorial/configurls.png)

    a. Identifikátor Azure AD

    b. Odhlašovací adresa URL

### <a name="configure-icertis-contract-management-platform-single-sign-on"></a>Konfigurovat Icertisou platformu pro správu kontraktů s jednou Sign-On

Pokud chcete nakonfigurovat jednotné přihlašování na straně **platformy pro správu smluv Icertis** , je potřeba odeslat stažený **kód XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory Icertis pro správu smluv](https://www.icertis.com/company/contact/). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například, BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k platformě správy kontraktů Icertis.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **Icertis kontrakt Management Platform**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Icertis kontrakt Management Platform**.

    ![Odkaz na platformu pro správu kontraktů Icertis v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-icertis-contract-management-platform-test-user"></a>Vytvořit testovacího uživatele platformy pro správu kontraktů Icertis

V této části vytvoříte uživatele s názvem Britta Simon v Icertis platformě pro správu smluv. Pokud chcete přidat uživatele na platformě Icertis pro správu kontraktů, pracujte s [týmem podpory správy kontraktů Icertis](https://www.icertis.com/company/contact/) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici platforma pro správu smluv Icertis, měli byste být automaticky přihlášeni k platformě správy smluv Icertis, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)