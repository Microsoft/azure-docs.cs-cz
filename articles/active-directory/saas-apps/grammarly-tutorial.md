---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s gramaticky | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a gramaticky.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2020
ms.author: jeedes
ms.openlocfilehash: 13b62646728eb03a7470d9bb57741bcb90cc24fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92447700"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-grammarly"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s gramaticky

V tomto kurzu se naučíte, jak gramaticky integrovat s Azure Active Directory (Azure AD). Při gramatické integraci s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k gramaticky.
* Umožněte uživatelům, aby se automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Gramatika podporuje **IDP** jednotné přihlašování.
* Gramatika podporuje **jenom při** zřizování uživatelů.

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-grammarly-from-the-gallery"></a>Přidat gramaticky z Galerie

Pokud chcete nakonfigurovat integraci gramaticky do Azure AD, je třeba do seznamu spravovaných aplikací pro SaaS přidat gramaticky z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **gramaticky** .
1. Z panelu výsledků vyberte **gramaticky** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-grammarly"></a>Konfigurace a testování jednotného přihlašování Azure AD pro gramaticky

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s gramaticky pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte v gramatice vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s gramaticky, postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte gramatické přihlašování](#configure-grammarly-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte gramaticky testovacího uživatele](#create-grammarly-test-user)** , abyste měli protějšek B. Simon v gramaticky, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **gramatická** integrace aplikací najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní sekci konfigurace SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .


1. Gramatická aplikace očekává kontrolní výrazy SAML v určitém formátu. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle svých požadavků.
    
    | Name |  Zdrojový atribut|
    | -------------- | --------- |
    | FirstName | User. křestní jméno |
    | LastName | User. příjmení |
    | EmailAddress | User. Principal|

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit gramaticky** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k gramatické úrovni.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **gramaticky**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-grammarly-sso"></a>Konfigurace gramatického jednotného přihlašování

Pokud chcete jednotné přihlašování nakonfigurovat na **gramaticky**, musíte zkopírovat **adresu URL pro přihlášení**, **identifikátor Azure AD** a stažený **certifikát (Base64)** na panel pro správu s gramatickým načtením. [Přečtěte si, jak](https://support.grammarly.com/hc/en-us/articles/360048683092-How-do-I-set-up-SAML-single-sign-on-for-my-Grammarly-Business-account-).

### <a name="create-grammarly-test-user"></a>Vytvořit gramaticky testovacího uživatele

V této části se v gramatice vytvoří uživatel nazvaný B. Simon. Gramatika podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povoleno. V této části není žádná položka akce. Pokud uživatel ještě v gramatice neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

1. Kliknutím na **otestovat tuto aplikaci** v Azure Portal a automaticky se přihlásíte k gramatické přihlášení, pro které jste nastavili jednotné přihlašování. 

2. Můžete použít panel Microsoft Access. Když na přístupovém panelu kliknete na gramaticky dlaždici, měli byste se automaticky přihlásit k gramatické přihlášení, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Jakmile gramaticky nakonfigurujete, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).