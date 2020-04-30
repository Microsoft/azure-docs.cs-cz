---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s aplikací SAML v Profit.co | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Profit.co aplikací SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 27f42934-c636-43dd-9c20-a3c6316f2763
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 564ca97210d85c5118901f30261abe3de9df1053
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81770930"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-profitco-saml-app"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s aplikací Profit.co SAML

V tomto kurzu se dozvíte, jak integrovat aplikaci Profit.co SAML pomocí Azure Active Directory (Azure AD). Když integrujete aplikaci Profit.co SAML s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k aplikaci Profit.co SAML.
* Umožněte uživatelům, aby se automaticky přihlásili k Profit.co aplikaci SAML pomocí svých účtů Azure AD.
* Spravujte své účty na jednom centrálním místě, Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Profit.co předplatného jednotného přihlašování aplikace SAML (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Profit.co aplikace SAML podporuje IDP iniciované jednotné přihlašování.

* Po konfiguraci aplikace Profit.co SAML můžete vynutili řízení relace. Tím se chrání exfiltrace a infiltrace citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-profitco-saml-app-from-the-gallery"></a>Přidání aplikace Profit.co SAML z Galerie

Pokud chcete nakonfigurovat integraci aplikace Profit.co SAML do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Profit.co aplikaci SAML z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Profit.co app aplikace SAML** .
1. Z panelu výsledků vyberte **Profit.co aplikace SAML** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-profitco-saml-app"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Profit.co aplikaci SAML

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí aplikace Profit.co SAML pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, vytvořte propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v Profit.co aplikaci SAML.

Tady je obecný postup konfigurace a testování jednotného přihlašování Azure AD pomocí aplikace Profit.co SAML:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. Konfigurace jednotného přihlašování **[aplikace Profit.co SAML](#configure-profitco-saml-app-sso)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte uživatele testu aplikace Profit.co SAML](#create-a-profitco-saml-app-test-user)** , který bude mít protějšek B. Simon v Profit.co aplikaci SAML. Tato strana je propojená se zastoupením uživatele v Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **Profit.co SAML** najděte část **Správa** . Vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Snímek obrazovky nastavení jednotného přihlašování pomocí stránky SAML se zvýrazněnou ikonou tužky](common/edit-urls.png)

1. V části **základní konfigurační oddíl SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné v Azure. Uživatelé musí konfiguraci uložit výběrem tlačítka **Uložit** .

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyberte tlačítko **Kopírovat** . Tím se zkopíruje **Adresa URL federačních metadat aplikace** a uloží se do počítače.

    ![Snímek pro podpisový certifikát SAML s zvýrazněným tlačítkem pro kopírování](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte **Azure Active Directory** > **Uživatelé** > **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a potom zapište hodnotu zobrazenou v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Profit.co aplikaci SAML.

1. V Azure Portal vyberte možnost **podnikové aplikace** > **všechny aplikace**.
1. V seznamu aplikace vyberte **Profit.co app aplikace SAML**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Snímek obrazovky oddílu Správa se zvýrazněnými uživateli a skupinami](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Snímek stránky uživatelů a skupin se zvýrazněným možností přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** . Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-profitco-saml-app-sso"></a>Konfigurace jednotného přihlašování aplikace SAML v Profit.co

Pokud chcete nakonfigurovat jednotné přihlašování na straně aplikace Profit.co SAML, musíte odeslat adresu URL federačních metadat aplikace [týmu podpory Profit.co aplikace SAML](mailto:support@profit.co). Nakonfigurují toto nastavení tak, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-a-profitco-saml-app-test-user"></a>Vytvořit Profit.co testovacího uživatele aplikace SAML

V této části vytvoříte uživatele s názvem B. Simon v aplikaci Profit.co SAML. Pokud chcete přidat uživatele na Profit.co App platformu SAML, pracujte s [týmem podpory aplikace Profit.co SAML](mailto:support@profit.co) . Jednotné přihlašování nemůžete použít, dokud nevytvoříte a neaktivujete uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici aplikace SAML Profit.co, měli byste se automaticky přihlášeni k aplikaci Profit.co SAML, pro kterou jste nastavili jednotné přihlašování. Další informace najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Kurzy integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Profit.co aplikaci SAML pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chránit Profit.co aplikaci SAML pomocí pokročilých viditelností a ovládacích prvků](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
