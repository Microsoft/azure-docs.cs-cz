---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s cestovném a výdajem na Concur | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Concur a výdajem na cestování.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.openlocfilehash: 525c70c18354e35998e564680c68a975bdb3ec54
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455438"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Concur cestovném a výdaji

V tomto kurzu se dozvíte, jak integrovat Concur cestovné a výdaje pomocí Azure Active Directory (Azure AD). Když integrujete Concur cestovné a náklady s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Concur Cestovnému a výdajům.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Concur Cestovnému a výdajům pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Concur cestovné a výdajové předplatné.
* Role "Správce společnosti" v rámci uživatelského účtu Concur. V případě, že máte správný přístup, se můžete pokusit přejít na [Concur SSO Self-Service Tool](https://www.concursolutions.com/nui/authadmin/ssoadmin). Pokud nemáte přístup, kontaktujte prosím podporu Concur nebo správce projektu implementace. 

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD.

* Concur cestovné a výdaje podporují jednotné přihlašování **IDP** a **SP**
* Concur cestovné a výdaje podporují testování jednotného přihlašování v produkčním i implementačním prostředí. 

> [!NOTE]
> Identifikátor této aplikace je pevná řetězcová hodnota pro každou ze tří oblastí: US, EMEA a Čína. Proto lze pro každou oblast v jednom tenantovi nakonfigurovat pouze jednu instanci. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Přidání cestovného Concur a výdajů z Galerie

Pokud chcete nakonfigurovat integraci Concur cestovného a výdajů do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Concur cestovné a náklady z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Concur cestovné a výdaje** .
1. Na panelu výsledků vyberte **Concur cestovné a výdaje** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Concur cestovné a výdaje

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Concur cestovného a výdajů pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Concur cestovné a výdaje.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s Concur cestovném a výdaji, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Concur cestovné a přihlašování výdajů](#configure-concur-travel-and-expense-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Concur cestovné a uživatele testovacího prostředí](#create-concur-travel-and-expense-test-user)** , abyste měli protějšek B. Simon v Concur cestování a náklady, které jsou propojené s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **Concur cestovné a výdaje** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základním oddílu konfigurace SAML** je aplikace předem nakonfigurovaná v režimu iniciované **IDP** a nezbytné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

    > [!NOTE]
    > Identifikátor (ID entity) a adresa URL odpovědi (adresa URL služby assertion Consumer Service) jsou specifické pro oblast. Vyberte prosím na základě datacentra vaší entity Concur. Pokud si nejste jisti datacentrem vaší entity Concur, obraťte se prosím na podporu Concur. 

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na atribut pro úpravy nebo ikonu pera u **atributu uživatel** a upravte nastavení. Jedinečný identifikátor uživatele musí odpovídat Concur User login_id. Obvykle byste měli změnit **User. userPrincipalName** na **User. mail**.

    ![Upravit atribut uživatele](common/edit-attribute.png)

6. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte si metadata a uložte je do počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Concur Cestovnému a výdajům.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Concur cestovné a výdaje**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-concur-travel-and-expense-sso"></a>Konfigurace cestovného Concur a jednotného přihlašování k výdajům

1. Ke konfiguraci jednotného přihlašování na straně **Concur cestovné a výdaje** je potřeba nahrát stažená **metadata XML federačních metadat** do [nástroje Concur SSO Self-Service](https://www.concursolutions.com/nui/authadmin/ssoadmin) a přihlásit se pomocí účtu s rolí správce společnosti. 

1. Klikněte na **Přidat**.
1. Zadejte vlastní název pro IdP, například Azure AD (US). 
1. Klikněte na **Odeslat soubor XML** a připojte **XML federačních metadat** , které jste si stáhli dřív.
1. Kliknutím na **Přidat metadata** uložte změnu.

    ![Snímek obrazovky samoobslužného nástroje Concur SSO](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Vytvoření Concur cestovného a uživatele testu výdajů

V této části vytvoříte uživatele s názvem B. Simon v Concur cestovné a výdaje. Pracujte s týmem podpory Concur a přidejte uživatele na Concurovou platformu pro cestování a výdaje. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele. 

> [!NOTE]
> B. ID přihlášení Concur Simon musí odpovídat jedinečnému identifikátoru B. Simon ve službě Azure AD. Například pokud je B. Simon Azure AD Unique identifikátorem je `B.Simon@contoso.com` . B. ID přihlášení Concur Simon musí být `B.Simon@contoso.com` také. 

## <a name="configure-concur-mobile-sso"></a>Konfigurace mobilního přihlašování Concur
Pokud chcete povolit Concur Mobile SSO, musíte zadat **adresu URL přístupového uživatele**týmu podpory Concur. Pomocí následujících kroků získáte **adresu URL přístupu uživatele** ze služby Azure AD:
1. Přejít k **podnikovým aplikacím**
1. Klikněte na **Concur cestovné a výdaje**
1. Klikněte na **vlastnosti** .
1. Kopírování **adresy URL přístupu uživatele** a udělení této adresy URL podpoře Concur

> [!NOTE]
> Možnost konfigurace jednotného přihlašování (SSO) Self-Service není k dispozici, pokud chcete povolit mobilní jednotné přihlašování, pracujte s týmem podpory Concur. 

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Concur cestovné a výdaje na přístupovém panelu, měli byste se automaticky přihlásit k Concur Cestovnému a výdajům, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte Concur cestovné a výdaje pomocí Azure AD](https://aad.portal.azure.com/)