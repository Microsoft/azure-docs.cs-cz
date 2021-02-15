---
title: 'Kurz: Azure Active Directory integrace s povolenou aplikací LOB s tokenem SAML 1,1 | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a obchodní aplikací s povoleným tokenem SAML 1,1.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 7b6273c504edfd96b61fe3fc9ea35baa6f1e08f3
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093212"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Kurz: Azure Active Directory integrace s povolenou aplikací obchodní aplikace s tokenem SAML 1,1

V tomto kurzu se dozvíte, jak integrovat obchodní aplikaci s povoleným tokenem SAML 1,1 s Azure Active Directory (Azure AD). Když integrujete obchodní aplikaci s podporou tokenu SAML 1,1 s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k aplikaci LOB s povoleným tokenem SAML 1,1
* Umožněte uživatelům, aby se automaticky přihlásili k obchodní aplikaci s povoleným tokenem SAML 1,1 s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Povoleno jednotné přihlašování (SSO) token SAML 1,1 s povoleným jednotným přihlašováním k aplikacím LOB.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Obchodní aplikace s povoleným tokenem SAML **1,1 podporuje jednotné** přihlašování na více instancích

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-saml-11-token-enabled-lob-app-from-the-gallery"></a>Přidání obchodní aplikace s povoleným tokenem SAML 1,1 z Galerie

Pokud chcete nakonfigurovat integraci obchodní aplikace s povoleným tokenem SAML 1,1 do služby Azure AD, musíte z Galerie přidat obchodní aplikaci s podporou tokenů SAML 1,1 do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **aplikaci LOB s povoleným tokenem SAML 1,1** .
1. Vyberte obchodní **aplikaci s podporou tokenu SAML 1,1** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-saml-11-token-enabled-lob-app"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro obchodní aplikaci s povoleným tokenem SAML 1,1

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí obchodní aplikace s povoleným tokenem SAML 1,1 pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v aplikaci LOB s povoleným tokenem SAML 1,1.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí obchodní aplikace s podporou tokenu SAML 1,1, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování k aplikaci s povoleným tokenem SAML 1,1](#configure-saml-11-token-enabled-lob-app-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit testovacího uživatele obchodní aplikace s povoleným tokenem saml 1,1](#create-saml-11-token-enabled-lob-app-test-user)** – abyste měli protějšek B. Simon v obchodní aplikaci s podporou tokenu SAML 1,1, která je propojená s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **LOB s povoleným tokenem SAML 1,1** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://your-app-url`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://your-app-url`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na tým podpory klientů aplikace LOB s povoleným tokenem SAML 1,1. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení obchodní aplikace s povoleným tokenem SAML 1,1** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k aplikaci LOB s povoleným tokenem SAML 1,1.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte obchodní **aplikaci s podporou tokenů SAML 1,1**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-saml-11-token-enabled-lob-app-sso"></a>Konfigurace jednotného přihlašování k aplikaci LOB pro tokeny SAML 1,1

Chcete-li nakonfigurovat jednotné přihlašování na straně obchodní **aplikace s povoleným tokenem saml 1,1** , je třeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do týmu podpory aplikace LOB s podporou tokenů SAML 1,1. Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>Vytvořit testovacího uživatele obchodní aplikace s povoleným tokenem SAML 1,1

V této části vytvoříte uživatele s názvem Britta Simon v obchodní aplikaci s povoleným tokenem SAML 1,1. Práce s povoleným tokenem SAML 1,1 pro obchodní aplikace (LOB) pro přidání uživatelů v platformě aplikace LOB s povoleným tokenem SAML 1,1 Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na přihlašovací adresu URL s povoleným tokenem SAML 1,1, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlašování k aplikaci LOB s povoleným tokenem SAML 1,1 a hned spustit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici aplikace LOB s povoleným tokenem SAML 1,1 v seznamu Moje aplikace, přesměruje se na přihlašovací adresu URL s povoleným tokenem SAML 1,1. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po konfiguraci obchodní aplikace s povoleným tokenem SAML 1,1 můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
