---
title: 'Kurz: Azure Active Directory integrace s ADP | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1597a4ca9cac7ba3885e863502f156d4c83aeed1
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516388"
---
# <a name="tutorial-integrate-adp-with-azure-active-directory"></a>Kurz: Integrace ADP s Azure Active Directory

V tomto kurzu se naučíte integrovat ADP s Azure Active Directory (Azure AD). Když ve službě Azure AD integrujete ADP, můžete:

* Řízení ve službě Azure AD, která má přístup k ADP.
* Umožněte uživatelům, aby se do ADP automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (SSO) ADP.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ADP podporuje jednotné přihlašování (SSO) iniciované **IDP**

## <a name="adding-adp-from-the-gallery"></a>Přidávání ADP z Galerie

Pokud chcete nakonfigurovat integraci ADP do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat ADP z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **ADP** .
1. Na panelu výsledků vyberte **ADP** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí ADP pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte v e-mailu vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v části ADP.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ADP, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování ADP](#configure-adp-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele ADP](#create-adp-test-user)** , abyste měli protějšek B. Simon ve ADP, který je propojený s reprezentací uživatele Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **ADP** klikněte na **kartu vlastnosti** a proveďte následující kroky: 

    ![Vlastnosti jednotného přihlašování](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Nastavte hodnotu pole **povoleno pro uživatele na** možnost **Ano**.

    b. Zkopírujte **adresu URL přístupu uživatele** a musíte ji vložit v **části konfigurace přihlašovacích adres URL**, která je vysvětlena dále v tomto kurzu.

    c. Nastavte hodnotu pole **vyžadováno přiřazení uživatele** na **Ano**.

    d. Nastavte hodnotu pole **viditelná pro uživatele** na **ne**.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **ADP** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    Do textového pole **identifikátor (ID entity)** zadejte adresu URL:`https://fed.adp.com`

5. Aplikace ADP očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno atributy uživatele. Název deklarace identity bude vždycky **PersonImmutableID** a hodnota, kterou jsme ukázali, že se má mapovat s **ČísloZaměstnance**.

    Mapování uživatele z Azure AD na ADP bude provedeno v poli **ČísloZaměstnance** , ale můžete ho namapovat na jinou hodnotu na základě nastavení aplikace. Aby bylo možné použít správný identifikátor uživatele a namapovat tuto hodnotu s deklarací **PersonImmutableID** , pracujte nejprve s [týmem podpory ADP](https://www.adp.com/contact-us/overview.aspx) .

    ![image](common/edit-attribute.png)

6. Kromě výše očekává aplikace ADP několik dalších atributů, které se vrátí zpátky v odpovědi SAML. V části deklarace identity uživatelů v dialogovém okně atributy uživatele proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce: 

    | Name | Zdrojový atribut|
    | ---------------| --------- |
    | PersonImmutableID  | user.employeeid |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **Uložit**.

    > [!NOTE] 
    > Než budete moct nakonfigurovat kontrolní výraz SAML, musíte se obrátit na [tým podpory ADP](https://www.adp.com/contact-us/overview.aspx) a požádat o hodnotu jedinečného atributu identifikátoru uživatele pro vašeho tenanta. Tuto hodnotu budete potřebovat ke konfiguraci vlastní deklarace identity pro vaši aplikaci. 

4. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **nastavit ADP** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-adp-sso"></a>Konfigurace jednotného přihlašování ADP

Ke konfiguraci jednotného přihlašování na straně **ADP** je potřeba nahrát stažené **metadata XML** na [webu ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Tento proces může trvat několik dní.

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurace služby ADP pro federovaný přístup

>[!Important]
> Zaměstnanci, kteří vyžadují federovaný přístup ke službám ADP, musí být přiřazeni k aplikaci služby ADP a následně musí být uživatelé přiřazeni ke konkrétní službě ADP.
Po přijetí potvrzení od zástupce ADP nakonfigurujte vaše služby ADP a přiřaďte/Spravujte uživatele, abyste mohli řídit přístup uživatelů ke konkrétní službě ADP.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **ADP** .
1. Na panelu výsledků vyberte **ADP** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.
1. V Azure Portal na stránce integrace aplikace **ADP** klikněte na **kartu vlastnosti** a proveďte následující kroky:  

    ![Propojené vlastnosti jednotného přihlašování](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Nastavte hodnotu pole **povoleno pro uživatele na** možnost **Ano**.

    b.  Nastavte hodnotu pole **vyžadováno přiřazení uživatele** na **Ano**.

    c.  Nastavte hodnotu pole **viditelné pro uživatele** na **Ano**.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **ADP** najděte část **Správa** a vyberte **jednotné přihlašování**.

1. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost **režim** jako **propojený**. propojení aplikace s **ADP**.

    ![Propojení s jednotným přihlašováním](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Přejděte do části **konfigurace přihlašovací adresy URL** , proveďte následující kroky:

    ![Prop – jednotné přihlašování](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Vložte **adresu URL přístupu uživatele**, kterou jste zkopírovali z **karty vlastností** výše (z hlavní aplikace ADP).
                                                             
    b. Níže jsou uvedené 5 aplikací, které podporují různé **adresy URL stavu přenosu**. Do **adresy URL přístupu uživatele**musíte ručně připojit příslušnou hodnotu **adresy URL stavu přenosu** konkrétní aplikace.
    
    * **Nyní můžete pracovní síly ADP**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **Pracovní síly ADP teď zvyšují čas**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Uložte** změny.

10. Po přijetí potvrzení od zástupce ADP spusťte test s jedním nebo dvěma uživateli.

    a. K otestování federovaného přístupu přiřaďte k aplikaci služby ADP několik uživatelů.

    b. Test je úspěšný, když uživatelé přistupují k aplikaci služby ADP v galerii a mají přístup ke službě ADP.
 
11. Po potvrzení úspěšného testu přiřaďte službu federovaného ADP jednotlivým uživatelům nebo skupinám uživatelů, které jsou vysvětleny dále v kurzu a převeďte je do vašich zaměstnanců.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k ADP.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **ADP**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-adp-test-user"></a>Vytvořit testovacího uživatele ADP

Cílem této části je vytvořit uživatele s názvem B. Simon v ADP. Pracujte s [týmem podpory ADP](https://www.adp.com/contact-us/overview.aspx) a přidejte uživatele do účtu ADP. 

### <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici ADP na přístupovém panelu, měli byste se automaticky přihlásit ke ADP, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

