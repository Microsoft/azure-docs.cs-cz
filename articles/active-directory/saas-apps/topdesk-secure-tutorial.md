---
title: 'Kurz: Integrace Azure Active Directory s TOPdesk – zabezpečení | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TOPdesk – zabezpečené.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: fa66651351278d9df2a98fb5a10b6cb0cb0beb47
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821970"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Kurz: Integrace Azure Active Directory s TOPdesk – zabezpečení

V tomto kurzu se dozvíte, jak integrovat TOPdesk – zabezpečení pomocí služby Azure Active Directory (Azure AD).
Integrace TOPdesk – zabezpečené pomocí Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k TOPdesk – zabezpečené.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k TOPdesk – zabezpečené (Single Sign-On) pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TOPdesk – zabezpečené, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk – zabezpečené jednotné přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* TOPdesk – zabezpečené podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-topdesk---secure-from-the-gallery"></a>Přidání TOPdesk – zabezpečení z Galerie

Konfigurace integrace TOPdesk – zabezpečení do služby Azure AD, budete muset přidat TOPdesk – zabezpečení z Galerie do seznamu spravovaných SaaS aplikací.

**Chcete-li přidat TOPdesk – zabezpečení z galerie, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TOPdesk – zabezpečené**vyberte **TOPdesk – zabezpečené** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![TOPdesk – zabezpečení v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, nakonfigurovat a otestovat Azure AD jednotné přihlašování s TOPdesk – zabezpečené podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci, vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v TOPdesk – zabezpečené musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TOPdesk – zabezpečení, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace TOPdesk – zabezpečené jednotné přihlašování](#configure-topdesk---secure-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření TOPdesk – zabezpečené testovacího uživatele](#create-topdesk---secure-test-user)**  – Pokud chcete mít protějšek Britta Simon v TOPdesk – zabezpečení, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s TOPdesk – zabezpečení, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **TOPdesk – zabezpečené** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![TOPdesk – zabezpečení domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.topdesk.net`

    b. V **identifikátor** pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.topdesk.net/tas/public/login/saml`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [TOPdesk - tým podpory zabezpečení klienta](https://www.topdesk.com/us/support/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení TOPdesk – zabezpečené** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-topdesk---secure-single-sign-on"></a>Konfigurace TOPdesk – zabezpečené jednotné přihlašování

1. Přihlaste se k vaší **TOPdesk – zabezpečené** společnosti serveru jako správce.

2. V **TOPdesk** nabídky, klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/topdesk-secure-tutorial/ic790598.png "nastavení")

3. Klikněte na tlačítko **nastavení přihlášení**.

    ![Nastavení přihlášení](./media/topdesk-secure-tutorial/ic790599.png "nastavení přihlášení")

4. Rozbalte **nastavení přihlášení** nabídky a pak klikněte na tlačítko **Obecné**.

    ![Obecné](./media/topdesk-secure-tutorial/ic790600.png "obecné")

5. V **Secure** část **SAML přihlášení** konfigurace části, proveďte následující kroky:

    ![Technické nastavení](./media/topdesk-secure-tutorial/ic790855.png "technické nastavení")

    a. Klikněte na tlačítko **Stáhnout** ke stažení souboru metadat veřejné a pak ho uložte místně ve vašem počítači.

    b. Otevřete soubor metadat a najděte **AssertionConsumerService** uzlu.

    ![Assertion Consumer Service](./media/topdesk-secure-tutorial/ic790856.png "Assertion Consumer Service")

    c. Kopírovat **AssertionConsumerService** hodnota a vložte tuto hodnotu v textovém poli Adresa URL pro odpověď v **TOPdesk – zabezpečení domény a adresy URL** oddílu.

6. Chcete-li vytvořit soubor certifikátu, proveďte následující kroky:

    ![Certifikát](./media/topdesk-secure-tutorial/ic790606.png "certifikátu")

    a. Otevřete soubor stažený metadat z webu Azure portal.

    b. Rozbalte **RoleDescriptor** uzel, který má **xsi: type** z **dodáni: ApplicationServiceType**.

    c. Zkopírujte hodnotu **certifikátu x 509** uzlu.

    d. Uložit zkopírovaný **certifikátu x 509** hodnotu místně na vašem počítači v souboru.

7. V **veřejné** klikněte na tlačítko **přidat**.

    ![Přidat](./media/topdesk-secure-tutorial/ic790607.png "přidat")

8. Na **pomocníka s nastavením konfigurace SAML** dialogového okna stránky, proveďte následující kroky:

    ![Pomocníka s nastavením konfigurace SAML](./media/topdesk-secure-tutorial/ic790608.png "Pomocníka s nastavením konfigurace SAML")

    a. Chcete-li nahrát soubor metadat stažené z webu Azure portal v části **federačních metadat**, klikněte na tlačítko **Procházet**.

    b. K nahrání souboru certifikátu v části **certifikátu (RSA)**, klikněte na tlačítko **Procházet**.

    c. Pro **privátní klíč (RSA, PKCS8, kódování DER)**, můžete nahrát vlastní privátní klíč, nebo můžete kontaktovat [TOPdesk - tým podpory zabezpečení klienta](http://www.topdesk.com/us/support) získat soukromý klíč.

    d. Nahrát soubor loga jste získali v části z na tým podpory TOPdesk **ikona loga**, klikněte na tlačítko **Procházet**.

    e. V **atribut uživatelského jména** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. V **zobrazovaný název** textového pole zadejte název pro vaši konfiguraci.

    g. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k TOPdesk – zabezpečené.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **TOPdesk – zabezpečené**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **TOPdesk – zabezpečené**.

    ![TOPdesk – zabezpečené připojení v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-topdesk---secure-test-user"></a>Vytvoření TOPdesk – zabezpečené testovacího uživatele

Chcete-li povolit uživatele Azure AD k přihlášení do TOPdesk – zabezpečené, se musí být poskytnuty do TOPdesk – zabezpečené.  
V případě TOPdesk – zabezpečené, zřizování je ruční úloha.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurace zřizování uživatelů, proveďte následující kroky:

1. Přihlaste se k vaší **TOPdesk – zabezpečené** společnosti serveru jako správce.

2. V nabídce v horní části klikněte na tlačítko **TOPdesk \> nový \> podpůrné soubory \> operátor**.

    ![Operator](./media/topdesk-secure-tutorial/ic790610.png "Operator")

3. Na **operátor New** dialogového okna, proveďte následující kroky:

    ![Operátor new](./media/topdesk-secure-tutorial/ic790611.png "New – operátor")

    a. Klikněte na tlačítko **Obecné** kartu.

    b. V **příjmení** , jako je textové pole, zadejte příjmení uživatele **Simon**.

    c. Vyberte **lokality** pro tento účet v **umístění** oddílu.

    d. V **přihlašovací jméno** textové pole z **TOPdesk přihlášení** části, zadejte přihlašovací jméno uživatele.

    e. Klikněte na **Uložit**.

> [!NOTE]
> Můžete použít jakékoli jiné TOPdesk – nástroje pro tvorbu zabezpečené uživatelského účtu nebo rozhraní API poskytovaných TOPdesk – zabezpečené ke zřízení účtů služby AAD uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na TOPdesk – zabezpečené dlaždici na přístupovém panelu, vám by měl být automaticky přihlášeni ke TOPdesk – zabezpečení u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

