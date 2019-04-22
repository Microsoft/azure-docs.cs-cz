---
title: 'Kurz: Integrace Azure Active Directory s Coralogix | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Coralogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0095a825f5582dc795f5bebdcf08be07a92946e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678288"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Kurz: Integrace Azure Active Directory s Coralogix

V tomto kurzu se dozvíte, jak integrovat Coralogix s Azure Active Directory (Azure AD).
Coralogix integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Coralogix.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Coralogix (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Coralogix, potřebujete následující položky:

- Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [zkušební verze na jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).
- Coralogix single-sign-on povolené předplatné. 

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Coralogix podporuje jednotné přihlašování iniciovaného Zprostředkovatelem přihlašování.

## <a name="add-coralogix-from-the-gallery"></a>Přidání Coralogix z Galerie

Pokud chcete nakonfigurovat integraci Coralogix do služby Azure AD, nejprve přidáte Coralogix z Galerie na váš seznam spravovaných aplikací SaaS.

Chcete-li přidat Coralogix z galerie, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Coralogix**. Vyberte **Coralogix** v podokně výsledků a pak vyberte **přidat** tlačítko pro přidání aplikace.

     ![Coralogix v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Coralogix podle volá Britta Simon testovacího uživatele.
Pro jednotné přihlašování pro práci budete muset vytvořit propojení mezi uživatele služby Azure AD a související uživatel v Coralogix.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Coralogix, proveďte následující stavebních bloků:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
2. [Konfigurace Coralogix jednotného přihlašování](#configure-coralogix-single-sign-on) ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
4. [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. [Vytvoření zkušebního uživatele Coralogix](#create-a-coralogix-test-user) mít protějšek Britta Simon Coralogix, který je propojený s Azure AD reprezentace uživatele.
6. [Otestovat jednotné přihlašování](#test-single-sign-on) k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Coralogix, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Coralogix** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogovém okně vyberte **SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** ikony otevřete **základní konfiguraci SAML** dialogové okno.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. V **základní konfiguraci SAML** dialogové okno pole, proveďte následující kroky:

    ![Coralogix domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** zadejte adresu URL pomocí následujícímu vzoru: `https://<SUBDOMAIN>.coralogix.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, jako například:
    
    `https://api.coralogix.com/saml/metadata.xml`

    nebo

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > Hodnota adresy URL přihlašování není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Obraťte se [tým podpory Coralogix klienta](mailto:info@coralogix.com) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají v **základní konfiguraci SAML** části webu Azure Portal.

5. Aplikace Coralogix očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** tlačítko Otevřít **atributy uživatele** dialogové okno.

    ![image](common/edit-attribute.png)

6. V **deklarace identity uživatelů** v tématu **atributy uživatele** dialogovém okně Úprava deklarace identity pomocí **upravit** ikonu. Můžete také přidat deklarace identity pomocí **přidat novou deklaraci** konfigurovat atribut tokenu SAML, jak je znázorněno na předchozím obrázku. Pak proveďte následující kroky:
    
    a. Vyberte **ikonu pro úpravu** otevřít **spravovat deklarace identity uživatelů** dialogové okno.

    ![image](./media/coralogix-tutorial/tutorial_usermail.png) ![image](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Z **vyberte formát identifikátor názvu** seznamu vyberte **e-mailová adresa**.

    c. Z **zdrojový atribut** seznamu vyberte **user.mail**.

    d. Vyberte **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků. Pak ho uložte na vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

8. V **nastavení Coralogix** tématu, zkopírujte příslušné adresy URL.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-coralogix-single-sign-on"></a>Konfigurace Coralogix jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **Coralogix** straně, odeslat na stažený **kód XML metadat federace** a zkopírovat z portálu Azure portal do adresy URL [tým podpory Coralogix](mailto:info@coralogix.com). Zajišťují, že je správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. V horní části obrazovky vyberte **nového uživatele**.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. V **uživatele** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte "brittasimon@yourcompanydomain.extension." Například v takovém případě můžete například zadat "brittasimon@contoso.com."

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a potom si poznamenejte hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Coralogix použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Coralogix**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Coralogix**.

    ![Odkaz Coralogix v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Vyberte **přidat uživatele** tlačítko. Potom vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů. Klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu kontrolního výrazu SAML, do role v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu. Klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno, vyberte **přiřadit** tlačítko.

### <a name="create-a-coralogix-test-user"></a>Vytvoření zkušebního uživatele Coralogix

V této části vytvoříte uživatele v Coralogix jako Britta Simon. Práce s [tým podpory Coralogix](mailto:info@coralogix.com) přidat uživatele na platformě Coralogix. Musíte vytvořit a aktivovat uživatelé používat jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace s použitím portálu MyApps.

Při výběru dlaždice Coralogix portálu MyApps můžete by měl být automaticky přihlášeni k Coralogix. Další informace o portálu MyApps najdete v tématu [novinky na portálu MyApps?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

