---
title: 'Kurz: Azure Active Directory integrace s 123FormBuilder SSO | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a 123FormBuilder SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: jeedes
ms.openlocfilehash: aa4bab2f7ecb90c61e22de46b01a5ed81342a408
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92319185"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-123formbuilder-sso"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s 123FormBuilder SSO

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování 123FormBuilder pomocí Azure Active Directory (Azure AD). Když integrujete jednotné přihlašování 123FormBuilder pomocí služby Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k 123FormBuilder SSO.
* Umožněte uživatelům, aby se automaticky přihlásili k 123FormBuilder SSO pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* předplatné 123FormBuilder jednotného přihlašování (SSO) jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* 123FormBuilder SSO podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.
* 123FormBuilder jednotného přihlašování **podporuje jednotné** zřizování uživatelů.
* Po nakonfigurování jednotného přihlašování 123FormBuilder můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-123formbuilder-sso-from-the-gallery"></a>Přidání jednotného přihlašování 123FormBuilder z Galerie

Pokud chcete nakonfigurovat integraci 123FormBuilder SSO do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat 123FormBuilder SSO z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **123FormBuilder SSO** .
1. Vyberte **123FORMBUILDER SSO** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-123formbuilder-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD pro jednotné přihlašování 123FormBuilder

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí jednotného přihlašování (123FormBuilder SSO) pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v 123FormBuilder SSO.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování 123FormBuilder, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte 123FORMBUILDER SSO](#configure-123formbuilder-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte uživatele 123FormBuilder jednotného přihlašování (SSO](#create-123formbuilder-sso-test-user)** ), který bude mít protějšek B. Simon v rámci 123FormBuilder SSO, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **jednotného přihlašování 123FormBuilder** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/metadata`


    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/acs`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Tuto hodnotu budete muset aktualizovat ze skutečných adres URL a identifikátorů, které jsou vysvětleny dále v tomto kurzu.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavení jednotného přihlašování 123FormBuilder** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k 123FormBuilder SSO.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **123FORMBUILDER SSO**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-123formbuilder-sso"></a>Konfigurace jednotného přihlašování 123FormBuilder

1. Pokud chcete nakonfigurovat jednotné přihlašování na **123FORMBUILDER SSO** , použijte [https://www.123formbuilder.com/form-2709121/](https://www.123formbuilder.com/form-2709121/) následující postup a proveďte následující kroky:

    ![Snímek obrazovky s přihlašováním – obrazovka konfigurace zprostředkovatele identity SAML](./media/123formbuilder-tutorial/submit.png) 

    a. Do textového pole **e-mail** zadejte e-maily uživatele, jako je `B.Simon@Contoso.com` .

    b. Klikněte na **nahrát** a vyhledejte stažený soubor XML s metadaty, který jste stáhli z Azure Portal.

    c. Klikněte na tlačítko **Odeslat formulář**.

2. V **Microsoft Azure AD – jednotné přihlašování – konfigurovat nastavení aplikace** proveďte následující kroky:

    ![Konfigurace jednoho Sign-On](./media/123formbuilder-tutorial/url3.png)

    a. Pokud chcete nakonfigurovat aplikaci v **režimu iniciované IDP**, zkopírujte hodnotu **identifikátoru** instance a vložte ji do textového pole **identifikátoru** v základní části **Konfigurace SAML** na Azure Portal.

    b. Pokud chcete nakonfigurovat aplikaci v **režimu iniciované IDP**, zkopírujte hodnotu **adresy URL odpovědi** pro vaši instanci a vložte ji do textového pole **Adresa URL odpovědi** v **základní části Konfigurace SAML** na Azure Portal.

    c. Pokud chcete nakonfigurovat aplikaci v **režimu SP iniciované**, zkopírujte hodnotu **adresy URL pro přihlášení** do vaší instance a vložte ji do TEXTOVÉHO pole pro **přihlášení URL** v **základní části Konfigurace SAML** na Azure Portal.

### <a name="create-123formbuilder-sso-test-user"></a>Vytvořit testovacího uživatele jednotného přihlašování 123FormBuilder

V této části se uživatel s názvem Britta Simon vytvoří v rámci 123FormBuilder SSO. 123FormBuilder SSO podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v 123FormBuilder SSO neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici 123FormBuilder SSO na přístupovém panelu, měli byste se automaticky přihlásit k 123FormBuilder SSO, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si 123FormBuilder SSO pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)