---
title: 'Kurz: Azure Active Directory integrace s LaunchDarkly | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LaunchDarkly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: ddfffb77ca889aea9ff32c7be1ce2e4cb7fc04a7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037582"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Kurz: Azure Active Directory integrace s LaunchDarkly

V tomto kurzu se dozvíte, jak integrovat LaunchDarkly s Azure Active Directory (Azure AD). Když integrujete LaunchDarkly s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k LaunchDarkly.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k LaunchDarkly svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

    > [!NOTE]
    > Integrace LaunchDarkly Azure Active Directory je jednosměrná. Po konfiguraci integrace můžete pomocí Azure AD spravovat uživatele, jednotné přihlašování a účty v LaunchDarkly, ale LaunchDarkly **nemůžete** použít ke správě uživatelů, SSO a účtů v Azure.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* LaunchDarkly odběr povoleného jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* LaunchDarkly podporuje jednotné přihlašování (SSO) iniciované **IDP** .
* LaunchDarkly podporuje zřizování uživatelů **jenom v čase** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-launchdarkly-from-the-gallery"></a>Přidání LaunchDarkly z Galerie

Pokud chcete nakonfigurovat integraci LaunchDarkly do služby Azure AD, musíte přidat LaunchDarkly z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **LaunchDarkly** .
1. Na panelu výsledků vyberte **LaunchDarkly** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-launchdarkly"></a>Konfigurace a testování jednotného přihlašování Azure AD pro LaunchDarkly

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí LaunchDarkly pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v LaunchDarkly.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí LaunchDarkly postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte LAUNCHDARKLY SSO](#configure-launchdarkly-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte LaunchDarkly Test User](#create-launchdarkly-test-user)** -to, abyste měli protějšek B. Simon v LaunchDarkly, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **LaunchDarkly** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL: `app.launchdarkly.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není reálné číslo. Hodnotu aktualizujete skutečnou adresou URL odpovědi, která je vysvětlena dále v tomto kurzu. Pokud hodláte používat aplikaci v režimu **IDP** , musíte ponechat pole pro **přihlášení URL** prázdné, jinak nebudete moct zahájit přihlášení z **IDP**. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavení LaunchDarkly** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k LaunchDarkly.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **LaunchDarkly**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-launchdarkly-sso"></a>Konfigurace jednotného přihlašování LaunchDarkly

1. V jiném okně webového prohlížeče se přihlaste k webu LaunchDarkly společnosti jako správce.

2. V levém navigačním panelu vyberte **Nastavení účtu** .

    ![Snímek obrazovky zobrazuje položku Nastavení účtu vybranou v části výroba.](./media/launchdarkly-tutorial/configure-1.png)

3. Klikněte na kartu **zabezpečení** .

    ![Snímek obrazovky s nastavením účtu se zobrazí na kartě zabezpečení.](./media/launchdarkly-tutorial/configure-2.png)

4. Klikněte na **Povolit jednotné přihlašování** a pak **Upravte konfiguraci SAML**.

    ![Snímek obrazovky se zobrazí na stránce jednotného přihlašování, kde můžete povolit nastavení S S S/O a upravit konfiguraci SAML.](./media/launchdarkly-tutorial/configure-3.png)

5. V části **Upravit konfiguraci SAML** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v oddílu upravit konfiguraci SAML, kde můžete provádět změny popsané tady.](./media/launchdarkly-tutorial/configure-4.png)

    a. Zkopírujte **adresu URL služby příjemce SAML** pro vaši instanci a vložte ji do textového pole Adresa URL odpovědi v části **LaunchDarkly domény a adresy URL** na Azure Portal.

    b. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Otevřete stažený certifikát z Azure Portal do programu Poznámkový blok, zkopírujte obsah a vložte ho do pole **certifikát X. 509** , nebo můžete certifikát nahrát přímo kliknutím na **nahrát** ho.

    d. Klikněte na **Uložit**.

### <a name="create-launchdarkly-test-user"></a>Vytvořit testovacího uživatele LaunchDarkly

V této části se v LaunchDarkly vytvoří uživatel s názvem B. Simon. LaunchDarkly podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V tomto oddílu není žádná položka akce. Pokud uživatel ještě v LaunchDarkly neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k LaunchDarkly, pro které jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici LaunchDarkly v okně moje aplikace, měli byste se automaticky přihlásit k LaunchDarkly, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování LaunchDarkly můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
