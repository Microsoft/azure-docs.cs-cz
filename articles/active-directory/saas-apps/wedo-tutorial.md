---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s WEDO | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a WEDO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2020
ms.author: jeedes
ms.openlocfilehash: 529c4e6433d16f9d70530ba516b5ec1426806984
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519227"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wedo"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s WEDO

V tomto kurzu se dozvíte, jak integrovat WEDO s Azure Active Directory (Azure AD). Když integrujete WEDO s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k WEDO.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k WEDO svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* WEDO odběr s povoleným jednotným přihlašováním (SSO). Obraťte se prosím na [tým podpory klienta WEDO](mailto:info@wedo.swiss) , aby získal předplatné jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* WEDO podporuje jednotné přihlašování (SSO) **a IDP** .

* [Po nakonfigurování WEDO můžete vymáhat ovládací prvky relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace přesahují podmíněný přístup. Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wedo-from-the-gallery"></a>Přidání WEDO z Galerie

Pokud chcete nakonfigurovat integraci WEDO do služby Azure AD, musíte přidat WEDO z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **WEDO** .
1. Na panelu výsledků vyberte **WEDO** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-wedo"></a>Konfigurace a testování jednotného přihlašování Azure AD pro WEDO

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí WEDO pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v WEDO.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí WEDO, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE WEDO SSO](#configure-wedo-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte WEDO Test User](#create-wedo-test-user)** -to, abyste měli protějšek B. Simon v WEDO, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **WEDO** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.wedo.swiss/sp/acs`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.wedo.swiss/sp/acs`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<SUBDOMAIN>.wedo.swiss/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta WEDO](mailto:info@wedo.swiss) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. WEDO aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    | Name | Zdrojový atribut|
    | ------------ | --------- |
    | e-mail | uživatel. e-mail |
    | firstName | User. firstName |
    | lastName | User. lasttName |
    | userName | User. userName |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení WEDO** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k WEDO.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **WEDO**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-wedo-sso"></a>Konfigurace jednotného přihlašování WEDO

Pomocí těchto kroků povolíte jednotné přihlašování Azure AD v WEDO.

1. Přihlaste se k [WEDO](https://login.wedo.swiss/). Musíte mít **roli správce**.
1. V nastavení profilu vyberte v části **nastavení sítě**možnost **ověřování** pomocí nabídky.
1. Na stránce **ověřování SAML** proveďte následující kroky:

   ![Odkaz na ověřování SAML](media/wedo-tutorial/network-security-authentification.png)

   a. Povolte **ověřování SAML**.

   b. Vyberte kartu **metadata zprostředkovatele identity (XML)** .

   c. Otevřete stažený **soubor XML federačních metadat** z Azure Portal do programu Poznámkový blok a ZKOPÍRUJTE obsah XML metadat a vložte ho do textového pole **certifikátu X. 509** .

   d. Klikněte na **Uložit**.

### <a name="create-wedo-test-user"></a>Vytvořit testovacího uživatele WEDO

V této části vytvoříte testovacího uživatele v WEDO s názvem Bob Simon. Informace se musí shodovat *s vytvořením testovacího uživatele Azure AD*.

1. V nastavení profilu v WEDO vyberte **Uživatelé** v části *nastavení sítě* .
1. Klikněte na **Přidat uživatele**.
1. V místní nabídce Přidat uživatele vyplňte informace o uživateli.

    a. Křestní jméno `B` .

    b. Příjmení `Simon` .

    c. Zadejte e-mail `username@companydomain.extension` . Například, `B.Simon@contoso.com`. Je nutné mít e-mail se stejnou doménou jako s krátkým názvem vaší společnosti.

    d. `User`Typ uživatele

    e. Klikněte na **vytvořit uživatele**.

    f. Na stránce *vybrat týmy* klikněte na **Uložit**.

    například  Na stránce *pozvat uživatele* klikněte na **Ano**.

1. Ověřit uživatele pomocí odkazu, který jste obdrželi e-mailem

> [!NOTE]
> Pokud chcete vytvořit falešného uživatele (výše uvedený e-mail ve vaší síti neexistuje), obraťte se na [naši podporu](mailto:info@wedo.swiss) a ověřte uživatele *.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici WEDO, měli byste se automaticky přihlásit k WEDO, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si WEDO s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit WEDO pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)