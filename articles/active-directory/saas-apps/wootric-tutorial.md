---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Wootric | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Wootric.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2020
ms.author: jeedes
ms.openlocfilehash: a959dd3615c9498411b141356420939ad7026189
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92638359"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wootric"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Wootric

V tomto kurzu se dozvíte, jak integrovat Wootric s Azure Active Directory (Azure AD). Když integrujete Wootric s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Wootric.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Wootric svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Wootric odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Wootric podporuje jednotné přihlašování **IDP** .
* Wootric podporuje zřizování uživatelů **jenom v čase** .
* Po nakonfigurování Wootric můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wootric-from-the-gallery"></a>Přidání Wootric z Galerie

Pokud chcete nakonfigurovat integraci Wootric do služby Azure AD, musíte přidat Wootric z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Wootric** .
1. Na panelu výsledků vyberte **Wootric** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-wootric"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Wootric

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Wootric pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Wootric.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Wootric, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte WOOTRIC SSO](#configure-wootric-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Wootric Test User](#create-wootric-test-user)** -to, abyste měli protějšek B. Simon v Wootric, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Wootric** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní sekci konfigurace SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .


1. Wootric aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace Wootric několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Name |  Zdrojový atribut |
    | -------------- | --------- |
    | id | User. objectID |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Wootric** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Wootric.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Wootric**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-wootric-sso"></a>Konfigurace jednotného přihlašování Wootric

1. Přihlaste se k Wootric v jiném okně prohlížeče jako správce.

1. V horní nabídce klikněte na **ikona nastavení** .

    ![Snímek obrazovky se zobrazí ikona nastavení vybraná z webu Wootric.](./media/wootric-tutorial/configure-1.PNG)

1. V části **integrace** vyberte v nabídce na levé straně **ověřování** a klikněte na **povolit jednotné přihlašování pomocí Azure Active Directory**.

    ![Snímek obrazovky ukazuje povolení jednotného přihlašování s Azure Active Directory připojenou v položce ověřování.](./media/wootric-tutorial/configure-2.PNG)

1. Na následující stránce proveďte následující kroky:

    ![Snímek obrazovky se zobrazí stránka nastavení, kde můžete zadat hodnoty, které jsou popsány.](./media/wootric-tutorial/configure-3.PNG)

    a. Do textového pole **URL s jedním Sign-On URL poskytovatele identity** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **vystavitele zprostředkovatele identity** vložte hodnotu **ID entity** , kterou jste zkopírovali z Azure Portal.

    c. Otevřete stažený **certifikát (Base64)** z Azure Portal do programu Poznámkový blok a vložte obsah do textového pole **certifikátu X. 509** .

    d. Zaškrtněte políčko **automaticky udělit přístup k novým uživatelům** .
    
    e. Klikněte na **Uložit**.

### <a name="create-wootric-test-user"></a>Vytvořit testovacího uživatele Wootric

V této části se v Wootric vytvoří uživatel s názvem B. Simon. Wootric podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Wootric neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Wootric, měli byste se automaticky přihlásit k Wootric, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Wootric s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit Wootric pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)