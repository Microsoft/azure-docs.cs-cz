---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s posouzením rizik Coupa | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Coupa hodnocení rizik.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: jeedes
ms.openlocfilehash: bad1ccc99575b788e1bb5ea541c6b6690ead6eca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180978"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coupa-risk-assess"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s posouzením rizik Coupa

V tomto kurzu se dozvíte, jak integrovat posouzení rizik Coupa pomocí Azure Active Directory (Azure AD). Když integrujete Coupa hodnocení rizik s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k posouzení rizik Coupa.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Coupai hodnocení rizik pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Coupa riziko vyhodnocuje předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Posouzení rizik Coupa podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="adding-coupa-risk-assess-from-the-gallery"></a>Přidání posouzení rizik Coupa z Galerie

Pokud chcete nakonfigurovat integraci Coupa rizikového hodnocení do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat posouzení rizik Coupa z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Coupa rizikové hodnocení** .
1. Vyberte **Coupa rizika vyhodnocování** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-coupa-risk-assess"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro posouzení rizik Coupa

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s Coupa rizikovým posouzením pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Coupa hodnocení rizik.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s posouzením rizik Coupa, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace rizika Coupa posouzení jednotného přihlašování](#configure-coupa-risk-assess-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte Coupa rizika vyhodnocení testovacího uživatele](#create-coupa-risk-assess-test-user)** – Pokud chcete mít protějšek B. Simon v Coupa rizikovém hodnocení, které je propojené s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce věnované integraci aplikace **Coupa rizika hodnocení** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:
 
    * Pro **přípravu**: `https://<STAGING ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

    * Pro **prod**: `https://<PROD ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:
 
    * Pro **přípravu**: `https://<STAGING ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

    * Pro **prod**: `https://<PROD ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:

    * Pro **přípravu**: `https://<STAGING ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

    * Pro **prod**: `https://<PROD ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [Coupa rizika vyhodnocení týmu podpory pro klienty](mailto:rasupport@coupa.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení posouzení rizik Coupa** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k vyhodnocení rizik Coupa.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Coupa rizika zhodnotit**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-coupa-risk-assess-sso"></a>Konfigurace rizika Coupa posouzení jednotného přihlašování

Chcete-li nakonfigurovat jednotné přihlašování na straně **posouzení rizik Coupa** , je třeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [Coupa rizika vyhodnocení týmu podpory](mailto:rasupport@coupa.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-coupa-risk-assess-test-user"></a>Vytvořit Coupa rizika vyhodnocení testovacího uživatele

V této části vytvoříte uživatele s názvem Britta Simon ve vyhodnocování rizik Coupa. Pracujte s [Coupa rizikem pro vyhodnocení rizik týmu podpory](mailto:rasupport@coupa.com) pro přidání uživatelů do platformy hodnocení rizik Coupa. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

1. Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na rizika Coupa, které vyhodnocuje podpis na adrese URL, kde můžete spustit tok přihlášení.  

1. Přejít na Coupa riziko vyhodnocování adresy URL pro přihlášení přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k vyhodnocení rizik Coupa, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít panel Microsoft Access. Když kliknete na dlaždici posouzení rizik Coupa na přístupovém panelu, pokud se nakonfiguruje v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k vyhodnocení rizika Coupa, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete posouzení rizik Coupa, můžete vyhovět řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).