---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Roadmunk'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Roadmunk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 26424106098a8385faff5ab6d3de33d98576ef4e
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381299"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Roadmunk

V tomto kurzu se dozvíte, jak integrovat Roadmunk s Azure Active Directory (Azure AD). Když integrujete Roadmunk s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Roadmunk.
* Umožněte uživatelům, aby se do Roadmunk automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte své účty na jednom centrálním místě, Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Roadmunk, které povoluje jednotné přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

Roadmunk podporuje jednotné přihlašování spouštěné *poskytovatelem služeb* (SP) a *zprostředkovatelem identity* (IDP).

## <a name="add-roadmunk-from-the-gallery"></a>Přidání Roadmunk z Galerie

Pokud chcete integrovat Roadmunk do služby Azure AD, přidejte z Galerie Roadmunk do seznamu spravovaných aplikací pro SaaS:

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém podokně vyberte **Azure Active Directory**.
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Roadmunk**.
1. Z výsledků vyberte **Roadmunk** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Roadmunk

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Roadmunk pomocí testovacího uživatele s názvem *B. Simon*. Aby bylo možné provést jednotné přihlašování, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Roadmunk.

Tady je přehled toho, jak nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Roadmunk:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) tak, aby uživatelé mohli tuto funkci používat.
    1. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. [Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) povolení B. Simon pro použití jednotného přihlašování služby Azure AD.
1. [NAKONFIGURUJTE jednotné](#configure-roadmunk-sso) přihlašování Roadmunk pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. [Vytvořte testovacího uživatele Roadmunk](#create-roadmunk-test-user) , abyste mohli propojit protějšek B. Simon v Roadmunk s uživatelem v zastoupení Azure AD.
1. [Otestujte jednotné přihlašování](#test-sso) a ujistěte se, že konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal:

1. V Azure Portal na stránce integrace aplikací **Roadmunk** najděte část **Správa** a pak vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu pera pro **základní konfiguraci SAML** a upravte nastavení.

   ![Snímek obrazovky znázorňující ikonu pro úpravu základní konfigurace SAML](common/edit-urls.png)

1. V části **základní konfigurační oddíl SAML** , pokud máte soubor s metadaty SP a chcete nakonfigurovat v režimu iniciované IDP, postupujte takto:

    a. Vyberte **nahrát soubor metadat**.

    ![Snímek obrazovky zobrazující odkaz na nahrání souboru s metadaty](common/upload-metadata.png)

    b. Vyberte ikonu složky a zvolte soubor metadat, který jste stáhli v kroku 4 postupu "konfigurace Roadmunk jednotného přihlašování". Potom vyberte **Nahrát**.

    ![Snímek obrazovky ukazující, jak zvolit soubor metadat](common/browse-upload-metadata.png)

    Po nahrání souboru metadat se v základní části **Konfigurace SAML** automaticky naplní **identifikátory** a **adresy URL odpovědí** .

    ![Snímek obrazovky znázorňující základní konfigurační oddíl SAML Pole identifikátoru a adresa URL odpovědi jsou zvýrazněny.](common/idp-intiated.png)

    > [!Note]
    > Pokud se hodnoty **adresy URL** **identifikátoru** a odpovědi nevyplní automaticky, vyplníte hodnoty ručně.

1. Chcete-li nakonfigurovat aplikaci v režimu iniciované SP, vyberte možnost **nastavit další adresy URL**. Do pole **Adresa URL pro přihlášení** zadejte `https://login.roadmunk.com`

    ![Snímek obrazovky s informacemi o tom, kde nastavit přihlašovací adresu URL pro režim inicializovaný v SP](common/metadata-upload-additional-signon.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML federačních metadat**. Pak vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Snímek obrazovky zobrazující odkaz ke stažení pro podpisový certifikát SAML](common/metadataxml.png)

1. V části **Nastavení Roadmunk** zkopírujte adresu URL nebo adresy URL, které potřebujete.

    ![Snímek obrazovky s informacemi o kopírování adres URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele v Azure Portal. Pojmenujte uživatele *B. Simon*.

1. V levém podokně Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části okna vyberte **Nový uživatel**.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Zadejte například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Roadmunk.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte **Roadmunk**.
1. Na stránce Přehled aplikace najděte část **Správa** a pak vyberte **Uživatelé a skupiny**.
1. Vyberte možnost **Přidat uživatele**. Pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.
1. V dialogovém okně **Uživatelé a skupiny** v seznamu **Uživatelé** vyberte **B. Simon**. Potom v dolní části dialogového okna zvolte **možnost vybrat**.
1. Pokud očekáváte, že bude role přiřazena uživatelům, zvolte ji z rozevírací nabídky **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, vybere se **výchozí role přístupu** .
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-roadmunk-sso"></a>Konfigurace jednotného přihlašování Roadmunk

1. Přihlaste se k webu Roadmunk jako správce.

1. V dolní části stránky vyberte ikonu uživatele a pak vyberte **Nastavení účtu**.

    ![Snímek obrazovky s informacemi o tom, kde vybrat nastavení uživatelského účtu](./media/roadmunk-tutorial/account.png)

1. Přejít na **Company**  >  **nastavení ověřování** společnosti.

1. Na stránce **nastavení ověřování** proveďte tyto kroky:

    ![Snímek obrazovky zobrazující stránku nastavení ověřování](./media/roadmunk-tutorial/saml-sso.png)

    a. Zapnout **jednotné přihlašování (SSO) SAML**.

    b. V části **Krok 1** buď nahrajte soubor XML metadat, nebo zadejte adresu URL pro metadata.

    c. V části **Krok 2** Stáhněte soubor metadat Roadmunk a pak ho uložte na svém počítači.

    d. Pokud se chcete přihlásit pomocí jednotného přihlašování (SSO), v části **Krok 3** vyberte možnost **vykonat pouze Sign-In SAML**.

    e. Vyberte **Uložit**.


### <a name="create-roadmunk-test-user"></a>Vytvořit testovacího uživatele Roadmunk

1. Přihlaste se k webu Roadmunk jako správce.

1. V dolní části stránky vyberte ikonu uživatele a pak vyberte **Nastavení účtu**.

    ![Snímek obrazovky ukazující, jak otevřít nastavení účtu pro testovacího uživatele](./media/roadmunk-tutorial/account.png)

1. Otevřete kartu **Uživatelé** a pak vyberte **pozvat uživatele**.

    ![Snímek obrazovky zobrazující kartu Uživatelé Je zvýrazněno tlačítko pozvat uživatele. V otevřeném okně se zvýrazní pole E-mail a role.](./media/roadmunk-tutorial/create-user.png)

1. Ve formuláři, který se zobrazí, vyplňte požadované informace a pak vyberte **pozvat**.


## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci služby Azure AD SSO pomocí přístupového panelu.

Když na portálu moje aplikace vyberete dlaždici **Roadmunk** , měli byste se automaticky přihlásit k účtu Roadmunk, pro který jste nastavili jednotné přihlašování. Další informace najdete v tématu [přihlášení a spouštění aplikací na portálu moje aplikace](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Roadmunk můžete vynutili řízení relace. Řízení relace chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. 

Přečtěte si, jak [vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


