---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s compasem Netvision | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Netvision COMPAS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.openlocfilehash: 423ac882c81582f2843eeba37d11c660662ad6e2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519397"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s compasem Netvision

V tomto kurzu se dozvíte, jak integrovat Netvision COMPAS s Azure Active Directory (Azure AD). Když integrujete Netvision COMPAS s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Netvision COMPAS.
* Umožněte uživatelům, aby se automaticky přihlásili k Netvision COMPAS s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Netvision COMPAS s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* NetVision COMPAS podporuje **SP a IDP** iniciované jednotné přihlašování.
* Po nakonfigurování Netvision COMPAS můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>Přidání Netvision COMPAS z Galerie

Pokud chcete nakonfigurovat integraci Netvision COMPAS do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Netvision COMPAS z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Netvision COMPAS** .
1. Z panelu výsledků vyberte **Netvision COMPAS** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Netvision COMPAS

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Netvision COMPAS s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Netvision COMPAS.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s Netvision COMPAS, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Netvision COMPAS SSO](#configure-netvision-compas-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Nakonfigurujte testovacího uživatele Netvision COMPAS](#configure-netvision-compas-test-user)** , aby měl protějšek B. Simon v Netvision COMPAS, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **COMPAS Netvision** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<TENANT>.compas.cloud/Identity/Saml20`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta podpory Netvision COMPAS](mailto:contact@net.vision) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte si soubor metadat a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)



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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Netvision COMPAS.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Netvision COMPAS**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-netvision-compas-sso"></a>Konfigurace jednotného přihlašování Netvision COMPAS

V této části povolíte jednotné přihlašování SAML v **Netvision COMPAS**.
1. Přihlaste se k **Netvision COMPAS** pomocí účtu správce a přihlaste se k oblasti pro správu.

    ![Oblast pro správu](media/netvision-compas-tutorial/admin.png)

1. Vyhledejte položku **Systémová** oblast a vyberte možnost **Zprostředkovatelé identity**.

    ![Zprostředkovatelů identity správce](media/netvision-compas-tutorial/admin-idps.png)

1. Vyberte akci **Přidat** a zaregistrujete službu Azure AD jako novou IDP.

    ![Přidat IDP](media/netvision-compas-tutorial/idps-add.png)

1. Jako **Typ poskytovatele**vyberte **SAML** .
1. Zadejte smysluplné hodnoty pro pole **Zobrazovaný název** a **Popis** .
1. Přiřaďte uživatele **Netvisione COMPAS** k IDP tak, že vyberete ze seznamu **dostupných uživatelů** a pak vyberete tlačítko **Přidat vybrané** . Uživatelům se dá IDP přiřazovat taky během procesu zřizování.
1. Pro možnost SAML **metadat** klikněte na tlačítko **zvolit soubor** a vyberte soubor metadat, který jste v počítači dříve uložili.
1. Klikněte na **Uložit**.

    ![Upravit IDP](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>Konfigurovat testovacího uživatele Netvision COMPAS

V této části nakonfigurujete stávajícího uživatele v **Netvision COMPAS** , aby používal Azure AD pro jednotné přihlašování.
1. Postupujte podle postupu zřizování uživatele **Netvision COMPAS** podle definice vaší společnosti nebo upravte existující uživatelský účet.
1. Při definování profilu uživatele se ujistěte, že je **e-mailová adresa uživatele (osobní)** shodná s uživatelským jménem Azure AD: username@companydomain.extension . Například, `B.Simon@contoso.com`.

    ![Upravit uživatele](media/netvision-compas-tutorial/user-config.png)

Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD.

### <a name="using-the-access-panel-idp-initiated"></a>Použití přístupového panelu (IDP inicied).

Když na přístupovém panelu kliknete na dlaždici Netvision COMPAS, měli byste být automaticky přihlášeni k Netvision COMPAS, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Přímý přístup k Netvision COMPAS (iniciovaná v SP).

1. Přístup k adrese URL **Netvision COMPAS** Například, `https://tenant.compas.cloud`.
1. Zadejte uživatelské jméno **Netvision COMPAS** a vyberte **Další**.

    ![Přihlášení uživatele](media/netvision-compas-tutorial/login-user.png)

1. **(volitelné)** Pokud je uživateli přiřazeno více zprostředkovatelů identity v rámci **Netvision COMPAS**, zobrazí se seznam dostupných zprostředkovatelů identity. Vyberte Azure AD IDP nakonfigurovaný dříve v **Netvision COMPAS**.

    ![Výběr přihlášení](media/netvision-compas-tutorial/login-choose.png)

1. Budete přesměrováni na službu Azure AD, aby bylo možné provést ověření. Po úspěšném ověření byste se měli automaticky přihlášeni k **Netvision COMPAS** , pro kterou jste nastavili jednotné přihlašování.

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Netvision COMPAS s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)