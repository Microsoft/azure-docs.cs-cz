---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Palo Alto Networks – GlobalProtect | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Palo Alto Networks – GlobalProtect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 72072feebfcf8dba249d2045a399e09714177698
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963668"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Palo Alto Networks – GlobalProtect

V tomto kurzu se dozvíte, jak integrovat Palo Alto Networks-GlobalProtect with Azure Active Directory (Azure AD). Když integrujete Palo Alto Networks – GlobalProtect s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Palo Alto Networks – GlobalProtect.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Palo Alto Networks – GlobalProtect s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Palo Alto Networks – předplatné s povoleným GlobalProtectm jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Palo Alto Networks – GlobalProtect podporuje jednotné přihlašování spouštěné v **SP**
* Palo Alto Networks – GlobalProtect podporuje zřizování uživatelů **jenom v čase**

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Přidávání Palo Alto Networks – GlobalProtect z Galerie

Pokud chcete nakonfigurovat integraci Palo Alto Networks-GlobalProtect do Azure AD, musíte přidat Palo Alto Networks-GlobalProtect z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Palo Alto Networks-GlobalProtect** .
1. Vyberte **Palo Alto Networks-GlobalProtect** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-palo-alto-networks---globalprotect"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Palo Alto Networks – GlobalProtect

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Palo Alto Networks – GlobalProtect pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Palo Alto Networks-GlobalProtect.

Ke konfiguraci a testování jednotného přihlašování služby Azure AD s Palo Alto Networks – GlobalProtect proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Palo Alto Networks – GLOBALPROTECT SSO](#configure-palo-alto-networks---globalprotect-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvoření Palo Alto Networks – GlobalProtect Test User](#create-palo-alto-networks---globalprotect-test-user)** -to znamená, že má protějšek B. Simon v Palo Alto Networks-GlobalProtect propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **Palo Alto Networks – GlobalProtect** Application Integration najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<Customer Firewall URL>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Chcete-li získat tyto hodnoty, kontaktujte [Palo Alto Networks – GlobalProtect tým podpory klientů](https://support.paloaltonetworks.com/support) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavit sítě Palo Alto – GlobalProtect** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Palo Alto Networks – GlobalProtect.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Palo Alto Networks-GlobalProtect**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Konfigurace Palo Alto Networks – GlobalProtect SSO

1. Otevřete Palo Alto Networks-GlobalProtect jako správce v jiném okně prohlížeče.

2. Klikněte na **zařízení**.

    ![Konfigurace jednotného přihlašování Palo Alto – 1](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. V levém navigačním panelu vyberte **poskytovatele identity SAML** a kliknutím na importovat importujte soubor metadat.

    ![Konfigurace jednotného přihlašování Palo Alto 2](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. V okně importu proveďte následující akce.

    ![Konfigurace jednotného přihlašování Palo Alto – 3](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Do textového pole **název profilu** zadejte název, třeba Azure AD GlobalProtect.

    b. V části **metadata zprostředkovatele identity** klikněte na **Procházet** a vyberte soubor metadata.xml, který jste stáhli z Azure Portal

    c. Klikněte na tlačítko **OK**.

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Vytvoření Palo Alto Networks – GlobalProtect Test User

V této části se v Palo Alto Networks-GlobalProtect vytvoří uživatel nazvaný B. Simon. Palo Alto Networks – GlobalProtect podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel v Palo Alto Networks – GlobalProtect ještě neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Palo Alto Networks – adresa URL pro přihlášení GlobalProtect, kde můžete spustit tok přihlášení. 

* Přejít na Palo Alto Networks – adresa URL pro GlobalProtect přihlašování přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Palo Alto Networks-GlobalProtect v části Moje aplikace, měli byste se automaticky přihlásit k Palo Alto Networks-GlobalProtect, pro kterou jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Palo Alto Networks – GlobalProtect můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).