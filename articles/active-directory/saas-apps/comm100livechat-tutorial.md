---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování pomocí programu Comm100 Live chat | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Comm100 Live chat.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: 9636c8cbb517c7aece450f53cfc37e4ddd9803b7
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455489"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-comm100-live-chat"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí programu Comm100 Live chat

V tomto kurzu se dozvíte, jak integrovat Comm100 Live chat s Azure Active Directory (Azure AD). Když integruje Comm100 Live chat s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Comm100 Live chat.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Comm100 živého chatu s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Comm100 Live chat s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Comm100 Live chat podporuje jednotné přihlašování iniciované v **SP**

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Přidání živého chatu Comm100 z Galerie

Pokud chcete nakonfigurovat integraci Comm100 Live chatu do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Comm100 Live Chat z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Comm100 Live chat** .
1. Z panelu výsledků vyberte **Comm100 Live chat** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-comm100-live-chat"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Comm100 Live chat

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Comm100 Live chatu pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Comm100 Live chat.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Comm100 Live chatu, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte jednotné přihlašování Comm100 Live chat](#configure-comm100-live-chat-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Comm100 Live chat Test User](#create-comm100-live-chat-test-user)** – abyste měli protějšek B. Simon v Comm100 živém chatu, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **Comm100 Live chat** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > Hodnota přihlašovací adresy URL není reálné číslo. Hodnotu adresy URL pro přihlášení aktualizujete pomocí skutečné přihlašovací adresy URL, která se vysvětluje později v tomto kurzu.

1. Aplikace Comm100 Live chat očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit-attribute.png)

1. Kromě toho očekává aplikace Comm100 Live chatu v odpovědi SAML méně dalších atributů, které jsou uvedené dál. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Name |  Zdrojový atribut|
    | ---------------| --------------- |
    |   e-mail    | uživatel. pošta |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavení živého chatu Comm100** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Comm100 Live chatu.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Comm100 Live chat**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-comm100-live-chat-sso"></a>Konfigurace jednotného přihlašování Comm100 Live chat

1. V jiném okně webového prohlížeče se přihlaste k Comm100 Live Chat jako správce zabezpečení.

1. V pravém horním rohu stránky klikněte na **můj účet**.

   ![Comm100 Live chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. V levé části nabídky klikněte na **zabezpečení** a pak klikněte na **jednotné přihlašování agenta**.

   ![Snímek obrazovky, který zobrazuje nabídku účtu na levé straně se zvýrazněnou možností zabezpečení a jednotné přihlašování agentů](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. Na stránce **jednotné přihlašování agenta** proveďte následující kroky:

   ![Zabezpečení Comm100 Live chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Zkopírujte první zvýrazněný odkaz a vložte ho do pole **Adresa URL pro přihlášení** v **základní části konfigurace SAML** na Azure Portal.

   b. Do textového pole **URL jednotného přihlašování SAML** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

   c. Do textového pole **Adresa URL vzdáleného odhlášení** vložte hodnotu **URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

   d. Kliknutím na **zvolit soubor** odešlete certifikát s kódováním base-64, který jste stáhli z Azure Portal, do **certifikátu**.

   e. Klikněte na **Save Changes** (Uložit změny).

### <a name="create-comm100-live-chat-test-user"></a>Vytvořit testovacího uživatele Comm100 Live chat

Aby se uživatelé Azure AD mohli přihlašovat k Comm100 Live chatu, musí se zřídit do Comm100 živého chatu. V Comm100 Live chat je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k Comm100 Live Chat jako správce zabezpečení.

2. V pravém horním rohu stránky klikněte na **můj účet**.

    ![Comm100 Live chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. V levé části nabídky klikněte na **agenti** a potom klikněte na **nový agent**.

    ![Agent Comm100 Live chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Na stránce **nový agent** proveďte následující kroky:

    ![Nový agent Comm100 Live chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Do textového pole **e-mail** zadejte e-maily uživatele jako **B. Simon \@ contoso.com**.

    b. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako **B**.

    c. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

    d. Do textového pole **Zobrazovaný název** zadejte zobrazované jméno uživatele jako **B. Simon**

    e. Do textového pole **heslo** zadejte heslo.

    f. Klikněte na **Uložit**.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Comm100 Live chat na přístupovém panelu, měli byste být automaticky přihlášeni k živému chatu Comm100, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Comm100 Live chat s Azure AD](https://aad.portal.azure.com/)