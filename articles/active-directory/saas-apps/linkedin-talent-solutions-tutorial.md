---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s řešeními LinkedIn talentů | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a talentů řešeními LinkedInu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: jeedes
ms.openlocfilehash: 160c7514b095a330a52dd1607dca6f2eb87215d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727326"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-talent-solutions"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s řešeními LinkedIn talentů

V tomto kurzu se dozvíte, jak integrovat řešení LinkedIn talentů pomocí Azure Active Directory (Azure AD). Při integraci řešení LinkedIn talentů pomocí Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k řešením LinkedIn talentů.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k řešením LinkedIn talentů pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Přístup k centru účtů na řídicím panelu řešení LinkedIn talentů

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Řešení LinkedIn talentů podporují **aktualizace SP a IDP,** která iniciovala jednotné přihlašování.
* Řešení LinkedIn talentů podporují zřizování uživatelů **jenom v čase**


## <a name="adding-linkedin-talent-solutions-from-the-gallery"></a>Přidávání řešení LinkedIn talentů z Galerie

Pokud chcete nakonfigurovat integraci řešení LinkedIn talentů do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat řešení LinkedIn talentů z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **řešení LinkedIn talentů** .
1. Z panelu výsledků vyberte **řešení LinkedIn talentů** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-linkedin-talent-solutions"></a>Konfigurace a testování jednotného přihlašování Azure AD pro talentů řešení LinkedInu

Nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí řešení LinkedIn talentů pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte v řešeních LinkedIn talentů vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí řešení LinkedIn talentů, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace řešení LinkedIn talentů SSO](#configure-linkedin-talent-solutions-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte testovacího uživatele řešení LinkedIn talentů](#create-linkedin-talent-solutions-test-user)** , abyste měli protějšek B. Simon v řešeních LinkedIn talentů, která jsou propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro integraci aplikací **řešení LinkedIn talentů** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud máte **soubor metadat poskytovatele služeb** v **základní části Konfigurace SAML** , proveďte následující kroky:

    a. Klikněte na **nahrát soubor metadat**.

    ![image1](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![image2](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** **identifikátoru** a odpovědi získají automaticky v základní části Konfigurace SAML:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Pokud hodnoty **adresy URL** pro **identifikátor** a odpověď nezískávají automaticky polulated, zadejte je ručně podle vašich požadavků.

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://www.linkedin.com/talent/`

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavení řešení LinkedIn talentů** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k řešením LinkedIn talentů.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **řešení LinkedIn talentů**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-linkedin-talent-solutions-sso"></a>Konfigurace řešení LinkedIn talentů SSO

1. Přihlaste se na web řešení LinkedIn talentů jako správce.
1. Přejděte do **centra účtů**. 
1. Na navigačním panelu vyberte kartu **Nastavení** .

    ![Stránka nastavení](./media/linkedin-talent-solutions-tutorial/settings.png)

1. Rozbalte oddíl **Single Sign-On (SSO)** . 

1. Kliknutím na tlačítko **Stáhnout** stáhněte **soubor metadat** nebo kliknutím na tlačítko **nebo kliknutím sem načtěte a zkopírujte jednotlivá pole z** odkazu na formulář, čímž zobrazíte konfigurační data.

    ![ konfigurační data](./media/linkedin-talent-solutions-tutorial/sso-settings.png)

1. Chcete-li zkopírovat jednotlivá pole z formuláře, proveďte následující kroky.

    ![ konfigurace se vstupními daty](./media/linkedin-talent-solutions-tutorial/configuration.png)

    a. Zkopírujte hodnotu **ID entity** , vložte tuto hodnotu do textového pole **identifikátor Azure AD** v základní části **Konfigurace SAML** v Azure Portal.

    b. Zkopírujte hodnotu **adresy URL služby ACS** a vložte tuto hodnotu do textového pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

    c. Zkopírujte obsah textového pole **certifikátu SP X. 509** do poznámkového bloku a uložte ho do svého počítače.

1. Kliknutím na **Odeslat soubor XML** nahrajte soubor **XML federačních metadat** , který jste zkopírovali z Azure Portal.

    ![ Odeslat soubor XML](./media/linkedin-talent-solutions-tutorial/xml-file.png)

### <a name="create-linkedin-talent-solutions-test-user"></a>Vytvořit testovacího uživatele pro řešení LinkedIn talentů

V této části se na řešení LinkedIn talentů vytvoří uživatel s názvem Britta Simon. Řešení LinkedIn talentů podporují zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v řešeních LinkedIn talentů neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro řešení LinkedIn talentů, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro řešení LinkedIn talentů přímo a zahájit tok přihlášení z tohoto účtu.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k řešením LinkedIn talentů, pro která jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici řešení LinkedIn talentů v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k řešením talentů LinkedIn, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete řešení talentů LinkedIn, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).