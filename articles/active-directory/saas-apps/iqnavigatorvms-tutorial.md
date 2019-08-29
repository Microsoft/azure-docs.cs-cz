---
title: 'Kurz: Azure Active Directory integrace s virtuálními počítači s IQNavigator | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a IQNavigator virtuálními počítači.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: dba50c984984363682efce1f09ef462b3c0c5def
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078488"
---
# <a name="tutorial-azure-active-directory-integration-with-iqnavigator-vms"></a>Kurz: Azure Active Directory integrace s virtuálními počítači s IQNavigator

V tomto kurzu se naučíte integrovat virtuální počítače s IQNavigator pomocí Azure Active Directory (Azure AD).
Integrace virtuálních počítačů s IQNavigator s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k VIRTUÁLNÍm počítačům s IQNavigator.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k VIRTUÁLNÍm počítačům IQNavigator (jednotné přihlašování) pomocí svých účtů Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s virtuálními počítači s IQNavigator potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné IQNavigator virtuálních počítačů s podporou jednotného přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Virtuální počítače s IQNavigator podporují jednotné přihlašování **IDP** .

## <a name="adding-iqnavigator-vms-from-the-gallery"></a>Přidání virtuálních počítačů s IQNavigator z Galerie

Pokud chcete nakonfigurovat integraci virtuálních počítačů s IQNavigator do služby Azure AD, musíte přidat virtuální počítače s IQNavigator z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat virtuální počítače s IQNavigator z Galerie, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **IQNavigator virtuální počítače**, vyberte **virtuální počítače s IQNavigator** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![IQNavigator virtuální počítače v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí virtuálních počítačů s IQNavigator na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v IQNavigator virtuálních počítačích.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí virtuálních počítačů s IQNavigator, musíte dokončit tyto stavební bloky:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurovat jednotné přihlašování pro virtuální počítače s IQNavigator](#configure-iqnavigator-vms-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření virtuálních počítačů s IQNavigator pro testování](#create-iqnavigator-vms-test-user)** , které mají protějšek Britta Simon v IQNavigator virtuálních počítačích, které jsou propojené s reprezentací uživatele Azure AD.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí virtuálních počítačů s IQNavigator, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **virtuálních počítačů s IQNavigator** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování IQNavigator virtuálních počítačů a adres URL](common/idp-relay.png)

    a. Do textového pole **identifikátor** zadejte adresu URL:`iqn.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`

    c. Klikněte na **nastavit další adresy URL**.

    d. Do textového pole **stav přenosu** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.iqnavigator.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a stavu přenosu. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory virtuálních počítačů s IQNavigator](https://www.beeline.com/support-iqn/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. IQNavigator aplikace očekává jedinečnou hodnotu identifikátoru uživatele v deklaraci identifikátoru názvu. Zákazník může namapovat správnou hodnotu deklarace identifikátoru názvu. V tomto případě jsme namapovali uživatele. UserPrincipalName pro účely ukázky V závislosti na nastaveních vaší organizace byste ale měli namapovat správnou hodnotu.

    ![image](common/edit-attribute.png)

6. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-iqnavigator-vms-single-sign-on"></a>Konfigurace jednotného přihlašování pro virtuální počítače s IQNavigator

Pokud chcete nakonfigurovat jednotné přihlašování na straně **virtuálních počítačů s IQNavigator** , musíte odeslat **adresu URL federačních metadat aplikace** [týmu podpory virtuálních počítačů s IQNavigator](https://www.beeline.com/support-iqn/). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména** **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k VIRTUÁLNÍm počítačům s IQNavigator.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **virtuální počítače s IQNavigator**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **virtuální počítače s IQNavigator**.

    ![Odkaz na virtuální počítače s IQNavigator v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-iqnavigator-vms-test-user"></a>Vytvořit testovacího uživatele pro virtuální počítače s IQNavigator

V této části vytvoříte na virtuálních počítačích s IQNavigator uživatele s názvem Britta Simon. Práce s [IQNavigator virtuálními počítači podporuje tým](https://www.beeline.com/support-iqn/) k přidávání uživatelů na platformě virtuálních počítačů IQNavigator. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici virtuální počítače s IQNavigator, měli byste se automaticky přihlásit k VIRTUÁLNÍm počítačům s IQNavigator, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)