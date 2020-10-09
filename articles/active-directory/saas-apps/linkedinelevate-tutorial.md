---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) se zvýšením úrovně LinkedInu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a se zvýšením úrovně LinkedInu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.openlocfilehash: 5dcd705719510cce3a113c5b0fdc46d016313b6d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856402"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>Kurz: Azure Active Directory integrace jednotného přihlašování se zvýšením úrovně LinkedInu

V tomto kurzu se naučíte integrovat zvýšení úrovně LinkedInu pomocí Azure Active Directory (Azure AD). Když integrujete zvýšení úrovně LinkedInu v Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke zvýšení oprávnění LinkedInu
* Umožněte, aby se vaši uživatelé automaticky přihlásili k LinkedInu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* LinkedIn zvyšuje předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.



* Zvýšení oprávnění LinkedInu podporuje **SP a IDP** iniciované jednotné přihlašování
* Zvýšení oprávnění LinkedInu podporuje **jenom při** zřizování uživatelů
* Zvýšení oprávnění LinkedInu podporuje [ **automatizované** zřizování uživatelů](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Přidání zvýšení oprávnění LinkedInu z Galerie

Pokud chcete nakonfigurovat integraci LinkedInu v Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat zvýšení úrovně LinkedInu z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **zvýšení oprávnění LinkedInu** .
1. Vyberte možnost **zvýšení úrovně LinkedInu** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-elevate"></a>Konfigurace a testování jednotného přihlašování Azure AD pro zvýšení oprávnění LinkedInu

Nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí LinkedInového zvýšení pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem ve zvýšení oprávnění LinkedInu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí zvýšení oprávnění LinkedInu, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat oprávnění LinkedInu pro jednotné přihlašování](#configure-linkedin-elevate-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvoření testovacího uživatele zvýšení oprávnění LinkedInu](#create-linkedin-elevate-test-user)** – Pokud chcete mít protějšek B. Simon ve zvýšení úrovně LinkedInu, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace na webu **LinkedIn – zvýšení úrovně** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **Identifier (identifikátor** ) zadejte hodnotu **ID entity** , kterou zkopírujete z portálu LinkedIn, který se vysvětluje dále v tomto kurzu.

    b. Do textového pole **Adresa URL odpovědi** zadejte hodnotu **adresy URL pro řízení přístupu uživatele (ACS)** . na portálu LinkedIn je vysvětleno později v tomto kurzu.

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

1. Žádost o zvýšení úrovně LinkedInu očekává, že se kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName**. Žádost o zvýšení úrovně LinkedInu očekává, že NameIdentifier budou namapovány pomocí **User. mail**, takže potřebujete upravit mapování atributů kliknutím na ikonu Upravit a změnit mapování atributů.

    ![image](common/edit-attribute.png)

1. Kromě toho bude aplikace LinkedIn zvýšení oprávnění v odpovědi SAML, která je uvedena níže, očekává několik atributů, které se mají znovu předat. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Zdrojový atribut|
    | -------| -------------|
    | Oddělení | User. Department |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavit zvýšení oprávnění LinkedInu** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup ke zvýšení oprávnění LinkedInu.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Prozvýšit úroveň LinkedInu**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-linkedin-elevate-sso"></a>Konfigurace zvýšení oprávnění LinkedInu pro jednotné přihlašování

1. V jiném okně webového prohlížeče se přihlaste do svého tenanta LinkedInu jako správce.

1. V **centru účtů**klikněte na **globální nastavení** v části **Nastavení**. Také v rozevíracím seznamu vyberte **zvýšit úroveň testu AAD** .

    ![Snímek obrazovky ukazuje globální nastavení, kde můžete vybrat možnost zvýšit úroveň testu na D.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Kliknutím **nebo kliknutím sem načtěte a zkopírujte jednotlivá pole z formuláře** a proveďte následující kroky:

    ![Snímek obrazovky zobrazuje jednu Sign-On, kde můžete zadat hodnoty, které jsou popsány.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Zkopírujte **ID entity** a vložte ho do textového pole **identifikátor** v **základní konfiguraci SAML** v Azure Portal.

    b. Zkopírujte **adresu URL pro přístup uživatelů kontrolního výrazu (ACS)** a vložte ji do textového pole **Adresa URL odpovědi** v **základní konfiguraci SAML** v Azure Portal.

1. Přejít na část **Nastavení správce LinkedIn** . Kliknutím na možnost nahrát soubor XML nahrajte soubor XML, který jste stáhli z Azure Portal.

    ![Snímek obrazovky ukazuje konfiguraci nastavení poskytovatele služby LinkedIn Service S příponami v, kde můžete nahrát soubor X M L.](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Kliknutím **na** povolíte jednotné přihlašování. Stav jednotného přihlašování se změní z **nepřipojeného** k **připojenému**

    ![Snímek obrazovky zobrazuje jednu Sign-On, kde můžete vybrat automaticky přiřadit licence.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-elevate-test-user"></a>Vytvořit testovacího uživatele ve zvýšení úrovně LinkedIn

Aplikace pro zvýšení úrovně LinkedIn podporuje při zřizování uživatelů jenom v čase a po ověření, že se uživatelé budou automaticky vytvářet v aplikaci. Na stránce nastavení pro správu na portálu pro přístup LinkedInu na webu překlopte přepínač **automaticky přiřazovat licence** k aktivnímu způsobu zřizování a k tomu se taky přiřadí licence uživateli. Zvýšení oprávnění LinkedInu podporuje taky Automatické zřizování uživatelů. Další podrobnosti najdete [tady](linkedinelevate-provisioning-tutorial.md) , jak nakonfigurovat automatické zřizování uživatelů.

   ![Vytvoření testovacího uživatele Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici zvýšení úrovně LinkedInu na přístupovém panelu, měli byste se automaticky přihlásit ke zvýšení oprávnění LinkedInu, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si LinkedIn ve zvýšení úrovně pomocí Azure AD](https://aad.portal.azure.com/)

