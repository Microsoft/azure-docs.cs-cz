---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s využitím nadřízeného kamene | Microsoft Docs'
description: Naučte se konfigurovat jednotné přihlašování mezi Azure Active Directory a základním kamenem OnDemand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 2837f8c5b098b0502bd293ec2414ad7401cc4f95
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455200"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-ondemand"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) se základním kamenem OnDemand

V tomto kurzu se dozvíte, jak integrovat OnDemand OnDemand pomocí Azure Active Directory (Azure AD). Když integrujete základní platformu OnDemand s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k platformě OnDemand.
* Umožněte uživatelům, aby se automaticky přihlásili k platformě OnDemand OnDemand pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednoduchým přihlašováním (SSO) s podporou OnDemand.

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Základní kámen OnDemand podporuje jednotné přihlašování iniciované v **SP**
* Základ OnDemand OnDemand podporuje [automatizované zřizování uživatelů](cornerstone-ondemand-provisioning-tutorial.md)
* Po konfiguraci špičkové OnDemand můžete vyhovět řízení relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Přidání nadzákladu OnDemand z Galerie

Pokud chcete nakonfigurovat integraci základní platformy OnDemand do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat nadřízenou platformu OnDemand z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **kamene OnDemand** .
1. Z panelu výsledků vyberte základní **kámen OnDemand** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cornerstone-ondemand"></a>Konfigurace a testování jednotného přihlašování Azure AD pro základní platformu OnDemand

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s využitím OnDemand OnDemand pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v základním kamenem OnDemand.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD se základním kamenem OnDemand, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
2. **[Konfigurovat základní nastavení OnDemand SSO](#configure-cornerstone-ondemand-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
    1. **[Vytvoření uživatele](#create-cornerstone-ondemand-test-user)** s podporou vícefaktorového testování – Pokud chcete mít protějšek B. Simon v nadřízeném OnDemand, který je propojený s reprezentací uživatele v Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **OnDemand na základě kamenů** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<company>.csod.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<company>.csod.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pro získání těchto hodnot se obraťte na [tým podpory klientů s podporou OnDemand](mailto:moreinfo@csod.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavit OnDemand základní kámen** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k platformě OnDemand.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **základ OnDemand**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-cornerstone-ondemand-sso"></a>Konfigurace základu pro jednotné přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování na bázi **OnDemand OnDemand** , je potřeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal na [tým podpory pro OnDemand](mailto:moreinfo@csod.com)nadřízeného. Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-cornerstone-ondemand-test-user"></a>Vytvořit uživatele s OnDemandm základem pro testování

Cílem této části je vytvořit uživatele s názvem B. Simon v základním kameni OnDemand. Základní kámen OnDemand podporuje automatické zřizování uživatelů, které je ve výchozím nastavení povolené. Další podrobnosti najdete v [tématu](./cornerstone-ondemand-provisioning-tutorial.md) konfigurace automatického zřizování uživatelů.

**Pokud potřebujete ručně vytvořit uživatele, proveďte následující kroky:**

Pokud chcete nakonfigurovat zřizování uživatelů, pošlete informace (např.: jméno, E-mail) o uživateli Azure AD, kterého chcete zřídit [týmu podpory OnDemand](mailto:moreinfo@csod.com).

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů nebo rozhraní API pro základní platformu OnDemand OnDemand.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici OnDemand OnDemand na přístupovém panelu, měli byste se automaticky přihlášeni k OnDemand OnDemand, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si z Azure AD základ OnDemand.](https://aad.portal.azure.com)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)