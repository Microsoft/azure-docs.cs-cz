---
title: 'Kurz: Azure Active Directory integrace s OpsGenie | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a OpsGenie.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 622ead38a62e593e304f49da9a9d3f652a868bde
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814393"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s OpsGenie

V tomto kurzu se dozvíte, jak integrovat OpsGenie s Azure Active Directory (Azure AD). Když integrujete OpsGenie s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k OpsGenie.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k OpsGenie svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* OpsGenie odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* OpsGenie podporuje jednotné přihlašování **IDP** .

## <a name="adding-opsgenie-from-the-gallery"></a>Přidání OpsGenie z Galerie

Pokud chcete nakonfigurovat integraci OpsGenie do služby Azure AD, musíte přidat OpsGenie z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **OpsGenie** .
1. Na panelu výsledků vyberte **OpsGenie** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-opsgenie"></a>Konfigurace a testování jednotného přihlašování Azure AD pro OpsGenie

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí OpsGenie pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v OpsGenie.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí OpsGenie postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte OPSGENIE SSO](#configure-opsgenie-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte OpsGenie Test User](#create-opsgenie-test-user)** -to, abyste měli protějšek B. Simon v OpsGenie, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **OpsGenie** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi, která je vysvětlena dále v tomto kurzu.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

1. V části **Nastavení OpsGenie** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k OpsGenie.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **OpsGenie**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-opsgenie-sso"></a>Konfigurace jednotného přihlašování OpsGenie

1. Pokud chcete automatizovat konfiguraci v rámci OpsGenie, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavit OpsGenie** , které vás přesměruje do aplikace OpsGenie. Odtud zadejte přihlašovací údaje správce, které se přihlásí k OpsGenie. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit OpsGenie ručně, v jiném okně webového prohlížeče se přihlaste k webu OpsGenie společnosti jako správce.

2. Klikněte na **Nastavení** a potom na kartu **jednotné přihlašování** .
   
    ![OpsGenie jednu Sign-On](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Pokud chcete povolit jednotné přihlašování, vyberte **povoleno**.
   
    ![Snímek obrazovky s vybraným zaškrtnutým políčkem "povoleno".](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. V části **poskytovatel** klikněte na kartu **Azure Active Directory** .
   
    ![Snímek obrazovky, který zobrazuje oddíl "Provider" s vybranou kartou "Azure Active Directory".](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Na stránce Azure Active Directory dialogovém okně proveďte následující kroky:
   
    ![Snímek obrazovky s oddílem jednotného přihlašování s přepínačem "Povolit jednotné přihlašování", "S A M L 2,0 koncový bod" a "metadata U R L".](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Zkopírujte hodnotu **identifikátoru URI ID aplikace** a vložte ji do textového pole **identifikátor (ID entity)** v části **základní konfigurace SAML** v Azure Portal.

    a. Zkopírujte hodnotu **adresy URL odpovědi** a vložte ji do textového pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

    a. Do textového pole **koncového bodu SAML 2,0** vložte hodnotu **URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.
    
    b. Do pole **Adresa URL metadat:** vložte hodnotu **adresy URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.
    
    c. Pokud chcete povolit jednotné přihlašování, zapněte přepínač **Povolit jednotné přihlašování** .

    d. Klikněte na **použít nastavení jednotného přihlašování**.

### <a name="create-opsgenie-test-user"></a>Vytvořit testovacího uživatele OpsGenie

Cílem této části je vytvořit uživatele s názvem B. Simon v OpsGenie. 

1. V okně webového prohlížeče se přihlaste k tenantovi OpsGenie jako správce.

2. Kliknutím na **Uživatelé** na levém panelu přejděte na seznam uživatelů.
   
    ![Nastavení OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Klikněte na **Add User** (Přidat uživatele).

4. V dialogovém okně **Přidat uživatele** proveďte následující kroky:
   
    ![Snímek obrazovky, který zobrazuje dialogové okno Přidat uživatele s zvýrazněnými textovými poli E-mail a celé jméno a vybraným tlačítkem Uložit.](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. Do textového pole **e-mail** zadejte e-mailovou adresu B. Simon adresovanou v Azure Active Directory.
   
    b. Do textového pole **celé jméno** zadejte **B. Simon**.
   
    c. Klikněte na **Uložit**. 

> [!NOTE]
> B. Simon získá e-mail s pokyny pro nastavení profilu.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k OpsGenie, pro které jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici OpsGenie v okně moje aplikace, měli byste se automaticky přihlásit k OpsGenie, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

* Po nakonfigurování OpsGenie můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).