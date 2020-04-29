---
title: 'Kurz: Azure Active Directory integrace s TigerText Secure Messenger | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TigerText Secure Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: ea3bda1dd51a7c3a2e5e3f8b669d7138898f1595
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "67088660"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Kurz: Azure Active Directory integrace s TigerText Secure Messenger

V tomto kurzu se dozvíte, jak integrovat TigerText Secure Messenger s Azure Active Directory (Azure AD).

Integrace TigerText Secure Messenger s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k TigerText zabezpečenému Messenger.
* Můžete uživatelům povolit, aby se automaticky přihlásili k TigerText zabezpečeným kurýrním programům (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění: Azure Portal.

Podrobnosti o integraci aplikací SaaS (software jako služba) se službou Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s TigerText Secure Messenger potřebujete následující položky:

* Předplatné služby Azure AD. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Předplatné zabezpečeného kurýrní služby TigerText s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí a integrujete TigerText Secure Messenger s Azure AD.

TigerText Secure Messenger podporuje jednotné přihlašování (SSO) iniciované v SP.

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Přidat TigerText zabezpečený Kurýrní službu z Azure Marketplace

Pokud chcete nakonfigurovat integraci TigerText Secure Messenger do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat TigerText zabezpečený Kurýrní službu z Azure Marketplace:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com?azure-portal=true).
1. V levém podokně vyberte **Azure Active Directory**.

    ![Možnost Azure Active Directory](common/select-azuread.png)

1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, v horní části podokna vyberte **+ Nová aplikace** .

    ![Možnost nové aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **TigerText Secure Messenger**. Ve výsledcích hledání vyberte **TigerText Secure Messenger**a pak vyberte **Přidat** , aby se aplikace přidala.

    ![TigerText zabezpečený Messenger v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí TigerText Secure Messenger na základě testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v TigerText Secure Messenger.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby TigerText Secure Messenger, je potřeba, abyste dokončili tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** , aby mohli vaši uživatelé používat tuto funkci.
1. **[Nakonfigurujte jednotné přihlašování TigerText Secure Messenger](#configure-tigertext-secure-messenger-single-sign-on)** ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí Britta Simon.
1. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** , aby mohl Britta Simon používat jednotné přihlašování Azure AD.
1. **[Vytvořte testovacího uživatele TigerText Secure Messenger](#create-a-tigertext-secure-messenger-test-user)** , aby byl uživatel s názvem Britta Simon v TigerText zabezpečený kurýrní program, který je propojený s uživatelem Azure AD s názvem Britta Simon.
1. **[Otestujte jednotné přihlašování](#test-single-sign-on)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí služby TigerText Secure Messenger, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **zabezpečeného kurýrní služby TigerText** vyberte **jednotné přihlašování**.

    ![Konfigurovat možnost jednotného přihlašování](common/select-sso.png)

1. V podokně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

1. V podokně **nastavit jednotné přihlašování pomocí SAML** vyberte **Upravit** (ikona tužky) a otevřete **základní podokno konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V podokně **základní konfigurace SAML** proveďte následující kroky:

    ![TigerText zabezpečených přihlašovacích údajů domény a adresy URL pro jednotné přihlašování](common/sp-identifier.png)

    1. Do pole **přihlašovací adresa URL** zadejte adresu URL:

       `https://home.tigertext.com`

    1. Do pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > Hodnota **identifikátoru (ID entity)** není skutečná. Aktualizuje tuto hodnotu skutečným identifikátorem. Pokud chcete získat hodnotu, obraťte se na [tým podpory TigerText Secure Messenger](mailto:prosupport@tigertext.com). Můžete také odkazovat na vzory zobrazené v podokně **základní konfigurace SAML** v Azure Portal.

1. V podokně **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyberte **Stáhnout** a ze daných možností stáhněte **XML federačních metadat** a uložte ho do svého počítače.

    ![Možnost stažení XML federačních metadat](common/metadataxml.png)

1. V části **nastavení zabezpečeného kurýrní služby TigerText** zkopírujte adresy URL nebo adresy URL, které potřebujete:

   * **Přihlašovací adresa URL**
   * **Identifikátor Azure AD**
   * **Odhlašovací adresa URL**

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Konfigurace jednotného přihlašování TigerText Secure Messenger

Pokud chcete nakonfigurovat jednotné přihlašování na straně zabezpečeného kurýrní služby TigerText, je potřeba odeslat stažený soubor XML federačních metadat a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory TigerText zabezpečeného kurýrní](mailto:prosupport@tigertext.com)služby. Tým TigerText Secure Messenger zajistí správné nastavení připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon v Azure Portal.

1. V Azure Portal v levém podokně vyberte **Azure Active Directory**   > **Uživatelé** > **Všichni uživatelé**.

    ![Možnosti uživatelé a všichni uživatelé](common/users.png)

1. V horní části obrazovky vyberte **+ Nový uživatel**.

    ![Možnost Nový uživatel](common/new-user.png)

1. V podokně **uživatel** proveďte následující kroky:

    ![Podokno uživatele](common/user-properties.png)

    1. Do pole **název** zadejte **BrittaSimon**.
  
    1. Do pole **uživatelské jméno** zadejte **BrittaSimon\@\<\< yourcompanydomain>.>rozšíření **. Například **BrittaSimon\@contoso.com**.

    1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že jim udělíte přístup k TigerText zabezpečenému Kurýrnímu programu.

1. V Azure Portal vyberte možnost **podnikové aplikace** > **všechny aplikace** > **TigerText zabezpečený Messenger**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikace vyberte **TigerText Secure Messenger**.

    ![TigerText zabezpečený Messenger v seznamu aplikací](common/all-applications.png)

1. V levém podokně v části **Spravovat**vyberte **Uživatelé a skupiny**.

    ![Možnost Uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **+ Přidat uživatele**a pak v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** vyberte v seznamu **uživatelů** položku **Britta Simon** a pak zvolte **Vybrat** v dolní části podokna.

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v podokně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. V dolní části podokna zvolte **možnost vybrat**.

1. V podokně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Vytvoření testovacího uživatele TigerText Secure Messenger

V této části vytvoříte uživatele s názvem Britta Simon v TigerText Secure Messenger. Spolupracujte s [týmem podpory TigerText Secure Messenger](mailto:prosupport@tigertext.com) a přidejte Britta Simon jako uživatele v TigerText Secure Messenger. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí portálu moje aplikace.

Když vyberete **TigerText zabezpečený Messenger** na portálu moje aplikace, měli byste být automaticky přihlášeni k předplatnému služby TigerText Secure Messenger, pro které jste nastavili jednotné přihlašování. Další informace o portálu moje aplikace najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

* [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
