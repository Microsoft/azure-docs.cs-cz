---
title: 'Kurz: Azure Active Directory integrace s xMatters OnDemand | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a xMatters OnDemand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2020
ms.author: jeedes
ms.openlocfilehash: 762bd1c536df0ca307149ba7c201f08f5bdfded5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735591"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Kurz: Azure Active Directory integrace s xMatters OnDemand

V tomto kurzu se dozvíte, jak integrovat xMatters OnDemand s Azure Active Directory (Azure AD).
Integrace xMatters OnDemand s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k xMatters OnDemand.
* Uživatelům můžete povolit, aby se automaticky přihlásili k xMatters OnDemand (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s xMatters OnDemand, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* předplatné xMatters OnDemand Single Sign-on Enabled

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* xMatters OnDemand podporuje **IDP** , iniciované jednotné přihlašování

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Přidání xMatters OnDemand z Galerie

Pokud chcete nakonfigurovat integraci xMatters OnDemand do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat xMatters OnDemand z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **xMatters OnDemand** .
1. Z panelu výsledků vyberte **XMatters OnDemand** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-xmatters-ondemand"></a>Konfigurace a testování jednotného přihlašování Azure AD pro xMatters OnDemand

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí xMatters OnDemand pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v xMatters OnDemand.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí xMatters OnDemand, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    2. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
2. **[Nakonfigurujte XMatters OnDemand SSO](#configure-xmatters-ondemand-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
    1. **[Vytvoření XMatters OnDemand Test User](#create-xmatters-ondemand-test-user)** – Pokud chcete mít protějšek Britta Simon v xMatters OnDemand, který je propojený s reprezentací uživatele Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **XMatters OnDemand** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí jednoho z následujících vzorů:

    | Identifikátor |
    | ---------- |
    | `https://<companyname>.au1.xmatters.com.au/` |
    | `https://<companyname>.cs1.xmatters.com/` |
    | `https://<companyname>.xmatters.com/` |
    | `https://www.xmatters.com` |
    | `https://<companyname>.xmatters.com.au/` |

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí jednoho z následujících vzorů:

    | Adresa URL odpovědi |
    | ---------- |
    |  `https://<companyname>.au1.xmatters.com.au` |
    | `https://<companyname>.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.au1.xmatters.com.au/<instancename>` |

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory XMatters OnDemand Client](https://www.xmatters.com/company/contact-us/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

    > [!IMPORTANT]
    > Je nutné předaný certifikát do [týmu XMatters OnDemand support](https://www.xmatters.com/company/contact-us/). Aby bylo možné dokončit konfiguraci jednotného přihlašování, je nutné, aby byl certifikát odeslán týmem podpory xMatters.

6. V části **nastavit XMatters OnDemand** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k xMatters OnDemand.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **XMatters OnDemand**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .


## <a name="configure-xmatters-ondemand-sso"></a>Konfigurace xMatters OnDemand SSO

1. V jiném okně webového prohlížeče se přihlaste k webu XMatters OnDemand Company jako správce.

2. Klikněte na **správce** a pak klikněte na **Podrobnosti společnosti**.

    ![Stránka pro správu](./media/xmatters-ondemand-tutorial/admin.png "správce")

3. Na stránce **Konfigurace SAML** proveďte následující kroky:

    ![Konfigurační oddíl SAML ](./media/xmatters-ondemand-tutorial/saml-configuration.png "Konfigurace SAML")

    a. Vyberte **Povolit SAML**.

    b. Do textového pole **ID zprostředkovatele identity** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL jednotného přihlašování** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. V textovém poli **přesměrování adresy URL pro odhlášení** vložte **adresu URL** pro odhlášení, kterou jste zkopírovali z Azure Portal.

    e. Klikněte na **zvolit soubor** a nahrajte **certifikát (Base64)** , který jste stáhli z Azure Portal. 

    f. Na stránce s informacemi o společnosti v horní části klikněte na **Uložit změny**.

    ![Podrobné informace o společnosti](./media/xmatters-ondemand-tutorial/save-button.png "Podrobné informace o společnosti")

### <a name="create-xmatters-ondemand-test-user"></a>Vytvořit uživatele xMatters OnDemand test

1. Přihlaste se ke klientovi **XMatters OnDemand** .

2. Přejděte na **ikonu uživatelé**  >  **Uživatelé** a potom klikněte na **Přidat uživatele**.

    ![Uživatelé](./media/xmatters-ondemand-tutorial/add-user.png "Uživatelé")

3. V části **Přidat uživatele** vyplňte požadovaná pole a klikněte na tlačítko **Přidat uživatele** .

    ![Přidat uživatele](./media/xmatters-ondemand-tutorial/add-user-2.png "Přidat uživatele")



### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k xMatters OnDemand, pro který jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici xMatters OnDemand v okně moje aplikace byste měli být automaticky přihlášeni k xMatters OnDemand, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování xMatters OnDemand můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).