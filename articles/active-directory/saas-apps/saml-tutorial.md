---
title: 'Kurz: Azure Active Directory integrace s povolenou aplikací LOB s tokenem SAML 1,1 | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a obchodní aplikací s povoleným tokenem SAML 1,1.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 0b15d560e2678772cefdf3d87c047013b24ed467
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675486"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Kurz: Azure Active Directory integrace s povolenou aplikací obchodní aplikace s tokenem SAML 1,1

V tomto kurzu se naučíte integrovat obchodní aplikaci s povoleným tokenem SAML 1,1 s Azure Active Directory (Azure AD).
Integrace obchodní aplikace s povoleným tokenem SAML 1,1 s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k aplikaci LOB s povoleným tokenem SAML 1,1.
* Uživatelům můžete povolit, aby se automaticky přihlásili k obchodní aplikaci s povoleným tokenem SAML 1,1 (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

K nakonfigurování integrace služby Azure AD pomocí obchodní aplikace s povoleným tokenem SAML 1,1 potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* U předplatného jednotného přihlašování aplikace LOB povolený token SAML 1,1

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Obchodní aplikace s povoleným tokenem SAML **1,1 podporuje jednotné** přihlašování na více instancích

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>Přidání obchodní aplikace s povoleným tokenem SAML 1,1 z Galerie

Pokud chcete nakonfigurovat integraci obchodní aplikace s povoleným tokenem SAML 1,1 do služby Azure AD, musíte z Galerie přidat obchodní aplikaci s podporou tokenů SAML 1,1 do seznamu spravovaných aplikací SaaS.

**Pokud chcete z Galerie přidat obchodní aplikaci s povoleným tokenem SAML 1,1, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte obchodní **aplikaci s povoleným tokenem saml 1,1** , vyberte obchodní **aplikaci s podporou tokenů 1,1 SAML** z panelu výsledků a kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Aplikace LOB s povoleným tokenem SAML 1,1 v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí aplikace LOB s povoleným tokenem SAML 1,1 na základě testovacího uživatele s názvem **Britta Simon** .
Aby jednotné přihlašování fungovalo, musí být navázán odkaz na odkaz mezi uživatelem služby Azure AD a souvisejícím uživatelem v obchodní aplikaci s povoleným tokenem SAML 1,1.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí obchodní aplikace s povoleným tokenem SAML 1,1, je potřeba, abyste dokončili tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování s povoleným tokenem SAML 1,1](#configure-saml-11-token-enabled-lob-app-single-sign-on)** , abyste na straně aplikace nakonfigurovali nastavení jednoho Sign-On.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoří testovacího uživatele aplikace s povoleným tokenem saml 1,1](#create-saml-11-token-enabled-lob-app-test-user)** , který má protějšek Britta Simon v obchodní aplikaci s podporou tokenu SAML 1,1, která je propojená s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí obchodní aplikace s povoleným tokenem SAML 1,1, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **LOB s povoleným tokenem SAML 1,1** vyberte **jednotné přihlašování** .

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![U tokenu SAML 1,1 s povolenou doménou a adresou URL jednotného přihlašování v doméně aplikace LOB](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://your-app-url`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://your-app-url`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na tým podpory klientů aplikace LOB s povoleným tokenem SAML 1,1. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení obchodní aplikace s povoleným tokenem SAML 1,1** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-saml-11-token-enabled-lob-app-single-sign-on"></a>Konfigurace jednotného Sign-On povolené tokeny SAML 1,1

Chcete-li nakonfigurovat jednotné přihlašování na straně obchodní **aplikace s povoleným tokenem saml 1,1** , je třeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do týmu podpory aplikace LOB s podporou tokenů SAML 1,1. Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k aplikaci LOB s povoleným tokenem SAML 1,1.

1. V Azure Portal vyberte možnost **podnikové aplikace** , vyberte možnost **všechny aplikace** a pak vyberte obchodní **aplikaci s podporou tokenu SAML 1,1** .

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte obchodní **aplikaci s podporou tokenu SAML 1,1** .

    ![Odkaz na aplikaci LOB s povoleným tokenem SAML 1,1 v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny** .

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>Vytvořit testovacího uživatele obchodní aplikace s povoleným tokenem SAML 1,1

V této části vytvoříte uživatele s názvem Britta Simon v obchodní aplikaci s povoleným tokenem SAML 1,1. Práce s povoleným tokenem SAML 1,1 pro obchodní aplikace (LOB) pro přidání uživatelů v platformě aplikace LOB s povoleným tokenem SAML 1,1 Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici aplikace LOB s povoleným tokenem SAML 1,1 na přístupovém panelu, měli byste se automaticky přihlásit k obchodní aplikaci s povoleným tokenem SAML 1,1, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)