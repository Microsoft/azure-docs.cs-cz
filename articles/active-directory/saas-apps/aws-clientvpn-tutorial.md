---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s ClientVPNem AWS | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AWS ClientVPN.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/29/2020
ms.author: jeedes
ms.openlocfilehash: 794934e9c3e45f2aeed9310636a0f3ec30daf7de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735330"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-clientvpn"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s ClientVPNem AWS

V tomto kurzu se dozvíte, jak integrovat AWS ClientVPN s Azure Active Directory (Azure AD). Když integrujete AWS ClientVPN s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k AWS ClientVPN.
* Umožněte uživatelům, aby se automaticky přihlásili k AWS ClientVPN s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné AWS ClientVPN s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* AWS ClientVPN podporuje jednotné přihlašování se spuštěným **SP**

* AWS ClientVPN podporuje zřizování uživatelů **jenom v čase**

## <a name="adding-aws-clientvpn-from-the-gallery"></a>Přidání AWS ClientVPN z Galerie

Pokud chcete nakonfigurovat integraci AWS ClientVPN do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat AWS ClientVPN z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **AWS ClientVPN** .
1. Z panelu výsledků vyberte **AWS ClientVPN** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-aws-clientvpn"></a>Konfigurace a testování jednotného přihlašování Azure AD pro AWS ClientVPN

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí AWS ClientVPN s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v AWS ClientVPN.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí AWS ClientVPN, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE AWS CLIENTVPN SSO](#configure-aws-clientvpn-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit AWS ClientVPN Test User](#create-aws-clientvpn-test-user)** -to znamená, že má protějšek B. Simon v AWS ClientVPN, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **ClientVPN AWS** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<LOCALHOST>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí jednoho z následujících vzorů:

    | Adresa URL odpovědi |
    |------------|
    | `http://<LOCALHOST>` |
    | `https://self-service.clientvpn.amazonaws.com/api/auth/sso/saml` |
    |

    > [!NOTE]
    > Tyto hodnoty nejsou reálné.  Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a adresy URL odpovědi.  Adresa URL pro přihlášení a adresa URL odpovědi můžou mít stejnou hodnotu ( http://127.0.0.1:35001) .  Podrobnosti najdete v [dokumentaci k síti VPN klienta AWS](https://docs.aws.amazon.com/vpn/latest/clientvpn-admin/client-authentication.html#ad) .   Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal. Pro všechny problémy s konfigurací se obraťte na [tým podpory AWS ClientVPN](https://aws.amazon.com/contact-us/) . 

1. Ve službě Azure Active Directory přejděte na **Registrace aplikací** a pak vyberte **všechny aplikace**.

1. Do vyhledávacího pole zadejte **AWS ClientVPN** a na panelu hledání vyberte **AWS ClientVPN** .

1. Klikněte na **manifest** a pokud chcete, aby se adresa URL odpovědi zanechala jako **http** namísto **https** , klikněte na **Uložit**.

    ![Stránka manifestu](./media/aws-clientvpn-tutorial/reply-url.png)

1. Aplikace AWS ClientVPN očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho aplikace AWS ClientVPN očekává, že se v odpovědi SAML vrátí zpátky několik atributů, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Name |  Zdrojový atribut|
    | -------------- | --------- |
    | memberOf | User. Groups |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení CLIENTVPN AWS** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k AWS ClientVPN.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **AWS ClientVPN**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-aws-clientvpn-sso"></a>Konfigurace jednotného přihlašování AWS ClientVPN

Ke konfiguraci jednotného přihlašování na straně **AWS ClientVPN** je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory AWS ClientVPN](https://aws.amazon.com/contact-us/). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-aws-clientvpn-test-user"></a>Vytvořit testovacího uživatele AWS ClientVPN

V této části se v AWS ClientVPN vytvoří uživatel s názvem Britta Simon. AWS ClientVPN podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v AWS ClientVPN neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení AWS ClientVPN, kde můžete spustit tok přihlášení. 

* Přejít přímo na adresu URL pro přihlášení k AWS ClientVPN a zahajte tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici AWS ClientVPN v nabídce Moje aplikace, přesměruje se na adresu URL pro přihlášení AWS ClientVPN. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování AWS ClientVPN můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).