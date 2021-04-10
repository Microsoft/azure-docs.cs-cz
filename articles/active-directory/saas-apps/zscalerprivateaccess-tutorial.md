---
title: 'Kurz: Azure Active Directory integrace s privátním přístupem Zscaler (ZPA) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscaler Private Access (ZPA).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 58e2a19f2d57eafc7d2967141d584dc7a22fe76c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955665"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Kurz: integrace Zscaler Private Access (ZPA) s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Zscaler Private Access (ZPA) s Azure Active Directory (Azure AD). Když integrujete Zscaler Private Access (ZPA) s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k privátnímu přístupu Zscaler (ZPA).
* Umožněte uživatelům, aby se automaticky přihlásili k Zscaler privátnímu přístupu (ZPA) pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Zscaler Private Access (ZPA) jednotného přihlašování (SSO) s povoleným přístupem.

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. 

* Zscaler Private Access (ZPA) podporuje jednotné přihlašování iniciované v **SP** .

## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Přidání Zscaler Private Access (ZPA) z Galerie

Pokud chcete nakonfigurovat integraci Zscaler Private Access (ZPA) na Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat z Galerie Zscaler privátní přístup (ZPA).

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Zscaler Private Access (ZPA)** .
1. Z panelu výsledků vyberte **Zscaler Private Access (ZPA)** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Zscaler Private Access (ZPA) pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zscaler Private Access (ZPA).

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Zscaler Private Access (ZPA), proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace jednotného přístupu Zscaler (ZPA) SSO](#configure-zscaler-private-access-zpa-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte testovacího uživatele Zscaler Private Access (ZPA)](#create-zscaler-private-access-zpa-test-user)** , který bude mít protějšek B. Simon v Zscaler privátní přístup (ZPA), který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **Zscaler Private Access (ZPA)** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    1. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > Hodnota **adresy URL pro přihlášení** není v reálném čase. Aktualizujte hodnotu skutečnou adresou URL pro přihlášení. Pokud chcete získat hodnotu, kontaktujte [tým podpory Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

   ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení Zscaler Private Access (ZPA)** zkopírujte příslušné adresy URL na základě vašeho požadavku.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `Britta Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `BrittaSimon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Zscaler Private Access (ZPA).

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Zscaler Private Access (ZPA)**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-zscaler-private-access-zpa-sso"></a>Konfigurace jednotného přístupu Zscaler (ZPA) SSO

1. Pokud chcete automatizovat konfiguraci v rámci Zscaler Private Access (ZPA), je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Nastavení Zscaler Private Access (ZPA)** vás přesměruje do aplikace Zscaler Private Access (ZPA). Odtud zadejte přihlašovací údaje správce pro přihlášení ke službě Zscaler Private Access (ZPA). Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit Zscaler Private Access (ZPA) ručně, otevřete nové okno webového prohlížeče a přihlaste se k vaší společnosti Zscaler Private Access (ZPA) jako správce a proveďte následující kroky:

4. V levé části nabídky klikněte na **Správa** a přejděte do části **ověřování** klikněte na **Konfigurace IDP**.

    ![Správa správce privátního přístupu Zscaler](./media/zscalerprivateaccess-tutorial/administration.png)

5. V pravém horním rohu klikněte na **Přidat konfiguraci IDP**. 

    ![Zscaler správce privátního přístupu IDP](./media/zscalerprivateaccess-tutorial/metadata.png)

6. Na stránce **Přidat konfiguraci IDP** proveďte následující kroky:
 
    ![Vybrat Zscaler správce privátního přístupu](./media/zscalerprivateaccess-tutorial/select.png)

    a. Klikněte na **Vybrat soubor** a nahrajte stažený soubor metadat z Azure AD do pole pro **nahrání souboru metadat IDP** .

    b. Přečte **metadata IDP** z Azure AD a naplní všechny informace o polích, jak je uvedeno níže.

    ![Konfigurace Správce privátního přístupu Zscaler](./media/zscalerprivateaccess-tutorial/configure.png)

    c. Vyberte pole doména z **domén** .
    
    d. Klikněte na **Uložit**.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Vytvořit testovacího uživatele Zscaler Private Access (ZPA)

V této části vytvoříte uživatele s názvem Britta Simon v Zscaler Private Access (ZPA). Pokud chcete přidat uživatele na platformě Zscaler Private Access (ZPA), obraťte se na [tým podpory Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) .

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL přihlašování Zscaler Private Access (ZPA), kde můžete spustit tok přihlášení. 

* Přejděte přímo na adresu URL pro přihlášení Zscaler Private Access (ZPA) a zahajte tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Zscaler Private Access (ZPA) v části Moje aplikace, přesměruje se na adresu URL pro přihlášení Zscaler Private Access (ZPA). Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Zscaler Private Access (ZPA) můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).