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
ms.date: 07/16/2020
ms.author: jeedes
ms.openlocfilehash: 822e28402d0b7829b835ad03a3b3cf7d05c3d343
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455802"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-cloud-academy---sso"></a>Kurz: Azure Active Directory integrace s jednotným přihlašováním pomocí cloudu Academy-SSO

V tomto kurzu se dozvíte, jak integrovat Cloud Academy-SSO pomocí Azure Active Directory (Azure AD). Když integrujete Cloud Academy-SSO s Azure AD, můžete:

* Pomocí Azure AD můžete řídit, kdo má přístup ke cloudu Academy-SSO.
* Umožněte, aby se vaši uživatelé automaticky přihlásili ke cloudu Academy-SSO pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Cloud Academy-SSO s povoleným jednotným přihlašováním (SSO).

## <a name="tutorial-description"></a>Popis kurzu

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

Cloud Academy – SSO podporuje jednotné přihlašování iniciované v SP.

Po nakonfigurování Cloud Academy-SSO můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace přesahují podmíněný přístup. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-cloud-academy---sso-from-the-gallery"></a>Přidání cloudu Academy-SSO z Galerie

Pokud chcete nakonfigurovat integraci Cloud Academy-SSO do Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat Cloud Academy-SSO z Galerie:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo pomocí osobního účet Microsoft.
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

1. V [Azure Portal](https://portal.azure.com/)na stránce **cloudová integrace aplikací Academy-SSO** v části **Spravovat** vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte tlačítko tužky pro **základní konfiguraci SAML** a upravte nastavení:

   ![Snímek obrazovky, který zobrazuje tlačítko tužky pro úpravu základní konfigurace SAML.](common/edit-urls.png)

1. V části **základní konfigurace SAML** v poli **přihlašovací adresa URL** zadejte `https://cloudacademy.com/login/enterprise/` .

1. Na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** vyberte tlačítko Kopírovat, aby se zkopírovala **Adresa URL federačních metadat aplikace**. Uložte adresu URL.

    ![Snímek obrazovky, který zobrazuje tlačítko pro kopírování pro adresu URL federačních metadat aplikace](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon ve Azure Portal.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**. Vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. V části vlastnosti **uživatele** proveďte tyto kroky:
   1. Do pole **název** zadejte **B. Simon**.  
   1. Do pole **uživatelské jméno** zadejte \<username> @ \<companydomain> . \<extension> . Například, `B.Simon@contoso.com`.
   1. Vyberte možnost **Zobrazit heslo**a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="grant-access-to-the-test-user"></a>Udělení přístupu testovacímu uživateli

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte tomuto uživateli přístup ke cloudu Academy-SSO.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Cloud Academy-SSO**.
1. Na stránce Přehled aplikace v části **Spravovat** vyberte **Uživatelé a skupiny**:

   ![Snímek obrazovky, který zobrazuje možnost Uživatelé a skupiny.](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** :

    ![Snímek obrazovky zobrazující tlačítko Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Klikněte na tlačítko **Vybrat** v dolní části obrazovky.
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

1. Přihlaste se ke cloudu Academy-SSO.

1. Vyberte název vaší společnosti a potom v zobrazené nabídce vyberte **Členové** :

    ![Snímek obrazovky zobrazující možnost členů](./media/cloud-academy-sso-tutorial/create-user.PNG)

1. Vyberte **pozvat členy** a pak vyberte **pozvat jednoho člena**:

    ![Snímek obrazovky, který ukazuje možnost pozvat jeden člen.](./media/cloud-academy-sso-tutorial/create-user-1.PNG)

1. Zadejte hodnoty do požadovaných polí a pak vyberte **pozvat**:

    ![Snímek obrazovky, který zobrazuje dialogové okno pozvat člena](./media/cloud-academy-sso-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>Test SSO 

Nyní otestujete konfiguraci služby Azure AD SSO pomocí přístupového panelu.

Když vyberete dlaždici Cloud Academy-SSO na přístupovém panelu, měli byste se automaticky přihlásit k instanci Cloud Academy-SSO, pro kterou jste nastavili jednotné přihlašování. Další informace najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy k integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte Cloud Academy-SSO s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit Cloud Academy-SSO pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)