---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s IBM Digital Business Automation v cloudu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a IBM Digital Business Automation na cloudu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: jeedes
ms.openlocfilehash: 91a50ed85b5802657a27c5a545de0013937e9da0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460559"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ibm-digital-business-automation-on-cloud"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s IBM Digital Business Automation v cloudu

V tomto kurzu se dozvíte, jak integrovat službu IBM Digital Business Automation do cloudu pomocí Azure Active Directory (Azure AD). Když integrujete službu IBM Digital Business Automation v cloudu s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k automatizaci digitální firmy IBM v cloudu.
* Umožněte, aby se vaši uživatelé automaticky přihlásili do společnosti IBM Digital Business Automation v cloudu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Společnost IBM Digital Business Automation pro Cloud s podporou jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Automatizace digitálního podnikání IBM v cloudu podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování
* Jakmile nakonfigurujete automatizaci digitální firmy IBM v cloudu, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-ibm-digital-business-automation-on-cloud-from-the-gallery"></a>Přidání automatizace digitální firmy IBM v cloudu z Galerie

Pokud chcete nakonfigurovat integraci společnosti IBM Digital Business Automation do cloudu do služby Azure AD, musíte do svého seznamu spravovaných aplikací SaaS přidat z Galerie digitální automatizaci IBM Digital Business Automation.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **IBM Digital Business Automation do cloudu** .
1. V panelu výsledků vyberte **IBM Digital Business Automation on Cloud** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ibm-digital-business-automation-on-cloud"></a>Konfigurace a testování jednotného přihlašování Azure AD pro automatizaci digitální firmy IBM v cloudu

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí programu IBM Digital Business Automation v cloudu pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v automatizaci digitální firmy IBM v cloudu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí programu IBM Digital Business Automation v cloudu, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace služby IBM Digital Business Automation na úrovni jednotného přihlašování pro Cloud](#configure-ibm-digital-business-automation-on-cloud-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte si Azure Digital Business Automation na Cloud Test User](#create-ibm-digital-business-automation-on-cloud-test-user)** – abyste měli protějšek B. Simon v IBM Digital Business Automation na cloudu, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **IBM Digital Business Automation na cloudové** integraci aplikací najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud máte **soubor metadat poskytovatele služeb**v **základní části Konfigurace SAML** , proveďte následující kroky:
    
    a. Klikněte na **nahrát soubor metadat**.

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    c. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** **identifikátoru** a odpovědi vyplní automaticky v nástroji IBM Digital Business Automation v oblasti textového pole cloudu:

    > [!Note]
    > Pokud se hodnoty **adresy URL** pro **identifikátor** a odpověď nezískají automaticky, vyplníte hodnoty ručně podle vašich požadavků.

    > [!Note]
    > Zákazníci mohou získat soubor metadat pro své cloudové předplatné od [společnosti IBM Digital Business Automation na tým podpory cloudových klientů](mailto:supportbpmoncloud@us.ibm.com).

1. Pokud nemáte **soubor metadat poskytovatele služeb**, v **základní části Konfigurace SAML** , pokud chcete nakonfigurovat aplikaci v režimu, který byl spuštěn **IDP** , zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/saml20`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/saml20/login`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se [na tým podpory IBM Digital Business Automation na Cloud Support Client](mailto:supportbpmoncloud@us.ibm.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavení služby IBM Digital Business Automation na Cloud** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k automatizaci digitální firmy IBM v cloudu.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **IBM Digital Business Automation v cloudu**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-ibm-digital-business-automation-on-cloud-sso"></a>Konfigurace služby IBM Digital Business Automation na cloud SSO

Ke konfiguraci jednotného přihlašování na straně **cloudu IBM Digital Business Automation** je potřeba odeslat stažené **federační metadata XML** a příslušné zkopírované adresy URL z Azure Portal do [IBM Digital Business Automation na tým podpory cloudu](mailto:supportbpmoncloud@us.ibm.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-ibm-digital-business-automation-on-cloud-test-user"></a>Vytvoření služby IBM Digital Business Automation pro Cloud Test User

V této části vytvoříte uživatele s názvem Britta Simon v automatizaci digitální firmy IBM v cloudu. Spolupracujte s [IBM Digital Business Automation na tým podpory pro Cloud](mailto:supportbpmoncloud@us.ibm.com) a přidejte uživatele v automatizaci digitální firmy IBM na cloudovou platformu. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici IBM Digital Business Automation on Cloud, měli byste být automaticky přihlášeni k automatizaci digitální firmy IBM v cloudu, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si automatizaci digitální firmy IBM v cloudu pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit automatizaci digitální firmy IBM v cloudu s pokročilými viditelnostmi a ovládacími prvky](/cloud-app-security/proxy-intro-aad)