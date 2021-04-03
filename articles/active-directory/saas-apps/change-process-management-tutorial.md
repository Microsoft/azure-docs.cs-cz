---
title: 'Kurz: integrace služby Azure AD SSO se správou změn procesů'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a měnit správu procesů.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: jeedes
ms.openlocfilehash: 03c78f05566876356e4f486368dc2a5b3a29de43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456251"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-change-process-management"></a>Kurz: Azure Active Directory integraci jednotného přihlašování se správou změn procesů

V tomto kurzu se dozvíte, jak integrovat správu procesů změn pomocí Azure Active Directory (Azure AD). Když integrujete správu změn procesu pomocí Azure AD, můžete:

* Pomocí Azure AD můžete řídit, kdo má přístup ke správě změn procesů.
* Umožněte uživatelům, aby se automaticky přihlásili ke změně správy procesů pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné správy procesu změny s povoleným jednotným přihlašováním (SSO).

## <a name="tutorial-description"></a>Popis kurzu

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

Změna správy procesů podporuje jednotné přihlašování iniciované IDP.

Po konfiguraci správy procesu změny můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace přesahují podmíněný přístup. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-change-process-management-from-the-gallery"></a>Přidání změny správy procesů z Galerie

Pokud chcete nakonfigurovat integraci správy změn v rámci služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat správu změn procesů z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo pomocí osobního účet Microsoft.
1. V levém podokně vyberte **Azure Active Directory**.
1. Přejít na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **změnit správu procesu** .
1. Na panelu výsledků vyberte **změnit správu procesů** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-change-process-management"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro správu procesu změny

Pomocí testovacího uživatele s názvem B. Simon nakonfigurujete a otestujete jednotné přihlašování Azure AD se správou procesů změn. Aby jednotné přihlašování fungovalo, musíte v rámci správy změn procesů vytvořit vztah propojení mezi uživatelem služby Azure AD a odpovídajícím uživatelem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí správy změn procesů, provedete tyto kroky vysoké úrovně:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD.
    1. **[Udělte k testovacímu uživateli přístup](#grant-access-to-the-test-user)** , aby mohl uživatel používat jednotné přihlašování Azure AD.
1. Na straně aplikace **[NAKONFIGURUJTE jednotné přihlašování pro správu procesu](#configure-change-process-management-sso)** .
    1. **[Vytvoří testovacího uživatele správy procesu změny](#create-a-change-process-management-test-user)** jako protějšek pro uživatele reprezentace Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce změnit integraci aplikací **správy procesů** v části **Spravovat** vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte tlačítko tužky pro **základní konfiguraci SAML** a upravte nastavení:

   ![Tlačítko tužky pro základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte tyto kroky:

    a. Do pole **identifikátor** zadejte adresu URL v následujícím vzoru: `https://<hostname>:8443/`

    b. Do pole **Adresa URL odpovědi** zadejte adresu URL v následujícím vzoru: `https://<hostname>:8443/changepilot/saml/sso`

    > [!NOTE]
    > Hodnoty **adresy URL** předchozího **identifikátoru** a odpovědi nejsou aktuálními hodnotami, které byste měli použít. Pokud chcete získat skutečné hodnoty, obraťte se na [tým podpory správy změn procesů](mailto:support@realtech-us.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** vyberte odkaz **ke stažení** pro **certifikát (Base64)** a Stáhněte certifikát a uložte ho do počítače:

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení správy procesu změny** zkopírujte příslušnou adresu URL nebo adresy URL na základě vašich požadavků:

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon ve Azure Portal.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**. Vyberte **Uživatelé** a pak vyberte **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. V části vlastnosti **uživatele** proveďte tyto kroky:
   1. Do pole **název** zadejte **B. Simon**.  
   1. Do pole **uživatelské jméno** zadejte \<username> @ \<companydomain> . \<extension> . Například, `B.Simon@contoso.com`.
   1. Vyberte možnost **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="grant-access-to-the-test-user"></a>Udělení přístupu testovacímu uživateli

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte tomuto uživateli přístup pro změnu správy procesů.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **změnit správu procesů**.
1. Na stránce Přehled aplikace v části **Spravovat** vyberte **Uživatelé a skupiny**:

   ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-change-process-management-sso"></a>Konfigurovat jednotné přihlašování pro správu procesů

Chcete-li konfigurovat jednotné přihlašování na straně změnit správu procesu, je třeba odeslat stažený certifikát Base64 a příslušné adresy URL, které jste zkopírovali z Azure Portal do [týmu podpory správy změn procesů](mailto:support@realtech-us.com). Konfigurují připojení SAML pro jednotné přihlašování na obou stranách správně.

### <a name="create-a-change-process-management-test-user"></a>Vytvořit testovacího uživatele správy procesu změny
 Pracujte s [týmem podpory správy změn procesů](mailto:support@realtech-us.com) a přidejte uživatele s názvem B. Simon ve správě změn procesů. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci služby Azure AD SSO pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Správa procesu změny, měli byste být automaticky přihlášeni k instanci správy procesu změny, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Kurzy k integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Zkuste změnit správu procesů pomocí Azure AD.](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit správu procesu změn pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)