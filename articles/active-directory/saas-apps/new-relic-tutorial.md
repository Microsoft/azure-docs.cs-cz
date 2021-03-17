---
title: 'Kurz: Azure Active Directory integraci s novým Relic podle účtu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a novým Relic pomocí účtu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: a2c149bfdf79102779abf7544fed9fb78796a50e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649946"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s novým Relic podle účtu

V tomto kurzu se dozvíte, jak integrovat nové Relic pomocí účtu s Azure Active Directory (Azure AD). Když integrujete nové Relic podle účtu s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k novým Relic podle účtu.
* Umožněte uživatelům, aby se automaticky přihlásili k novým Relic pomocí účtu s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Nové Relic podle předplatného jednotného přihlašování (SSO) s povoleným účtem.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Nový Relic podle účtu podporuje jednotné přihlašování (SSO) spouštěné v **SP**

## <a name="add-new-relic-by-account-from-the-gallery"></a>Přidání nového Relic podle účtu z Galerie

Pokud chcete nakonfigurovat integraci nového Relic pomocí účtu do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat nový Relic podle účtu z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Nový Relic podle účtu** .
1. Vyberte **Nový Relic podle účtu** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-new-relic-by-account"></a>Konfigurace a testování jednotného přihlašování Azure AD pro nové Relic podle účtu

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s novým Relic podle účtu pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v New Relic podle obchodního vztahu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s novým Relic podle účtu, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte nové Relic pomocí jednotného přihlašování k účtu](#configure-new-relic-by-account-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte nové Relic podle účtu test User](#create-new-relic-by-account-test-user)** – abyste měli protějšek B. Simon v novém Relic podle účtu, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce Nová integrace aplikace **Relic podle účtu** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)
   
1. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:

    `https://rpm.newrelic.com:443/accounts/{acc_id}/sso/saml/finalize` – Nezapomeňte nahradit `acc_id` vlastní ID účtu novým Relic podle účtu.

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `rpm.newrelic.com`

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit nové Relic podle účtu** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k novému Relic podle účtu.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Nový Relic podle účtu**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-new-relic-by-account-sso"></a>Konfigurace nového Relic pomocí jednotného přihlašování k účtu

1. V jiném okně webového prohlížeče se přihlaste k **novému Relic podle účtu** společnosti jako správce.

2. V nabídce v horní části klikněte na **Nastavení účtu**.
   
    ![Snímek obrazovky s vybraným nastavením účtu zobrazuje úvodní stránku.](./media/new-relic-tutorial/settings.png "Nastavení účtu")

3. Klikněte na kartu **zabezpečení a ověřování** a pak klikněte na kartu **jednotné přihlašování** .
   
    ![Jednotné přihlašování](./media/new-relic-tutorial/single-sign-on-tab.png "Jednotné přihlašování")

4. Na stránce dialogu SAML proveďte následující kroky:
   
    ![SAML](./media/new-relic-tutorial/save.png "SAML")
   
    a. Klikněte na **zvolit soubor** a nahrajte stažený Azure Active Directory certifikát.

    b. Do textového pole **Adresa URL vzdáleného přihlášení** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.
   
    c. Do textového pole **Adresa URL odpočívadla odhlášení** vložte hodnotu **URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

    d. Klikněte na **Uložit změny**.

### <a name="create-new-relic-by-account-test-user"></a>Vytvořit nové Relic podle účtu testovacího uživatele

1. Přihlaste se k **novému Relic podle účtu** společnosti jako správce.

2. V nabídce v horní části klikněte na **Nastavení účtu**.
   
    ![Snímek obrazovky zobrazuje nastavení účtu vybrané na úvodní stránce.](./media/new-relic-tutorial/account.png "Nastavení účtu")

3. V podokně **účet** na levé straně klikněte na **Souhrn** a pak klikněte na **Přidat uživatele**.
   
    ![Snímek obrazovky se zobrazí v podokně Souhrn, kde můžete vybrat přidat uživatele.](./media/new-relic-tutorial/add.png "Nastavení účtu")

4. V dialogovém okně **aktivní uživatelé** proveďte následující kroky:
   
    ![Aktivní uživatelé](./media/new-relic-tutorial/user.png "Aktivní uživatelé")
   
    a. Do textového pole **e-mail** zadejte e-mailovou adresu platného Azure Active Directory uživatele, kterého chcete zřídit.

    b. Jako **role** vyberte **uživatele**.

    c. Klikněte na **Přidat tohoto uživatele**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli další nové Relic pomocí nástrojů pro vytváření uživatelských účtů nebo rozhraní API, které poskytuje nový Relic podle účtu.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na nové Relic podle adresy URL pro přihlášení k účtu, kde můžete spustit tok přihlášení. 

* Přejít na nový Relic podle adresy URL pro přihlášení k účtu a spustit tok přihlášení přímo z těchto účtů.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici nový účet Relic podle účtu v části Moje aplikace, přesměruje se na nové Relic podle přihlašovací adresy URL účtu. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování nových Relic podle účtu můžete vyhovět řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).