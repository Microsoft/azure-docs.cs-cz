---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s kanálu ServiceChannel | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a kanálu ServiceChannel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.openlocfilehash: 46ef5dda412013dfa5c832472447ee5ec9179239
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92672746"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicechannel"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s kanálu ServiceChannel

V tomto kurzu se dozvíte, jak integrovat kanálu ServiceChannel s Azure Active Directory (Azure AD). Když integrujete kanálu ServiceChannel s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k kanálu ServiceChannel.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k kanálu ServiceChannel svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Kanálu ServiceChannel odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Kanálu ServiceChannel podporuje jednotné přihlašování **IDP** .
* Kanálu ServiceChannel podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-servicechannel-from-the-gallery"></a>Přidání kanálu ServiceChannel z Galerie

Pokud chcete nakonfigurovat integraci kanálu ServiceChannel do služby Azure AD, musíte přidat kanálu ServiceChannel z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace** .
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace** .
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **kanálu ServiceChannel** .
1. Na panelu výsledků vyberte **kanálu ServiceChannel** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicechannel"></a>Konfigurace a testování jednotného přihlašování Azure AD pro kanálu ServiceChannel

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí kanálu ServiceChannel pomocí testovacího uživatele s názvem **B. Simon** . Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v kanálu ServiceChannel.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí kanálu ServiceChannel, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte kanálu SERVICECHANNEL SSO](#configure-servicechannel-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte kanálu ServiceChannel Test User](#create-servicechannel-test-user)** -to, abyste měli protějšek B. Simon v kanálu ServiceChannel, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **kanálu ServiceChannel** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování** .
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML** .
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte hodnotu jako: `http://adfs.<domain>.com/adfs/service/trust`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<customer domain>.servicechannel.com/saml/acs`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Tady doporučujeme, abyste v identifikátoru použili jedinečnou hodnotu řetězce. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta kanálu ServiceChannel](https://servicechannel.zendesk.com/hc/en-us) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Deklarace role je předem nakonfigurovaná, takže ji nemusíte konfigurovat, ale je potřeba, abyste je ve službě Azure AD mohli vytvořit pomocí tohoto [článku](../develop/active-directory-enterprise-app-role-management.md). Další pokyny k deklaracím identity najdete [tady](https://servicechannel.zendesk.com/hc/articles/217514326-Azure-AD-Configuration-Example) : kanálu ServiceChannel příručka.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení kanálu ServiceChannel** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory** , vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé** .
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit** .

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k kanálu ServiceChannel.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace** .
1. V seznamu aplikace vyberte **kanálu ServiceChannel** .
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny** .

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-servicechannel-sso"></a>Konfigurace jednotného přihlašování kanálu ServiceChannel

Ke konfiguraci jednotného přihlašování na straně **kanálu ServiceChannel** je potřeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory kanálu ServiceChannel](https://servicechannel.zendesk.com/hc/en-us). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-servicechannel-test-user"></a>Vytvořit testovacího uživatele kanálu ServiceChannel

Aplikace podporuje zřizování uživatelů pouze v čase a po ověření budou automaticky vytvořena v aplikaci. Pro úplné zřizování uživatelů prosím kontaktujte [tým podpory kanálu ServiceChannel](https://servicechannel.zendesk.com/hc/).

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici kanálu ServiceChannel, měli byste se automaticky přihlásit k kanálu ServiceChannel, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si kanálu ServiceChannel s Azure AD](https://aad.portal.azure.com/)