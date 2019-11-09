---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Dome9em Check Point CloudGuard | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Check Point CloudGuard Dome9 ARC.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b46ac34db21576c7e2de2271a468e3e782ff6aa9
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885351"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Dome9em Check Point CloudGuard

V tomto kurzu se dozvíte, jak integrovat kontrolní bod CloudGuard Dome9 ARC pomocí Azure Active Directory (Azure AD). Když integrujete službu Check Point CloudGuard Dome9 ARC s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke kontrolnímu bodu CloudGuard Dome9 ARC.
* Umožněte uživatelům, aby se automaticky přihlásili k kontrolnímu bodu CloudGuard Dome9 ARC pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Check Point CloudGuard Dome9 ARC pro jednotné přihlašování (SSO) s povoleným odběrem.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Check Point CloudGuard Dome9 ARC podporuje **SP a IDP** iniciované jednotné přihlašování.

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Přidání CloudGuardu kontrolního bodu Dome9 ARC z Galerie

Pokud chcete nakonfigurovat integraci Check Point CloudGuard Dome9 ARC do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat z Galerie položku Check Point CloudGuard Dome9 ARC.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte text **Check Point cloudguard Dome9 ARC** .
1. Z panelu výsledků vyberte **příkaz Check Point cloudguard Dome9 ARC** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-check-point-cloudguard-dome9-arc"></a>Konfigurace a testování jednotného přihlašování Azure AD pro kontrolní bod CloudGuard Dome9 ARC

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí služby Check Point CloudGuard Dome9 ARC pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v CloudGuard Dome9 ARC v kontrolním bodě.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s CloudGuard Dome9em Check Point, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte kontrolní bod cloudguard Dome9 ARC SSO](#configure-check-point-cloudguard-dome9-arc-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele cloudguard Dome9 ARC](#create-check-point-cloudguard-dome9-arc-test-user)** , abyste měli protějšek B. Simon ve službě Check Point cloudguard Dome9 ARC, která je propojená s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **Check Point cloudguard Dome9 ARC** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL: `https://secure.dome9.com/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a přihlašovací adresy URL. Hodnota `<company name>` se zobrazí v části **Konfigurace jednotného přihlašování Dome9 ARC pro cloudguard** , která je vysvětleno dále v tomto kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace Check Point CloudGuard Dome9 ARC očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit-attribute.png)

1. Kromě výše je u aplikace Check Point CloudGuard Dome9ed očekáváno, že se v odpovědi SAML vrátí další atributy, které jsou uvedeny níže. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Name (Název) |  Zdrojový atribut|
    | ---------------| --------------- |
    | memberOf | User. assignedroles |

    >[!NOTE]
    >Kliknutím [sem](https://docs.microsoft.com/azure/active-directory/saas-apps/apptio-tutorial) zobrazíte informace o tom, jak ve službě Azure AD vytvářet role.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Dome9 ARC cloudguard kontrolního bodu** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k CloudGuard Dome9 ARC pro Check Point.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Check Point cloudguard Dome9 ARC**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Konfigurace kontrolního bodu SSO CloudGuard Dome9 ARC

1. Chcete-li automatizovat konfiguraci v rámci služby Check Point CloudGuard Dome9 ARC, je nutné nainstalovat **rozšíření prohlížeče zabezpečeného přihlašování aplikace** kliknutím na tlačítko **nainstalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **cloudguard bod kontroly instalace. Dome9 ARC** vás nasměruje do aplikace Check Point cloudguard Dome9 ARC. Odtud zadejte přihlašovací údaje správce, které se budou přihlašovat k CloudGuard Dome9 ARC kontrolního bodu. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit CloudGuard Dome9 ARC ručně, otevřete nové okno webového prohlížeče a přihlaste se k pracovnímu serveru společnosti Check Point CloudGuard Dome9 ARC jako správce a proveďte následující kroky:

2. Klikněte na **nastavení profilu** v pravém horním rohu a pak klikněte na **Nastavení účtu**. 

    ![Konfigurace ARC kontrolního bodu CloudGuard Dome9](./media/dome9arc-tutorial/configure1.png)

3. Přejděte na **jednotné přihlašování** a pak klikněte na **Povolit**.

    ![Konfigurace ARC kontrolního bodu CloudGuard Dome9](./media/dome9arc-tutorial/configure2.png)

4. V části Konfigurace jednotného přihlašování proveďte následující kroky:

    ![Konfigurace ARC kontrolního bodu CloudGuard Dome9](./media/dome9arc-tutorial/configure3.png)

    a. Do textového pole **ID účtu** zadejte název společnosti. Tato hodnota se použije v adrese URL pro **odpověď** a **přihlášení** zmíněné v části **základní konfigurace SAML** v Azure Portal.

    b. Do textového pole **vystavitele** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z formuláře Azure Portal.

    c. Do textového pole **Adresa URL koncového bodu IDP** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali, a formu Azure Portal.

    d. Otevřete stažený certifikát kódovaný v kódování Base64 v programu Poznámkový blok, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikát X. 509** .

    e. Klikněte na **Uložit**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Vytvořit testovacího uživatele CloudGuard Dome9 ARC pro kontrolní bod

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili ke službě Check Point CloudGuard Dome9 ARC, musí se zřídit do aplikace. Kontrolní bod CloudGuard Dome9 ARC podporuje zřizování za běhu, ale aby fungovalo správně, musí uživatel vybrat konkrétní **roli** a přiřadit ji uživateli.

   >[!Note]
   >Pro vytváření **rolí** a další podrobnosti kontaktuje [tým podpory cloudguard Dome9 pro klienty podpory](mailto:Dome9@checkpoint.com).

**Chcete-li zřídit uživatelský účet ručně, proveďte následující kroky:**

1. Přihlaste se ke svému CloudGuardmu webu společnosti Check Point jako správce.

2. Klikněte na **role uživatelé &** a potom klikněte na **Uživatelé**.

    ![Přidat zaměstnance](./media/dome9arc-tutorial/user1.png)

3. Klikněte na **Přidat uživatele**.

    ![Přidat zaměstnance](./media/dome9arc-tutorial/user2.png)

4. V části **vytvořit uživatele** proveďte následující kroky:

    ![Přidat zaměstnance](./media/dome9arc-tutorial/user3.png)

    a. Do textového pole **e-mail** zadejte e-mailové adresy uživatele, jako B.Simon@contoso.com.

    b. Do textového pole **název** zadejte jméno uživatele jako B.

    c. Do textového pole **příjmení** zadejte příjmení uživatele, jako je Simon.

    d. Proveďte **uživatele jednotného přihlašování** **.**

    e. Klikněte na **vytvořit**.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici CloudGuard (kontrolní bod) Dome9 oblouk na přístupovém panelu, měli byste se automaticky přihlásit ke CloudGuard Dome9 ARC, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušet kontrolní bod CloudGuard Dome9 ARC s Azure AD](https://aad.portal.azure.com/)