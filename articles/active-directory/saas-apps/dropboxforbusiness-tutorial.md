---
title: 'Kurz: Azure Active Directory integraci s Dropboxem pro firmy | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Dropboxem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: 93d13023dfda8164bf0d327bee6106f9803e1bf7
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91775132"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>Kurz: integrace firemního Dropboxu pomocí Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat obchodní Dropbox pomocí Azure Active Directory (Azure AD). Při integraci firemního Dropboxu s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k Dropboxu společnosti.
* Umožněte uživatelům, aby se k Dropboxu přihlásili pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné s povoleným jednotným přihlašováním (SSO).

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

* V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Společnost **Dropbox podporuje jednotné** přihlašování v/v.

* Společnost Dropbox podporuje [automatizované zřizování a rušení zřizování uživatelů](dropboxforbusiness-tutorial.md)
* Jakmile nakonfigurujete Dropbox, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-business-from-the-gallery"></a>Přidání firemního Dropboxu z Galerie

Pokud chcete nakonfigurovat integraci firemního Dropboxu do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat z Galerie obchodní prostředí Dropboxu.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Společnost Dropbox** .
1. Z panelu výsledků vyberte možnost **obchodní Dropbox** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Pomocí testovacího uživatele s názvem **Britta Simon**konfigurujte a otestujte jednotné přihlašování Azure AD pomocí Dropboxu firmy. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Dropboxu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Dropboxu, vyplňte následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.    
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
1. **[Konfigurace podnikového přihlašování Dropboxu](#configure-dropbox-business-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte si uživatele Dropboxu pro obchodní testy](#create-dropbox-business-test-user)** , abyste měli protějšek Britta Simon v rámci Dropboxu, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **obchodních** aplikací pro Dropbox najděte část **Správa** a vyberte **jednotné přihlašování**.
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

1. V části **nastavit firmu Dropboxu** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL


### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `Britta Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `BrittaSimon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Dropboxu společnosti.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Dropbox Business**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-dropbox-business-sso"></a>Konfigurace podnikového jednotného přihlašování k Dropboxu

1. Pokud chcete automatizovat konfiguraci v rámci Dropboxu, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavit Dropbox Business** . budete přesměrováni na obchodní aplikaci Dropbox. Odtud zadejte přihlašovací údaje správce, které se budou přihlašovat do společnosti Dropbox. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-8.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete firemní Dropbox nastavit ručně, otevřete nové okno webového prohlížeče a Projděte si firemního tenanta Dropboxu a přihlaste se k firemnímu tenantovi Dropbox. a proveďte následující kroky:

    ![Snímek obrazovky zobrazující stránku přihlášení k Dropboxu](./media/dropboxforbusiness-tutorial/ic769509.png "Konfigurace jednotného přihlašování")

4. Klikněte na **ikonu uživatele** a vyberte kartu **Nastavení** .

    ![Snímek obrazovky zobrazující, že je vybraná akce ikona uživatele a nastavení.](./media/dropboxforbusiness-tutorial/configure1.png "Konfigurace jednotného přihlašování")

5. V navigačním podokně na levé straně klikněte na Konzola pro **správu**.

    ![Snímek obrazovky zobrazující, že je vybraná Konzola pro správu](./media/dropboxforbusiness-tutorial/configure2.png "Konfigurace jednotného přihlašování")

6. V **konzole pro správu**klikněte v levém navigačním podokně na **Nastavení** .

    ![Snímek obrazovky, který zobrazuje vybrané nastavení.](./media/dropboxforbusiness-tutorial/configure3.png "Konfigurace jednotného přihlašování")

7. V části **ověřování** vyberte možnost **jednotné přihlašování** .

    ![Snímek obrazovky, který zobrazuje oddíl ověřování s vybraným možnostmi jednotné přihlašování](./media/dropboxforbusiness-tutorial/configure4.png "Konfigurace jednotného přihlašování")

8. V části **jednotné přihlašování** proveďte následující kroky:  

    ![Snímek obrazovky se zobrazením nastavení konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure5.png "Konfigurace jednotného přihlašování")

    a. V rozevíracím seznamu vyberte možnost **požadováno** pro **jednotné přihlašování**.

    b. Klikněte na **Přidat adresu URL** pro přihlášení a v poli **Adresa URL pro přihlášení zprostředkovatele identity** vložte hodnotu URL pro **přihlášení** , kterou jste zkopírovali z Azure Portal, a potom vyberte **Hotovo**.

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure6.png "Konfigurace jednotného přihlašování")

    c. Klikněte na **nahrát certifikát**a potom přejděte k **souboru certifikátu kódovanému pomocí Base64** , který jste stáhli z Azure Portal.

    d. Klikněte na **Kopírovat odkaz** a vložte zkopírované hodnoty do textového pole **Adresa URL pro přihlášení** v části **obchodní doména Dropboxu a adresy URL** v Azure Portal.

    e. Klikněte na **Uložit**.

### <a name="create-dropbox-business-test-user"></a>Vytvořit obchodního testovacího uživatele Dropboxu

V této části se na firemním Dropboxu vytvoří uživatel s názvem B. Simon. Společnost Dropbox podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Dropboxu neexistuje, vytvoří se po ověření nový.

>[!Note]
>Pokud potřebujete ručně vytvořit uživatele, kontaktujte [tým podpory pro firemního klienta Dropbox](https://www.dropbox.com/business/contact) .

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete obchodní dlaždici Dropbox, měli byste být automaticky přihlášeni k firemnímu Dropboxu, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Vyzkoušejte si službu Dropbox pro firmy pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)