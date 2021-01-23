---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s AwareGo | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AwareGo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: 4682396f68d6ff1af0b2fb6a5b1a8419d6963529
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735329"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-awarego"></a>Kurz: Azure Active Directory integrace s jednotným přihlašováním pomocí AwareGo

V tomto kurzu se dozvíte, jak integrovat AwareGo s Azure Active Directory (Azure AD). Když integrujete AwareGo s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k AwareGo.
* Umožněte uživatelům, aby se automaticky přihlásili k AwareGo pomocí svých účtů Azure AD.
* Spravujte své účty na jednom centrálním místě, Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným AwareGom jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. AwareGo podporuje jednotné přihlašování iniciované poskytovatelem služeb (SP).


## <a name="adding-awarego-from-the-gallery"></a>Přidání AwareGo z Galerie

Pokud chcete nakonfigurovat integraci AwareGo do služby Azure AD, musíte přidat AwareGo z Galerie do seznamu spravovaných aplikací software jako služba (SaaS).

1. Přihlaste se k Azure Portal pomocí pracovního účtu, školního účtu nebo osobního účet Microsoft.
1. V levém podokně vyberte službu **Azure Active Directory** .
1. Vyberte **podnikové aplikace**  >  **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **AwareGo** .
1. V podokně výsledků vyberte **AwareGo** a pak přidejte aplikaci. Během několika sekund se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-awarego"></a>Konfigurace a testování jednotného přihlašování Azure AD pro AwareGo

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí AwareGo pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v AwareGo.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí AwareGo postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.  

    a. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD s uživatelem B. Simon.  
    b. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** , abyste povolili uživateli B. Simon používat jednotné přihlašování Azure AD.  

1. Nakonfigurujte jednotné přihlašování **[AWAREGO SSO](#configure-awarego-sso)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.

    a. **[Vytvořte AwareGo testovacího uživatele](#create-an-awarego-test-user)** , který bude mít protějšek B. Simon v AwareGo, který je propojený s zastoupením uživatele Azure AD.  
    b. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pokud chcete povolit jednotné přihlašování služby Azure AD v Azure Portal, udělejte toto:

1. V Azure Portal na stránce integrace aplikací **AwareGo** v části **Spravovat** vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Chcete-li upravit nastavení, vyberte v podokně **nastavit jednu Sign-On s** podoknem SAML tlačítko **Upravit** .

   ![Snímek obrazovky s tlačítkem upravit pro základní konfiguraci SAML](common/edit-urls.png)

1. V podokně upravit v části **základní konfigurace SAML** proveďte tyto kroky:

    a. Do pole **Adresa URL pro přihlášení** zadejte jednu z následujících adres URL:

    * `https://lms.awarego.com/auth/signin/` 
    * `https://my.awarego.com/auth/signin/`

    b. V poli **identifikátor (ID entity)** zadejte adresu URL v následujícím formátu: `https://<SUBDOMAIN>.awarego.com`

    c. Do pole **Adresa URL odpovědi** zadejte adresu URL v následujícím formátu: `https://<SUBDOMAIN>.awarego.com/auth/sso/callback`

    > [!NOTE]
    > Předchozí hodnoty nejsou reálné. Aktualizujte je skutečným identifikátorem a adresou URL pro odpovědi. Pokud chcete získat hodnoty, obraťte se na [tým podpory klienta AwareGo](mailto:support@awarego.com). Můžete se také podívat na příklady v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vedle **certifikátu (Base64)** vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Snímek obrazovky s odkazem na stažení certifikátu v podokně podpisový certifikát SAML.](common/certificatebase64.png)

1. V části **Nastavení AwareGo** zkopírujte jednu nebo více adres URL v závislosti na vašich požadavcích.

    ![Snímek obrazovky s podoknem nastavení AwareGo pro kopírování adres URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte **Azure Active Directory** a pak vyberte **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. V podokně vlastnosti **uživatele** proveďte následující akce:

   a. Do pole **název** zadejte **B. Simon**.  
   b. Do pole **uživatelské jméno** zadejte uživatelské jméno v následujícím formátu: `<username>@<companydomain>.<extension>` (například B.Simon@contoso.com ).  
   c. Zaškrtněte políčko **Zobrazit heslo** a potom si poznamenejte hodnotu zobrazenou v poli **heslo** pro pozdější použití.  
   d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte uživateli B. Simon použití jednotného přihlašování Azure tak, že udělíte přístup k AwareGo.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu **aplikace** vyberte **AwareGo**.
1. Na stránce Přehled aplikace v části **Spravovat** vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a potom v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.
1. V podokně **Uživatelé a skupiny** v seznamu **Uživatelé** vyberte **B. Simon** a pak vyberte tlačítko **Vybrat** .
1. Pokud očekáváte, že uživatelům přiřadíte roli, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, vybere se *výchozí role přístupu* .
1. V podokně **Přidat přiřazení** vyberte tlačítko **přiřadit** .

## <a name="configure-awarego-sso"></a>Konfigurace jednotného přihlašování AwareGo

Pokud chcete nakonfigurovat jednotné přihlašování na straně **AwareGo** , pošlete certifikát **(Base64)** , který jste stáhli dříve, a adresy URL, které jste zkopírovali z Azure Portal do [týmu podpory AwareGo](mailto:support@awarego.com). Tým podpory vytvoří toto nastavení, aby na obou stranách správně navázalo připojení k jednotnému přihlašování SAML.

### <a name="create-an-awarego-test-user"></a>Vytvořit testovacího uživatele v AwareGo

V této části vytvoříte uživatele s názvem Britta Simon v AwareGo. Pracujte s [týmem podpory AwareGo](mailto:support@awarego.com) a přidejte uživatele na platformě AwareGo. Než budete moct použít jednotné přihlašování, musíte uživatele vytvořit a aktivovat.

## <a name="test-sso"></a>Test SSO 

V této části můžete otestovat konfiguraci jednotného přihlašování Azure AD pomocí některého z následujících postupů: 

* V Azure Portal vyberte možnost **testovat tuto aplikaci**. Tím vás přesměruje na přihlašovací stránku AwareGo, kde můžete zahájit tok přihlášení. 

* Přejít na přihlašovací stránku AwareGo přímo a zahájit z něj přihlašovací tok.

* Přejít do složky Microsoft moje aplikace Když v mých aplikacích vyberete dlaždici **AwareGo** , budete přesměrováni na přihlašovací stránku AwareGo. Další informace najdete v tématu [přihlášení a spouštění aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování AwareGo můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšíří z Řízení podmíněného přístupu k aplikacím. Další informace najdete v tématu [vymáhání řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).