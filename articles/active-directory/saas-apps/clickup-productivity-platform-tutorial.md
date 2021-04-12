---
title: 'Kurz: Azure Active Directory integrace s platformou produktivity ClickUp | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directoryou a ClickUpou produkční platformou.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: jeedes
ms.openlocfilehash: 29bc02466825aa2ea2c1a9ece2826b1262293392
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285144"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Kurz: Azure Active Directory integrace s platformou produktivity ClickUp

V tomto kurzu se dozvíte, jak integrovat platformu ClickUp produktivity s využitím služby Azure Active Directory (Azure AD). Když integrujete platformu ClickUp produktivity s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k produkční platformě ClickUp
* Umožněte, aby se vaši uživatelé automaticky přihlásili k ClickUp kancelářské platformě pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné ClickUp produktivity (SSO) s podporou jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Platforma ClickUp produktivity podporuje jednotné přihlašování (SSO) spouštěné v **SP**

## <a name="add-clickup-productivity-platform-from-the-gallery"></a>Přidání platformy produktivity ClickUp z Galerie

Pokud chcete nakonfigurovat integraci ClickUp produktivity platformy do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat ClickUpou produktivitu platformy z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **ClickUp produktivní Platform** .
1. Na panelu výsledků vyberte **ClickUp produktivní platforma** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-clickup-productivity-platform"></a>Konfigurace a testování jednotného přihlašování Azure AD pro platformu ClickUp produktivity

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s platformou produktivity ClickUp s využitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem na platformě ClickUp produktivity.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s platformou ClickUp produktivity, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat jednotné přihlašování pro ClickUp produktivitu platformy](#configure-clickup-productivity-platform-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte testovacího uživatele platformy pro ClickUp produktivitu](#create-clickup-productivity-platform-test-user)** – abyste měli protějšek B. Simon ve ClickUp platformě, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací pro **produktivitu ClickUp** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://app.clickup.com/login/sso`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > Hodnota identifikátoru není reálné číslo. Aktualizujte tuto hodnotu skutečným identifikátorem, který je vysvětlen dále v tomto kurzu.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k platformě ClickUp produktivity.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **ClickUp produktivní platforma**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-clickup-productivity-platform-sso"></a>Nakonfigurovat jednotné přihlašování pro ClickUp produktivitu platformy

1. V jiném okně webového prohlížeče se přihlaste do svého tenanta ClickUp produktivity vaší platformy jako správce.

2. Klikněte na **Profil uživatele** a pak vyberte **Nastavení**.

    ![Snímek obrazovky s vybraným ikonou nastavení zobrazuje tenant ClickUp produktivity.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Snímek obrazovky se zobrazí nastavení.](./media/clickup-productivity-platform-tutorial/configure-1.png)

3. V části zprostředkovatel jednotného Sign-On (SSO) vyberte **Microsoft**.

    ![Snímek obrazovky zobrazuje podokno ověřování s vybraným Microsoftem.](./media/clickup-productivity-platform-tutorial/configure-2.png)

4. Na stránce **Konfigurovat jednotné přihlašování Microsoftu** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí na stránce Konfigurovat jednotné přihlašování Microsoftu, kde můžete zkopírovat entitu I D a uložit federační metadata Azure U R L.](./media/clickup-productivity-platform-tutorial/configure-3.png)

    a. Kliknutím na **Kopírovat** ZKOPÍRUJTE hodnotu ID entity a vložte ji do textového pole **identifikátor (ID entity)** v části **základní konfigurace SAML** v Azure Portal.

    b. V poli **Adresa URL federačních metadat Azure** vložte hodnotu adresy URL federačních metadat aplikace, kterou jste zkopírovali z Azure Portal, a pak klikněte na **Uložit**.

5. Instalaci dokončíte kliknutím na **ověřit u Microsoftu a dokončíte instalaci** a ověříte ho pomocí účtu Microsoft.

    ![Snímek obrazovky s tlačítkem ověřit s Microsoftem dokončit instalaci](./media/clickup-productivity-platform-tutorial/configure-4.png)


### <a name="create-clickup-productivity-platform-test-user"></a>Vytvořit testovacího uživatele platformy pro ClickUp produktivitu

1. V jiném okně webového prohlížeče se přihlaste do svého tenanta ClickUp produktivity vaší platformy jako správce.

2. Klikněte na **Profil uživatele** a pak vyberte **lidé**.

    ![Snímek obrazovky se zobrazí jako tenant ClickUp produktivity.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Snímek obrazovky se zobrazuje vybraný odkaz lidé.](./media/clickup-productivity-platform-tutorial/user-1.png)

3. Do textového pole zadejte e-mailovou adresu uživatele a klikněte na **pozvat**.

    ![Snímek obrazovky se zobrazí v nastavení týmových uživatelů, kde můžete pozvat lidi e-mailem.](./media/clickup-productivity-platform-tutorial/user-2.png)

    > [!NOTE]
    > Uživateli se zobrazí oznámení a musí přijmout pozvánku k aktivaci účtu.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlašování ClickUp produktivity, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlašování k ClickUp produktivní platformy přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici ClickUp produktivitu v nabídce Moje aplikace, přesměruje se na adresu URL pro přihlašování k ClickUp produktivním platformám. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování ClickUp produktivity můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).