---
title: 'Kurz: Azure Active Directory integrace s Secret Server (On-Premises) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Secret Server (On-Premises).
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
ms.openlocfilehash: 926bbfba172e90e3d03fb61eda7d6a5fd30fb0b6
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095063"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Kurz: integrace Secret Server (On-Premises) s Azure Active Directory

V tomto kurzu se naučíte, jak integrovat Secret Server (On-Premises) s Azure Active Directory (Azure AD). Když integrujete Secret Server (On-Premises) s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Secret Server (On-Premises).
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Secret Server (On-Premises) se svými účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Secret Server (On-Premises) odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Secret Server (On-Premises) podporuje **IDP** jednotného přihlašování.

## <a name="add-secret-server-on-premises-from-the-gallery"></a>Přidání Secret Server (On-Premises) z Galerie

Pokud chcete nakonfigurovat integraci Secret Server (On-Premises) do služby Azure AD, musíte přidat Secret Server (On-Premises) z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Secret Server (on-premises)** .
1. Na panelu výsledků vyberte **Secret Server (on-premises)** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-secret-server-on-premises"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Secret Server (On-Premises)

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Secret Server (On-Premises) pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Secret Server (On-Premises).

K nakonfigurování a testování jednotného přihlašování služby Azure AD pomocí Secret Server (On-Premises) proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Secret Server (on-PREMISES) SSO](#configure-secret-server-on-premises-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Secret Server (on-premises) testovacího uživatele](#create-secret-server-on-premises-test-user)** – můžete mít protějšek B. Simon v Secret Server (on-premises), která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **Secret Server (on-premises)** integrace aplikací najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL: `https://secretserveronpremises.azure`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > Výše uvedené ID entity je pouze příklad a Vy si můžete vybrat libovolnou jedinečnou hodnotu, která identifikuje vaši tajnou instanci serveru ve službě Azure AD. ID této entity je potřeba odeslat [Secret Server (on-premises) týmu podpory klientů](https://thycotic.force.com/support/s/) a nakonfigurujete je na jejich straně. Další podrobnosti najdete v [tomto článku](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<SecretServerURL>/login.aspx`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a adresy URL Sign-On. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory Secret Server (on-premises) klientů](https://thycotic.force.com/support/s/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu **Upravit** pro otevření dialogového okna **podpisový certifikát SAML** .

    ![Snímek obrazovky s oddílem "S A M L Signing Certificate" se zvolenou akcí "certifikát (Base64" "stažení".)](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. Vyberte **možnost podepisování** jako **podepsat odpověď SAML a kontrolní výraz**.

    ![Možnosti podepisování](./media/secretserver-on-premises-tutorial/signing-option.png)

1. V části **nastavit Secret Server (on-premises)** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Secret Server (On-Premises).

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Secret Server (on-premises)**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-secret-server-on-premises-sso"></a>Konfigurace Secret Server (On-Premises) jednotného přihlašování

Chcete-li nakonfigurovat jednotné přihlašování na straně **Secret Server (on-premises)** , je třeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory Secret Server (on-premises)](https://thycotic.force.com/support/s/). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-secret-server-on-premises-test-user"></a>Vytvořit Secret Server (On-Premises) testovacího uživatele

V této části vytvoříte uživatele s názvem Britta Simon v Secret Server (On-Premises). Pokud chcete přidat uživatele na Secret Server (On-Premises) platformě, pracujte s [Secret Server (on-premises) týmu podpory](https://thycotic.force.com/support/s/) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Secret Server (On-Premises) adresa URL pro přihlášení, kde můžete spustit tok přihlášení.  

* Přejít na Secret Server (On-Premises) adresa URL pro přihlášení přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Secret Server (on-premises), pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Secret Server (On-Premises) v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k Secret Server (On-Premises), pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Secret Server (On-Premises) můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
