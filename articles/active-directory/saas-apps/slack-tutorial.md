---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s časovou rezervou | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a časovou rezervou.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: jeedes
ms.openlocfilehash: 1cdce2108130b64533bc6f14a4e9084a15678d2c
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515878"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s časovou rezervou

V tomto kurzu se dozvíte, jak integrovat časovou rezervu pomocí Azure Active Directory (Azure AD). Když integrujete časovou rezervu v Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k časové rezervě.
* Umožněte uživatelům, aby se automaticky přihlásili k časové rezervě svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (SSO).

> [!NOTE]
> Pokud potřebujete provést integraci s více než jednou instancí časové rezervy v jednom klientovi, identifikátor pro každou aplikaci může být proměnná.

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Časová rezerva podporuje jednotné přihlašování na webu **SP**
* Časová rezerva podporuje zřizování uživatelů **jenom v čase**
* Časová rezerva podporuje [ **automatizované** zřizování uživatelů](./slack-provisioning-tutorial.md)
* Jakmile nakonfigurujete časovou rezervu, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-slack-from-the-gallery"></a>Přidání časové rezervy z Galerie

Pokud chcete nakonfigurovat integraci časové rezervy do Azure AD, musíte přidat časovou rezervu z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Časová rezerva** .
1. Vyberte možnost **Časová rezerva** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-slack"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro časovou rezervu

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s časovou rezervou pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v časové rezervě.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s časovou rezervou, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování časové rezervy](#configure-slack-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořit testovacího uživatele pro časovou rezervu](#create-slack-test-user)** – můžete mít protějšek B. Simon v časové rezervě, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **časové rezervy** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<DOMAIN NAME>.slack.com/sso/saml/start`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `https://slack.com`
    
    c. V poli **Adresa URL odpovědi**zadejte jeden z následujících vzorů adresy URL:
    
    | Adresa URL odpovědi|
    |----------|
    | `https://<DOMAIN NAME>.slack.com/sso/saml` |
    | `https://<DOMAIN NAME>.enterprise.slack.com/sso/saml` |

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Tyto hodnoty je potřeba aktualizovat pomocí skutečné přihlašovací adresy URL a adresy URL odpovědi. Pro získání hodnoty se obraťte na [tým podpory pracovní rezervy klienta](https://slack.com/help/contact) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

    > [!NOTE]
    > Hodnota **identifikátoru (ID entity)** může být proměnná, pokud máte více než jednu instanci časové rezervy, kterou potřebujete integrovat s klientem. Použijte vzorek `https://<DOMAIN NAME>.slack.com` . V tomto scénáři je také nutné spárovat s jiným nastavením v časové rezervě pomocí stejné hodnoty.

1. Aplikace časové rezervy očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit-attribute.png)

1. Kromě výše očekává aplikace s časovou rezervou v odpovědi SAML několik atributů, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků. Také je nutné přidat `email` atribut. Pokud uživatel nemá e-mailovou adresu, namapujte **EmailAddress** na **User. userPrincipalName** a namapujte **e-mail** na **User. userPrincipalName**.

    | Name | Zdrojový atribut |
    | -----|---------|
    | EmailAddress | User. userPrincipalName |
    | e-mail | User. userPrincipalName |

   > [!NOTE]
   > Aby bylo možné nastavit konfiguraci poskytovatele služeb (SP), je nutné na stránce konfigurace SAML kliknout na **Rozbalit** vedle **Možnosti Pokročilá nastavení** . Do pole **Vystavitel poskytovatele služeb** zadejte adresu URL pracovního prostoru. Výchozí hodnota je slack.com. 

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit časovou rezervu** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k časové rezervě.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Slack**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-slack-sso"></a>Konfigurace jednotného přihlašování k časové rezervě

1. V jiném okně webového prohlížeče se přihlaste k webu vaší časové rezervy jako správce.

2. Přejděte na **Microsoft Azure AD** a pak přejděte na **Nastavení týmu**.

     ![Konfigurace jednotného přihlašování při Microsoft Azure AD](./media/slack-tutorial/tutorial-slack-team-settings.png)

3. V části **Nastavení týmu** klikněte na kartu **ověřování** a pak klikněte na **změnit nastavení**.

    ![Konfigurace jednotného přihlašování v nastavení týmu](./media/slack-tutorial/tutorial-slack-authentication.png)

4. V dialogovém okně **nastavení ověřování SAML** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování v nastavení ověřování SAML](./media/slack-tutorial/tutorial-slack-save-authentication.png)

    a.  Do textového pole **koncového bodu SAML 2,0 (http)** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    b.  Do textového pole **vystavitele zprostředkovatele identity** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.

    c.  Otevřete stažený soubor certifikátu v programu Poznámkový blok, zkopírujte jeho obsah do schránky a vložte ho do textového pole **veřejné certifikáty** .

    d. Nakonfigurujte výše uvedená tři nastavení podle potřeby pro váš tým časové rezervy. Další informace o nastavení najdete tady v **příručce pro konfiguraci jednotného přihlašování k Zárezervám** . `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/slack-tutorial/tutorial-slack-expand.png)

    e. Klikněte na **Rozbalit** a zadejte `https://slack.com` do textového pole **vystavitele poskytovatele služby** .

    f.  Klikněte na **Uložit konfiguraci**.
    
    > [!NOTE]
    > Pokud máte více než jednu instanci časové rezervy, kterou potřebujete integrovat s Azure AD, nastavte `https://<DOMAIN NAME>.slack.com` na **vystavitele poskytovatele služeb** , aby se mohl spárovat s nastavením **identifikátoru** aplikace Azure.

### <a name="create-slack-test-user"></a>Vytvořit testovacího uživatele pro časovou rezervu

Cílem této části je vytvořit uživatele s názvem B. Simon v časové rezervě. Časová rezerva podporuje zřizování za běhu, které je ve výchozím nastavení povolené. V této části není žádná položka akce. Nový uživatel se vytvoří během pokusu o přístup k časové rezervě, pokud ještě neexistuje. Časová rezerva podporuje taky Automatické zřizování uživatelů. Další podrobnosti najdete [tady](slack-provisioning-tutorial.md) , jak nakonfigurovat automatické zřizování uživatelů.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, musíte kontaktovat [tým podpory časové rezervy](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect je synchronizační nástroj, který může synchronizovat místní identity služby Active Directory s Azure AD, a pak tito synchronizující uživatelé můžou aplikace používat i stejně jako ostatní cloudové uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici časová rezerva na přístupovém panelu, měli byste se automaticky přihlásit k časové rezervě, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si časovou rezervu pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)