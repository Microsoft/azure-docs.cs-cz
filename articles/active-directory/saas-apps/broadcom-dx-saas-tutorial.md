---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s využitím Broadcom DX SaaS | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Broadcom DX SaaS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2021
ms.author: jeedes
ms.openlocfilehash: 35ab4d335560689a7e55bf1a8dff9fcb0bcc7d7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601512"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-broadcom-dx-saas"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s využitím Broadcom DX SaaS

V tomto kurzu se dozvíte, jak integrovat Broadcom DX SaaS s Azure Active Directory (Azure AD). Když integrujete Broadcom DX SaaS s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Broadcom DX SaaS.
* Umožněte uživatelům, aby se automaticky přihlásili k Broadcom DX SaaS s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Broadcom DX SaaS jednotného přihlašování (SSO) s povoleným přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Broadcom DX SaaS podporuje **IDP** iniciované jednotné přihlašování.

* Broadcom DX SaaS podporuje zřizování uživatelů **jenom v čase** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-broadcom-dx-saas-from-the-gallery"></a>Přidání Broadcom DX SaaS z Galerie

Pokud chcete nakonfigurovat integraci Broadcom DX SaaS do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat z Galerie příkaz Broadcom DX SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole příkaz **Broadcom DX SaaS** .
1. Z panelu výsledků vyberte **Broadcom DX SaaS** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-broadcom-dx-saas"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Broadcom DX SaaS

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Broadcom DX SaaS pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Broadcom DX SaaS.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Broadcom DX SaaS, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte si SaaS SSO pro Broadcom DX](#configure-broadcom-dx-saas-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte SaaS Test User](#create-broadcom-dx-saas-test-user)** , který bude obsahovat protějšek B. Simon v Broadcom DX SaaS, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **Broadcom DX SaaS** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `DXI_<TENANT_NAME>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://axa.dxi-na1.saas.broadcom.com/ess/authn/<TENANT_NAME>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory Broadcom DX SaaS Client support](mailto:dxi-na1@saas.broadcom.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace Broadcom DX SaaS očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace Broadcom DX SaaS několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Name |  Zdrojový atribut|
    | --------------- | --------- |
    | Group (Skupina) | User. Groups |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Broadcom DX SaaS** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Broadcom DX SaaS.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Broadcom DX SaaS**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-broadcom-dx-saas-sso"></a>Konfigurace Broadcom DX SaaS SSO

1. Přihlaste se k webu Broadcom DX SaaS společnosti jako správce.

2. Přejděte do **Nastavení** a klikněte na kartu **Uživatelé** .

    ![Uživatelé](./media/broadcom-dx-saas-tutorial/settings.png "Uživatelé")

3. V části **Správa uživatelů** klikněte na tlačítko se třemi tečkami a vyberte **SAML**.

    ![Správa uživatelů](./media/broadcom-dx-saas-tutorial/local.png "Správa uživatelů")

4. V části **identifikace účtu SAML** proveďte následující kroky.
   
    ![Zohledňují](./media/broadcom-dx-saas-tutorial/broadcom-1.png "Účet") 

    a. Do textového pole **vystavitele** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **Adresa URL pro přihlášení zprostředkovatele identity (IDP)** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL pro odhlášení zprostředkovatele identity (IDP)** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    d. Otevřete stažený **certifikát (Base64)** z Azure Portal do programu Poznámkový blok a vložte obsah do textového pole **certifikát poskytovatele identity** .

    e. Klikněte na tlačítko **Další**.

5. V části **atributy mapy** vyplňte požadované atributy SAML na následující stránce a klikněte na tlačítko **Další**.

    ![Atributy](./media/broadcom-dx-saas-tutorial/broadcom-2.png "Atributy") 


6. V části **identifikovat skupinu uživatelů** zadejte ID objektu dané skupiny a klikněte na **Další**.

    ![Přidání skupiny](./media/broadcom-dx-saas-tutorial/broadcom-3.png "Přidání skupiny") 

7. V části **naplnit účet SAML** ověřte naplněné hodnoty a klikněte na **Uložit**.

    ![Účet SAML](./media/broadcom-dx-saas-tutorial/broadcom-4.png "Účet SAML") 

### <a name="create-broadcom-dx-saas-test-user"></a>Vytvořit testovacího uživatele SaaS Broadcom DX

V této části se v Broadcom DX SaaS vytvoří uživatel s názvem Britta Simon. Broadcom DX SaaS podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Broadcom DX SaaS neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k SaaS Broadcom DX, pro který jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici Broadcom DX SaaS v okně moje aplikace byste měli být automaticky přihlášeni ke službě Broadcom DX SaaS, pro kterou jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Broadcom DX SaaS můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).