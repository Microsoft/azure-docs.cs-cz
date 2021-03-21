---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s SiteIntel | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SiteIntel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: jeedes
ms.openlocfilehash: 0c8224bf84a2235086d941df7d02ab6f458f3f16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92509960"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-siteintel"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s SiteIntel

V tomto kurzu se dozvíte, jak integrovat SiteIntel s Azure Active Directory (Azure AD). Když integrujete SiteIntel s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SiteIntel.
* Umožněte uživatelům, aby se automaticky přihlásili k SiteIntel pomocí svých účtů Azure AD.
* Spravujte své účty na jednom centrálním místě, Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* SiteIntel odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SiteIntel podporuje jednotné přihlašování iniciované v SP a IdP.
* Po nakonfigurování SiteIntel můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-siteintel-from-the-gallery"></a>Přidání SiteIntel z Galerie

Pokud chcete nakonfigurovat integraci SiteIntel do služby Azure AD, musíte přidat SiteIntel z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém podokně vyberte **Azure Active Directory**.
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V poli **Přidat z Galerie** zadejte **SiteIntel**.
1. V seznamu výsledků vyberte **SiteIntel** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-siteintel"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SiteIntel

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SiteIntel pomocí testovacího uživatele s názvem *B. Simon*. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SiteIntel.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SiteIntel, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.  

    a. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD s uživatelem B. Simon.  

    b. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** , abyste povolili uživateli B. Simon používat jednotné přihlašování Azure AD.

1. Nakonfigurujte jednotné přihlašování **[SITEINTEL SSO](#configure-siteintel-sso)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.

    * **[Vytvořte SiteIntel testovacího uživatele](#create-a-siteintel-test-user)** , který bude mít protějšek uživatele B. Simon v SiteIntel, který je propojený s Předprezentací služby Azure AD.

1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pokud chcete povolit jednotné přihlašování služby Azure AD v Azure Portal, udělejte toto:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **SiteIntel** přejít do části **Spravovat** a pak vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte vedle **základní konfigurace SAML** na možnost **Upravit** (ikona pera).

   ![Snímek obrazovky s podoknem nastavení jednotného přihlašování pomocí SAML](common/edit-urls.png)

1. Chcete-li nakonfigurovat aplikaci v režimu iniciované IdP, proveďte v **základní části Konfigurace SAML** následující akce:

    a. Do pole **identifikátor** zadejte adresu URL v následujícím formátu: `urn:amazon:cognito:sp:<REGION>_<USERPOOLID>`

    b. Do pole **Adresa URL odpovědi** zadejte adresu URL v následujícím formátu: `https://<CLIENT>.auth.siteintel.com/saml2/idpresponse`

    c. Do pole **stav přenosu** zadejte adresu URL v následujícím formátu: `https://<CLIENT>.siteintel.com`

1. Chcete-li nakonfigurovat aplikaci v režimu iniciované SP, vyberte možnost **nastavit další adresy URL** a proveďte následující kroky:

   * Do pole **přihlašovací adresa URL** zadejte adresu URL v následujícím formátu: `https://<CLIENT>.siteintel.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte je skutečným identifikátorem, adresou URL odpovědi, přihlašovací adresou URL a stavem přenosu. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory SiteIntel Client](mailto:support@intalytics.com). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyberte tlačítko **Kopírovat** a zkopírujte adresu URL v poli **Adresa URL federačních metadat aplikace** .

    ![Snímek obrazovky s tlačítkem Kopírovat adresu URL federačních metadat aplikace](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části podokna vyberte **Nový uživatel** .
1. V části vlastnosti **uživatele** proveďte následující:

   a. Do pole **název** zadejte **B. Simon**.  

   b. Do pole **uživatelské jméno** zadejte uživatelské jméno v následujícím formátu: `username@companydomain.extension` (například `B.Simon@contoso.com` ).

   c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .

   d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte uživateli B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k SiteIntel.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu **aplikace** vyberte **SiteIntel**.
1. Na stránce Přehled aplikace v části **Spravovat** vyberte **Uživatelé a skupiny**.

   ![Snímek obrazovky s odkazem na uživatele a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a potom v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Snímek obrazovky s tlačítkem Přidat uživatele](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** vyberte **B. Simon** a pak vyberte tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v podokně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak vyberte tlačítko **Vybrat** .
1. V podokně **Přidat přiřazení** vyberte tlačítko **přiřadit** .

## <a name="configure-siteintel-sso"></a>Konfigurace jednotného přihlašování SiteIntel

Pokud chcete nakonfigurovat jednotné přihlašování na straně SiteIntel, pošlete adresu URL, kterou jste zkopírovali z pole **Adresa URL federačních metadat aplikace** do [týmu podpory SiteIntel](mailto:support@intalytics.com). Tuto hodnotu nastavili tak, aby na obou stranách správně navázalo připojení k jednotnému přihlašování SAML.

### <a name="create-a-siteintel-test-user"></a>Vytvořit testovacího uživatele v SiteIntel

V této části vytvoříte uživatele s názvem *Britta Simon* v SiteIntel. Pokud chcete přidat uživatele na platformě SiteIntel, pracujte s [týmem podpory SiteIntel](mailto:support@intalytics.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici **SiteIntel** , měli byste se automaticky přihlásit k SiteIntel, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)
- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)
- [Vyzkoušejte si SiteIntel s Azure AD](https://aad.portal.azure.com/)
- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
- [Jak chránit SiteIntel pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)