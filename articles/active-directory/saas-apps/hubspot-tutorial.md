---
title: 'Kurz: Azure Active Directory integrace s HubSpot | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a HubSpot.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 91dfdcef01a121c8282b8fad2e75f67989b75dc3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98733229"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Kurz: Azure Active Directory integrace s HubSpot

V tomto kurzu se dozvíte, jak integrovat HubSpot s Azure Active Directory (Azure AD). Když integrujete HubSpot s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k HubSpot.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k HubSpot svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s HubSpot potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte předplatné služby Azure AD, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Předplatné HubSpot s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí a integrujete HubSpot se službou Azure AD.

HubSpot podporuje následující funkce:

* **Jednotné přihlašování inicializované v SP**
* **Jednotné přihlašování iniciované IDP**

## <a name="adding-hubspot-from-the-gallery"></a>Přidání HubSpot z Galerie

Pokud chcete nakonfigurovat integraci HubSpot do služby Azure AD, musíte přidat HubSpot z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **HubSpot** .
1. Na panelu výsledků vyberte **HubSpot** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-hubspot"></a>Konfigurace a testování jednotného přihlašování Azure AD pro HubSpot

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí HubSpot na základě testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v HubSpot.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí HubSpot, musíte dokončit tyto stavební bloky:

| Úkol | Popis |
| --- | --- |
| **[Konfigurace jednotného přihlašování Azure AD](#configure-azure-ad-single-sign-on)** | Umožňuje uživatelům používat tuto funkci. |
| **[Konfigurace jednotného přihlašování HubSpot](#configure-hubspot-single-sign-on)** | Nakonfiguruje nastavení jednotného přihlašování v aplikaci. |
| **[Vytvoření testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** | Testuje jednotné přihlašování Azure AD pro uživatele s názvem Britta Simon. |
| **[Přiřazení testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** | Povolí službě Britta Simon používat jednotné přihlašování Azure AD. |
| **[Vytvořit testovacího uživatele v HubSpot](#create-a-hubspot-test-user)** | Vytvoří protějšek Britta Simon v HubSpot, která je propojená se zastoupením uživatele v Azure AD. |
| **[Test jednotného přihlašování](#test-single-sign-on)** | Ověřuje, že konfigurace funguje. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

1. V Azure Portal na stránce integrace aplikací **HubSpot** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V podokně **základní konfigurace SAML** nakonfigurujte *režim iniciované IDP* a proveďte následující kroky:

    1. Do pole **identifikátor** zadejte adresu URL, která má následující vzor: https: \/ /API.HubSpot.com/login-API/v1/SAML/Login?portalId = \<CUSTOMER ID\> .

    1. Do pole **Adresa URL odpovědi** zadejte adresu URL, která má následující vzor: https: \/ /API.HubSpot.com/login-API/v1/SAML/ACS?portalId = \<CUSTOMER ID\> .

    ![Informace o jednotném přihlašování v doméně HubSpot a adresách URL](common/idp-intiated.png)

    > [!NOTE]
    > Pokud chcete naformátovat adresy URL, můžete se také podívat na vzory zobrazené v podokně **základní konfigurace SAML** v Azure Portal.

1. Konfigurace aplikace v režimu *iniciované SP* :

    1. Vyberte **nastavit další adresy URL**.

    1. Do pole **Adresa URL pro přihlášení** zadejte **https: \/ /App.HubSpot.com/login**.

    ![Možnost nastavit další adresy URL](common/metadata-upload-additional-signon.png)

1. V podokně **nastavit jednu Sign-On s** podoknem SAML vyberte v části **podpisový certifikát SAML** možnost **Stáhnout** další **certifikát (Base64)**. Vyberte možnost stažení podle vašich požadavků. Uložte certifikát do počítače.

    ![Možnost stažení certifikátu (Base64)](common/certificatebase64.png)

1. V části **Nastavení HubSpot** zkopírujte na základě vašich požadavků následující adresy URL:

    * Přihlašovací adresa URL
    * Identifikátor Azure AD
    * Odhlašovací adresa URL

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Konfigurace jednotného přihlašování HubSpot

1. V prohlížeči otevřete novou kartu a přihlaste se ke svému účtu správce HubSpot.

1. V pravém horním rohu stránky vyberte ikonu **Nastavení** .

    ![Ikona nastavení v HubSpot](./media/hubspot-tutorial/config1.png)

1. Vyberte **výchozí hodnoty účtu**.

    ![Možnost výchozí hodnoty účtu v HubSpot](./media/hubspot-tutorial/config2.png)

1. Přejděte dolů k části **zabezpečení** a pak vyberte **nastavit**.

    ![Možnost nastavení v HubSpot](./media/hubspot-tutorial/config3.png)

1. V části **nastavení jednotného přihlašování** proveďte následující kroky:

    1. V poli **Adresa URL cílové skupiny (ID entity poskytovatele služeb)** vyberte **Kopírovat** a zkopírujte hodnotu. V Azure Portal v **základní podokně Konfigurace SAML** vložte hodnotu do pole **identifikátor** .

    1. V poli **přihlašovat URL, ACS, příjemce nebo přesměrování** vyberte **Kopírovat** a zkopírujte hodnotu. V podokně Azure Portal **základní konfigurace SAML** vložte hodnotu do pole **Adresa URL odpovědi** .

    1. V HubSpot v poli **Identifikátor zprostředkovatele identity nebo adresa URL vystavitele** vložte hodnotu pro **identifikátor Azure AD** , který jste zkopírovali v Azure Portal.

    1. V HubSpot zadejte do pole **Adresa URL pro adresu URL jednu Sign-On adresu** **URL pro přihlášení** , kterou jste zkopírovali v Azure Portal.

    1. V programu Poznámkový blok otevřete soubor certifikátu (Base64), který jste stáhli. Vyberte a zkopírujte obsah souboru. Pak v HubSpot vložte do pole **certifikát X. 509** .

    1. Vyberte **Ověřit**.

        ![Část nastavení jednotného přihlašování v HubSpot](./media/hubspot-tutorial/config4.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k HubSpot.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **HubSpot**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-a-hubspot-test-user"></a>Vytvořit testovacího uživatele v HubSpot

Pokud chcete povolit, aby se uživatel k HubSpot přihlásil k Azure, musí být uživatel zřízený v HubSpot. V HubSpot je zřizování ručním úkolem.

Zřízení uživatelského účtu v HubSpot:

1. Přihlaste se k webu HubSpot společnosti jako správce.

1. V pravém horním rohu stránky vyberte ikonu **Nastavení** .

    ![Ikona nastavení v HubSpot](./media/hubspot-tutorial/config1.png)

1. Vyberte **uživatelé & týmy**.

    ![Možnost Uživatelé & týmy v HubSpot](./media/hubspot-tutorial/user1.png)

1. Vyberte **vytvořit uživatele**.

    ![Možnost vytvořit uživatele v HubSpot](./media/hubspot-tutorial/user2.png)

1. Do pole **Přidat e-mailové addessy** zadejte e-mailovou adresu uživatele ve formátu brittasimon \@ contoso.com a pak vyberte **Další**.

    ![Pole přidat e-mailové adresy v části Vytvoření uživatelů v HubSpot](./media/hubspot-tutorial/user3.png)

1. V části **vytvořit uživatele** vyberte jednotlivé karty. Na každé kartě nastavte příslušné možnosti a oprávnění pro uživatele. Pak vyberte **Další**.

    ![Karty v části Vytvoření uživatelů v HubSpot](./media/hubspot-tutorial/user4.png)

1. Pokud chcete odeslat pozvánku uživateli, vyberte **Odeslat**.

    ![Možnost Send v HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Uživatel se aktivuje po přijetí pozvánky uživatelem.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k HubSpot, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k HubSpot přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k HubSpot, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici HubSpot v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k HubSpot, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování HubSpot můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).