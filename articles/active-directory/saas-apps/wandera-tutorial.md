---
title: 'Kurz: Azure Active Directory integrace s Wandera PAPRSK admin | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Wanderaem správce PAPRSKů.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: d13619b818e18c64d9882f9e3181824173403859
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519261"
---
# <a name="tutorial-integrate-wandera-radar-admin-with-azure-active-directory"></a>Kurz: integrace správce Wandera RADARu pomocí Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat správce Wandera RADAR pomocí Azure Active Directory (Azure AD). Když integrujete Wandera PAPRSKový správce s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke Správci Wandera PAPRSKů.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Wandera PAPRSKového správce s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Wandera pro jednotné přihlašování (SSO) s podporou jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Správce Wandera PAPRSKů podporuje **IDP** iniciované jednotné přihlašování.
* Po konfiguraci správce Wandera pro PAPRSKy můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wandera-radar-admin-from-the-gallery"></a>Přidání správce PAPRSKů Wandera z Galerie

Pokud chcete nakonfigurovat integraci správce Wandera pro RADAR do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Wandera z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Wandera paprskový správce** .
1. Vyberte **WANDERA paprskový správce** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Wandera pro správce PAPRSKů pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Wandera pro správu PAPRSKů.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Wandera pro správce PAPRSKů, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
   * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
   * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte jednotné přihlašování Wandera pro správu](#configure-wandera-radar-admin-sso)** a konfiguraci nastavení jednoho Sign-On na straně aplikace.
   * **[Vytvořit testovacího uživatele Wandera s paprskovým správcem](#create-wandera-radar-admin-test-user)** – musí mít protějšek B. Simon ve Správci Wandera paprsků, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikací pro **správu paprsků Wandera** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:  `https://radar.wandera.com/saml/acs/<tenant id>`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou URL odpovědi. Pokud chcete získat hodnotu, obraťte se na [tým podpory Wandera pro správce s paprsky](https://www.wandera.com/about-wandera/contact/#supportsection) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **podpisový certifikát SAML** a upravte nastavení.

    ![Možnost podepisování](common/signing-option.png)

    1. Vyberte **možnost podepisování** jako **podepsat odpověď SAML a kontrolní výraz**.

    1. Jako **SHA-256**vyberte **podpisový algoritmus** .

1. V části **Nastavení WANDERA paprskového správce** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Wandera pro správce PAPRSKů.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **WANDERA paprskový správce**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-wandera-radar-admin-sso"></a>Nakonfigurovat jednotné přihlašování Wandera pro správce PAPRSKů

1. Pokud chcete automatizovat konfiguraci v rámci správce Wandera pro PAPRSKy, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Nastavení WANDERA paprskový správce** vás přesměruje do aplikace Wandera pro správu paprsků. Odtud zadejte přihlašovací údaje správce, abyste se mohli přihlásit k Wandera PAPRSKového správce. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-4.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit Wandera PAPRSKového správce ručně, otevřete nové okno webového prohlížeče a přihlaste se ke svému webu Wandera pro správce PAPRSKů jako správce a proveďte následující kroky:

4. V pravém horním rohu stránky klikněte na **Nastavení**  >  **Správa**  >  **jednotného přihlašování** a potom zaškrtněte možnost **Povolit SAML 2,0** k provedení následujících kroků.

    ![Konfigurace Správce Wandera PAPRSKů](./media/wandera-tutorial/config01.png)

    a. Klikněte **nebo ručně zadejte požadovaná pole**.

    b. Do textového pole **IDP EntityId** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    c. V programu Poznámkový blok otevřete XML metadata federace, zkopírujte jeho obsah a vložte ho do textového pole **IDP Public X. 509 Certificate** .

    d. Klikněte na **Uložit**.

### <a name="create-wandera-radar-admin-test-user"></a>Vytvořit testovacího uživatele pro správce Wandera PAPRSKů

V této části vytvoříte uživatele s názvem B. Simon ve Správci Wandera pro PAPRSKy. Pokud chcete přidat uživatele do platformy pro správu Wandera s paprsky, pracujte s [týmem podpory pro správce](https://www.wandera.com/about-wandera/contact/#supportsection) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Správce Wandera PAPRSKů na přístupovém panelu byste se měli automaticky přihlášeni ke Správci Wandera PAPRSKů, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)