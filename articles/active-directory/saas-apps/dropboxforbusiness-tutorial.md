---
title: 'Kurz: Azure Active Directory integrace s Dropboxem pro firmy | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Dropboxem pro firmy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 615c10357e099b547008b128ca5f5a773ff1aa14
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158306"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Kurz: integrace Dropboxu pro firmy s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Dropbox pro firmy pomocí Azure Active Directory (Azure AD). Když integrujete Dropbox pro firmy s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Dropboxu pro firmy.
* Umožněte uživatelům, aby se automaticky přihlásili do Dropboxu pro firmy pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Dropbox pro předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

* V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Dropbox pro **firmy podporuje jednotné** přihlašování (SSO) iniciované.

* Dropbox pro firmy podporuje zřizování uživatelů **jenom v čase**

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Přidání Dropboxu pro firmy z Galerie

Pokud chcete nakonfigurovat integraci Dropboxu pro firmy do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Dropbox pro firmy z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Dropbox pro firmy** .
1. Z panelu výsledků vyberte **Dropbox pro firmy** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Dropboxu pro firmy pomocí testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Dropboxu pro firmy.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Dropboxu pro firmy, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[NAKONFIGURUJTE jednotné přihlašování Dropbox pro firmy](#configure-dropbox-for-business-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte si uživatele Dropbox pro obchodní test](#create-dropbox-for-business-test-user)** , který bude mít protějšek Britta Simon v Dropboxu pro firmy, který je propojený s reprezentací uživatele Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **Dropbox pro firmy** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://www.dropbox.com/sso/<id>`

    b. Do textového pole **identifikátor (ID entity)** zadejte hodnotu: `Dropbox`

    > [!NOTE]
    > Hodnota předchozí přihlašovací adresy URL není reálné číslo. Tuto hodnotu aktualizujete pomocí skutečné přihlašovací adresy URL, která se vysvětluje později v tomto kurzu.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit Dropbox pro firmy** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-dropbox-for-business-sso"></a>Konfigurace Dropboxu pro jednotné přihlašování pro firmy

1. Pokud chcete automatizovat konfiguraci v rámci Dropboxu pro firmy, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Nastavení Dropbox pro firmy** , které vás přesměruje na aplikaci Dropbox pro firmy. Odtud zadejte přihlašovací údaje správce, které se přihlásí k Dropboxu pro firmy. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-8.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete ručně nastavit Dropbox pro firmy, otevřete nové okno webového prohlížeče a navštivte svého tenanta Dropbox pro firmy a přihlaste se k tenantovi Dropbox pro firmy. a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/ic769509.png "Konfigurace jednotného přihlašování")

4. Klikněte na **ikonu uživatele** a vyberte kartu **Nastavení** .

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure1.png "Konfigurace jednotného přihlašování")

5. V navigačním podokně na levé straně klikněte na Konzola pro **správu**.

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure2.png "Konfigurace jednotného přihlašování")

6. V **konzole pro správu**klikněte v levém navigačním podokně na **Nastavení** .

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure3.png "Konfigurace jednotného přihlašování")

7. V části **ověřování** vyberte možnost **jednotné přihlašování** .

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure4.png "Konfigurace jednotného přihlašování")

8. V části **jednotné přihlašování** proveďte následující kroky:  

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure5.png "Konfigurace jednotného přihlašování")

    a. V rozevíracím seznamu vyberte možnost **požadováno** pro **jednotné přihlašování**.

    b. Klikněte na **Přidat adresu URL** pro přihlášení a v poli **Adresa URL pro přihlášení zprostředkovatele identity** vložte hodnotu URL pro **přihlášení** , kterou jste zkopírovali z Azure Portal, a potom vyberte **Hotovo**.

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure6.png "Konfigurace jednotného přihlašování")

    c. Klikněte na **nahrát certifikát**a potom přejděte k **souboru certifikátu kódovanému pomocí Base64** , který jste stáhli z Azure Portal.

    d. Klikněte na **Kopírovat odkaz** a vložte zkopírovanou hodnotu do pole **přihlašovací adresa URL** **pro doménu Dropbox pro firmy a adresy URL** na Azure Portal.

    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `Britta Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `BrittaSimon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Dropboxu pro firmy.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Dropbox pro firmy**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-dropbox-for-business-test-user"></a>Vytvořit uživatele Dropbox pro firemní testování

V této části se ve Dropboxu pro firmy vytvoří uživatel s názvem Britta Simon. Dropbox pro firmy podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Dropboxu pro firmy neexistuje, vytvoří se po ověření nový.

>[!Note]
>Pokud potřebujete ručně vytvořit uživatele, kontaktujte [tým podpory Dropboxu pro firmy](https://www.dropbox.com/business/contact) .

### <a name="test-sso"></a>Test SSO

Když vyberete dlaždici Dropbox pro firmy na přístupovém panelu, měli byste se automaticky přihlásit k Dropboxu pro firmy, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)