---
title: 'Kurz: Azure Active Directory integrace s T&E Express | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a T&E Express.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: f44a67b4943c6897f82aedf47d493b36e5e2d3b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92521867"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Kurz: Azure Active Directory integrace s T&E Express

V tomto kurzu se naučíte integrovat T&E Express s Azure Active Directory (Azure AD).
Integrace T&E Express s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k T&E Express.
* Uživatelům můžete povolit, aby se automaticky přihlásili k T&E Express (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s T&E Express budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* T&E pro jednotné přihlašování s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* T&E Express podporuje jednotné přihlašování iniciované **IDP**

## <a name="adding-te-express-from-the-gallery"></a>Přidání T&E Express z Galerie

Pokud chcete nakonfigurovat integraci T&E Express do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat T&E z galerie.

**Pokud chcete přidat T&E Express z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **t&e Express**, vyberte **t&e Express** z panelu výsledků a pak klikněte na **Přidat** tlačítko pro přidání aplikace.

    ![T&E Express v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s T&E Express na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán odkaz na odkaz mezi uživatelem služby Azure AD a souvisejícím uživatelem v T&E Express.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí T&E Express, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte T&E Express jednotného přihlašování](#configure-te-express-single-sign-on)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte T&E Express Test User](#create-te-express-test-user)** – abyste měli protějšek Britta Simon v T&E Express, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD s T&E Express, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **T&E Express** Application Integration vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    ![T&E expresní informace o jednotném přihlašování v doméně a adresách URL](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte hodnotu jako URL pomocí následujícího vzoru: `https://<domain>.tyeexpress.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Tady doporučujeme, abyste v identifikátoru použili jedinečnou hodnotu řetězce. Chcete-li získat tyto hodnoty, obraťte se na kontakt [T&E tým podpory klientů](https://www.tyeexpress.com/contacto.aspx) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **nastavit T&E Express** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-te-express-single-sign-on"></a>Konfigurovat T&E Express Single Sign-On

1. Pokud chcete nakonfigurovat jednotné přihlašování na **T&e** na straně Express, přihlaste se k aplikaci t&e Express bez jednotného přihlašování pomocí SAML s použitím přihlašovacích údajů správce.

1. Na kartě **správce** kliknutím na **doména SAML** otevřete stránku nastavení SAML.

    ![Snímek obrazovky zobrazuje doménu SAML vybranou v nabídce správce.](./media/tyeexpress-tutorial/tye-SAML.png)

1. Vyberte možnost **activar (aktivovat)** od **ne** po **(Ano)**. Do textového pole **metadata poskytovatele identity** vložte XML metadat, které jste stáhli z Azure Portal.

    ![Snímek obrazovky se zobrazí stránka Dominio SAML, kde můžete zadat metadata.](./media/tyeexpress-tutorial/tyeAdmin.png)

1. Kliknutím na tlačítko **Guardar (Uložit)** nastavení uložte.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména****brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k T&E Express.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte **T&E Express**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **T&E Express**.

    ![Odkaz T&E Express v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-te-express-test-user"></a>Vytvořit T&E Express Test User

Aby se uživatelé Azure AD mohli přihlásit k T&E Expressu, musí se zřídit do T&E Express. V případě T&E Express je zřizování ručním úkolem.

**Při zřizování uživatelských účtů proveďte následující kroky:**

1. Přihlaste se k firemnímu webu T&E Express jako správce.

1. V části značka Správce klikněte na uživatelé a otevřete stránku předloha uživatelé.

    ![Snímek obrazovky se zobrazí uživatelé vybraní z nabídky správce.](./media/tyeexpress-tutorial/tye-adminusers.png)

1. Na domovské stránce klikněte na tlačítko **+** a přidejte uživatele.

    ![Snímek obrazovky zobrazuje ikonu Plus pro přidání uživatelů.](./media/tyeexpress-tutorial/tye-usershome.png)

1. Zadejte všechny povinné podrobnosti ve formuláři a kliknutím na tlačítko Uložit uložte podrobnosti.

    ![Snímek obrazovky se zobrazí v sekci informace o uživateli, kde můžete zadat příslušné hodnoty.](./media/tyeexpress-tutorial/tye-usersadd.png)

    ![Snímek obrazovky se zobrazí v sekcích schvalovatelé a asistent, kde můžete zadat příslušné hodnoty.](./media/tyeexpress-tutorial/tye-userssave.png)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici T&E Express na přístupovém panelu, měli byste se automaticky přihlásit k E-&E Expressu, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)