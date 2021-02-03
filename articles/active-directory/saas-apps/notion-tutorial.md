---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s fiktivním | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a pojem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: 520eb25bcb138c96b24166816d3374255fb7c3b2
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493985"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-notion"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s pojmem

V tomto kurzu se dozvíte, jak integrovat pojem s Azure Active Directory (Azure AD). Když Integrujte pojem s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k pojmu.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k vašemu účtu Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Fiktivní předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Pojem podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování
* Fiktivní podporuje zřizování uživatelů **jenom v čase**
> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže jeden pracovní prostor fiktivní je možné nakonfigurovat v jednom tenantovi.


## <a name="adding-notion-from-the-gallery"></a>Přidání fiktivního z Galerie

Pokud chcete nakonfigurovat integraci fiktivního do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat fiktivní z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **fiktivní** .
1. Vyberte možnost **fiktivní** z panelu výsledků a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-notion"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro fiktivní

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí pojmu testovací uživatel s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v části fiktivní.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí rozhraní fiktivní, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace fiktivního jednotného přihlašování](#configure-notion-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte fiktivního testovacího uživatele](#create-notion-test-user)** – můžete mít protějšek B. Simon v pojmu, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **fiktivní** Application Integration najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL následujícího vzoru, který můžete získat z nastavení svého fiktivního prostoru **&** > **zabezpečení & identitou** > **Adresa URL jednotného přihlašování**:  `https://www.notion.so/sso/saml/<CUSTOM_ID>`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte následující adresu URL:  `https://www.notion.so/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a adresy URL Sign-On. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Fiktivní aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho fiktivní aplikace očekává, že se v odpovědi SAML vrátí další atributy, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Name |  Zdrojový atribut|
    | ----------- | --------- |
    | e-mail | uživatel. pošta |
    | firstName | User. křestní jméno |
    | lastName | User. příjmení |


1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na tlačítko Kopírovat, aby se zkopírovala **Adresa URL federačních metadat aplikace**. V části **fiktivní** pracovní prostor **Nastavení &**  >  **zabezpečení & identity** a vložte hodnotu zkopírovanou do pole **Adresa URL metadat IDP** .

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k fiktivnímu.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **fiktivní**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-notion-sso"></a>Konfigurace fiktivního jednotného přihlašování

Přejít do svého **fiktivního** pracovního prostoru **Nastavení &**  >  **zabezpečení & identity** a vložte hodnotu **URL federačních metadat aplikace** , kterou jste zkopírovali do pole **Adresa URL metadat IDP** .

Na stránce stejné nastavení v části **e-mailové domény** klikněte na **kontaktovat podporu** a přidejte e-mailové domény vaší organizace.

Po schválení a přidání e-mailových domén povolte jednotné přihlašování SAML pomocí přepínače **Povolit SAML** .

Po úspěšném testování můžete vynutili jednotné přihlašování SAML pomocí přepínače **SAML pro vykonání** . Upozorňujeme, že váš pracovní prostor administrastrators si ponechá možnost přihlásit se pomocí e-mailu, ale všichni ostatní členové budou muset pro přihlášení k pojmu použít jednotné přihlašování SAML.

### <a name="create-notion-test-user"></a>Vytvořit fiktivního testovacího uživatele

V této části se v pojmu vytvoří uživatel s názvem Britta Simon. Pojem podporuje zřizování uživatelů za běhu, které je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v prostředí fiktivní neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na fiktivní adresu URL pro přihlášení, kde můžete spustit tok přihlášení.  

* Přejít na pojem adresa URL pro přihlašování přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k fiktivnímu zařízení, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici fiktivní v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k pojmu, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete pojem, můžete vynutilit řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).