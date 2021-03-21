---
title: 'Kurz: Azure Active Directory integrace s Overdrive | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Overdrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: 390ccfa828ebaa0d3dec15990ffeb60cbb24360e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648573"
---
# <a name="tutorial-azure-active-directory-integration-with-overdrive"></a>Kurz: Azure Active Directory integrace s Overdrive

V tomto kurzu se dozvíte, jak integrovat Overdrive s Azure Active Directory (Azure AD). Když integrujete Overdrive s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Overdrive.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Overdrive svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:
 
* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným Overdrivem jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Overdrive podporuje jednotné přihlašování iniciované v **SP** .

* Overdrive podporuje zřizování uživatelů **jenom v čase** .

## <a name="add-overdrive-from-the-gallery"></a>Přidání Overdrive z Galerie

Pokud chcete nakonfigurovat integraci Overdrive do služby Azure AD, přidejte Overdrive z Galerie do svého seznamu spravovaných aplikací pro SaaS pomocí následujícího postupu:
 
1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo pomocí osobního účet Microsoft.
1. V levém podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Overdrive** .
1. V podokně výsledků vyberte **Overdrive** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-overdrive"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Overdrive

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Overdrive pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Overdrive.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Overdrive postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Overdrive SSO](#configure-overdrive-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Overdrive Test User](#create-overdrive-test-user)** -to, abyste měli protějšek B. Simon v Overdrive, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Overdrive** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `http://<subdomain>.libraryreserve.com`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta Overdrive](https://help.overdrive.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení Overdrive** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Overdrive.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **Overdrive**.

2. V seznamu aplikace vyberte **Overdrive**.

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-overdrive-sso"></a>Konfigurace jednotného přihlašování Overdrive

Ke konfiguraci jednotného přihlašování na straně **Overdrive** je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory Overdrive](https://help.overdrive.com/). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-overdrive-test-user"></a>Vytvořit testovacího uživatele Overdrive

V této části se v Overdrive vytvoří uživatel s názvem Britta Simon. Overdrive podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Overdrive neexistuje, vytvoří se po ověření nový.

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů OverDrive nebo rozhraní API poskytovaná OverDrive.
>

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Overdrive, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k Overdrive přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Overdrive v části Moje aplikace, přesměruje se na přihlašovací adresu Overdrive. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Overdrive můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).