---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s EBSCO | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ebaf3a1d877025cafe8829bc937ef032a3c95d03
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163457"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s EBSCO

V tomto kurzu se dozvíte, jak integrovat EBSCO s Azure Active Directory (Azure AD). Když integrujete EBSCO s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k EBSCO.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k EBSCO svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* EBSCO odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* EBSCO podporuje jednotné přihlašování (SSO) **a IDP** .
* EBSCO podporuje zřizování uživatelů **jenom v čase** .

> [!NOTE]
> Vzhledem k tomu, že identifikátor této aplikace je pevná řetězcová hodnota, může být v jednom klientovi nakonfigurovaná jenom jedna instance.

## <a name="adding-ebsco-from-the-gallery"></a>Přidání EBSCO z Galerie

Pokud chcete nakonfigurovat integraci EBSCO do služby Azure AD, musíte přidat EBSCO z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **EBSCO** .
1. Na panelu výsledků vyberte **EBSCO** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>Konfigurace a testování jednotného přihlašování Azure AD pro EBSCO

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí EBSCO pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v EBSCO.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí EBSCO, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE EBSCO SSO](#configure-ebsco-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte EBSCO Test User](#create-ebsco-test-user)** -to, abyste měli protějšek B. Simon v EBSCO, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **EBSCO** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    Do textového pole **identifikátor** zadejte adresu URL:`pingsso.ebscohost.com`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > Hodnota přihlašovací adresy URL není reálné číslo. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta EBSCO](mailto:support@ebsco.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

    o **jedinečných prvcích:**  

    o **custid** = zadejte jedinečné ID zákazníka EBSCO 

    **Profiling** = klienti můžou přizpůsobit odkaz na směrování uživatelů na konkrétní profil (v závislosti na tom, co kupují z EBSCO). Můžou zadat konkrétní ID profilu. Hlavní ID jsou EDS (služba EBSCO Discovery) a ehost (databáze EBSOCOhost). Pokyny pro stejné jsou uvedeny [zde](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení EBSCO** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k EBSCO.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **EBSCO**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-ebsco-sso"></a>Konfigurace jednotného přihlašování EBSCO

Ke konfiguraci jednotného přihlašování na straně **EBSCO** je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory EBSCO](mailto:support@ebsco.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-ebsco-test-user"></a>Vytvořit testovacího uživatele EBSCO

V případě EBSCO je zřizování uživatelů automatické.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

Azure AD předá požadovaná data EBSCO aplikaci. Zřizování uživatelů EBSCO může být automatické nebo vyžadovat jednorázový formulář. Záleží na tom, jestli má klient mnoho již existujících účtů EBSCOhost s uloženým osobním nastavením. Totéž můžete v rámci implementace projednávat s [týmem podpory EBSCO](mailto:support@ebsco.com) . V obou případech klient nemusí vytvářet žádné účty EBSCOhost před testováním.

   >[!Note]
   >Můžete automatizovat EBSCOhost zřizování a přizpůsobení uživatelů. Obraťte se na [tým podpory EBSCO](mailto:support@ebsco.com) o zřizování uživatelů za běhu.

## <a name="test-sso"></a>Test SSO

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

1. Když na přístupovém panelu kliknete na dlaždici EBSCO, měli byste se automaticky přihlásili ke své aplikaci EBSCO.
Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/active-directory-saas-access-panel-introduction.md).

1. Po přihlášení k aplikaci klikněte v pravém horním rohu na tlačítko **Přihlásit** se.

    ![Přihlášení EBSCO v seznamu aplikací](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. Zobrazí se jednorázová výzva k párování místního přihlášení a přihlašovacího jména SAML s odkazem na **váš stávající účet MyEBSCOhost k vašemu účtu instituce** nebo **vytvořte nový účet MyEBSCOhost a propojte ho s účtem instituce**. Účet se používá pro přizpůsobení aplikace EBSCOhost. Vyberte možnost **vytvořit nový účet** a uvidíte, že formulář pro přizpůsobení je předem dokončen s hodnotami z odpovědi SAML, jak je znázorněno na snímku obrazovky níže. Kliknutím na **pokračovat** uložte tento výběr.
    
     ![Uživatel EBSCO v seznamu aplikací](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Po dokončení výše uvedeného nastavení vymažte soubory cookie/mezipaměť a znovu se přihlaste. Nebudete se muset ručně přihlašovat znovu a nastavení přizpůsobení se zapamatuje.

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si EBSCO s Azure AD](https://aad.portal.azure.com/)

