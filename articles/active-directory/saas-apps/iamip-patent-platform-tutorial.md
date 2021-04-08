---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s patentovou platformou IamIP | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a IamIP patentovou platformou.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.openlocfilehash: dc9be41cd7975610f2779da09b94066c70a91652
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460525"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s patentovou platformou IamIP

V tomto kurzu se dozvíte, jak integrovat IamIP patentovou platformu s Azure Active Directory (Azure AD). Když integrujete IamIP patentovou platformu s Azure AD, můžete:

* Pomocí Azure AD můžete řídit, kdo má přístup k IamIP patentové platformě.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k IamIP patentové platformě pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné IamIP patentové platformy s povoleným jednotným přihlašováním (SSO).

## <a name="tutorial-description"></a>Popis kurzu

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

Patentová platforma IamIP podporuje jednotné přihlašování (SSO) iniciované v SP a IDP.

Po nakonfigurování patentové platformy IamIP můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace přesahují podmíněný přístup. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).


## <a name="add-iamip-patent-platform-from-the-gallery"></a>Přidání patentové platformy IamIP z Galerie

Pokud chcete nakonfigurovat integraci IamIP patentové platformy do Azure AD, musíte z Galerie přidat IamIP patentovou platformu do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo pomocí osobního účet Microsoft.
1. V levém podokně vyberte **Azure Active Directory**.
1. Přejít na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **IamIP patentovou platformu** .
1. Na panelu výsledků vyberte **IamIP patentová platforma** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro patentovou platformu IamIP

Pomocí testovacího uživatele s názvem B. Simon nakonfigurujete a otestujete jednotné přihlašování Azure AD s IamIP patentovou platformou. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem Azure AD a odpovídajícím uživatelem v IamIP patentové platformě.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s IamIP patentovou platformou, provedete tyto kroky vysoké úrovně:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD.
    * **[Udělte k testovacímu uživateli přístup](#grant-access-to-the-test-user)** , aby mohl uživatel používat jednotné přihlašování Azure AD.

1. **[NAKONFIGURUJTE jednotné přihlašování pro patentovou platformu IamIP](#configure-iamip-patent-platform-sso)** na straně aplikace.
    * **[Vytvořte uživatele testovací platformy IamIP patentu](#create-iamip-patent-platform-test-user)** jako protějšek pro uživatele reprezentace v Azure AD.

1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací pro **patentovou platformu IamIP** v části **Spravovat** vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte tlačítko tužky pro **základní konfiguraci SAML** a upravte nastavení:

   ![Tlačítko tužky pro základní konfiguraci SAML](common/edit-urls.png)

1. Pokud v části **základní konfigurace SAML** máte soubor metadat poskytovatele služeb a chcete nakonfigurovat jednotné přihlašování v režimu iniciované IDP, proveďte tyto kroky:

    a. Vyberte **nahrát soubor metadat**:

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Vyberte tlačítko Složka, vyberte soubor metadat a pak vyberte **nahrát**:

    ![Tlačítka pro složku a odeslání](common/browse-upload-metadata.png)

    c. Po nahrání souboru metadat se automaticky naplní hodnoty **adresy URL** **identifikátoru** a odpovědi v **základní části Konfigurace SAML** :

    ![Identifikátor adresy URL pro odpovědi](common/idp-intiated.png)

    > [!Note]
    > Pokud se hodnoty **adresy URL** **identifikátoru** a odpovědi nevyplní automaticky, zadejte hodnoty ručně podle vašich požadavků.

1. Vyberte **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované pomocí SP:

    Do pole **Adresa URL pro přihlášení** zadejte  **https: \/ /Patents.iamip.com/login-User**.

1. Na stránce **nastavit jeden Sign-On s SAML** v části **podpisový certifikát SAML** vyberte odkaz **ke stažení** pro **certifikát (RAW)** a Stáhněte certifikát a uložte ho do počítače:

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

1. V části **nastavit patentovou platformu IamIP** zkopírujte příslušnou adresu URL nebo adresy URL na základě vašich požadavků:

    ![Kopírovat adresy URL konfigurace](common/idp-intiated.png))

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte tomuto uživateli přístup k patentové platformě IamIP.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **IamIP patentová platforma**.
1. Na stránce Přehled aplikace v části **Spravovat** vyberte **Uživatelé a skupiny**:

   ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** :

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte tlačítko **přiřadit** .

## <a name="configure-iamip-patent-platform-sso"></a>Konfigurace jednotného přihlašování pro patentovou platformu IamIP

Ke konfiguraci jednotného přihlašování na straně IamIP pro patentovou platformu je potřeba odeslat stažený nezpracovaný certifikát a příslušné adresy URL, které jste zkopírovali z Azure Portal do [týmu podpory IamIP pro patentovou platformu](mailto:info@iamip.com). Konfigurují připojení SAML pro jednotné přihlašování na obou stranách správně.

### <a name="create-iamip-patent-platform-test-user"></a>Vytvořit testovacího uživatele pro patentovou platformu IamIP

Spolupracujte s [týmem podpory IamIP patentové platformy](mailto:info@iamip.com) , který přidá uživatele s názvem B. Simon v IamIP patentové platformě. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci služby Azure AD SSO pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici IamIP patentové platformy, měli byste se automaticky přihlásit k instanci IamIP patentové platformy, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Kurzy k integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si IamIP patentovou platformu pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)