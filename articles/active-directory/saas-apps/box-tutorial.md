---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s rámečkem | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a box.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: jeedes
ms.openlocfilehash: 8045076ce77e98345c0db3cd19293b4ef76293e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88548183"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-box"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s rámečkem

V tomto kurzu se naučíte, jak integrovat box s Azure Active Directory (Azure AD). Když integruje box s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k boxu.
* Umožněte uživatelům, aby se automaticky přihlásili k poli s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Přihlaste se k odběru s povoleným jednotném přihlašováním (SSO).

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Box podporuje jednotné přihlašování spouštěné v **SP**
* Box podporuje [ **automatizované** zřizování a rušení zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/box-userprovisioning-tutorial) (doporučeno)
* Box podporuje zřizování uživatelů **jenom v čase** .
* Po nakonfigurování pole můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-box-from-the-gallery"></a>Přidávání boxu z Galerie

Pokud chcete nakonfigurovat integraci boxu do Azure AD, musíte přidat box z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **pole** .
1. Vyberte **pole** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-box"></a>Konfigurace a testování jednotného přihlašování Azure AD pro box

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí boxu s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v poli.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s polem, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat jednotné přihlašování](#configure-box-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořit testovacího uživatele box](#create-box-test-user)** – Pokud chcete mít protějšek B. Simon v krabici, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **box** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.account.box.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `box.net`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL: `https://sso.services.box.net/sp/ACS.saml2`

    > [!NOTE]
    > Hodnota přihlašovací adresy URL není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. [Tým podpory pro pracovníky oddělení](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) IT, který vám poskytne hodnotu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k poli.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **box**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-box-sso"></a>Nakonfigurovat jednotné přihlašování k boxu

Pokud chcete nakonfigurovat jednotné přihlašování pro vaši aplikaci, postupujte podle pokynů v části [nastavení jednotného přihlašování](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown).

> [!NOTE]
> Pokud nemůžete nakonfigurovat nastavení jednotného přihlašování pro váš účet box, musíte odeslat stažený [tým podpory](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) **federačních metadat XML** pro box. Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-box-test-user"></a>Vytvořit testovacího uživatele boxu

V této části se v krabici vytvoří uživatel s názvem Britta Simon. Box podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povoleno. V této části není žádná položka akce. Pokud uživatel v boxu ještě neexistuje, vytvoří se po ověření nový.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire).

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici, měli byste se automaticky přihlásit k poli, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Zkusit box s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chránit krabici pomocí pokročilých viditelností a ovládacích prvků](https://docs.microsoft.com/cloud-app-security/protect-box)