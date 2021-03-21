---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s Illusive sítěmi | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Illusive sítěmi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 54b01cbd7d88484a5edfd0d12b0b6583242759f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98734760"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-illusive-networks"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s Illusive sítěmi

V tomto kurzu se dozvíte, jak integrovat Illusive sítě s Azure Active Directory (Azure AD). Když integrujete sítě Illusive s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Illusive sítím.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Illusive sítím pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Illusive sítě s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Illusive Networks podporuje jednotné přihlašování (SSO) **a IDP** .

## <a name="adding-illusive-networks-from-the-gallery"></a>Přidávání sítí Illusive z Galerie

Pokud chcete nakonfigurovat integraci Illusivech sítí do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Illusive sítě z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Illusive Networks** .
1. Vyberte **Illusive sítě** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-illusive-networks"></a>Konfigurace a testování jednotného přihlašování Azure AD pro sítě Illusive

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Illusive sítí pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Illusive sítích.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s Illusive sítěmi, postupujte podle následujících kroků:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování Illusive Networks](#configure-illusive-networks-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvoření Illusivech sítí testovacího uživatele](#create-illusive-networks-test-user)** – Pokud chcete mít protějšek B. Simon v sítích Illusive propojených s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Illusive Networks** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<ILLUSIVE-MGMT-SERVER>.<DOMAIN>.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<ILLUSIVE-MGMT-SERVER>.<DOMAIN>.com/saml2/splogin/<CUSTOM_ID>`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<ILLUSIVE-MGMT-SERVER>.<DOMAIN>.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pro získání těchto hodnot se obraťte na [tým podpory klientů Illusive Networks](mailto:support@illusivenetworks.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit sítě Illusive** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Illusive sítím.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Illusive sítě**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-illusive-networks-sso"></a>Konfigurace jednotného přihlašování Illusive sítě

Chcete-li nakonfigurovat jednotné přihlašování na straně **sítě Illusive** , je třeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory sítí Illusive](mailto:support@illusivenetworks.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-illusive-networks-test-user"></a>Vytvořit testovacího uživatele pro sítě Illusive

V této části vytvoříte uživatele s názvem Britta Simon v sítích Illusive. Pracujte s [týmem podpory sítí Illusive](mailto:support@illusivenetworks.com) a přidejte uživatele do platformy Illusive Networks. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL Illusive sítě, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlašování k sítím Illusive přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k sítím Illusive, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici sítě Illusive v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k sítím Illusive, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete sítě Illusive, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).