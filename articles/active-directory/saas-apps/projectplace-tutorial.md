---
title: 'Kurz: Azure Active Directory integrace s Projectplace | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Projectplace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.openlocfilehash: 508a3ca02ec6c0475e78d16691f6597e1a389f97
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92511847"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Kurz: integrace Projectplace s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Projectplace s Azure Active Directory (Azure AD). Když integrujete Projectplace s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Projectplace.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Projectplace svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.
* Uživatele je možné zřídit v Projectplace automaticky.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Projectplace odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Projectplace podporuje **aktualizace SP a IDP** , které iniciovaly jednotné přihlašování, a podporuje **pouze dobu** zřizování uživatelů.

## <a name="adding-projectplace-from-the-gallery"></a>Přidání Projectplace z Galerie

Pokud chcete nakonfigurovat integraci Projectplace do služby Azure AD, musíte přidat Projectplace z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Projectplace** .
1. Na panelu výsledků vyberte **Projectplace** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Projectplace pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Projectplace.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Projectplace, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte Projectplace](#configure-projectplace)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
4. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
5. **[Vytvořte uživatele Projectplace test](#create-projectplace-test-user)** , který bude mít protějšek B. Simon v Projectplace, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Projectplace** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , je v **základní části Konfigurace SAML** předem nakonfigurovaná aplikace a nezbytné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://service.projectplace.com`

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na Kopírovat **ikonu** a zkopírujte **adresu URL federačních metadat aplikace**podle vašich požadavků a uložte je do poznámkového bloku.

   ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

1. V části **Nastavení Projectplace** zkopírujte na základě vašeho požadavku příslušné adresy URL.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Konfigurace Projectplace

Pokud chcete nakonfigurovat jednotné přihlašování na straně **Projectplace** , musíte poslat zkopírovanou **adresu URL federačních metadat aplikace** z Azure Portal [týmu podpory Projectplace](https://success.planview.com/Projectplace/Support). Tento tým zajišťuje správné nastavení připojení SAML SSO na obou stranách.

>[!NOTE]
>Konfiguraci jednotného přihlašování je nutné provést v rámci [týmu podpory Projectplace](https://success.planview.com/Projectplace/Support). Jakmile se konfigurace dokončí, dostanete oznámení. 

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B. Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `BrittaSimon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Projectplace.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Projectplace**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-projectplace-test-user"></a>Vytvořit testovacího uživatele Projectplace

>[!NOTE]
>Pokud je zřizování povoleno v Projectplace, můžete tento krok přeskočit. Můžete požádat [tým podpory Projectplace](https://success.planview.com/Projectplace/Support) , aby povolil provisoning. Jakmile se uživatelé po prvním přihlášení vytvoří v Projectplace, budou vytvořeni.

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili k Projectplace, musíte je přidat do Projectplace. Musíte je přidat ručně.

**Chcete-li vytvořit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu **Projectplace** společnosti jako správce.

2. Přejít na **lidi**a pak vybrat **členy**:
   
    ![Přejít na lidi a pak vybrat členy](./media/projectplace-tutorial/ic790228.png "People")

3. Vyberte **Přidat člena**:
   
    ![Vyberte Přidat člena.](./media/projectplace-tutorial/ic790232.png "Přidat členy")

4. V části **Přidat člena** proveďte následující kroky.
   
    ![Přidat člena – oddíl](./media/projectplace-tutorial/ic790233.png "Noví členové")
   
    1. Do pole **noví členové** zadejte e-mailovou adresu platného účtu Azure AD, který chcete přidat.
   
    1. Vyberte **Poslat**.

   E-mail s odkazem na potvrzení účtu se pošle na držitele účtu Azure AD.

>[!NOTE]
>K přidání uživatelských účtů Azure AD můžete použít taky jakýkoli jiný nástroj pro vytváření uživatelských účtů nebo rozhraní API, které poskytuje Projectplace.


### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici Projectplace, měli byste se automaticky přihlásit k Projectplace, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)