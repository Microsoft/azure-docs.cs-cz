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
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 0ebcb746e10ae910c695e52fb053fc5d0b316e70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543903"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s OpsGenie

V tomto kurzu se dozvíte, jak integrovat OpsGenie s Azure Active Directory (Azure AD). Když integrujete OpsGenie s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k OpsGenie.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k OpsGenie svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* OpsGenie odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* OpsGenie podporuje jednotné přihlašování **IDP** .
* Po nakonfigurování OpsGenie můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-opsgenie-from-the-gallery"></a>Přidání OpsGenie z Galerie

Pokud chcete nakonfigurovat integraci OpsGenie do služby Azure AD, musíte přidat OpsGenie z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **OpsGenie** .
1. Na panelu výsledků vyberte **OpsGenie** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-opsgenie"></a>Konfigurace a testování jednotného přihlašování Azure AD pro OpsGenie

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí OpsGenie pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v OpsGenie.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí OpsGenie, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte OPSGENIE SSO](#configure-opsgenie-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte OpsGenie Test User](#create-opsgenie-test-user)** -to, abyste měli protějšek B. Simon v OpsGenie, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **OpsGenie** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
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

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k OpsGenie.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **OpsGenie**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-opsgenie-sso"></a>Konfigurace jednotného přihlašování OpsGenie

1. Otevřete jinou instanci prohlížeče a přihlaste se k OpsGenie jako správce.

2. Klikněte na **Nastavení**a potom na kartu **jednotné přihlašování** .
   
    ![OpsGenie jednu Sign-On](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Pokud chcete povolit jednotné přihlašování, vyberte **povoleno**.
   
    ![Nastavení OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. V části **poskytovatel** klikněte na kartu **Azure Active Directory** .
   
    ![Nastavení OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Na stránce Azure Active Directory dialogovém okně proveďte následující kroky:
   
    ![Nastavení OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
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
   
    ![Nastavení OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. Do textového pole **e-mail** zadejte e-mailovou adresu B. Simon adresovanou v Azure Active Directory.
   
    b. Do textového pole **celé jméno** zadejte **B. Simon**.
   
    c. Klikněte na **Uložit**. 

> [!NOTE]
> B. Simon získá e-mail s pokyny pro nastavení profilu.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici OpsGenie, měli byste se automaticky přihlásit k OpsGenie, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si OpsGenie s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)