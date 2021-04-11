---
title: 'Kurz: Azure Active Directory integrace s cloudovou konfigurací jednotného přihlašování služby Azure AD v MVISION | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a MVISION cloudovou konfigurací jednotného přihlašování Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: eaf6b2526125b13eec67680c292ed1dbae6fcfee
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284392"
---
# <a name="tutorial-integrate-mvision-cloud-azure-ad-sso-configuration-with-azure-active-directory"></a>Kurz: integrace MVISION cloudového jednotného přihlašování do cloudu Azure AD pomocí Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat konfiguraci MVISION cloudu Azure AD SSO pomocí Azure Active Directory (Azure AD). Když integrujete MVISION cloudovou konfiguraci jednotného přihlašování Azure AD pomocí služby Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k MVISION cloudové konfiguraci jednotného přihlašování služby Azure AD.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k MVISION konfiguraci jednotného přihlašování služby Azure AD pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* MVISION cloudové předplatné Azure AD jednotného přihlašování (SSO) s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* MVISION cloudová konfigurace služby Azure AD jednotného přihlašování podporuje **aktualizace SP a IDP** .

## <a name="add-mvision-cloud-azure-ad-sso-configuration-from-the-gallery"></a>Přidání konfigurace MVISION cloudového jednotného přihlašování do Azure AD z Galerie

Pokud chcete nakonfigurovat integraci MVISION cloudu služby Azure AD SSO do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat MVISION cloudovou konfiguraci služby Azure AD SSO z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **MVISION cloudová konfigurace služby Azure AD SSO** .
1. Na panelu výsledků vyberte **MVISION cloudová konfigurace jednotného přihlašování Azure AD** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-mvision-cloud-azure-ad-sso-configuration"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro MVISION cloudové konfigurace služby Azure AD SSO

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s MVISION cloudovou konfigurací služby Azure AD SSO pomocí testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v konfiguraci služby MVISION cloudu Azure AD SSO.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s MVISION cloudovou konfigurací jednotného přihlašování Azure AD, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
1. **[Nakonfigurujte jednotné přihlašování MVISION cloudové služby Azure AD pro konfiguraci](#configure-mvision-cloud-azure-ad-sso-configuration-sso)** jednotného přihlašování – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
    1. **[Vytvořte MVISION cloudového testu konfigurace jednotného přihlašování Azure AD](#create-mvision-cloud-azure-ad-sso-configuration-test-user)** , abyste měli protějšek Britta Simon v MVISION cloudové konfiguraci služby Azure AD, která je propojená s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **služby Datadog** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory konfigurace jednotného přihlašování služby Azure AD pro MVISION Cloud](mailto:support@skyhighnetworks.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavení cloudového oddílu pro konfiguraci služby Azure AD jednotného přihlašování pro MVISION** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup ke konfiguraci jednotného přihlašování MVISION cloudu Azure AD.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **MVISION cloudová konfigurace služby Azure AD jednotného přihlašování**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-mvision-cloud-azure-ad-sso-configuration-sso"></a>Konfigurace jednotného přihlašování k MVISION cloudového jednotného přihlašování služby Azure AD

Ke konfiguraci jednotného přihlašování na straně **MVISION cloudové konfigurace jednotného přihlašování služby Azure AD** je potřeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [MVISION cloudu podpory konfigurace jednotného přihlašování Azure AD](mailto:support@skyhighnetworks.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-mvision-cloud-azure-ad-sso-configuration-test-user"></a>Vytvořit MVISION cloudového testu konfigurace jednotného přihlašování Azure AD

V této části vytvoříte uživatele s názvem B. Simon v konfiguraci služby jednotného přihlašování v MVISION cloudu Azure AD. Pokud chcete přidat uživatele na MVISION cloudovou platformu pro jednotné přihlašování Azure AD, pracujte s [MVISION cloudem podpory konfigurace jednotného přihlašování služby Azure AD](mailto:support@skyhighnetworks.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL MVISION cloudu služby Azure AD jednotného přihlašování, kde můžete spustit tok přihlášení.  

* Přejít na MVISION cloudová adresa URL pro přihlášení ke konfiguraci jednotného přihlašování Azure AD a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni ke konfiguraci služby Azure AD SSO MVISION cloudu, pro kterou jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici konfigurace MVISION cloudu Azure AD v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit ke konfiguraci služby MVISION cloudu Azure AD SSO, pro kterou jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete cloudovou konfiguraci jednotného přihlašování Azure AD MVISION, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
