---
title: 'Kurz: Azure Active Directory integrace s helpdeskem Jitbit | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jitbit helpdeskem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: b3fbf73ab51092f614a416355477fc552a404fd3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581796"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Kurz: Azure Active Directory integrace s helpdeskem Jitbit

V tomto kurzu se dozvíte, jak integrovat Jitbit helpdesk s Azure Active Directory (Azure AD). Když integrujete technickou podporu Jitbit s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Jitbit helpdesku.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Jitbit helpdesku pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Jitbit helpdesku s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* HelpDesk pro Jitbit podporuje jednotné přihlašování iniciované v **SP** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-jitbit-helpdesk-from-the-gallery"></a>Přidání helpdesku Jitbit z Galerie

Pokud chcete nakonfigurovat integraci Jitbit helpdesku do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Jitbit Helpdesk z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Jitbit Helpdesk** .
1. Z panelu výsledků vyberte **Jitbit Helpdesk** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-jitbit-helpdesk"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Jitbit Helpdesk

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí helpdesku Jitbit pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Jitbit helpdesku.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí helpdesku Jitbit, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování Jitbit helpdesku](#configure-jitbit-helpdesk-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit testovacího uživatele Jitbit Helpdesk](#create-jitbit-helpdesk-test-user)** – Pokud chcete mít protějšek B. Simon ve Jitbit helpdesku, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro integraci aplikace **Jitbit Helpdesk** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte jednu z adres URL pomocí následujícího vzoru:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Tato hodnota není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL Sign-On. Pokud chcete získat tuto hodnotu, obraťte se na [tým podpory Jitbit Helpdesk klienta](https://www.jitbit.com/support/) .

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `https://www.jitbit.com/web-helpdesk/`

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení helpdesku pro Jitbit** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Jitbit helpdesku.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Jitbit Helpdesk**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-jitbit-helpdesk-sso"></a>Konfigurace jednotného přihlašování Jitbit helpdesku

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti Jitbit helpdesku jako správce.

1. Na panelu nástrojů v horní části klikněte na možnost **Správa**.

    ![Správa](./media/jitbit-helpdesk-tutorial/settings.png "Správa")

1. Klikněte na **Obecné nastavení**.

    ![Snímek obrazovky se zobrazeným odkazem na Obecné nastavení.](./media/jitbit-helpdesk-tutorial/general.png "Uživatelé, společnosti a oprávnění")

1. V části konfigurace **nastavení ověřování** proveďte následující kroky:

    ![Nastavení ověřování](./media/jitbit-helpdesk-tutorial/authentication.png "Nastavení ověřování")

    a. Vyberte **Povolit jednotné přihlašování SAML 2,0**, abyste se mohli přihlásit pomocí jednotného Sign-On (SSO), a to pomocí **OneLogin**.

    b. Do textového pole **Adresa URL koncového bodu** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Otevřete v programu Poznámkový blok certifikát s kódováním **Base-64** , zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikátu X. 509.**

    d. Klikněte na **Uložit změny**.

### <a name="create-jitbit-helpdesk-test-user"></a>Vytvořit testovacího uživatele Jitbit Helpdesk

Aby se uživatelé Azure AD mohli přihlašovat k Jitbit helpdesku, musí se zřídit v Jitbit helpdesku. V případě helpdesku Jitbit je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **helpdesku Jitbit** .

1. V nabídce v horní části klikněte na **Správa**.

    ![Správa](./media/jitbit-helpdesk-tutorial/settings.png "Správa")

1. Klikněte na **Uživatelé, společnosti a oprávnění**.

    ![Uživatelé, společnosti a oprávnění](./media/jitbit-helpdesk-tutorial/users.png "Uživatelé, společnosti a oprávnění")

1. Klikněte na **Přidat uživatele**.

    ![Přidat uživatele](./media/jitbit-helpdesk-tutorial/add.png "Přidat uživatele")

1. V části vytvořit zadejte data účtu Azure AD, který chcete zřídit, následovně:

    ![Vytvořit](./media/jitbit-helpdesk-tutorial/create-section.png "Vytvořit")

   a. Do textového pole **uživatelské jméno** zadejte uživatelské jméno uživatele, jako je **BrittaSimon**.

   b. Do textového pole **e-mailu** zadejte e-maily uživatele jako **BrittaSimon@contoso.com** .

   c. Do textového pole **jméno** zadejte jméno uživatele, jako je **Britta**.

   d. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

   e. Klikněte na **Vytvořit**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli další nástroje pro vytváření uživatelských účtů Jitbit helpdesku nebo rozhraní API poskytovaná Jitbit helpdesk.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení helpdesku Jitbit, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k Jitbit helpdesku přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici helpdesku Jitbit v části Moje aplikace, přesměruje se na přihlašovací adresu URL Jitbit helpdesku. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování helpdesku pro Jitbit můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).