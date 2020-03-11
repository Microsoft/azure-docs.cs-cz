---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s záchytný bod | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a záchytný bod.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab3eead7-8eb2-4c12-bb3a-0e46ec899d37
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56d34a331821ffbc3d0d4f2cf5e9b033f4011ff
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968497"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-catchpoint"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s záchytný bod

V tomto kurzu se dozvíte, jak integrovat záchytný bod s Azure Active Directory (Azure AD). Když integrujete záchytný bod s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k záchytný bod.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k záchytný bod svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Záchytný bod odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Záchytný bod podporuje jednotné přihlašování (SSO) **a IDP** .
* Záchytný bod podporuje zřizování uživatelů **jenom v čase** .
* Po nakonfigurování záchytný bod můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-catchpoint-from-the-gallery"></a>Přidání záchytný bod z Galerie

Pokud chcete nakonfigurovat integraci záchytný bod do služby Azure AD, musíte přidat záchytný bod z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **záchytný bod** .
1. Na panelu výsledků vyberte **záchytný bod** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Konfigurace a testování jednotného přihlašování Azure AD pro záchytný bod

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí záchytný bod pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v záchytný bod.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí záchytný bod, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte záchytný bod SSO](#configure-catchpoint-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte záchytný bod test User](#create-catchpoint-test-user)** -to, abyste měli protějšek B. Simon v záchytný bod, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **záchytný bod** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavení jednotného přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL: `https://portal.catchpoint.com/SAML2`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu url: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu url: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Záchytný bod aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace záchytný bod několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Název | Zdrojový atribut|
    | ------------ | --------- |
    | Obor názvů | user.assignedrole |

    > [!NOTE]
    > deklarace identity oboru názvů musí být namapována s názvem účtu. Název tohoto účtu by měl být nastavený jako role ve službě Azure AD, které se budou předávat zpátky v odpovědi SAML. Informace o nastavení rolí najdete v tomto [článku](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) .

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení záchytný bod** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k záchytný bod.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **záchytný bod**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-catchpoint-sso"></a>Konfigurace jednotného přihlašování záchytný bod

1. V jiném okně webového prohlížeče se přihlaste do aplikace záchytný bod jako správce.

1. Klikněte na ikonu **Nastavení** a vyberte **zprostředkovatel identity SSO**.

    ![Konfigurace záchytný bod](./media/catchpoint-tutorial/configuration1.png)

1. Na stránce **jednotného přihlašování** proveďte následující kroky:

    ![Konfigurace záchytný bod](./media/catchpoint-tutorial/configuration2.png)

    1. Do textového pole **obor názvů** zadejte platnou hodnotu oboru názvů.

    1. Do textového pole **vystavitele zprostředkovatele identity** zadejte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    1. Do textového pole **Adresa URL jednotného přihlašování** zadejte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    1. Otevřete stažený soubor **certifikátu (Base64)** do programu Poznámkový blok, zkopírujte obsah souboru certifikátu a vložte ho do textového pole **certifikátu** .

    1. Můžete také nahrát **XML federačních metadat** kliknutím na možnost **nahrát metadata** .

    1. Klikněte na **Uložit**.

### <a name="create-catchpoint-test-user"></a>Vytvořit testovacího uživatele záchytný bod

V této části se v záchytný bod vytvoří uživatel s názvem Britta Simon. Záchytný bod podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v záchytný bod neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici záchytný bod, měli byste se automaticky přihlásit k záchytný bod, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

> [!NOTE]
> Když se přihlašujete k aplikaci záchytný bod prostřednictvím přihlašovací stránky, po zadání **přihlašovacích údajů záchytný bod**zadejte platnou hodnotu **oboru názvů** do textového pole **pověření společnosti (SSO)** a klikněte na **Přihlásit**.

![Konfigurace záchytný bod](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si záchytný bod s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)