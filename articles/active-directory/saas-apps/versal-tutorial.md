---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) k průřezu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a v angličtině.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: ae966e56b0ea679f20445648926ce2beccb9f080
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88532044"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-versal"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) k průřezu

V tomto kurzu se dozvíte, jak integrovat průřezy s Azure Active Directory (Azure AD). Když integrujete průřez s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k průřezu
* Umožněte uživatelům, aby se automaticky přihlásili k průřezu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr povoleného jednoduchého jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.


* Průřez podporuje **IDP** , které bylo iniciováno pro jednotné přihlašování

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-versal-from-the-gallery"></a>Přidání průřezu z Galerie

Pokud chcete nakonfigurovat integraci průřezu do Azure AD, musíte přidat do seznamu spravovaných aplikací SaaS průřez z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** **Zadejte do** vyhledávacího pole text.
1. Vyberte možnost **příčně** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-versal"></a>Konfigurace a testování jednotného přihlašování Azure AD pro průřez

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí průřezu pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Bruselu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s použitím tohoto průřezu, vyplňte následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurovat průřez jednotného přihlašování](#configure-versal-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořit průřezový testovací uživatel](#create-versal-test-user)** – Pokud chcete mít protějšek B. Simon v průřezu, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce věnované integraci **aplikací Najděte** část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL: `VERSAL`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://versal.com/sso/saml/orgs/<organization_id>`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL odpovědi. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory pro průřezy klientů](https://support.versal.com/hc/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Průřezová aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName**. V případě, že je **NameIdentifier** namapována pomocí **User. mail**, je třeba upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![image](common/edit-attribute.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavit v průřezu** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k průřezu.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **příčně**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-versal-sso"></a>Konfigurovat průřez jednotného přihlašování

Ke konfiguraci jednotného přihlašování na bázi **na levé straně** je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL od Azure Portal k [týmu pro průřezy podpory](https://support.versal.com/hc/). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-versal-test-user"></a>Vytvořit průřez testovacího uživatele

V této části vytvoříte uživatele s názvem B. Simon v oblasti v průřezu. Postup vytvoření uživatele B. Simon ve vaší organizaci najdete v průvodci podporou [testování uživatelských služeb SAML](https://support.versal.com/hc/articles/115011672887-Creating-a-SAML-test-user) . Aby bylo možné použít jednotné přihlašování, musí být uživatelé vytvořeni a aktivováni v průřezu. 

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí kurzu vloženého do svého webu na průřez.
Pokyny k vložení kurzu s podporou jednotného přihlašování pomocí služby Azure AD najdete v tématu věnovaném vložení kurzu pro podporu **jednotného přihlašování** v rámci [organizačních kurzů](https://support.versal.com/hc/articles/203271866-Embedding-organizational-courses) SAML. 

Budete muset vytvořit kurz, sdílet ho s vaší organizací a publikovat ho za účelem testování vkládání kurzů. Další informace najdete v tématu [Vytvoření kurzu](https://support.versal.com/hc/articles/203722528-Create-a-course), [publikování kurzu](https://support.versal.com/hc/articles/203753398-Publishing-a-course)a [Správa informací](https://support.versal.com/hc/articles/206029467-Course-and-learner-management) o kurzu.

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si v Azure AD průřez](https://aad.portal.azure.com/)

