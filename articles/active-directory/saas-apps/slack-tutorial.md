---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s časovou rezervou | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a časovou rezervou.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0085db3f38fb8af014434f36893182e1682b05a7
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972110"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s časovou rezervou

V tomto kurzu se dozvíte, jak integrovat časovou rezervu pomocí Azure Active Directory (Azure AD). Když integrujete časovou rezervu v Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k časové rezervě.
* Umožněte uživatelům, aby se automaticky přihlásili k časové rezervě svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Časová rezerva podporuje jednotné přihlašování na webu **SP**
* Časová rezerva podporuje [ **automatizované** zřizování a rušení zřizování uživatelů](slack-provisioning-tutorial.md) (doporučeno)

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-slack-from-the-gallery"></a>Přidání časové rezervy z Galerie

Pokud chcete nakonfigurovat integraci časové rezervy do Azure AD, musíte přidat časovou rezervu z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Časová rezerva** .
1. Vyberte možnost **Časová rezerva** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>Konfigurace a testování jednotného přihlašování Azure AD pro časovou rezervu

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s časovou rezervou pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v časové rezervě.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s časovou rezervou, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování časové rezervy](#configure-slack-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit testovacího uživatele pro časovou rezervu](#create-slack-test-user)** – můžete mít protějšek B. Simon v časové rezervě, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **časové rezervy** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<your Slack company>.slack.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `https://slack.com`

    > [!NOTE]
    > Hodnota adresy URL pro přihlášení není v reálném čase. Aktualizujte hodnotu skutečnou adresou URL pro přihlášení. Pro získání hodnoty se obraťte na [tým podpory pracovní rezervy klienta](https://slack.com/help/contact) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit časovou rezervu** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k časové rezervě.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Časová rezerva**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-slack-sso"></a>Konfigurace jednotného přihlašování k časové rezervě

1. V jiném okně webového prohlížeče se přihlaste k webu vaší časové rezervy jako správce.

2. Na levé straně přejděte na název vaší časové rezervy, v našem případě se nastavila **Microsoft Azure AD** a pak přejděte na **Nastavení týmu** , jak je znázorněno na následujícím snímku obrazovky.

     ![Konfigurace jednotného přihlašování na straně aplikace](./media/slack-tutorial/tutorial_slack_001.png)

3. V části **Nastavení týmu** klikněte na kartu **ověřování** a pak klikněte na **změnit nastavení**.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/slack-tutorial/tutorial_slack_002.png)

4. V dialogovém okně **nastavení ověřování SAML** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/slack-tutorial/tutorial_slack_003.png)

    a.  Do textového pole **koncového bodu SAML 2,0 (http)** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    b.  Do textového pole **vystavitele zprostředkovatele identity** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.

    c.  Otevřete stažený soubor certifikátu v programu Poznámkový blok, zkopírujte jeho obsah do schránky a vložte ho do textového pole **veřejné certifikáty** .

    d. Nakonfigurujte výše uvedená tři nastavení podle potřeby pro váš tým časové rezervy. Další informace o nastavení najdete tady v **příručce pro konfiguraci jednotného přihlašování k Zárezervám** . `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Klikněte na **Uložit konfiguraci**.

### <a name="create-slack-test-user"></a>Vytvořit testovacího uživatele pro časovou rezervu

Cílem této části je vytvořit uživatele s názvem B. Simon v časové rezervě. Časová rezerva podporuje zřizování za běhu, které je ve výchozím nastavení povolené. V této části není žádná položka akce. Nový uživatel se vytvoří během pokusu o přístup k časové rezervě, pokud ještě neexistuje. Časová rezerva podporuje taky Automatické zřizování uživatelů. Další podrobnosti najdete [tady](slack-provisioning-tutorial.md) , jak nakonfigurovat automatické zřizování uživatelů.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, musíte kontaktovat [tým podpory časové rezervy](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect je synchronizační nástroj, který může synchronizovat místní identity služby Active Directory s Azure AD, a pak tito synchronizující uživatelé můžou aplikace používat i stejně jako ostatní cloudové uživatele.

## <a name="test-sso"></a>Test SSO

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici časová rezerva na přístupovém panelu, měli byste se automaticky přihlásit k časové rezervě, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si časovou rezervu pomocí Azure AD](https://aad.portal.azure.com/)
