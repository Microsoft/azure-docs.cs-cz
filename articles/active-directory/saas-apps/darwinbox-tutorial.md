---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Darwinbox | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Darwinbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.openlocfilehash: a2c1ba8feb933be7c4970f061dd5acdc1ef68a16
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91775435"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-darwinbox"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Darwinbox

V tomto kurzu se dozvíte, jak integrovat Darwinbox s Azure Active Directory (Azure AD). Když integrujete Darwinbox s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Darwinbox.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Darwinbox svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.
Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Darwinbox odběr s povoleným jednotným přihlašováním (SSO).
> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.


## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Darwinbox podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-darwinbox-from-the-gallery"></a>Přidání Darwinbox z Galerie

Pokud chcete nakonfigurovat integraci Darwinbox do služby Azure AD, musíte přidat Darwinbox z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Darwinbox** .
1. Na panelu výsledků vyberte **Darwinbox** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-darwinbox"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Darwinbox

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Darwinbox pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Darwinbox.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Darwinbox, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte DARWINBOX SSO](#configure-darwinbox-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Darwinbox Test User](#create-darwinbox-test-user)** -to, abyste měli protějšek B. Simon v Darwinbox, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Darwinbox** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

   1. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.darwinbox.in/`

   1. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.darwinbox.in/adfs/module.php/saml/sp/metadata.php/<CUSTOMID>`

      > [!NOTE]
      > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Darwinbox](https://darwinbox.com/contact-us.php) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení Darwinbox** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Darwinbox.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Darwinbox**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-darwinbox-sso"></a>Konfigurace jednotného přihlašování Darwinbox

Ke konfiguraci jednotného přihlašování na straně **Darwinbox** je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory Darwinbox](https://darwinbox.com/contact-us.php). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-darwinbox-test-user"></a>Vytvořit testovacího uživatele Darwinbox

V této části vytvoříte uživatele s názvem B. Simon v Darwinbox. Pokud chcete přidat uživatele na platformě Darwinbox, pracujte s [týmem podpory Darwinbox](https://darwinbox.com/contact-us.php) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Darwinbox, měli byste se automaticky přihlásit k Darwinbox, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-darwinbox-mobile"></a>Testování jednotného přihlašování pro Darwinbox (mobilní zařízení)

1. Otevřete mobilní aplikaci Darwinbox. Klikněte na **zadat adresu URL organizace** , do textového pole zadejte adresu URL vaší organizace a klikněte na tlačítko se šipkou.

    ![Snímek obrazovky, na které se zobrazuje mobilní aplikace "Darwinbox" s vybranou možností "zadat organizaci U R L", a zvýrazní se vzorové tlačítko "šipka".](media/darwinbox-tutorial/DarwinboxMobile01.jpg)

1. Pokud máte více domén, klikněte na doménu.

    ![Snímek obrazovky zobrazující obrazovku zvolit vaši doménu s ukázkovou doménou, která je vybrána.](media/darwinbox-tutorial/DarwinboxMobile02.jpg)

1. Do aplikace Darwinbox zadejte svůj e-mail Azure AD a klikněte na **Další**.

    ![Snímek obrazovky zobrazující obrazovku Přihlásit se zvýrazněným tlačítkem Další](media/darwinbox-tutorial/DarwinboxMobile03.jpg)

1. Do aplikace Darwinbox zadejte heslo Azure AD a klikněte na **Přihlásit**se.

    ![Snímek obrazovky zobrazující obrazovku "zadání hesla" se zvýrazněným tlačítkem Další.](media/darwinbox-tutorial/DarwinboxMobile04.jpg)

1. Nakonec po úspěšném přihlášení se zobrazí domovská stránka aplikace.

    ![Mobilní aplikace Darwinbox](media/darwinbox-tutorial/DarwinboxMobile05.jpg)

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Darwinbox s Azure AD](https://aad.portal.azure.com/)

