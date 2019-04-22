---
title: 'Kurz: Integrace Azure Active Directory s Signagelive | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Signagelive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d923f0e7-ad31-4d59-a6fd-f0e895e1a32d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bff57635c87b77270e1bd20e04834dec132b2df6
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678340"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Kurz: Integrace Azure Active Directory s Signagelive

V tomto kurzu se dozvíte, jak integrovat Signagelive s Azure Active Directory (Azure AD).
Signagelive integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Signagelive.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Signagelive (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Signagelive, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [zkušební verze na jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).
* Signagelive jeden znak na podporou předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Signagelive podporuje jednotné přihlašování iniciovaného Zprostředkovatelem přihlašování.

## <a name="add-signagelive-from-the-gallery"></a>Přidání Signagelive z Galerie

Pokud chcete nakonfigurovat integraci Signagelive do služby Azure AD, nejprve přidáte Signagelive z Galerie na váš seznam spravovaných aplikací SaaS.

Chcete-li přidat Signagelive z galerie, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Signagelive**. 

     ![Signagelive v seznamu výsledků](common/search-new-app.png)

5. Vyberte **Signagelive** v podokně výsledků a pak vyberte **přidat** tlačítko pro přidání aplikace.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Signagelive podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba vytvořit propojení mezi uživatele služby Azure AD a související uživatel v Signagelive.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Signagelive, proveďte následující stavebních bloků:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
2. [Konfigurace Signagelive jednotného přihlašování](#configure-signagelive-single-sign-on) ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
4. [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. [Vytvoření zkušebního uživatele Signagelive](#create-a-signagelive-test-user) mít protějšek Britta Simon Signagelive, který je propojený s Azure AD zastoupení uživatele.
6. [Otestovat jednotné přihlašování](#test-single-sign-on) k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Signagelive, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Signagelive** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogovém okně vyberte **SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavit jednotné přihlašování pomocí SAML** stránce **upravit** otevřít **základní konfiguraci SAML** dialogové okno.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. V **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Signagelive domény a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** zadejte adresu URL, která používá následující vzorec:  `https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. K získání hodnoty, obraťte se [tým podpory Signagelive klienta](mailto:support@signagelive.com) . Můžete také odkazovat na vzorce, které jsou uvedeny v **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **Stáhnout** ke stažení **certifikát (Raw)** z se zadanými možnostmi podle vašich požadavků. Pak ho uložte na vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. V **nastavení Signagelive** tématu, zkopírujte adres(y) URL, které potřebujete.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-signagelive-single-sign-on"></a>Konfigurace Signagelive jednotné přihlašování

Konfigurace jednotného přihlašování na straně Signagelive, odeslat na stažený **certifikát (Raw)** a zkopírovat z portálu Azure portal do adresy URL [tým podpory Signagelive](mailto:support@signagelive.com). Zajišťují, že je správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. V **uživatele** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte "brittasimon@yourcompanydomain.extension". Například v takovém případě můžete například zadat "BrittaSimon@contoso.com".

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a potom si poznamenejte hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Signagelive použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Signagelive**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Signagelive**.

    ![Odkaz Signagelive v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Vyberte **přidat uživatele** tlačítko. Potom v **přidat přiřazení** dialogu **uživatelů a skupin**.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** v dialogu **uživatelé** seznamu vyberte **Britta Simon**. Klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolní výraz SAML, pak v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu. Klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno, vyberte **přiřadit** tlačítko.

### <a name="create-a-signagelive-test-user"></a>Vytvoření zkušebního uživatele Signagelive

V této části vytvoříte uživatele v Signagelive jako Britta Simon. Práce s [tým podpory Signagelive](mailto:support@signagelive.com) přidat uživatele na platformě Signagelive. Musíte vytvořit a aktivovat uživatelé používat jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace s použitím portálu MyApps.

Když vyberete **Signagelive** dlaždici na portálu MyApps vám by měl být automaticky přihlášeni. Další informace o portálu MyApps najdete v tématu [novinky na portálu MyApps?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

