---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s SynchroNet klikněte na | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SynchroNet kliknutím.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2020
ms.author: jeedes
ms.openlocfilehash: 7aec0a7ff2e8dfdfca93971e01f8ed13af34acc6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92517850"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-synchronet-click"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) pomocí SynchroNet kliknutí

V tomto kurzu se naučíte, jak integrovat SynchroNet klikněte na Azure Active Directory (Azure AD). Když integrujete SynchroNet KLIKNete na Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SynchroNet kliknutí
* Umožněte, aby se vaši uživatelé automaticky přihlásili k SynchroNet kliknutím na své účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* SynchroNet klikněte na odběr s povoleným jednotným přihlašováním (SSO).

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SynchroNet klikněte na možnost podporuje jednotné přihlašování v **SP**
* Po nakonfigurování SynchroNet kliknutím můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-synchronet-click-from-the-gallery"></a>Přidání SynchroNet kliknutím z Galerie

Pokud chcete nakonfigurovat integraci SynchroNet kliknutím na Azure AD, musíte přidat SynchroNet kliknutím z Galerie na svůj seznam spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte SynchroNet do vyhledávacího pole **klikněte** .
1. Vyberte **SynchroNet klikněte na** panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-synchronet-click"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SynchroNet klikněte na

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SynchroNet klikněte na použít testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SynchroNet klikněte na.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SynchroNet, klikněte na dokončit následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace SynchroNet klikněte na SSO](#configure-synchronet-click-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvoření SynchroNet klikněte na testovací uživatel](#create-synchronet-click-test-user)** – Pokud chcete mít protějšek B. Simon v SynchroNet, klikněte na odkaz, který je propojený s Předprezentací Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **SynchroNet klikněte na možnost** integrace aplikace, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://click.synchronet.com`

1. SynchroNet klikněte na aplikace očekává kontrolní výrazy SAML v určitém formátu, který vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **EmailAddress** je mapován pomocí **User. mail**. SynchroNet klikněte na aplikace očekává, že **EmailAddress** se namapuje pomocí **User. userPrincipalName**, takže potřebujete upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![image](common/edit-attribute.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k SynchroNet kliknutí.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SynchroNet klikněte na**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-synchronet-click-sso"></a>Konfigurace SynchroNet klikněte na jednotné přihlašování.

Pokud chcete nakonfigurovat jednotné přihlašování na **SynchroNet, klikněte** na stranu. Pokud chcete [SynchroNet kliknout na tým podpory](mailto:tickets@synchronet.com), musíte poslat **adresu URL federačních metadat aplikace** . Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-synchronet-click-test-user"></a>Vytvořit SynchroNet klikněte na testovací uživatel.

V této části vytvoříte uživatele s názvem Britta Simon v SynchroNet KLIKNete na. Pracujte s [SynchroNet kliknutím na tým podpory](mailto:tickets@synchronet.com) pro přidání uživatelů v SynchroNet klikněte na platformu. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici SynchroNet kliknout na přístupovém panelu, měli byste se automaticky přihlásit k SynchroNet kliknutím na které jste si nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte SynchroNet kliknutím na Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Postup ochrany SynchroNet kliknutí s pokročilou viditelností a ovládacími prvky](/cloud-app-security/proxy-intro-aad)