---
title: 'Kurz: Azure Active Directory integrace s platformou Civic | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Civic platformou.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.openlocfilehash: 26109d4b8875dd0b442521513dbd219dc0de06e0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455965"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Kurz: integrace platformy Civic s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat platformu Civic s Azure Active Directory (Azure AD). Když integrujete platformu Civic s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k platformě Civic
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Civic platformě pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné s podporou jednotného přihlašování (SSO) Civic Platform.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Platforma Civic podporuje jednotné přihlašování iniciované v **SP**





## <a name="adding-civic-platform-from-the-gallery"></a>Přidání platformy Civic z Galerie

Pokud chcete nakonfigurovat integraci platformy Civic do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat platformu Civic z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Civic Platform** .
1. Z panelu výsledků vyberte **Civic Platform** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s platformou Civic pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Civic platformě.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s platformou Civic, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování k platformě Civic](#configure-civic-platform-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořte uživatele s Civic platformou pro testování](#create-civic-platform-test-user)** , abyste měli protějšek B. Simon na platformě Civic, která je propojená s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **platformy Civic** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.accela.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `civicplatform.accela.com`

    > [!NOTE]
    > Hodnota adresy URL pro přihlášení není v reálném čase. Aktualizujte tuto hodnotu skutečnou adresou URL pro přihlášení. Pokud chcete získat tuto hodnotu, obraťte se na [tým podpory Civic Platform Client](mailto:skale@accela.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Snímek obrazovky se zobrazí stránka podpisového certifikátu SAML, kde můžete kopírovat federační metadata aplikací.](common/copy-metadataurl.png)

1. V Azure AD přejděte na **Azure Active Directory**  >  **Registrace aplikací** a vyberte svou aplikaci.

1. Zkopírujte **ID adresáře (tenant)** a uložte ho do poznámkového bloku.

    ![Zkopírujte adresář (ID tenanta) a uložte ho do kódu vaší aplikace.](media/civic-platform-tutorial/directoryid.png)

1. Zkopírujte **ID aplikace** a uložte ho do poznámkového bloku.

   ![Kopírovat ID aplikace (klienta)](media/civic-platform-tutorial/applicationid.png)

1. V Azure AD přejděte na **Azure Active Directory**  >  **Registrace aplikací** a vyberte svou aplikaci. Vyberte **certifikáty & tajných**kódů.

1. Vyberte **Client tajných klíčů – > nový tajný klíč klienta**.

1. Zadejte popis tajného kódu a dobu trvání. Až budete hotovi, vyberte **Přidat**.

   > [!NOTE]
   > Po uložení tajného klíče klienta se zobrazí hodnota tajného klíče klienta. Zkopírujte tuto hodnotu, protože nemůžete získat klíč později.

   ![Zkopírujte tajnou hodnotu, protože ji nemůžete později načíst.](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>Konfigurace jednotného přihlašování k platformě Civic

1. Otevřete nové okno webového prohlížeče a přihlaste se k webu cloudové společnosti Atlassian jako správce.

1. Klikněte na **standardní volby**.

    ![Snímek obrazovky ukazuje cloudový web Atlassian se standardními možnostmi vyvolanými v části nástroje pro správu.](media/civic-platform-tutorial/standard-choices.png)

1. Vytvořte standardní možnost **ssoconfig**.

1. Vyhledejte **ssoconfig**  a odešlete.

    ![Snímek obrazovky zobrazuje standardní možnosti hledání s zadaným názvem s příponou o.](media/civic-platform-tutorial/sso-config.png)

1. Rozbalte SSOCONFIG kliknutím na červenou tečku.

    ![Snímek obrazovky zobrazuje standardní volby, které jsou k dispozici pro vyhledání s konfigurací s.](media/civic-platform-tutorial/sso-config01.png)

1. Zadejte informace o konfiguraci související s jednotným přihlašováním v následujícím kroku:

    ![Snímek obrazovky zobrazuje standardní možnosti úprav položek pro S konfigurací S S-O.](media/civic-platform-tutorial/sso-config02.png)

    1. Do pole **ApplicationId** zadejte hodnotu **ID aplikace** , kterou jste zkopírovali z Azure Portal.

    1. Do pole **clientSecret** zadejte **tajnou** hodnotu, kterou jste zkopírovali z Azure Portal.

    1. Do pole **directoryId** zadejte hodnotu **ID adresáře (tenant)** , kterou jste zkopírovali z Azure Portal.

    1. Zadejte idpName. Např.: – `Azure` .

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k platformě Civic.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **platforma Civic**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-civic-platform-test-user"></a>Vytvořit testovacího uživatele platformy Civic

V této části vytvoříte uživatele s názvem B. Simon na platformě Civic. Pokud chcete přidat uživatele v [týmu podpory Civic Platform Client](mailto:skale@accela.com), pracujte s týmem podpory platformy Civic. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Civic Platform na přístupovém panelu, měli byste se automaticky přihlásit k platformě Civic, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)