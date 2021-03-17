---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s segmentem | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a segmentem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: fe8acfd1bfd14f339a0109cab215b8a9ab65256f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021550"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-segment"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s segmentem

V tomto kurzu se dozvíte, jak integrovat segment s Azure Active Directory (Azure AD). Když integrujete segment s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k segmentu
* Umožněte, aby se vaši uživatelé automaticky přihlásili k segmentaci pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Umožňuje segmentovat odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Segment podporuje **SP a IDP** iniciované jednotné přihlašování.
* Segment podporuje zřizování uživatelů **jenom v čase** .

* Jakmile nakonfigurujete segment, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-segment-from-the-gallery"></a>Přidání segmentu z Galerie

Pokud chcete nakonfigurovat integraci segmentu do Azure AD, musíte přidat segment z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **segment** .
1. Vyberte **segment** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-segment"></a>Konfigurace a testování jednotného přihlašování Azure AD pro segment

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí segmentu pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v segmentu.

Ke konfiguraci a testování jednotného přihlašování služby Azure AD pomocí segmentu dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování segmentu](#configure-segment-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit segment testu uživatele](#create-segment-test-user)** – Pokud chcete mít protějšek B. Simon v segmentu, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **segmentovat** integraci aplikace najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `urn:auth0:segment-prod:samlp-<CUSTOMER_VALUE>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://segment-prod.auth0.com/login/callback?connection=<CUSTOMER_VALUE>`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://app.segment.com`

    > [!NOTE]
    > Tyto hodnoty jsou zástupné symboly. Musíte použít skutečný identifikátor, adresu URL odpovědi a přihlašovací adresu URL. Postup získání těchto hodnot je popsán dále v tomto kurzu.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit segment** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k segmentu.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **segment**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-segment-sso"></a>Konfigurace jednotného přihlašování segmentů

1. V novém okně webového prohlížeče se přihlaste k webu segment společnosti jako správce.

1. Klikněte na **ikonu nastavení** , přejděte dolů k možnosti **ověřování** a klikněte na **připojení**.

    ![Snímek obrazovky zobrazující vybranou ikonu nastavení a možnost připojení v nabídce ověřování.](./media/segment-tutorial/segment1.PNG)

1. Klikněte na **Přidat nové připojení**.

    ![Snímek obrazovky s vybraným tlačítkem přidat nové připojení, které zobrazuje oddíl připojení](./media/segment-tutorial/segment2.PNG)

1. Vyberte **SAML 2,0** jako připojení, které chcete konfigurovat, a klikněte na tlačítko **Vybrat připojení** .

    ![Snímek obrazovky, který zobrazuje oddíl "Zvolte připojení" s "S A M L 2,0" a vybraným tlačítkem vybrat připojení.](./media/segment-tutorial/segment3.PNG)

1. Na následující stránce proveďte následující kroky:

    ![Snímek obrazovky se zvýrazněnou stránkou "Konfigurovat zprostředkovatele identity" se zvýrazněnými textovými poli "Single Sign-On U R L" a "cílová U R L", kde je vybrané tlačítko Další.](./media/segment-tutorial/segment4.PNG)

    a. Zkopírujte hodnotu **adresy URL jednoho Sign-On** a vložte ji do pole **Adresa URL odpovědi** v DIALOGOVÉM okně **základní konfigurace SAML** v Azure Portal.

    b. Zkopírujte hodnotu * * * * cílové adresy URL * * * * a vložte ji do pole **Adresa URL identifikátoru** v dialogovém okně **základní konfigurace SAML** v Azure Portal.

    c. Klikněte na **Další**.

    ![Konfigurace segmentu](./media/segment-tutorial/segment5.PNG)

1. Do pole **Adresa URL koncového bodu SAML 2,0** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

1. Otevřete stažený **certifikát (Base64)** z Azure Portal do programu Poznámkový blok a vložte obsah do textového pole **veřejné certifikáty** .

1. Klikněte na **Konfigurovat připojení**.

### <a name="create-segment-test-user"></a>Vytvořit segment testovacího uživatele

V této části se v segmentu vytvoří uživatel s názvem B. Simon. Segment podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel v segmentu ještě neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici segment na přístupovém panelu, měli byste se automaticky přihlásit k segmentu, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si segment pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit segment pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)