---
title: 'Kurz: Integrace Azure Active Directory s portálem Inativ Viareport (Evropa) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Viareport portálem Inativ (Evropa).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 249a61c6-a32e-40cc-a46a-268b89652f74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4a7e8359d3c4b80a4dc29a4845749ea690b57fc
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480507"
---
# <a name="tutorial-integrate-viareports-inativ-portal-europe-with-azure-active-directory"></a>Kurz: Integrace portálu Inativ (Evropa) pro Viareport pomocí Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat portál Inativ (Evropa) pro Viareport pomocí Azure Active Directory (Azure AD). Když integrujete portál Inativ (Evropa) pro Viareport s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k portálu Inativ (Evropa) pro Viareport.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Viareport portálu Inativ (Evropa) pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné portálu Inativ (Evropa) pro jednotné přihlašování (SSO) Viareport.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Viareport portál Inativ (Evropa) podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="adding-viareports-inativ-portal-europe-from-the-gallery"></a>Přidání portálu Inativ (Evropa) pro Viareport z Galerie

Pokud chcete nakonfigurovat integraci Viareport portálu Inativ (Evropa) na Azure AD, musíte z Galerie přidat portál Inativ (Evropa) Viareport do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Viareport portál Inativ (Evropa)** .
1. Z panelu výsledků vyberte **Viareport portál Inativ (Evropa)** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí portálu Viareport Inativ (Evropa) pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Inativ portálu Viareport (Evropa).

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí portálu Viareport Inativ (Evropa), dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[NAKONFIGURUJTE jednotné přihlašování Inativ portálu (Evropa) pro Viareport](#configure-viareports-inativ-portal-europe-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele portálu Inativ (Evropa) pro Viareport](#create-viareports-inativ-portal-europe-test-user)** , abyste měli protějšek B. Simon v Viareport portálu Inativ (Evropa), která je propojená s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **portál Inativ (Evropa)** pro integraci aplikace Viareport najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://inativ.viareport.com/SSO/<tenant_id>/callback`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://inativ.viareport.com/SSO/<tenant_id>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a přihlašovací adresy URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klientů Inativ (Evropa) Viareport](mailto:ycezard@viareport.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-viareports-inativ-portal-europe-sso"></a>Konfigurace jednotného přihlašování Inativ portálu (Evropa) pro Viareport

Pokud chcete nakonfigurovat jednotné přihlašování na straně **portálu Viareport Inativ (Evropa)** , musíte odeslat **adresu URL federačních metadat aplikace** na [tým podpory portálu Inativ (Evropa) Viareport](mailto:ycezard@viareport.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.
### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k portálu Inativ Viareport (Evropa).

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **portál Inativ (Evropa) pro Viareport**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-viareports-inativ-portal-europe-test-user"></a>Vytvoření testovacího uživatele portálu Inativ (Evropa) pro Viareport

V této části vytvoříte uživatele s názvem B. Simon na portálu Inativ (Evropa) Viareport. Pokud chcete přidat uživatele na platformě Viareport portálu Inativ (Evropa), pracujte s [týmem podpory portálu Inativ (Evropa) Viareport](mailto:ycezard@viareport.com) . Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Viareport na portálu Inativ (Evropa), měli byste být automaticky přihlášeni k portálu Viareport Inativ (Evropa), pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

