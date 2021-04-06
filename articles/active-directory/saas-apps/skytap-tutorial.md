---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s jednotným přihlašováním pro Skytap | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotným přihlašováním pro Skytap.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.openlocfilehash: 5577b0cb480b372b64a9cb25f52617edfca3f2e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98108139"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s jednotným přihlašováním pro Skytap

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování pro Skytap s Azure Active Directory (Azure AD). Když integrujete jednotné přihlašování pro Skytap s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k jednotnému přihlašování pro Skytap.
* Umožněte uživatelům, aby se automaticky přihlásili k jednotnému přihlašování pro Skytap s účty Azure AD.
* Spravujte své účty na jednom centrálním místě, Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Jednotné přihlašování pro předplatné s povoleným Skytapm jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Jednotné přihlašování pro Skytap podporuje aktualizace SP a IDP iniciované jednotného přihlašování.
* Po nakonfigurování jednotného přihlašování pro Skytap můžete vynutili řízení relace. Tím se chrání exfiltrace a infiltrace citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>Přidání jednotného přihlašování pro Skytap z Galerie

Pokud chcete nakonfigurovat integraci jednotného přihlašování pro Skytap do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat jednotné přihlašování pro Skytap z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejít na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **jednotné přihlašování pro Skytap** .
1. Z panelu výsledků vyberte **jednotné přihlašování pro Skytap** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Konfigurace a testování jednotného přihlašování Azure AD pro jednotné přihlašování pro Skytap

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s jednotným přihlašováním pro Skytap pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, vytvořte propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci jednotného přihlašování pro Skytap.

Tady je obecný postup konfigurace a testování jednotného přihlašování služby Azure AD s jednotným přihlašováním pro Skytap:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.

    a. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.

    b. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. **[Konfigurace jednotného přihlašování pro jednotné](#configure-single-sign-on-for-skytap-sso)** přihlašování Skytap ke konfiguraci nastavení jednotného přihlašování na straně aplikace

    a. **[Vytvořte jednotné přihlašování pro Skytap testovacího uživatele](#create-single-sign-on-for-skytap-test-user)** , které bude mít protějšek B. Simon v rámci jednotného přihlašování pro Skytap. Tato strana je propojená se zastoupením uživatele v Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **jednotné přihlašování pro** integraci aplikace Skytap najděte část **Správa** . Vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Snímek obrazovky nastavení jednotného přihlašování pomocí stránky SAML se zvýrazněnou ikonou tužky](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v části **základní konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL, která používá následující vzor: `http://pingone.com/<custom EntityID>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL, která používá následující vzor: `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Volitelně můžete vybrat **nastavit další adresy URL** a provedením následujících kroků nakonfigurovat aplikaci v režimu iniciované **SP** :

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL, která používá následující vzor: `https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. Do textového pole **stav přenosu** zadejte adresu URL, která používá následující vzor: `https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi, přihlašovací adresou URL a stavem přenosu. Chcete-li získat tyto hodnoty, obraťte se na [jednotné přihlašování pro Skytap tým podpory klientů](mailto:support@skytap.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML federačních metadat**. Vyberte **Stáhnout** a Stáhněte si soubor metadat a uložte ho do svého počítače.

    ![Snímek obrazovky s odkazem na stažení certifikátu](common/metadataxml.png)

1. V části **nastavení jednotného přihlašování pro Skytap** zkopírujte na základě vašeho požadavku příslušnou adresu URL nebo adresy URL.

    ![Snímek obrazovky s adresami URL konfigurace kopírování](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a potom zapište hodnotu zobrazenou v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k jednotnému přihlašování pro Skytap.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte **jednotné přihlašování pro Skytap**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Snímek obrazovky oddílu Správa se zvýrazněnými uživateli a skupinami](common/users-groups-blade.png)

1. Vyberte možnost **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Snímek stránky uživatelů a skupin se zvýrazněným možností přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** . Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Konfigurace jednotného přihlašování pro jednotné přihlašování Skytap

Ke konfiguraci jednotného přihlašování na straně jednotného přihlašování pro Skytap je potřeba odeslat stažená **federační metadata XML** a příslušné zkopírované adresy url z Azure Portal do [jednotného přihlašování pro tým podpory Skytap klientů](mailto:support@skytap.com). Nakonfigurují toto nastavení tak, aby bylo správně nastaveno připojení SAML SSO na obou stranách.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Vytvoření jednotného přihlašování pro Skytap testovacího uživatele

V této části vytvoříte uživatele s názvem B. Simon v rámci jednotného přihlašování pro Skytap. Pokud chcete přidat uživatele do jednotného přihlašování pro platformu Skytap, pracujte s [jednotným přihlašováním pro tým podpory Skytap](mailto:support@skytap.com) . Jednotné přihlašování nemůžete použít, dokud nevytvoříte a neaktivujete uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici jednotného přihlašování pro Skytap, měli byste se automaticky přihlásit k jednotnému přihlašování pro Skytap, pro které jste nastavili jednotné přihlašování. Další informace najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Kurzy integrace aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si časovou rezervu pomocí Azure AD](https://aad.portal.azure.com/)
