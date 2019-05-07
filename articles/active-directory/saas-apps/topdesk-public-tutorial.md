---
title: 'Kurz: Integrace Azure Active Directory s TOPdesk - Public | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TOPdesk - Public.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: c4052dd3b4c8f49b19193109eb5e8d2306646960
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192562"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Kurz: Integrace Azure Active Directory s TOPdesk – veřejné

V tomto kurzu se dozvíte, jak integrovat TOPdesk - veřejnosti s Azure Active Directory (Azure AD).
Integrace TOPdesk - veřejnosti s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k TOPdesk - Public.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k TOPdesk – Public (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TOPdesk – veřejné, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk – veřejné jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje veřejné TOPdesk - **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-topdesk---public-from-the-gallery"></a>Přidání TOPdesk - Public z Galerie

Konfigurace integrace TOPdesk - Public do služby Azure AD, potřebujete přidat TOPdesk - Public z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat TOPdesk - Public z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TOPdesk - Public**vyberte **TOPdesk - Public** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![TOPdesk - Public v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, nakonfigurovat a otestovat Azure AD jednotné přihlašování s TOPdesk – Public podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování k práci, vztah odkazu mezi uživatele služby Azure AD a související uživatelské v TOPdesk – Public potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TOPdesk - Public, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace TOPdesk – veřejné Single Sign-On](#configure-topdesk---public-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření TOPdesk – veřejné testovacího uživatele](#create-topdesk---public-test-user)**  – Pokud chcete mít protějšek Britta Simon TOPdesk - Public, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s TOPdesk – veřejné, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **TOPdesk - Public** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4.  Na **základní konfiguraci SAML** části, pokud máte **soubor metadat poskytovatele služeb**, proveďte následující kroky:

    >[!NOTE]
    >Zobrazí se **soubor metadat poskytovatele služeb** z **TOPdesk konfigurace – veřejné Single Sign-On** oddílu, který je vysvětlen později v tomto kurzu.

    a. Klikněte na tlačítko **nahrát soubor metadat**.
    
    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![Zvolte soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v části základní konfiguraci SAML.

    ![TOPdesk – veřejné domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    d. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.topdesk.net`

    e. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.topdesk.net/tas/public/login/verify`

    > [!NOTE] 
    > Pokud **identifikátor** a **adresy URL odpovědi** hodnoty nezobrazí automaticky vyplní, budete muset zadat ručně. Pro identifikátor, tvar, jak je uvedeno výše, a získáte hodnotu adresy URL odpovědi z **TOPdesk konfigurace – veřejné Single Sign-On** oddílu, který je vysvětlen později v tomto kurzu. **Přihlašovací adresa URL** hodnota není skutečný, proto musíte zaktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [TOPdesk - tým podpory veřejným klientem](https://help.topdesk.com/saas/enterprise/user/) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení TOPdesk - Public** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-topdesk---public-single-sign-on"></a>Konfigurace TOPdesk – veřejné jednotného přihlašování

1. Přihlaste se k vaší **TOPdesk - Public** společnosti serveru jako správce.

2. V **TOPdesk** nabídky, klikněte na tlačítko **nastavení**.
   
    ![Nastavení](./media/topdesk-public-tutorial/ic790598.png "nastavení")

3. Klikněte na tlačítko **nastavení přihlášení**.
   
    ![Nastavení přihlášení](./media/topdesk-public-tutorial/ic790599.png "nastavení přihlášení")

4. Rozbalte **nastavení přihlášení** nabídky a pak klikněte na tlačítko **Obecné**.
   
    ![Obecné](./media/topdesk-public-tutorial/ic790600.png "obecné")

5. V **veřejné** část **SAML přihlášení** konfigurace části, proveďte následující kroky:
   
    ![Technické nastavení](./media/topdesk-public-tutorial/ic790601.png "technické nastavení")
   
    a. Klikněte na tlačítko **Stáhnout** ke stažení souboru metadat veřejné a pak ho uložte místně ve vašem počítači.
   
    b. Otevřete soubor stažený metadat a najděte **AssertionConsumerService** uzlu.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Kopírovat **AssertionConsumerService** hodnota a vložte tuto hodnotu v **adresy URL odpovědi** textového pole v **základní konfiguraci SAML** oddílu.      
   
6. Chcete-li vytvořit soubor certifikátu, proveďte následující kroky:
    
    ![Certifikát](./media/topdesk-public-tutorial/ic790606.png "certifikátu")
    
    a. Otevřete soubor stažený metadat z webu Azure portal.
    
    b. Rozbalte **RoleDescriptor** uzel, který má **xsi: type** z **dodáni: ApplicationServiceType**.
    
    c. Zkopírujte hodnotu **certifikátu x 509** uzlu.
    
    d. Uložit zkopírovaný **certifikátu x 509** hodnotu místně na vašem počítači v souboru.

7. V **veřejné** klikněte na tlačítko **přidat**.
    
    ![Přihlášení SAML](./media/topdesk-public-tutorial/ic790625.png "SAML přihlášení")

8. Na **pomocníka s nastavením konfigurace SAML** dialogového okna stránky, proveďte následující kroky:
    
    ![Pomocníka s nastavením konfigurace SAML](./media/topdesk-public-tutorial/ic790608.png "Pomocníka s nastavením konfigurace SAML")
    
    a. Chcete-li nahrát soubor metadat stažené z webu Azure portal v části **federačních metadat**, klikněte na tlačítko **Procházet**.

    b. K nahrání souboru certifikátu v části **certifikátu (RSA)**, klikněte na tlačítko **Procházet**.

    c. Nahrát soubor loga jste získali v části z na tým podpory TOPdesk **ikona loga**, klikněte na tlačítko **Procházet**.

    d. V **atribut uživatelského jména** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. V **zobrazovaný název** textového pole zadejte název pro vaši konfiguraci.

    f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k TOPdesk - Public.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **TOPdesk - Public**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **TOPdesk - Public**.

    ![TOPdesk - veřejný odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-topdesk---public-test-user"></a>Vytvoření TOPdesk – veřejné testovacího uživatele

Chcete-li povolit Azure AD uživatelům přihlašovat se do TOPdesk - veřejný, musí být poskytnuty do TOPdesk - Public. V případě TOPdesk – veřejné, zřizování se ruční úlohy.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurace zřizování uživatelů, proveďte následující kroky:

1. Přihlaste se k vaší **TOPdesk - Public** společnosti serveru jako správce.

2. V nabídce v horní části klikněte na tlačítko **TOPdesk \> nový \> podpůrné soubory \> osoba**.
   
    ![Osoba](./media/topdesk-public-tutorial/ic790628.png "osoby")

3. V dialogovém okně Nový uživatel proveďte následující kroky:
   
    ![Nové osobě](./media/topdesk-public-tutorial/ic790629.png "nové osoby")
   
    a. Klikněte na kartu Obecné.

    b. V **příjmení** textového pole zadejte příjmení uživatele, jako je Simon
 
    c. Vyberte **lokality** pro účet.
 
    d. Klikněte na **Uložit**.

> [!NOTE]
> Můžete použít jakékoli jiné TOPdesk – zadáním hodnoty nástrojů pro vytváření veřejné uživatelského účtu nebo rozhraní API poskytovaných TOPdesk - Public zřízení uživatelských účtů služby Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete TOPdesk – Public dlaždici na přístupovém panelu, vám by měl být automaticky přihlášeni ke TOPdesk - Public, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
