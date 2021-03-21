---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s cloudovým Academyem – jednotné přihlašování'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cloud Academy-SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: jeedes
ms.openlocfilehash: fa46d6e5c7f1007e3a90e22eb9d4f46e18251a28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729825"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-cloud-academy---sso"></a>Kurz: Azure Active Directory integrace s jednotným přihlašováním pomocí cloudu Academy-SSO

V tomto kurzu se dozvíte, jak integrovat Cloud Academy-SSO pomocí Azure Active Directory (Azure AD). Když integrujete Cloud Academy-SSO s Azure AD, můžete:

* Pomocí Azure AD můžete řídit, kdo má přístup ke cloudu Academy-SSO.
* Umožněte, aby se vaši uživatelé automaticky přihlásili ke cloudu Academy-SSO pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Cloud Academy-SSO s povoleným jednotným přihlašováním (SSO).

## <a name="tutorial-description"></a>Popis kurzu

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cloud Academy – jednotné přihlašování (SSO) s podporou služeb **SP** iniciované
* Cloud Academy – jednotné přihlašování (SSO) podporuje zřizování uživatelů **jenom včas**

## <a name="add-cloud-academy---sso-from-the-gallery"></a>Přidání cloudu Academy-SSO z Galerie

Pokud chcete nakonfigurovat integraci Cloud Academy-SSO do Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat Cloud Academy-SSO z Galerie:

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo pomocí osobního účet Microsoft.
1. V levém podokně vyberte **Azure Active Directory**.
1. Přejít na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Cloud Academy-SSO** .
1. Na panelu výsledků vyberte **Cloud Academy-SSO** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Cloud Academy-SSO

Nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí služby Cloud Academy-SSO pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a odpovídajícím uživatelem v cloudu Academy-SSO.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí cloudu Academy-SSO, dokončíte tyto kroky vysoké úrovně:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD.
    1. **[Udělte k testovacímu uživateli přístup](#grant-access-to-the-test-user)** , aby mohl uživatel používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte jednotné přihlašování pro Cloud Academy-SSO](#configure-single-sign-on-for-cloud-academy)** na straně aplikace.
    1. **[Vytvořte si testovacího uživatele Academy-SSO pro jednotné přihlašování](#create-a-cloud-academy-test-user)** jako protějšek pro uživatele reprezentace v Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal:

1. V Azure Portal na stránce **cloudová integrace aplikací Academy-SSO** v části **Spravovat** vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte tlačítko tužky pro **základní konfiguraci SAML** a upravte nastavení:

   ![Snímek obrazovky, který zobrazuje tlačítko tužky pro úpravu základní konfigurace SAML.](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte jednu z těchto adres URL:
    
    | Přihlašovací adresa URL |
    |--------------|
    | `https://cloudacademy.com/login/enterprise/` |
    | `https://app.qa.com/login/enterprise/` |
    |
    
    b. Do textového pole **Adresa URL odpovědi** zadejte jednu z těchto adres URL:
    
    | Adresa URL odpovědi |
    |--------------|
    | `https://cloudacademy.com/labs/social/complete/saml/` |
    | `https://app.qa.com/labs/social/complete/saml/` |
    |
1. Na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** vyberte tlačítko Kopírovat, aby se zkopírovala **Adresa URL federačních metadat aplikace**. Uložte adresu URL.

    ![Snímek obrazovky, který zobrazuje tlačítko pro kopírování pro adresu URL federačních metadat aplikace](common/copy-metadataurl.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte tomuto uživateli přístup ke cloudu Academy-SSO.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Cloud Academy-SSO**.
1. Na stránce Přehled aplikace v části **Spravovat** vyberte **Uživatelé a skupiny**:
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** :
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-single-sign-on-for-cloud-academy"></a>Konfigurace jednotného přihlašování pro cloudové Academy

1. V jiném okně prohlížeče se přihlaste k webu Cloud Academy-SSO jako správce.

1. Vyberte název vaší společnosti a v zobrazené nabídce vyberte **nastavení & integrace** :

    ![Snímek obrazovky zobrazující možnost nastavení & integrace](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. Na stránce **Nastavení integrace &** na kartě **integrace** vyberte kartu **jednotného přihlašování** :

    ![Snímek obrazovky zobrazující kartu jednotného přihlašování na kartě integrace](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Na této stránce proveďte následující kroky:

    ![Snímek obrazovky zobrazující stránku Inegrations > SSO](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. Do pole **Adresa URL s ID entity** zadejte hodnotu ID entity, kterou jste zkopírovali z Azure Portal.

    b. Do pole **Adresa URL jednotného přihlašování** vložte hodnotu URL pro přihlášení, kterou jste zkopírovali z Azure Portal.

    c. Otevřete stažený certifikát Base64 z Azure Portal v programu Poznámkový blok. Vložte obsah do pole **certifikát** .

    d. V poli **Formát názvu ID** ponechte výchozí hodnotu: `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` .

1. Vyberte **Uložit**.

    > [!NOTE]
    > Další informace o tom, jak nakonfigurovat cloud Academy-SSO, najdete v tématu [nastavení jednotného přihlašování](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On).

### <a name="create-a-cloud-academy-test-user"></a>Vytvoření Academy testovacího uživatele cloudu

V této části se v cloudu Academy-SSO vytvoří uživatel s názvem Britta Simon. Cloud Academy – SSO podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v cloudu Academy-SSO neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL Academy přihlášení ke cloudu, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení ke cloudu Academy-SSO přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Cloud Academy-SSO v části Moje aplikace, přesměruje se na přihlašovací adresu Cloud Academy-SSO Signing. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování Cloud Academy-SSO můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).