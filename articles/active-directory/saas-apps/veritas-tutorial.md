---
title: 'Kurz: integrace s Azure Active Directoryem pomocí jednotného přihlašování společnosti Veritas Enterprise Vault.cloud | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Veritas Enterprise Vault.cloud SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 2796928316197bd48723253dbc97dfcd34ac95e8
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222278"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Kurz: integrace s Azure Active Directoryem pomocí jednotného přihlašování společnosti Veritas Enterprise Vault.cloud

V tomto kurzu se dozvíte, jak integrovat službu Veritas Enterprise Vault.cloud SSO pomocí služby Azure Active Directory (Azure AD). Když integrujete službu Veritas Enterprise Vault.cloud SSO s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k webu Veritas Enterprise Vault.cloud SSO.
* Umožněte uživatelům, aby se automaticky přihlásili k programu Veritas Enterprise Vault.cloud SSO pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

K nakonfigurování integrace služby Azure AD pomocí jednotného přihlašování společnosti Veritas Enterprise Vault.cloud potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Veritas Enterprise Vault.cloud SSO s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Veritas Enterprise Vault.cloud SSO podporuje jednotné přihlašování (SSO) spouštěné v **SP** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Přidání programu Veritas Enterprise Vault.cloud SSO z Galerie

Pokud chcete nakonfigurovat integraci programu Veritas Enterprise Vault.cloud SSO do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat program Veritas Enterprise Vault.cloud SSO z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Veritas Enterprise Vault.cloud SSO** .
1. Z panelu výsledků vyberte **Veritas Enterprise Vault.cloud SSO** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-veritas-enterprise-vaultcloud-sso"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Veritas Enterprise Vault.cloud SSO

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí jednotného přihlašování pomocí programu Veritas Enterprise Vault.cloud SSO pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Veritas Enterprise Vault.cloud SSO.

K nakonfigurování a testování jednotného přihlašování služby Azure AD pomocí jednotného přihlašování společnosti Veritas Enterprise Vault.cloud proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace programu Veritas Enterprise Vault.cloud SSO SSO](#configure-veritas-enterprise-vaultcloud-sso-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte uživatele Veritas enterprise Vault.cloud SSO Test User](#create-veritas-enterprise-vaultcloud-sso-test-user)** – abyste měli protějšek B. Simon v programu Veritas Enterprise Vault.cloud SSO, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce věnované integraci aplikací pro **Veritas Enterprise Vault.cloud SSO** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. Do pole **identifikátor** zadejte jednu z adres URL dle datového centra:

    | Datové centrum| URL |
    |----------|----|
    | Severní Amerika| `https://auth.lax.archivecloud.net` |
    | Evropa | `https://auth.ams.archivecloud.net` |
    | Asie a Tichomoří| `https://auth.syd.archivecloud.net`|

    c. Do textového pole **Adresa URL odpovědi** zadejte jednu z adres URL dle datového centra:

    | Datové centrum| URL |
    |----------|----|
    | Severní Amerika| `https://auth.lax.archivecloud.net` |
    | Evropa | `https://auth.ams.archivecloud.net` |
    | Asie a Tichomoří| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Tato hodnota není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL Sign-On. Tuto hodnotu získáte od [společnosti Veritas Enterprise Vault.cloud SSO Client Support Team](https://www.veritas.com/support/.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavení pro Veritas Enterprise Vault.cloud SSO** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k aplikaci Veritas Enterprise Vault.cloud SSO.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Veritas Enterprise Vault.cloud SSO**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-veritas-enterprise-vaultcloud-sso-sso"></a>Konfigurace jednotného přihlašování pro jednotné přihlašování pro Veritas Enterprise Vault.cloud

Ke konfiguraci jednotného přihlašování na straně **jednotného přihlašování společnosti Veritas enterprise Vault.Cloud** je potřeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory pro Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Vytvoření testovacího uživatele jednotného přihlašování pro Veritas Enterprise Vault.cloud

V této části vytvoříte uživatele s názvem Britta Simon v Veritas Enterprise Vault.cloud SSO. Spolupracujte se svým [týmem podpory pro Veritas enterprise Vault.cloud SSO](https://www.veritas.com/support/.html) a přidejte uživatele na platformě veritas Enterprise Vault.cloud SSO. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k webu Veritas Enterprise Vault.cloud SSO, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlašování k webu Veritas Enterprise Vault.cloud SSO přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici Veritas Enterprise Vault.cloud SSO v části Moje aplikace se přesměruje na přihlašovací adresu URL pro Veritas Enterprise Vault.cloud jednotného přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování jednotného přihlašování pro Veritas Enterprise Vault.cloud můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
