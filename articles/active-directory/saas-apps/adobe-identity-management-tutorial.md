---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování se službou Adobe Identity Management | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a aplikací Adobe Identity Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fdca04c645e1bb956c8e9f294c702b639c8e2f74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98726364"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-identity-management"></a>Kurz: Azure Active Directory integrace jednotného přihlašování se službou Adobe Identity Management

V tomto kurzu se dozvíte, jak integrovat službu Adobe Identity Management s Azure Active Directory (Azure AD). Když integrujete službu Adobe Identity Management s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke službě Adobe Identity Management.
* Umožněte uživatelům, aby se přihlásili k programu Adobe Identity Management pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (SSO) Adobe Identity Management.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Aplikace Adobe Identity Management podporuje jednotné přihlašování v programu **SP**

## <a name="adding-adobe-identity-management-from-the-gallery"></a>Přidání služby Adobe Identity Management z Galerie

Pokud chcete nakonfigurovat integraci Adobe identity managementu do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat z Galerie Adobe Identity Management.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Adobe Identity Management** .
1. Na panelu výsledků vyberte **Adobe Identity Management** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-adobe-identity-management"></a>Konfigurace a testování jednotného přihlašování Azure AD pro službu Adobe Identity Management

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí služby Adobe Identity Management pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci správy technologie Adobe identity.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí nástroje Adobe Identity Management, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte jednotné přihlašování pro aplikaci Adobe Identity Management](#configure-adobe-identity-management-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele v programu Adobe Identity Management](#create-adobe-identity-management-test-user)** , abyste měli protějšek B. Simon ve službě Adobe Identity Management, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **pro správu aplikací Adobe** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://adobe.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://federatedid-na1.services.adobe.com/federated/saml/metadata/alias/<CUSTOM_ID>`

    > [!NOTE]
    > Hodnota identifikátoru není reálné číslo. Aktualizujte hodnotu skutečným identifikátorem. Pokud chcete získat hodnotu, obraťte se na [tým podpory pro klienta podpory společnosti Adobe Identity Management](mailto:identity@adobe.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavení aplikace Adobe Identity Management** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke službě Adobe Identity Management.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Adobe Identity Management**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-adobe-identity-management-sso"></a>Konfigurace jednotného přihlašování v programu Adobe Identity Management

1. Chcete-li automatizovat konfiguraci v rámci služby Adobe Identity Management, je nutné nainstalovat **rozšíření prohlížeče zabezpečeného přihlašování aplikace** kliknutím na tlačítko **nainstalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavit Adobe Identity Management** , který vás přesměruje na aplikaci Adobe Identity Management. Odtud zadejte přihlašovací údaje správce pro přihlášení do služby Adobe Identity Management. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-8.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete aplikaci Adobe Identity Management nastavit ručně, v jiném okně webového prohlížeče se přihlaste ke svému webu společnosti Adobe Identity Management jako správce.

4. Přejděte na kartu **Nastavení** a klikněte na **vytvořit adresář**.

    ![Nastavení správy aplikací Adobe identity](./media/adobe-identity-management-tutorial/settings.png)

5. Zadejte název adresáře v textovém poli a vyberte **FEDEROVANÉ ID**, klikněte na **Další**.

    ![Vytvoření adresáře služby Adobe Identity Management](./media/adobe-identity-management-tutorial/create-directory.png)

6. Vyberte **jiné zprostředkovatele SAML** a klikněte na tlačítko **Další**.
 
    ![Poskytovatelé služeb Adobe Identity Management SAML](./media/adobe-identity-management-tutorial/saml-providers.png)

7. Klikněte na **Vybrat** a nahrajte soubor **XML s metadaty** , který jste stáhli z Azure Portal.

    ![Konfigurace aplikace Adobe Identity Management SAML](./media/adobe-identity-management-tutorial/saml-configuration.png)

8. Klikněte na **Hotovo**.

### <a name="create-adobe-identity-management-test-user"></a>Vytvořit testovacího uživatele v programu Adobe Identity Management

1. Přejděte na kartu **Uživatelé** a klikněte na **Přidat uživatele**.

    ![Adobe Identity Management – přidat uživatele](./media/adobe-identity-management-tutorial/add-user.png)

2. Do textového pole **zadat e-mailovou adresu uživatele zadejte** **e-mailovou adresu**.

    ![Adobe Identity Management – uložení uživatele](./media/adobe-identity-management-tutorial/save-user.png)

3. Klikněte na **Uložit**.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na přihlašovací adresu URL pro správu Adobe identity, kde můžete spustit tok přihlášení.

* Přejít na adresu URL pro přihlášení k Adobe identity managementu přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Správa identit Adobe v části Moje aplikace, přesměruje se na přihlašovací adresu URL společnosti Adobe Identity Management. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete aplikaci Adobe Identity Management, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).