---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Lenses.io | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Lenses.io.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 181d58baf128c4848a538e776aea0e43213994dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458604"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) k portálu DataOps pro Lenses.io

V tomto kurzu se dozvíte, jak integrovat portál [lenses.IO](https://lenses.io/) DataOps s Azure Active Directory (Azure AD). Po integraci Lenses.io s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k portálu Lenses.io.
* Umožněte uživatelům, aby se automaticky přihlásili k objektivu pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure AD](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Instance portálu čočky. Můžete si vybrat z řady [možností nasazení](https://lenses.io/product/deployment/).
* [Licence](https://lenses.io/product/pricing/) lenses.IO, která podporuje jednotné přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Lenses.io podporuje poskytovatele služeb (SP) iniciované jednotné přihlašování.

* Po nakonfigurování Lenses.io můžete vynutili řízení relace. Řízení relace chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-lensesio-from-the-gallery"></a>Přidání Lenses.io z Galerie

Pokud chcete nakonfigurovat integraci Lenses.io do služby Azure AD, přidejte Lenses.io do seznamu spravovaných aplikací SaaS:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Vyberte **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **lenses.IO** .
1. Z panelu výsledků vyberte **lenses.IO** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Lenses.io

Vytvoříte testovacího uživatele s názvem *B. Simon* ke konfiguraci a testování jednotného přihlašování služby Azure AD pomocí portálu lenses.IO. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Lenses.io.

Dokončete následující kroky:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) , aby vaši uživatelé mohli používat tuto funkci.
    1. [Vytvořte testovacího uživatele a skupinu Azure AD](#create-an-azure-ad-test-user-and-group) pro otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. [Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) povolení B. Simon pro použití jednotného přihlašování služby Azure AD.
1. [NAKONFIGURUJTE jednotné](#configure-lensesio-sso) přihlašování lenses.IO pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. [Vytvořte oprávnění skupiny testů lenses.IO](#create-lensesio-test-group-permissions) , abyste mohli řídit, co B. Simon má přístup v lenses.IO (autorizaci).
1. [Otestujte jednotné přihlašování](#test-sso) a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **lenses.IO** najděte část **Správa** a pak vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte pro **základní konfiguraci SAML** ikonu Upravit/pero, abyste mohli upravit nastavení.

   ![Snímek obrazovky, který zobrazuje ikonu pro úpravu základní konfigurace SAML.](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty do následujících polí pro zadávání textu:

    a. **Adresa URL pro přihlášení**: zadejte adresu URL, která má následující vzor: `https://<CUSTOMER_LENSES_BASE_URL>` . Příklad: `https://lenses.my.company.com`.

    b. **Identifikátor (ID entity)**: zadejte adresu URL, která má následující vzor: `https://<CUSTOMER_LENSES_BASE_URL>` . Příklad: `https://lenses.my.company.com`.

    c. **Adresa URL odpovědi**: zadejte adresu URL, která má následující vzor: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client` . Příklad: `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`.

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte je pomocí skutečné přihlašovací adresy URL, adresy URL odpovědi a identifikátoru základní adresy URL vaší instance portálu čočky. Další informace najdete v [dokumentaci k LENSES.IO SSO](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0) .

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** přejít do části **podpisový certifikát SAML** . Najděte **federační metadata XML** a potom vyberte **Stáhnout** a stáhněte a uložte certifikát na svém počítači.

    ![Snímek obrazovky, který zobrazuje odkaz na stažení certifikátu.](common/metadataxml.png)

1. V části **nastavení lenses.IO** se pomocí souboru XML, který jste stáhli, nakonfiguruje rozptylová skla proti vašemu Azure SSO.

### <a name="create-an-azure-ad-test-user-and-group"></a>Vytvoření testovacího uživatele a skupiny Azure AD

V Azure Portal vytvoříte testovacího uživatele s názvem B. Simon. Pak vytvoříte testovací skupinu, která řídí přístup B. Simon má v čočky.

Můžete zjistit, jak rozptylová skla používá mapování členství ve skupině k autorizaci v [dokumentaci k jednotnému přihlašování pomocí objektivu](https://docs.lenses.io/install_setup/configuration/security.html#id3).

**Chcete-li vytvořit testovacího uživatele:**

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel**.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **název** zadejte **B. Simon**.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, B.Simon@contoso.com.
   1. Zaškrtněte políčko **Zobrazit heslo** . Zapište si heslo, které se zobrazí v poli **heslo** .
   1. Vyberte **Vytvořit**.

**Vytvoření skupiny:**

1. Přejít na **Azure Active Directory** a pak vyberte **skupiny**.
1. V horní části obrazovky vyberte možnost **Nová skupina**.
1. Ve **vlastnostech skupiny** proveďte tyto kroky:
   1. V poli **typ skupiny** vyberte **zabezpečení**.
   1. Do pole **název skupiny** zadejte **LensesUsers**.
   1. Vyberte **Vytvořit**.
1. Vyberte skupinu **LensesUsers** a zkopírujte **ID objektu** (například f8b5c1ec-45de-4abd-af5c-e874091fb5f7). Toto ID použijete v čočky k mapování uživatelů skupiny na [správná oprávnění](https://docs.lenses.io/install_setup/configuration/security.html#id3).  

**Přiřazení skupiny k testovacímu uživateli:**

1. Přejít na **Azure Active Directory** a pak vyberte **Uživatelé**.
1. Vyberte testovacího uživatele **B. Simon**.
1. Vyberte **skupiny**.
1. V horní části obrazovky vyberte **Přidat členství**.
1. Vyhledejte a vyberte **LensesUsers**.
1. Klikněte na **Vybrat**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Lenses.io.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **lenses.IO**.
1. Na stránce Přehled aplikace v části **Spravovat** vyberte **Uživatelé a skupiny**.

   ![Snímek obrazovky zobrazující odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte možnost **Přidat uživatele**.

   ![Snímek obrazovky zobrazující odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** . Pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte tlačítko **přiřadit** .

## <a name="configure-lensesio-sso"></a>Konfigurace jednotného přihlašování Lenses.io

Pokud chcete nakonfigurovat jednotné přihlašování na portálu **lenses.IO** , nainstalujte stažený **soubor XML federačních metadat** do instance čočky a [nakonfigurujte rozptylová skla pro povolení jednotného přihlašování](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses).

### <a name="create-lensesio-test-group-permissions"></a>Vytvořit oprávnění skupiny testů Lenses.io

1. Chcete-li vytvořit skupinu v čočky, použijte **ID objektu** skupiny **LensesUsers** . Toto je ID, které jste zkopírovali v [části Vytvoření](#create-an-azure-ad-test-user-and-group)uživatele.
1. Přiřaďte požadovaná oprávnění pro B. Simon.

Další informace najdete v tématu [mapování skupin Azure-čočky](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups).

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci služby Azure AD SSO pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici Lenses.io, měli byste se automaticky přihlásit k portálu Lenses.io. Další informace najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Nastavení jednotného přihlašování v instanci Lenses.io](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [Seznam kurzů pro integraci aplikací SaaS s Azure AD](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí služby Azure AD?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup ve službě Azure AD?](../conditional-access/overview.md)

- [Vyzkoušejte si Lenses.io s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit Lenses.io pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)