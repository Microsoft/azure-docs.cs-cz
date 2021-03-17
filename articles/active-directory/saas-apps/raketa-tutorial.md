---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s raketa | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a raketa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: 649a26e474c7c4d6b4f51a035b3f8f9da35b9dd1
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92511150"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-raketa"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s raketa

V tomto kurzu se dozvíte, jak integrovat raketa s Azure Active Directory (Azure AD). Když integrujete raketa s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k raketa.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k raketa svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Raketa odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Raketa podporuje jednotné přihlašování iniciované v **SP** .
* Po nakonfigurování raketa můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-raketa-from-the-gallery"></a>Přidání raketa z Galerie

Pokud chcete nakonfigurovat integraci raketa do služby Azure AD, musíte přidat raketa z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** [1].

    ![rkt_1](./media/raketa-tutorial/azure-active-directory.png)

1. Přejděte k **podnikovým aplikacím** [2] a pak vyberte **všechny aplikace** [3].

1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace** [4]. 

    ![rkt_2](./media/raketa-tutorial/new-app.png)

1. V části **Přidat z Galerie** [5] zadejte **raketa** do vyhledávacího pole [6].

1. Vyberte **raketa** z panelu výsledků [7] a potom klikněte na tlačítko **Přidat** [8]. 

    ![rkt_3](./media/raketa-tutorial/add-btn.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-raketa"></a>Konfigurace a testování jednotného přihlašování Azure AD pro raketa

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí raketa pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v raketa.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí raketa, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte raketa SSO](#configure-raketa-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte raketa Test User](#create-raketa-test-user)** -to, abyste měli protějšek B. Simon v raketa, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **raketa** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování** [9].

    ![rkt_4](./media/raketa-tutorial/manage-sso.png)

1. Na stránce **metody výběru jednotného přihlašování** [9] vyberte **SAML** [10].

    ![rkt_5](./media/raketa-tutorial/saml.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** [11] a upravte nastavení.

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    1. Do textového pole **identifikátor (ID entity)** [12] a **přihlašovací adresa URL** [14] zadejte adresu URL: `https://raketa.travel/` .

    1. Do textového pole **Adresa URL odpovědi** [13] zadejte adresu URL pomocí následujícího vzoru: `https://raketa.travel/sso/acs?clientId=<CLIENT_ID>` .  

    ![rkt_6](./media/raketa-tutorial/enter-urls.png)

    > [!NOTE]
    > Hodnota adresy URL odpovědi není reálné číslo. Aktualizujte hodnotu skutečnou adresou URL odpovědi. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta raketa](mailto:help@raketa.travel) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** [15] a Stáhněte certifikát a uložte ho do počítače.

1. V části **Nastavení raketa** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    1. Přihlašovací adresa URL [16] – Adresa URL autorizační webové stránky, která se používá k přesměrování uživatelů na ověřovací systém.

    1. Identifikátor Azure AD [17] – identifikátor Azure AD.

    1. Odhlašovací adresa URL [18] – Adresa URL webové stránky, která se používá k přesměrování uživatelů po odhlášení

    ![rkt_7](./media/raketa-tutorial/copy-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte **Azure Active Directory** [1], vyberte **uživatele** [19] a pak vyberte **Všichni uživatelé** [20].

1. V horní části obrazovky vyberte **Nový uživatel** [21].

    ![rkt_8](./media/raketa-tutorial/new-user.png)

1. Ve vlastnostech **uživatele** proveďte následující kroky:

   1. Do pole **uživatelské jméno** [22] zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.

   1. Do pole **název** [23] zadejte `B.Simon` .

   1. Zaškrtněte políčko **Zobrazit heslo** [25] a pak zapište hodnotu, která se zobrazí v poli **heslo** [24].

   1. Klikněte na **vytvořit** [26]. 

    ![rkt_9](./media/raketa-tutorial/create-user.png)


### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k raketa.

1. V Azure Portal vyberte **podnikové aplikace** [2] a pak vyberte **všechny aplikace** [3].

1. V seznamu aplikace vyberte **raketa** [27].  

    ![rkt_10](./media/raketa-tutorial/add-raketa.png)

1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny** [28]. 

    ![rkt_11](./media/raketa-tutorial/users-groups.png)

1. Vyberte **Přidat uživatele** [29] a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** [30].

    ![rkt_12](./media/raketa-tutorial/add-user-raketa.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte **B. Simon** [31] ze seznamu uživatelů a pak klikněte na tlačítko **Vybrat** [32] v dolní části obrazovky.

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** [33]. 

    ![rkt_13](./media/raketa-tutorial/assign-user.png)

## <a name="configure-raketa-sso"></a>Konfigurace jednotného přihlašování raketa

Ke konfiguraci jednotného přihlašování na straně **raketa** je potřeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory raketa](mailto:help@raketa.travel). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-raketa-test-user"></a>Vytvořit testovacího uživatele raketa

V této části vytvoříte uživatele s názvem B. Simon v raketa. Pokud chcete přidat uživatele na platformě raketa, pracujte s [týmem podpory raketa](mailto:help@raketa.travel) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici raketa, měli byste se automaticky přihlásit k raketa, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si raketa s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit raketa pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)