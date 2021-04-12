---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Profit.co | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Profit.co.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: jeedes
ms.openlocfilehash: 027fb66538dfcead24470507b8080f2e0aacb469
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515198"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-profitco"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Profit.co

V tomto kurzu se dozvíte, jak integrovat Profit.co s Azure Active Directory (Azure AD). Když integrujete Profit.co s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Profit.co.
* Umožněte uživatelům, aby se automaticky přihlásili k Profit.co pomocí svých účtů Azure AD.
* Spravujte své účty na jednom centrálním místě, Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Profit.co odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Profit.co podporuje jednotné přihlašování (SSO) iniciované IDP.

* Po nakonfigurování Profit.co můžete vynutili řízení relace. Tím se chrání exfiltrace a infiltrace citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-profitco-from-the-gallery"></a>Přidání Profit.co z Galerie

Pokud chcete nakonfigurovat integraci Profit.co do služby Azure AD, musíte přidat Profit.co z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Profit.co** .
1. Na panelu výsledků vyberte **Profit.co** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-profitco"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Profit.co

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Profit.co pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, vytvořte propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v Profit.co.

Tady je obecný postup konfigurace a testování jednotného přihlašování Azure AD pomocí Profit.co:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. Nakonfigurujte jednotné přihlašování **[Profit.co SSO](#configure-profitco-sso)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte uživatele Profit.co test](#create-a-profitco-test-user)** , který bude mít protějšek B. Simon v Profit.co. Tato strana je propojená se zastoupením uživatele v Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Profit.co** Najděte oddíl **Manage** . Vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Snímek obrazovky nastavení jednotného přihlašování pomocí stránky SAML se zvýrazněnou ikonou tužky](common/edit-urls.png)

1. V části **základní konfigurační oddíl SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné v Azure. Uživatelé musí konfiguraci uložit výběrem tlačítka **Uložit** .

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyberte tlačítko **Kopírovat** . Tím se zkopíruje **Adresa URL federačních metadat aplikace** a uloží se do počítače.

    ![Snímek pro podpisový certifikát SAML s zvýrazněným tlačítkem pro kopírování](common/copy-metadataurl.png)

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Profit.co.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte **Profit.co**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Snímek obrazovky oddílu Správa se zvýrazněnými uživateli a skupinami](common/users-groups-blade.png)

1. Vyberte možnost **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Snímek stránky uživatelů a skupin se zvýrazněným možností přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** . Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-profitco-sso"></a>Konfigurace jednotného přihlašování Profit.co

Ke konfiguraci jednotného přihlašování na straně Profit.co je potřeba poslat [týmu podpory Profit.co](mailto:support@profit.co)adresu URL federačních metadat aplikace. Nakonfigurují toto nastavení tak, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-a-profitco-test-user"></a>Vytvořit testovacího uživatele v Profit.co

V této části vytvoříte uživatele s názvem B. Simon v Profit.co. Pracujte s [týmem podpory Profit.co](mailto:support@profit.co) a přidejte uživatele na platformě Profit.co. Jednotné přihlašování nemůžete použít, dokud nevytvoříte a neaktivujete uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici Profit.co, měli byste se automaticky přihlásit k Profit.co, pro které jste nastavili jednotné přihlašování. Další informace najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Kurzy integrace aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Profit.co s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit Profit.co pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)