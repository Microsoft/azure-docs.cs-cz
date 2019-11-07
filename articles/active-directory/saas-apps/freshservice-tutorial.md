---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Freshservice | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3ba2f59799629873553fe261abebf59529ad94d
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "73570615"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Freshservice

V tomto kurzu se dozvíte, jak integrovat Freshservice s Azure Active Directory (Azure AD). Když integrujete Freshservice s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Freshservice.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Freshservice svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Freshservice odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Freshservice podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-freshservice-from-the-gallery"></a>Přidání Freshservice z Galerie

Pokud chcete nakonfigurovat integraci Freshservice do služby Azure AD, musíte přidat Freshservice z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Freshservice** .
1. Na panelu výsledků vyberte **Freshservice** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Freshservice

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Freshservice pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Freshservice.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Freshservice, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte FRESHSERVICE SSO](#configure-freshservice-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Freshservice Test User](#create-freshservice-test-user)** -to, abyste měli protějšek B. Simon v Freshservice, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Freshservice** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<democompany>.freshservice.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Freshservice](https://support.freshservice.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. Freshservice vyžaduje k získání jednotného přihlašování otisk otisku SHA-256. K získání otisku prstu SHA-256 proveďte následující kroky:

    Otisk ![otisku prstu](./media/freshservice-tutorial/ic790821.png "")

    1. Otevřete [odkaz](https://www.samltool.com/fingerprint.php) v jiném webovém prohlížeči.

    1. Otevřete stažený soubor certifikátu (Base64) v programu Poznámkový blok a vložte obsah do textového pole **certifikát X. 509** .

    1. V poli algoritmus vyberte **SHA256** z rozevíracího seznamu.

    1. Klikněte na **Vypočítat otisk prstu**.

    1. Kliknutím na ikonu Kopírovat zkopírujte vygenerovaný **otisk prstu** a uložte ho do svého počítače.

1. V části **Nastavení Freshservice** na **Azure Portal**zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Freshservice.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Freshservice**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-freshservice-sso"></a>Konfigurace jednotného přihlašování Freshservice

1. Pokud chcete automatizovat konfiguraci v rámci Freshservice, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Freshservice nastavení** a nasměrujte vás na aplikaci Freshservice. Odtud zadejte přihlašovací údaje správce, které se přihlásí k Freshservice. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit Freshservice ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu Freshservice společnosti jako správce a proveďte následující kroky:

4. V nabídce v horní části klikněte na **správce**.

    ![](./media/freshservice-tutorial/ic790814.png "Správce") správy

5. Na **portálu Customer**klikněte na **zabezpečení**.

    ![](./media/freshservice-tutorial/ic790815.png "Zabezpečení") zabezpečení

6. V části **zabezpečení** proveďte následující kroky:

    (./media/freshservice-tutorial/ic790816.png "Jednotné") přihlašování jednotného ![přihlašování]

    a. Přepnout **jednotné přihlašování**

    b. Vyberte **jednotné přihlašování SAML**.

    c. Do textového pole **Adresa URL pro přihlášení SAML** vložte hodnotu **přihlašovací adresa URL**, kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **Adresa URL pro odhlášení** vložte hodnotu **URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

    e. Do textového pole **otisk otisku certifikátu zabezpečení** vložte hodnotu **otisku prstu** , kterou jste vygenerovali dříve.

    f. Klikněte na **Uložit**.

### <a name="create-freshservice-test-user"></a>Vytvořit testovacího uživatele Freshservice

Aby se uživatelé Azure AD mohli přihlašovat k FreshService, musí se zřídit v FreshService. V případě FreshService je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu **FreshService** společnosti jako správce.

2. V nabídce v horní části klikněte na **správce**.

    ![](./media/freshservice-tutorial/ic790814.png "Správce") správy

3. V části **Správa uživatelů** klikněte na možnost **žadatelé**.

    ![](./media/freshservice-tutorial/ic790818.png "Žadatelé") žadatele

4. Klikněte na **Nový žadatel**.

    ![Noví noví](./media/freshservice-tutorial/ic790819.png "žadatelé")

5. V části **Nový žadatel** proveďte následující kroky:

    Nový žadatel ![nového žadatele](./media/freshservice-tutorial/ic790820.png "")  

    a. Zadejte **jméno** a **e-mailovou** adresu platného Azure Active Directory účtu, který chcete zřídit do souvisejících textových polí.

    b. Klikněte na **Uložit**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory získá e-mail, včetně odkazu na potvrzení účtu, než bude aktivní.
    >  

> [!NOTE]
> K zřizování uživatelských účtů AAD můžete použít jiné nástroje pro vytváření uživatelských účtů FreshService nebo rozhraní API poskytovaná FreshService.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Freshservice, měli byste se automaticky přihlásit k Freshservice, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Freshservice s Azure AD](https://aad.portal.azure.com/)