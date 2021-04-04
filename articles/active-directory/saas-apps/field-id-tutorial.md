---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s iD pole | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iD pole.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.openlocfilehash: 55a66f3f287ffb50d932263e407772efffa839ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453531"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-field-id"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s iD pole

V tomto kurzu se dozvíte, jak integrovat iD pole pomocí Azure Active Directory (Azure AD). Když integrujete iD pole s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k iD pole.
* Umožněte uživatelům, aby se automaticky přihlásili k iD pole pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Na začátek budete potřebovat:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ID pole podporuje IDP jednotné přihlašování.
* Po konfiguraci iD pole můžete vyhovět řízení relace. To chrání před exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-field-id-from-the-gallery"></a>Přidat iD pole z Galerie

Pokud chcete nakonfigurovat integraci iD pole do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat iD pole z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte **Azure Active Directory**.
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **ID pole** .
1. Z panelu výsledků vyberte **ID pole** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-field-id"></a>Konfigurace a testování jednotného přihlašování Azure AD pro iD pole

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s iD pole pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v poli iD.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s iD pole, proveďte následující kroky:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) , aby vaši uživatelé mohli používat tuto funkci.
    1. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, [přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) .
1. Nakonfigurujte jednotné přihlašování pro [ID pole](#configure-field-id-sso) a nakonfigurujte nastavení jednotného přihlašování na straně aplikace.
    1. [Vytvořte testovacího uživatele ID pole](#create-a-field-id-test-user) , aby měl protějšek B. Simon v ID pole, které je propojené s reprezentací uživatele v Azure AD.
1. [Otestujte jednotné přihlašování](#test-sso) a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace s **ID pole** Najděte oddíl **Spravovat** . Pak vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Snímek obrazovky nastavení jednoho Sign-On se stránkou SAML se zvýrazněnou ikonou tužky](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

   a. Do textového pole **identifikátor** zadejte adresu URL, která používá následující vzor: `https://<tenantname>.fieldid.com/fieldid`

   b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL, která používá následující vzor: `https://<tenantname>.fieldid.com/fieldid/saml/SSO/alias/<Tenant Name>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory ID pole](mailto:support@ecompliance.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyberte ikonu kopírování pro kopírování **adresy URL federačních metadat aplikace**. Uložte ho do svého počítače.

    ![Snímek pro podpisový certifikát SAML se zvýrazněnou ikonou kopírování](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Jako **název** zadejte `B.Simon` .  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension (například `B.Simon@contoso.com` ).
   1. Zaškrtněte políčko **Zobrazit heslo** a potom zapište hodnotu zobrazenou v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k iD pole.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte položku **ID pole**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Snímek obrazovky oddílu Správa se zvýrazněnými uživateli a skupinami](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Snímek obrazovky s přidáním uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** a pak zvolte **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak zvolte **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-field-id-sso"></a>Konfigurace iD pole jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování na straně iD pole, odešlete do [týmu podpory ID pole](mailto:support@ecompliance.com) **adresu URL federačních metadat aplikace** . Zajišťují, že připojení SAML SSO je na obou stranách správně nastavené.

### <a name="create-a-field-id-test-user"></a>Vytvoření iD pole testovacího uživatele

V této části vytvoříte uživatele s názvem Britta Simon v poli iD pole. Pokud chcete přidat uživatele v platformě iD pole, pracujte s [týmem podpory pole s ID](mailto:support@ecompliance.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když vyberete dlaždici iD pole na přístupovém panelu, měli byste se automaticky přihlásit k iD pole, pro které jste nastavili jednotné přihlašování. Další informace najdete v tématu [přihlášení a spouštění aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Kurzy integrace aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušet iD pole pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit iD pole pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)