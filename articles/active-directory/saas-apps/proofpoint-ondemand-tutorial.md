---
title: 'Kurz: Azure Active Directory integrace s Proofpoint na vyžádání | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Proofpoint na vyžádání.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: jeedes
ms.openlocfilehash: e9beb2c9e265982a9e10f3e6abee6bc3cba4a4a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648454"
---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Kurz: Azure Active Directory integrace s Proofpoint na vyžádání

V tomto kurzu se dozvíte, jak integrovat Proofpoint na vyžádání pomocí Azure Active Directory (Azure AD). Když integruje Proofpoint na vyžádání s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Proofpoint na vyžádání.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Proofpoint na vyžádání pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s podporou jednotného přihlašování (SSO) Proofpoint na vyžádání.

> [!NOTE]
> Pokud se službou Azure AD používáte ověřování MFA nebo bez hesla, pak v žádosti SAML přepněte hodnotu AuthnContext. V opačném případě Azure AD vyvolá chybu na neshodu AuthnContext a nepošle token zpátky do aplikace.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Proofpoint on **Demand podporuje jednotné** přihlašování (SSO) iniciované.

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>Přidání Proofpoint na vyžádání z Galerie

Pokud chcete nakonfigurovat integraci Proofpoint na vyžádání do služby Azure AD, musíte přidat Proofpoint na vyžádání z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Proofpoint na vyžádání** .
1. Na panelu výsledků vyberte **Proofpoint na vyžádání** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-proofpoint-on-demand"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Proofpoint na vyžádání

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Proofpoint na vyžádání pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Proofpoint na vyžádání.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Proofpoint na vyžádání, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování Proofpoint na vyžádání](#configure-proofpoint-on-demand-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Proofpoint testovacího uživatele na vyžádání](#create-proofpoint-on-demand-test-user)** – abyste měli protějšek B. Simon v Proofpoint na vyžádání, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Proofpoint na vyžádání** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Proofpoint na vyžádání a v adresách URL](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<hostname>.pphosted.com/ppssamlsp_hostname`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<hostname>.pphosted.com/ppssamlsp`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<hostname>.pphosted.com:portnumber/v1/samlauth/samlconsumer`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátor a adresu URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klientů na vyžádání Proofpoint](https://www.proofpoint.com/us/support-services) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Proofpoint na vyžádání** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Proofpoint na vyžádání.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Proofpoint na vyžádání**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-proofpoint-on-demand-sso"></a>Konfigurace jednotného přihlašování Proofpoint na vyžádání

Pokud chcete nakonfigurovat jednotné přihlašování na straně **Proofpoint na vyžádání** , je potřeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory Proofpoint na vyžádání](https://www.proofpoint.com/us/support-services). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-proofpoint-on-demand-test-user"></a>Vytvořit testovacího uživatele Proofpoint na vyžádání

V této části vytvoříte na vyžádání uživatele s názvem Britta Simon v Proofpoint. Pracujte s [týmem podpory klientů Proofpoint na vyžádání](https://www.proofpoint.com/us/support-services) a přidejte uživatele na platformě Proofpoint na vyžádání.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení na vyžádání Proofpoint, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k Proofpoint na vyžádání přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici Proofpoint na vyžádání v okně moje aplikace byste měli být automaticky přihlášeni k Proofpoint na vyžádání, pro kterou jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Proofpoint na vyžádání můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).