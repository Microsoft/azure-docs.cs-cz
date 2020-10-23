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
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: c53b7fed17ba4ef2000b905b202286338cd461ed
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442896"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Kurz: Azure Active Directory integrace s HubSpot

V tomto kurzu se dozvíte, jak integrovat HubSpot s Azure Active Directory (Azure AD).

Integrace HubSpot s Azure AD přináší následující výhody:

* Pomocí Azure AD můžete řídit, kdo má přístup k HubSpot.
* Uživatelé můžou být automaticky přihlášení k HubSpot pomocí svých účtů Azure AD (jednotné přihlašování).
* Účty můžete spravovat v jednom centrálním umístění, Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s HubSpot potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte předplatné služby Azure AD, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Předplatné HubSpot s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí a integrujete HubSpot se službou Azure AD.

HubSpot podporuje následující funkce:

* **Jednotné přihlašování inicializované v SP**
* **Jednotné přihlašování iniciované IDP**

## <a name="add-hubspot-in-the-azure-portal"></a>Přidat HubSpot do Azure Portal

Pokud chcete integrovat HubSpot s Azure AD, musíte přidat HubSpot do seznamu spravovaných aplikací SaaS.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **Azure Active Directory**.

    ![Možnost Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace**  >  **všechny aplikace**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat aplikaci, vyberte možnost **Nová aplikace**.

    ![Možnost nové aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **HubSpot**. Ve výsledcích hledání vyberte **HubSpot**a pak vyberte **Přidat**.

    ![HubSpot v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí HubSpot na základě testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v HubSpot.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí HubSpot, musíte dokončit tyto stavební bloky:

| Úloha | Popis |
| --- | --- |
| **[Konfigurace jednotného přihlašování Azure AD](#configure-azure-ad-single-sign-on)** | Umožňuje uživatelům používat tuto funkci. |
| **[Konfigurace jednotného přihlašování HubSpot](#configure-hubspot-single-sign-on)** | Nakonfiguruje nastavení jednotného přihlašování v aplikaci. |
| **[Vytvoření testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** | Testuje jednotné přihlašování Azure AD pro uživatele s názvem Britta Simon. |
| **[Přiřazení testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** | Povolí službě Britta Simon používat jednotné přihlašování Azure AD. |
| **[Vytvořit testovacího uživatele v HubSpot](#create-a-hubspot-test-user)** | Vytvoří protějšek Britta Simon v HubSpot, která je propojená se zastoupením uživatele v Azure AD. |
| **[Test jednotného přihlašování](#test-single-sign-on)** | Ověřuje, že konfigurace funguje. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části nakonfigurujete jednotné přihlašování Azure AD pomocí HubSpot v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)v podokně integrace aplikace **HubSpot** vyberte **jednotné přihlašování**.

    ![Konfigurovat možnost jednotného přihlašování](common/select-sso.png)

1. V podokně **Vyberte metodu jednotného přihlašování** vyberte možnost **SAML** nebo **SAML/WS-nakrmený** režim pro povolení jednotného přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

1. V podokně **nastavit jednu Sign-On s** podoknem SAML vyberte **Upravit** (ikona tužky) a otevřete základní podokno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V podokně **základní konfigurace SAML** nakonfigurujte *režim iniciované IDP*a proveďte následující kroky:

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

V této části vytvoříte testovacího uživatele s názvem Britta Simon v Azure Portal.

1. V Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.

    ![Možnosti uživatelé a všichni uživatelé](common/users.png)

1. Vyberte **Nový uživatel**.

    ![Možnost Nový uživatel](common/new-user.png)

1. V podokně **uživatel** proveďte následující kroky:

    1. Do pole **název** zadejte **BrittaSimon**.
  
    1. Do pole **uživatelské jméno** zadejte **brittasimon \@ \<your-company-domain> . \<extension\> **. Například **brittasimon \@ contoso.com**.

    1. Zaškrtněte políčko **Zobrazit heslo** . Zapište hodnotu, která se zobrazí v poli **heslo** .

    1. Vyberte **Vytvořit**.

    ![Podokno uživatele](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části udělíte Britta Simon přístup k HubSpot, aby mohli používat jednotné přihlašování Azure.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**  >  **HubSpot**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikace vyberte **HubSpot**.

    ![HubSpot v seznamu aplikací](common/all-applications.png)

1. V nabídce vyberte **Uživatelé a skupiny**.

    ![Možnost Uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte možnost **Přidat uživatele**. Pak v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Podokno přidat přiřazení](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** . Zvolte **Vybrat**.

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v podokně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Zvolte **Vybrat**.

1. V podokně **Přidat přiřazení** vyberte **přiřadit**.

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

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí portálu moje aplikace.

Po nastavení jednotného přihlašování se při výběru **HubSpot** na portálu moje aplikace automaticky přihlásíte k HubSpot. Další informace o portálu moje aplikace najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc, přečtěte si tyto články:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)