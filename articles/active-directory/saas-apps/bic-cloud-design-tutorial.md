---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s návrhem cloudu pro program BIC | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a společností BIC – návrh cloudu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.openlocfilehash: 08e96fbf422d734b34af63cfbc42479c059e3866
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457151"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bic-cloud-design"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí jednotného přihlašování (SSO) s návrhem cloudu BIC

V tomto kurzu se dozvíte, jak integrovat návrh aplikace BIC Cloud pomocí Azure Active Directory (Azure AD). Když integrujete cloudový návrh BIC pomocí Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k návrhu cloudu BIC
* Umožněte, aby se vaši uživatelé automaticky přihlásili do aplikace BIC Cloud design pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné BIC Cloud design jednotného přihlašování (SSO) s povoleným jednotným přihlašováním.

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Návrh pro Cloud BIC, **který podporuje jednotné** přihlašování
* Jakmile nakonfigurujete návrh cloudu BIC, můžete vyhovět ovládacím prvkům relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace přesahují podmíněný přístup. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-bic-cloud-design-from-the-gallery"></a>Přidání návrhu cloudu BIC z Galerie

Pokud chcete nakonfigurovat integraci nástroje BIC Cloud design do služby Azure AD, musíte přidat návrh cloudu BIC z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **BIC Cloud design** .
1. Z panelu výsledků vyberte položku **BIC Cloud design** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bic-cloud-design"></a>Konfigurace a testování jednotného přihlašování Azure AD pro návrh pro cloudy BIC

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí návrhu cloudu BIC pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v návrhu pro Cloud BIC.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí návrhu cloudu BIC, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat jednotné přihlašování pro cloudový návrh BIC](#configure-bic-cloud-design-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    * **[Vytvořte testovacího uživatele pro cloudový návrh BIC](#create-bic-cloud-design-test-user)** , abyste měli protějšek B. Simon v návrhu cloudu BIC, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **design cloudu BIC** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud máte **soubor metadat poskytovatele služeb**v **základní části Konfigurace SAML** , proveďte následující kroky:

    a. Klikněte na **nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![zvolit soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnota **identifikátoru** automaticky naplní v základní části Konfigurace SAML.

    ![Přihlašovací údaje domény a adresy URL jednotného přihlašování společnosti BIC pro Cloud](common/sp-identifier.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:

    ```https
    https://<customer-specific-name/tenant>.biccloud.com
    https://<customer-specific-name/tenant>.biccloud.de
    ```

    > [!Note]
    > Pokud hodnota **identifikátoru** nezíská auto polulated, vyplňte prosím hodnotu ručně podle vašeho požadavku. Hodnota přihlašovací adresy URL není reálné číslo. Aktualizujte tuto hodnotu pomocí skutečné přihlašovací adresy URL. Pokud chcete získat tuto hodnotu, obraťte se na [tým podpory klienta pro návrh cloudu společnosti BIC](mailto:bicsupport@gbtec.de) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace pro návrh aplikace BIC Cloud očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě výše očekává aplikace programu pro vytváření cloudových aplikací BIC v odpovědi SAML méně dalších atributů, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Zdrojový atribut|
    | ------------ | --------- |
    | Name | user.name |
    | E-mailová adresa | uživatel. pošta |
    | ID jména | User. userPrincipalName |
    | e-mail | uživatel. pošta |
    | nametest | User. DisplayName |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k návrhu cloudu BIC.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **BIC Cloud design**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-bic-cloud-design-sso"></a>Konfigurace jednotného přihlašování pro cloudový návrh BIC

Chcete-li nakonfigurovat jednotné přihlašování na straně návrhu na úrovni **cloudu společnosti BIC** , je nutné odeslat **adresu URL federačních metadat aplikace** [týmu podpory pro návrh cloudu BIC](mailto:bicsupport@gbtec.de). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-bic-cloud-design-test-user"></a>Vytvořit testovacího uživatele pro cloudový návrh BIC

V této části vytvoříte uživatele s názvem B. Simon v návrhu cloudu BIC. Pracujte s [týmem podpory pro návrh cloudu společnosti BIC](mailto:bicsupport@gbtec.de) a přidejte uživatele do platformy pro návrh cloudu BIC. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici návrh nástroje BIC Cloud na přístupovém panelu byste měli být automaticky přihlášení k návrhu cloudu BIC, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si v Azure AD návrh cloudu BIC](https://aad.portal.azure.com/)