---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s jedním Sign-Onm kamenem | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotného přihlašování.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2021
ms.author: jeedes
ms.openlocfilehash: ba6eb0a1b607fc05c4d0c660dd3d7016f81ef4b3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449476"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-single-sign-on"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s jednoduchým základem Sign-On

V tomto kurzu se dozvíte, jak integrovat jednotné Sign-On s Azure Active Directory (Azure AD). Při integraci jednotného Sign-On s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k jednotnému přihlašování na základě kamenů
* Umožněte uživatelům, aby se automaticky přihlásili k základním Sign-Onům pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s jednou Sign-On jednotným přihlašováním (SSO) s podporou jednotného přihlašování.

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Základní Sign-On jednotného přihlašování s podporou **SP** .
* Jednoduchá podpora pro uživatele v základní Sign-On podporuje [automatizované zřizování uživatelů](cornerstone-ondemand-provisioning-tutorial.md).


## <a name="adding-cornerstone-single-sign-on-from-the-gallery"></a>Přidání jednoho Sign-Onového kamene z Galerie

Pokud chcete nakonfigurovat integraci samostatného Sign-On do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat základní Sign-On z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **jednotné přihlašování** .
1. Vyberte možnost **jednotné přihlašování** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-cornerstone-single-sign-on"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro samostatný Sign-On

Konfigurace a testování jednotného přihlašování služby Azure AD pomocí jednotného základu Sign-On pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci jednotného přihlašování na základě kamenů.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s jednotným přihlašováním, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
2. **[Nakonfigurovat jednotné základní Sign-On jednotné přihlašování](#configure-cornerstone-single-sign-on-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace
    1. **[Vytvořit jednoho Sign-Onho testovacího uživatele](#create-cornerstone-single-sign-on-test-user)** pro každého nadřízeného, aby měl protějšek B. Simon v základní Sign-On, která je propojená s reprezentací uživatele v Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **jednotného přihlašování** k aplikaci, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<PORTAL_NAME>.csod.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    c. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi, identifikátoru a adresy URL pro přihlášení. Pokud chcete získat tyto hodnoty, obraťte se na [jednoho Sign-On tým podpory klientů](mailto:moreinfo@csod.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavení jednotného přihlašování** pro základní kámen zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k jednotnému přihlašování.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte základní **jednotné přihlašování**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-cornerstone-single-sign-on-sso"></a>Konfigurace jednotného Sign-On jednotného přihlašování

Chcete-li nakonfigurovat jednotné přihlašování na straně **jednotného přihlašování** , je třeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal k [jednomu Sign-On týmu podpory](mailto:moreinfo@csod.com) nebo se obraťte na svého partnera. Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-cornerstone-single-sign-on-test-user"></a>Vytvořit jednoho Sign-On testovacího uživatele v základním základu

Cílem této části je vytvořit uživatele s názvem B. Simon v rámci jednotného přihlašování od základu. Základní základní Sign-On podporuje automatické zřizování uživatelů, které je ve výchozím nastavení povolené. Další podrobnosti najdete v [tématu](./cornerstone-ondemand-provisioning-tutorial.md) konfigurace automatického zřizování uživatelů.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na základní adresu URL jednotného Sign-On pro přihlášení, kde můžete spustit tok přihlášení. 

* Přejít na základní Sign-On adresu URL jednotného přihlašování přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Sign-On v okně moje aplikace na základní kámen, tato akce se přesměruje na zákrokovou adresu URL jednotného přihlašování Sign-On. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete základní Sign-On, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)