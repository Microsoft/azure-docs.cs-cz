---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s NEOGOV | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a NEOGOV.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: 03efcd91553ab2235782d68cc70f4acdeedce4c6
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92506890"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-neogov"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s NEOGOV

V tomto kurzu se dozvíte, jak integrovat NEOGOV s Azure Active Directory (Azure AD). Když integrujete NEOGOV s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k NEOGOV.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k NEOGOV svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* NEOGOV odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* NEOGOV podporuje jednotné přihlašování **IDP** .

## <a name="adding-neogov-from-the-gallery"></a>Přidání NEOGOV z Galerie

Pokud chcete nakonfigurovat integraci NEOGOV do služby Azure AD, musíte přidat NEOGOV z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace** .
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace** .
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **NEOGOV** .
1. Na panelu výsledků vyberte **NEOGOV** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-neogov"></a>Konfigurace a testování jednotného přihlašování Azure AD pro NEOGOV

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí NEOGOV pomocí testovacího uživatele s názvem **B. Simon** . Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v NEOGOV.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí NEOGOV, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE NEOGOV SSO](#configure-neogov-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte NEOGOV Test User](#create-neogov-test-user)** -to, abyste měli protějšek B. Simon v NEOGOV, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **NEOGOV** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování** .
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML** .
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    | Prostředí | Vzor adresy URL |
    | -- | -- |
    | Výroba | `https://www.neogov.com/` |
    | Sandbox | `https://www.uat.neogov.net/` |
    | | |

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    | Prostředí | Vzor adresy URL |
    | -- | -- |
    | Výroba | `https://login.neogov.com/authentication/saml/consumer` |
    | Sandbox | `https://login.uat.neogov.net/authentication/saml/consumer` |
    | | |

1. NEOGOV aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName** . Aplikace NEOGOV očekává, že **NameIdentifier** má být namapován pomocí **User. objectID** , takže je nutné upravit mapování atributu kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![image](common/edit-attribute.png)

1. Kromě toho očekává aplikace NEOGOV několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Název |  Zdrojový atribut|
    | -------|--------- |
    | pošta | uživatel. pošta |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k NEOGOV.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace** .
1. V seznamu aplikace vyberte **NEOGOV** .
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny** .

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-neogov-sso"></a>Konfigurace jednotného přihlašování NEOGOV

Pokud chcete nakonfigurovat jednotné přihlašování na straně **NEOGOV** , musíte odeslat **adresu URL federačních metadat aplikace** [týmu podpory NEOGOV](mailto:itops@neogov.net). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-neogov-test-user"></a>Vytvořit testovacího uživatele NEOGOV

V této části vytvoříte uživatele s názvem B. Simon v NEOGOV. Pokud chcete přidat uživatele na platformě NEOGOV, pracujte s [týmem podpory NEOGOV](mailto:itops@neogov.net) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici NEOGOV, měli byste se automaticky přihlásit k NEOGOV, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si NEOGOV s Azure AD](https://aad.portal.azure.com/)