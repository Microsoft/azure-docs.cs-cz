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
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 19bdb6d2a586dcb279687673c7fa4e302dc4928b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652636"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Kurz: integrace cloudu SAP Analytics s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat službu SAP Analytics Cloud s Azure Active Directory (Azure AD). Když integrujete Cloud SAP Analytics s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke cloudu SAP Analytics.
* Umožněte uživatelům, aby se automaticky přihlásili ke cloudu SAP Analytics pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné SAP Analytics Cloud s jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cloud SAP Analytics podporuje jednotné přihlašování (SSO) iniciované v **SP** .

## <a name="add-sap-analytics-cloud-from-the-gallery"></a>Přidání cloudu SAP Analytics z Galerie

Pokud chcete nakonfigurovat integraci služby SAP Analytics Cloud do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Cloud SAP Analytics z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Cloud SAP Analytics** .
1. Z panelu výsledků vyberte **Cloud SAP Analytics** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-sap-analytics-cloud"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro SAP Analytics Cloud

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí služby SAP Analytics Cloud pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v cloudu SAP Analytics.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí cloudu SAP Analytics, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace nástroje SAP Analytics cloud SSO](#configure-sap-analytics-cloud-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte uživatele cloudového testu SAP Analytics](#create-sap-analytics-cloud-test-user)** , abyste měli protějšek B. Simon ve službě SAP Analytics Cloud, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace s **cloudovou aplikací SAP Analytics** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí jednoho z následujících vzorů:

    - `https://<sub-domain>.sapanalytics.cloud/`
    - `https://<sub-domain>.sapbusinessobjects.cloud/`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí jednoho z následujících vzorů:

    - `<sub-domain>.sapbusinessobjects.cloud`
    - `<sub-domain>.sapanalytics.cloud`

    > [!NOTE] 
    > Hodnoty v těchto adresách URL jsou pouze pro ukázku. Aktualizujte hodnoty pomocí skutečné přihlašovací adresy URL a adresy URL identifikátoru. Pokud chcete získat přihlašovací adresu URL, obraťte se na [tým podpory pro cloudové klienty SAP Analytics](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Adresu URL identifikátoru můžete získat stažením metadat cloudu SAP Analytics z konzoly pro správu. To je vysvětleno dále v tomto kurzu.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení cloudu SAP Analytics** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke cloudu SAP Analytics.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Cloud SAP Analytics**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-sap-analytics-cloud-sso"></a>Konfigurace cloudového jednotného přihlašování SAP Analytics

1. V jiném okně webového prohlížeče se přihlaste k webu SAP Analytics Cloud společnosti jako správce.

2. Vyberte možnost  >    >  **Správa** systému v nabídce.
    
    ![Vyberte nabídku, pak systém a potom na Správa.](./media/sapboc-tutorial/configure-1.png)

3. Na kartě **zabezpečení** vyberte ikonu **Upravit** (pero).
    
    ![Na kartě zabezpečení vyberte ikonu Upravit.](./media/sapboc-tutorial/configure-2.png)  

4. V případě **metody ověřování** vyberte **SAML Single Sign-On (SSO)**.

    ![Pro metodu ověřování vybrat jeden Sign-On SAML](./media/sapboc-tutorial/configure-3.png)  

5. Pokud chcete stáhnout metadata poskytovatele služeb (krok 1), vyberte **Stáhnout**. V souboru metadat vyhledejte a zkopírujte hodnotu **entityID** . V Azure Portal v základní dialogovém okně **Konfigurace SAML** vložte hodnotu do pole **identifikátor** .

    ![Zkopírování a vložení hodnoty entityID](./media/sapboc-tutorial/configure-4.png)  

6. Pokud chcete nahrát metadata poskytovatele služeb (krok 2) do souboru, který jste stáhli z Azure Portal, vyberte v části **nahrát metadata zprostředkovatele identity** možnost **nahrát**.  

    ![V části nahrát metadata zprostředkovatele identity vyberte nahrát.](./media/sapboc-tutorial/configure-5.png)

7. V seznamu **atribut uživatele** vyberte atribut uživatele (krok 3), který chcete použít pro vaši implementaci. Tento atribut uživatele se mapuje na zprostředkovatele identity. Pokud chcete na stránce uživatele zadat vlastní atribut, použijte možnost **vlastní mapování SAML** . Případně můžete jako atribut uživatele vybrat buď možnost **e-mail** , nebo **ID uživatele** . V našem příkladu jsme vybrali **e-mail** , protože jsme namapovali deklaraci identity uživatele s atributem **userPrincipalName** v části **atributy uživatele & deklarace** v Azure Portal. To poskytuje jedinečný e-mail uživatele, který se pošle do cloudové aplikace SAP Analytics v každé úspěšné odpovědi SAML.

    ![Vybrat atribut uživatele](./media/sapboc-tutorial/configure-6.png)

8. Pokud chcete ověřit účet se zprostředkovatelem identity (krok 4), zadejte do pole přihlašovací **údaje (e-mail)** e-mailovou adresu uživatele. Pak vyberte **ověřit účet**. Systém přidá pověření pro přihlášení k uživatelskému účtu.

    ![Zadejte e-mail a vyberte ověřit účet.](./media/sapboc-tutorial/configure-7.png)

9. Vyberte ikonu **Uložit** .

    ![Ikona Uložit](./media/sapboc-tutorial/save.png)

### <a name="create-sap-analytics-cloud-test-user"></a>Vytvořit uživatele cloudového testu SAP Analytics

Aby se uživatelé Azure AD mohli přihlásit ke cloudu SAP Analytics, musí se v cloudu SAP Analytics zřídit. V cloudu SAP Analytics je zřizování ručním úkolem.

Zřízení uživatelského účtu:

1. Přihlaste se k webu SAP Analytics Cloud Company jako správce.

2. Vyberte možnost  >    >  **Uživatelé** zabezpečení nabídky.

    ![Přidat zaměstnance](./media/sapboc-tutorial/user-1.png)

3. Na stránce **Uživatelé** přidejte podrobnosti o novém uživateli výběrem **+** . 

    ![Stránka Přidat uživatele](./media/sapboc-tutorial/user-4.png)

    Pak proveďte následující kroky:

    1. Do pole **ID uživatele** zadejte ID uživatele, například **B**.

    1. Do pole **jméno** zadejte jméno uživatele, například **B**.

    1. Do pole **příjmení** zadejte příjmení uživatele, například **Simon**.

    1. Do pole **ZOBRAZOVANÝ název** zadejte jméno a příjmení uživatele, například **B. Simon**.

    1. Do pole **e-mail** zadejte e-mailovou adresu uživatele, třeba `b.simon@contoso.com` .

    1. Na stránce **Vybrat role** vyberte příslušnou roli pro uživatele a pak vyberte **OK**.

        ![Vybrat roli](./media/sapboc-tutorial/user-3.png)

    1. Vyberte ikonu **Uložit** .

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení do cloudu SAP Analytics, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL cloudového přihlašování SAP Analytics přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Cloud SAP Analytics v části Moje aplikace, přesměruje se na adresu URL pro přihlášení do cloudu SAP Analytics. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete Cloud SAP Analytics, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).