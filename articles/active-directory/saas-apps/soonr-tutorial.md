---
title: 'Kurz: Azure Active Directory integrace s Brzyu na pracovišti | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a na pracovišti.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: jeedes
ms.openlocfilehash: 64c4eabf665fe64290f484adc1b4dfe7483170fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514754"
---
# <a name="tutorial-azure-active-directory-integration-with-soonr-workplace"></a>Kurz: Integrace Azure Active Directory s využitím na pracovišti

V tomto kurzu se naučíte integrovat pracovní plochu služby Azure Active Directory (Azure AD).
Integrování pracovního místa na pracovišti pomocí Azure AD vám poskytne následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k brzkému pracovišti.
* Uživatelům můžete povolit, aby se k účtu služby Azure AD automaticky přihlásili k samoobslužnému pracovnímu místu (jednotné přihlašování).
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s využitím pracoviště na pracovišti potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným jednotným přihlašováním na pracovišti

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Na pracovišti podporujeme **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování

## <a name="adding-soonr-workplace-from-the-gallery"></a>Přidání pracovního místa na pracovišti z Galerie

Pokud chcete nakonfigurovat integraci služby brzo na pracovišti do Azure AD, musíte přidat pracovní plochu z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat pracovní plochu z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **pracovní plochu na pracovišti**, z panelu výsledek vyberte **pracovní plochu** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Pracovní plocha v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí pracovního místa na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je potřeba zřídit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem na pracovišti.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s využitím pracoviště, musíte provést následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování pro pracovní plochu](#configure-soonr-workplace-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte uživatele zkušebního pracoviště na pracovišti](#create-soonr-workplace-test-user)** , který bude mít protějšek Britta Simon na pracovišti, které je propojené s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování pomocí služby Azure AD, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace na **pracovišti** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat identifikátor, odpovědět U R L a vybrat Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<servername>.soonr.com/singlesignon/saml/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<servername>.soonr.com/singlesignon/saml/SSO`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<servername>.soonr.com/singlesignon/saml/SSO`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klientů na pracovišti](https://awp.autotask.net/help/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **nastavit pracovní plochu** si zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-soonr-workplace-single-sign-on"></a>Konfigurace Sign-On na pracovišti

Pokud chcete nakonfigurovat jednotné přihlašování na straně **pracoviště na pracovišti** , je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal, aby [tým podpory pro pracovní plochu](https://awp.autotask.net/help/). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

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
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k pracovnímu místu, které brzy využijete.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **pracoviště**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **pracoviště na pracovišti**.

    ![Odkaz na pracovní plochu v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-soonr-workplace-test-user"></a>Vytvořit uživatele zkušebního pracoviště na pracovišti

V této části vytvoříte uživatele s názvem Britta Simon na pracovišti. Pomocí [týmu podpory](https://awp.autotask.net/help/) pro pracovní plochu můžete přidat uživatele na platformě na pracovišti. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici pracovní plocha na přístupovém panelu, měli byste se automaticky přihlásit k pracovnímu místu, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)