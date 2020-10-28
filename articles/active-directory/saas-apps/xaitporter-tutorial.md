---
title: 'Kurz: Azure Active Directory integrace s XaitPorter | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a XaitPorter.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: f729828627fdb0be18f220d34227aac756ed4e13
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92894861"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Kurz: Azure Active Directory integrace s XaitPorter

V tomto kurzu se dozvíte, jak integrovat XaitPorter s Azure Active Directory (Azure AD).
Integrace XaitPorter s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k XaitPorter.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k XaitPorter (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s XaitPorter potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným XaitPorterm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* XaitPorter podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-xaitporter-from-the-gallery"></a>Přidání XaitPorter z Galerie

Pokud chcete nakonfigurovat integraci XaitPorter do služby Azure AD, musíte přidat XaitPorter z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat XaitPorter z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **XaitPorter** , vyberte **XaitPorter** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![XaitPorter v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí XaitPorter na základě testovacího uživatele s názvem **Britta Simon** .
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v XaitPorter.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí XaitPorter, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování XaitPorter](#configure-xaitporter-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření XaitPorter Test User](#create-xaitporter-test-user)** – pro Britta Simon v XaitPorter, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí XaitPorter, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **XaitPorter** vyberte **jednotné přihlašování** .

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně XaitPorter a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.xaitporter.com/saml/login`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.xaitporter.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta XaitPorter](https://www.xait.com/support/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

6. Zadejte **IP adresu** nebo **adresu URL federačních metadat aplikace** [týmu podpory SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/), aby XAITPORTER mohl zajistit, aby byla IP adresa dostupná z vaší instance XaitPorter s konfigurací schváleného seznamu na jejich straně. 

### <a name="configure-xaitporter-single-sign-on"></a>Nakonfigurovat XaitPorter jednu Sign-On

1. Pokud chcete automatizovat konfiguraci v rámci XaitPorter, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření** .

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **XaitPorter nastavení** a nasměrujte vás na aplikaci XaitPorter. Odtud zadejte přihlašovací údaje správce, které se přihlásí k XaitPorter. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit XaitPorter ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu XaitPorter společnosti jako správce a proveďte následující kroky:

4. Klikněte na **správce** .

    ![Snímek obrazovky ukazuje správce vybraný na webu XaitPorter.](./media/xaitporter-tutorial/user1.png)

5. V rozevíracím seznamu **nastavení systému** vyberte **Spravovat jednotné přihlašování** .

    ![Snímek obrazovky ukazuje možnost spravovat jednu Sign-On vybranou z nastavení systému.](./media/xaitporter-tutorial/user2.png)

6. V části **Spravovat jednotné přihlašování** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí část Správa jednotného přihlašování, kde můžete provádět tyto kroky.](./media/xaitporter-tutorial/user3.png)

    a. Vyberte možnost **Povolit ověřování pomocí jednoho Sign-On** .

    b. V poli **nastavení zprostředkovatele identity** vložte **adresu URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal, a klikněte na **načíst** .

    c. Vyberte **Povolit autotvorbu uživatelů** .

    d. Klikněte na **OK** .

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k XaitPorter.

1. V Azure Portal vyberte **podnikové aplikace** , vyberte **všechny aplikace** a pak vyberte **XaitPorter** .

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **XaitPorter** .

    ![Odkaz XaitPorter v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny** .

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-xaitporter-test-user"></a>Vytvořit testovacího uživatele XaitPorter

V této části vytvoříte uživatele s názvem Britta Simon v XaitPorter. Pokud chcete přidat uživatele na platformě XaitPorter, pracujte s [týmem podpory klienta XaitPorter](https://www.xait.com/support/) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici XaitPorter, měli byste se automaticky přihlásit k XaitPorter, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)