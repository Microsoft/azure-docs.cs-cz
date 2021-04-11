---
title: 'Kurz: Azure Active Directory integrace s Expensify | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Expensify.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: 5c68453f7f7d638877683920a17022cf3d29bdc5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597803"
---
# <a name="tutorial-integrate-expensify-with-azure-active-directory"></a>Kurz: integrace Expensify s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Expensify s Azure Active Directory (Azure AD). Když integrujete Expensify s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Expensify.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Expensify svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Expensify odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Expensify podporuje jednotné přihlašování iniciované v **SP** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-expensify-from-the-gallery"></a>Přidání Expensify z Galerie

Pokud chcete nakonfigurovat integraci Expensify do služby Azure AD, musíte přidat Expensify z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Expensify** .
1. Na panelu výsledků vyberte **Expensify** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-expensify"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Expensify

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Expensify pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Expensify.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Expensify postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
2. **[Nakonfigurujte EXPENSIFY SSO](#configure-expensify-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    1. **[Vytvořte Expensify Test User](#create-expensify-test-user)** -to, abyste měli protějšek B. Simon v Expensify, která je propojená s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Expensify** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://www.expensify.com/authentication/saml/login`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `https://www.expensify.com`

    c. b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://www.expensify.com/authentication/saml/loginCallback?domain=<yourdomain>`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL odpovědi. Pokud chcete získat tuto hodnotu, obraťte se na [tým podpory klienta Expensify](mailto:help@expensify.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **XML metadata** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení Expensify** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Expensify.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Expensify**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-expensify-sso"></a>Konfigurace jednotného přihlašování Expensify

Pokud chcete povolit jednotné přihlašování v Expensify, musíte nejdřív v aplikaci povolit **řízení z domény** . V aplikaci můžete povolit ovládací prvek doména prostřednictvím kroků uvedených [tady](https://help.expensify.com/domain-control). Pro další podporu Pracujte s [týmem podpory klienta Expensify](mailto:help@expensify.com). Jakmile je povoleno řízení domény, postupujte podle těchto kroků:

![Konfigurace jednoho Sign-On](./media/expensify-tutorial/domain-control.png)

1. Přihlaste se k aplikaci Expensify.

2. Na levém panelu klikněte na **Nastavení** a přejděte na **SAML**.

3. Přepněte možnost **přihlášení SAML** jako **povolenou**.

4. Otevřete stažená federační metadata ze služby Azure AD v programu Poznámkový blok, zkopírujte obsah a vložte ho do textového pole **metadata poskytovatele identity** .

### <a name="create-expensify-test-user"></a>Vytvořit testovacího uživatele Expensify

V této části vytvoříte uživatele s názvem B. Simon v Expensify. Pokud chcete přidat uživatele na platformě Expensify, pracujte s [týmem podpory klienta Expensify](mailto:help@expensify.com) .

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Expensify, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k Expensify přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Expensify v části Moje aplikace, přesměruje se na přihlašovací adresu Expensify. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Expensify můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).