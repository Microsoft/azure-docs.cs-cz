---
title: 'Kurz: Azure Active Directory integrace s cloudem SAP Analytics | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a cloudem SAP Analytics.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.openlocfilehash: 9edbb499788b61135d761c5dfa69ce42c9275932
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88548735"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Kurz: integrace cloudu SAP Analytics s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat službu SAP Analytics Cloud s Azure Active Directory (Azure AD). Když integrujete Cloud SAP Analytics s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke cloudu SAP Analytics.
* Umožněte uživatelům, aby se automaticky přihlásili ke cloudu SAP Analytics pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné SAP Analytics Cloud s jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cloud SAP Analytics podporuje jednotné přihlašování (SSO) iniciované **SP**

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>Přidání cloudu SAP Analytics z Galerie

Pokud chcete nakonfigurovat integraci služby SAP Analytics Cloud do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Cloud SAP Analytics z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Cloud SAP Analytics** .
1. Z panelu výsledků vyberte **Cloud SAP Analytics** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí služby SAP Analytics Cloud pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v cloudu SAP Analytics.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí cloudu SAP Analytics, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování pro SAP Analytics](#configure-sap-analytics-cloud-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořte uživatele cloudového testu SAP Analytics](#create-sap-analytics-cloud-test-user)** , abyste měli protějšek B. Simon ve službě SAP Analytics Cloud, který je propojený s reprezentací uživatele Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace s **cloudovou aplikací SAP Analytics** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:

    - `https://<sub-domain>.sapanalytics.cloud/`
    - `https://<sub-domain>.sapbusinessobjects.cloud/`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:

    - `<sub-domain>.sapbusinessobjects.cloud`
    - `<sub-domain>.sapanalytics.cloud`

    > [!NOTE] 
    > Hodnoty v těchto adresách URL jsou pouze pro ukázku. Aktualizujte hodnoty pomocí skutečné přihlašovací adresy URL a adresy URL identifikátoru. Pokud chcete získat přihlašovací adresu URL, obraťte se na [tým podpory pro cloudové klienty SAP Analytics](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Adresu URL identifikátoru můžete získat stažením metadat cloudu SAP Analytics z konzoly pro správu. To je vysvětleno dále v tomto kurzu.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení cloudu SAP Analytics** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>Konfigurace cloudového jednotného přihlašování SAP Analytics

1. V jiném okně webového prohlížeče se přihlaste k webu SAP Analytics Cloud společnosti jako správce.

2. Vyberte **Menu**možnost  >  **System**  >  **Správa**systému v nabídce.
    
    ![Vyberte nabídku, pak systém a potom na Správa.](./media/sapboc-tutorial/config1.png)

3. Na kartě **zabezpečení** vyberte ikonu **Upravit** (pero).
    
    ![Na kartě zabezpečení vyberte ikonu Upravit.](./media/sapboc-tutorial/config2.png)  

4. V případě **metody ověřování**vyberte **SAML Single Sign-On (SSO)**.

    ![Pro metodu ověřování vybrat jeden Sign-On SAML](./media/sapboc-tutorial/config3.png)  

5. Pokud chcete stáhnout metadata poskytovatele služeb (krok 1), vyberte **Stáhnout**. V souboru metadat vyhledejte a zkopírujte hodnotu **entityID** . V Azure Portal v základní dialogovém okně **Konfigurace SAML** vložte hodnotu do pole **identifikátor** .

    ![Zkopírování a vložení hodnoty entityID](./media/sapboc-tutorial/config4.png)  

6. Pokud chcete nahrát metadata poskytovatele služeb (krok 2) do souboru, který jste stáhli z Azure Portal, vyberte v části **nahrát metadata zprostředkovatele identity**možnost **nahrát**.  

    ![V části nahrát metadata zprostředkovatele identity vyberte nahrát.](./media/sapboc-tutorial/config5.png)

7. V seznamu **atribut uživatele** vyberte atribut uživatele (krok 3), který chcete použít pro vaši implementaci. Tento atribut uživatele se mapuje na zprostředkovatele identity. Pokud chcete na stránce uživatele zadat vlastní atribut, použijte možnost **vlastní mapování SAML** . Případně můžete jako atribut uživatele vybrat buď možnost **e-mail** , nebo **ID uživatele** . V našem příkladu jsme vybrali **e-mail** , protože jsme namapovali deklaraci identity uživatele s atributem **userPrincipalName** v části **atributy uživatele & deklarace** v Azure Portal. To poskytuje jedinečný e-mail uživatele, který se pošle do cloudové aplikace SAP Analytics v každé úspěšné odpovědi SAML.

    ![Vybrat atribut uživatele](./media/sapboc-tutorial/config6.png)

8. Pokud chcete ověřit účet se zprostředkovatelem identity (krok 4), zadejte do pole přihlašovací **údaje (e-mail)** e-mailovou adresu uživatele. Pak vyberte **ověřit účet**. Systém přidá pověření pro přihlášení k uživatelskému účtu.

    ![Zadejte e-mail a vyberte ověřit účet.](./media/sapboc-tutorial/config7.png)

9. Vyberte ikonu **Uložit** .

    ![Ikona Uložit](./media/sapboc-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke cloudu SAP Analytics.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Cloud SAP Analytics**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-sap-analytics-cloud-test-user"></a>Vytvořit uživatele cloudového testu SAP Analytics

Aby se uživatelé Azure AD mohli přihlásit ke cloudu SAP Analytics, musí se v cloudu SAP Analytics zřídit. V cloudu SAP Analytics je zřizování ručním úkolem.

Zřízení uživatelského účtu:

1. Přihlaste se k webu SAP Analytics Cloud Company jako správce.

2. Vyberte **Menu**možnost  >  **Security**  >  **Uživatelé**zabezpečení nabídky.

    ![Přidat zaměstnance](./media/sapboc-tutorial/user1.png)

3. Na stránce **Uživatelé** přidejte podrobnosti o novém uživateli výběrem **+** . 

    ![Stránka Přidat uživatele](./media/sapboc-tutorial/user4.png)

    Pak proveďte následující kroky:

    a. Do pole **ID uživatele** zadejte ID uživatele, například **B**.

    b. Do pole **jméno** zadejte jméno uživatele, například **B**.

    c. Do pole **příjmení** zadejte příjmení uživatele, například **Simon**.

    d. Do pole **ZOBRAZOVANÝ název** zadejte jméno a příjmení uživatele, například **B. Simon**.

    e. Do pole **e-mail** zadejte e-mailovou adresu uživatele, třeba `b.simon@contoso.com` .

    f. Na stránce **Vybrat role** vyberte příslušnou roli pro uživatele a pak vyberte **OK**.

      ![Vybrat roli](./media/sapboc-tutorial/user3.png)

    například Vyberte ikonu **Uložit** .

### <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici cloudu SAP Analytics na přístupovém panelu byste se měli automaticky přihlášeni ke cloudu SAP Analytics, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

