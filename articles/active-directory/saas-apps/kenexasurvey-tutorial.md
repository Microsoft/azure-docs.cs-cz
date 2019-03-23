---
title: 'Kurz: Integrace Azure Active Directory s IBM Kenexa průzkumu Enterprise | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a IBM Kenexa průzkumu Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: jeedes
ms.openlocfilehash: ed34267aa1f18b4c66fe841164e6a2cde4e27d47
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361043"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Kurz: Integrace Azure Active Directory s IBM Kenexa průzkumu Enterprise

V tomto kurzu se dozvíte, jak integrovat IBM Kenexa průzkumu organizace Azure Active Directory (Azure AD).
IBM Kenexa průzkumu podnikové integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup do firemní IBM Kenexa zjišťování sítě.
* Uživatelům se automaticky přihlášeni k IBM Kenexa průzkumu Enterprise (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s IBM Kenexa průzkumu Enterprise, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* IBM Kenexa průzkumu podnikové jednotné přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje IBM Kenexa průzkumu Enterprise **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Přidání organizace průzkumu Kenexa IBM z Galerie

Pokud chcete nakonfigurovat integrace IBM Kenexa průzkumu organizace do služby Azure AD, potřebujete přidat IBM Kenexa průzkumu Enterprise z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání organizace průzkumu Kenexa IBM z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **IBM Kenexa průzkumu Enterprise**vyberte **IBM Kenexa průzkumu Enterprise** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![IBM Kenexa průzkumu Enterprise v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí IBM Kenexa průzkumu Enterprise na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v IBM Kenexa průzkumu Enterprise.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s IBM Kenexa průzkumu organizace, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace IBM Kenexa průzkumu Enterprise Single Sign-On](#configure-ibm-kenexa-survey-enterprise-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele organizace průzkumu Kenexa IBM](#create-ibm-kenexa-survey-enterprise-test-user)**  – Pokud chcete mít protějšek Britta Simon IBM Kenexa průzkumu organizace, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s IBM Kenexa průzkumu Enterprise, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **IBM Kenexa průzkumu Enterprise** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránce, proveďte následující kroky:

    ![IBM Kenexa průzkumu podnikové domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://surveys.kenexa.com/<companycode>`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory IBM Kenexa průzkumu Enterprise Client](https://www.ibm.com/support/home/?lnk=fcw) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. IBM Kenexa průzkumu podniková aplikace očekává kontrolní výrazy zabezpečení kontrolní výrazy SAML (Markup Language) v určitém formátu, který je potřeba přidat mapování vlastních atributů ke konfiguraci vaší atributy tokenu SAML. Hodnota deklarace identity identifikátor uživatele v odpovědi musí odpovídat ID jednotného přihlašování, který je nakonfigurovaný v Kenexa systému. Pokud chcete namapovat identifikátor odpovídajícího uživatele ve vaší organizaci jako jednotné přihlašování Internet Datagram Protocol (IDP), pracovat s [tým podpory IBM Kenexa průzkumu Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

    Ve výchozím nastavení Azure AD Nastaví uživatelský identifikátor jako hodnotu hlavního názvu (UPN) uživatele. Tuto hodnotu můžete změnit na **atributy uživatele** kartu, jak je znázorněno na následujícím snímku obrazovky. Integrace funguje pouze po dokončení mapování správně.

    ![image](common/edit-attribute.png)

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. Na **nastavení IBM Kenexa průzkumu Enterprise** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>Konfigurace IBM Kenexa průzkumu Enterprise Single Sign-On

Ke konfiguraci jednotného přihlašování na **IBM Kenexa průzkumu Enterprise** straně, je nutné odeslat na stažený **certifikát (Base64)** a vhodné zkopírovaný adresy URL z webu Azure portal [IBM Kenexa Zjišťování tým podpory Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k IBM Kenexa průzkumu Enterprise.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **IBM Kenexa průzkumu Enterprise**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **IBM Kenexa průzkumu Enterprise**.

    ![IBM Kenexa průzkumu Enterprise odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>Vytvořit testovacího uživatele IBM Kenexa průzkumu Enterprise

V této části vytvořte uživatele Britta Simon v IBM Kenexa průzkumu Enterprise.

Vytvořit uživatele v systému IBM Kenexa průzkumu Enterprise a mapování ID jednotného přihlašování pro ně, můžete pracovat [tým podpory IBM Kenexa průzkumu Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Tato hodnota ID jednotné přihlašování musí být také mapováno na hodnotu identifikátoru uživatele ze služby Azure AD. Toto výchozí nastavení můžete změnit na **atribut** kartu.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici IBM Kenexa průzkumu Enterprise na přístupovém panelu, vám by měl být automaticky přihlášeni na Enterprise IBM Kenexa průzkumu, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

