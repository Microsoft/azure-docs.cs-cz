---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s zástupné | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a zástupné.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2021
ms.author: jeedes
ms.openlocfilehash: bbf961e7b99efe29fd8b13c2104c33e42ae7d4be
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286495"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-envoy"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s zástupné

V tomto kurzu se dozvíte, jak integrovat zástupné s Azure Active Directory (Azure AD). Když integrujete zástupné s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k zástupné.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k zástupné svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Zástupné odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zástupné podporuje jednotné přihlašování iniciované v **SP** .

* Zástupné podporuje zřizování uživatelů **jenom v čase** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-envoy-from-the-gallery"></a>Přidání zástupné z Galerie

Pokud chcete nakonfigurovat integraci zástupné do služby Azure AD, musíte přidat zástupné z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **zástupné** .
1. Na panelu výsledků vyberte **zástupné** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-envoy"></a>Konfigurace a testování jednotného přihlašování Azure AD pro zástupné

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí zástupné pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v zástupné.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí zástupné postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte zástupné SSO](#configure-envoy-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte zástupné Test User](#create-envoy-test-user)** -to, abyste měli protějšek B. Simon v zástupné, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **zástupné** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://app.envoy.com/a/saml/auth/<company-ID-from-Envoy>`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta zástupné](https://envoy.com/contact/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. V části **podpisový certifikát SAML** kliknutím na tlačítko **Upravit** otevřete dialogové okno **podpisový certifikát SAML** .

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

1. V části **podpisový certifikát SAML** zkopírujte **hodnotu kryptografického otisku** a uložte ji do svého počítače.

    ![Kopírovat hodnotu kryptografického otisku](common/copy-thumbprint.png)

1. V části **Nastavení zástupné** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k zástupné.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **zástupné**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-envoy-sso"></a>Konfigurace jednotného přihlašování zástupné

1. Pokud chcete automatizovat konfiguraci v rámci zástupné, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **zástupné nastavení** a nasměrujte vás na aplikaci zástupné. Odtud zadejte přihlašovací údaje správce, které se přihlásí k zástupné. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit zástupné ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu zástupné společnosti jako správce a proveďte následující kroky:

4. Na panelu nástrojů v horní části klikněte na **Nastavení**.

    ![Envoy](./media/envoy-tutorial/envoy-1.png "Envoy")

5. Klikněte na **Společnost**.

    ![Společnost](./media/envoy-tutorial/envoy-2.png "Společnost")

6. Klikněte na **SAML**.

    ![SAML](./media/envoy-tutorial/envoy-3.png "SAML")

7. V části konfigurace **ověřování SAML** proveďte následující kroky:

    ![Ověřování SAML](./media/envoy-tutorial/envoy-4.png "Ověřování SAML")
    
    >[!NOTE]
    >Hodnota pro ID umístění sídel je automaticky generovaná aplikací.
    
    a. Do textového pole **otisk prstu** vložte hodnotu **kryptografického otisku** certifikátu, kterou jste zkopírovali z Azure Portal.
    
    b. Vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali, do TEXTOVÉHO pole **URL poskytovatele identity SAML http SAML** , které jste zkopírovali Azure Portal.
    
    c. Klikněte na **Uložit změny**.

### <a name="create-envoy-test-user"></a>Vytvořit testovacího uživatele zástupné

V této části se v zástupné vytvoří uživatel s názvem Britta Simon. Zástupné podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v zástupné neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k zástupné, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k zástupné přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici zástupné v části Moje aplikace, přesměruje se na přihlašovací adresu zástupné. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování zástupné můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
