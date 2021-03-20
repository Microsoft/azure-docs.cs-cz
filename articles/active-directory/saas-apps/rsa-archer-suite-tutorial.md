---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s využitím technologie RSA Archer Suite | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a RSA Archer Suite.
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
ms.openlocfilehash: 397d59c60ed90e0e25df671baa3d46660eff1d47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181573"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rsa-archer-suite"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s využitím RSA Archer Suite

V tomto kurzu se dozvíte, jak integrovat sadu RSA Archer Suite pomocí Azure Active Directory (Azure AD). Když integrujete RSA Archer Suite s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k sadě RSA Archer Suite.
* Umožněte uživatelům, aby se automaticky přihlásili k sadě RSA Archer Suite pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné RSA Archer Suite s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Archer Suite podporuje jednotné přihlašování (SSO) iniciované v **SP**
* Archer Suite RSA podporuje zřizování uživatelů **jenom v čase**

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-rsa-archer-suite-from-the-gallery"></a>Přidání Archer sady RSA z Galerie

Pokud chcete nakonfigurovat integraci RSA Archer Suite do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat z Galerie Archer sadu RSA.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **RSA Archer Suite** .
1. Z panelu výsledků vyberte **RSA Archer Suite** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-rsa-archer-suite"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Archer Suite RSA

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí RSA Archer Suite pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v sadě RSA Archer Suite.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Archer sady RSA, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte jednotné přihlašování RSA Archer Suite](#configure-rsa-archer-suite-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele RSA Archer Suite](#create-rsa-archer-suite-test-user)** – abyste měli protějšek B. Simon v Archer sadě, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **RSA Archer Suite** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<BASE_URL>/default.aspx?IDP=<REALM_NAME>`

    b. Do textového pole **identifikátor (ID entity)** zadejte hodnotu: `RSAArcherSuite_TENANT_STRING`

    > [!NOTE]
    > Hodnota adresy URL pro přihlášení není v reálném čase. Aktualizujte hodnotu skutečnou adresou URL pro přihlášení. Pokud chcete získat hodnotu, obraťte se na [tým podpory pro klienta podpory RSA Archer Suite](mailto:archersupport@rsa.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace RSA Archer Suite očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho aplikace RSA Archer Suite očekává, že se v odpovědi SAML vrátí zpátky několik atributů, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Name |  Zdrojový atribut|
    | -------------- | --------- |
    | FirstName | User. křestní jméno |
    | LastName | User. příjmení |
    | PhoneNumber | User. telephoneNumber |
    | City (Město) | User. City |
    | PSČ | User. PostalCode |
    | Stav | User. State |
    | Ulice | User. StreetAddress |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení Archer sady RSA** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k sadě RSA Archer Suite.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **RSA Archer Suite**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci nebyla nastavena žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-rsa-archer-suite-sso"></a>Konfigurace jednotného přihlašování RSA Archer Suite

1. Přihlaste se k webu RSA Archer Suite v jiném prohlížeči jako správce.

1. Na následující stránce proveďte následující kroky.

    ![Konfigurace jednotného přihlašování RSA Archer Suite](./media/rsa-archer-suite-tutorial/configuring-saml-sso.png)

    a. Přejít na kartu **jednotného přihlašování** a v rozevíracím seznamu vyberte možnost **SAML** jako **jeden Sign-On režim** .

    b. Zaškrtněte políčko pro **Povolení ručního obejití** .

    c. Zadejte platný název v textovém poli **ID entity instance** .

    d. Vložte **hodnotu kryptografického otisku** do textového pole **kryptografický otisk certifikátu** .

    e. Klikněte na tlačítko **Vybrat** a nahrajte stažený soubor **XML federačních metadat** z Azure Portal.

    f. **Uložte** nastavení jednoho Sign-On. 

### <a name="create-rsa-archer-suite-test-user"></a>Vytvořit testovacího uživatele RSA Archer Suite

V této části se v sadě RSA Archer Suite vytvoří uživatel nazvaný B. Simon. RSA Archer Suite podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v sadě RSA Archer neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

1. Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k sadě RSA Archer Suite, kde můžete spustit tok přihlášení. 

2. Přejít na adresu URL pro přihlašování k RSA Archer Suite přímo a zahájit tok přihlášení.

3. Můžete použít panel Microsoft Access. Když kliknete na dlaždici RSA Archer Suite na přístupovém panelu, měli byste se automaticky přihlásit do sady RSA Archer Suite, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

 Po nakonfigurování Archer sady RSA můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).