---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování pomocí ADP | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ADP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.openlocfilehash: 0d78f2c8da338a92ef88734371647a48ddb190c3
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591187"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) pomocí ADP

V tomto kurzu se naučíte integrovat ADP s Azure Active Directory (Azure AD). Když ve službě Azure AD integrujete ADP, můžete:

* Řízení ve službě Azure AD, která má přístup k ADP.
* Umožněte uživatelům, aby se do ADP automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (SSO) ADP.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ADP podporuje jednotné přihlašování (SSO) iniciované **IDP**

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-adp-from-the-gallery"></a>Přidávání ADP z Galerie

Pokud chcete nakonfigurovat integraci ADP do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat ADP z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **ADP** .
1. Na panelu výsledků vyberte **ADP** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>Konfigurace a testování jednotného přihlašování Azure AD pro ADP

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí ADP pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte v e-mailu vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v části ADP.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ADP, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
2. **[Nakonfigurujte jednotné přihlašování ADP](#configure-adp-sso)** – ke konfiguraci jednoho Sign-On nastavení na straně aplikace.
    1. **[Vytvořte testovacího uživatele ADP](#create-adp-test-user)** , abyste měli protějšek B. Simon ve ADP, který je propojený s reprezentací uživatele Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **ADP** klikněte na **kartu vlastnosti** a proveďte následující kroky: 

    ![Vlastnosti jednotného přihlašování](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Nastavte hodnotu pole **povoleno pro uživatele na** možnost **Ano**.

    b. Zkopírujte **adresu URL přístupu uživatele** a musíte ji vložit v **části konfigurace přihlašovacích adres URL**, která je vysvětlena dále v tomto kurzu.

    c. Nastavte hodnotu pole **vyžadováno přiřazení uživatele** na **Ano**.

    d. Nastavte hodnotu pole **viditelná pro uživatele** na **ne**.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **ADP** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    Do textového pole **identifikátor (ID entity)** zadejte adresu URL:  `https://fed.adp.com`

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **nastavit ADP** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k ADP.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **ADP**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-adp-sso"></a>Konfigurace jednotného přihlašování ADP

Ke konfiguraci jednotného přihlašování na straně **ADP** je potřeba nahrát stažené **metadata XML** na [webu ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Tento proces může trvat několik dní.

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurace služby ADP pro federovaný přístup

>[!Important]
> Zaměstnanci, kteří vyžadují federovaný přístup ke službám ADP, musí být přiřazeni k aplikaci služby ADP a následně musí být uživatelé přiřazeni ke konkrétní službě ADP.
Po přijetí potvrzení od zástupce ADP nakonfigurujte vaše služby ADP a přiřaďte/Spravujte uživatele, abyste mohli řídit přístup uživatelů ke konkrétní službě ADP.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **ADP** .
1. Na panelu výsledků vyberte **ADP** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.
1. V Azure Portal na stránce integrace aplikace **ADP** klikněte na **kartu vlastnosti** a proveďte následující kroky:  

    ![Propojené vlastnosti jednotného přihlašování](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    1. Nastavte hodnotu pole **povoleno pro uživatele na** možnost **Ano**.

    1. Nastavte hodnotu pole **vyžadováno přiřazení uživatele** na **Ano**.

    1. Nastavte hodnotu pole **viditelné pro uživatele** na **Ano**.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **ADP** najděte část **Správa** a vyberte **jednotné přihlašování**.

1. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost **režim** jako **propojený**. propojení aplikace s **ADP**.

    ![Propojení s jednotným přihlašováním](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Přejděte do části **konfigurace přihlašovací adresy URL** , proveďte následující kroky:

    ![Prop – jednotné přihlašování](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    1. Vložte **adresu URL přístupu uživatele**, kterou jste zkopírovali z **karty vlastností** výše (z hlavní aplikace ADP).

    1. Níže jsou uvedené 5 aplikací, které podporují různé **adresy URL stavu přenosu**. Do **adresy URL přístupu uživatele** musíte ručně připojit příslušnou hodnotu **adresy URL stavu přenosu** konkrétní aplikace.

        * **Nyní můžete pracovní síly ADP**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

        * **Pracovní síly ADP teď zvyšují čas**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`

        * **Vantage HCM ADP**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

        * **ADP Enterprise HR**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

        * **MyADP**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

1. **Uložte** změny.

1. Po přijetí potvrzení od zástupce ADP spusťte test s jedním nebo dvěma uživateli.

    1. K otestování federovaného přístupu přiřaďte k aplikaci služby ADP několik uživatelů.

    1. Test je úspěšný, když uživatelé přistupují k aplikaci služby ADP v galerii a mají přístup ke službě ADP.

1. Po potvrzení úspěšného testu přiřaďte službu federovaného ADP jednotlivým uživatelům nebo skupinám uživatelů, které jsou vysvětleny dále v kurzu a převeďte je do vašich zaměstnanců.

### <a name="create-adp-test-user"></a>Vytvořit testovacího uživatele ADP

Cílem této části je vytvořit uživatele s názvem B. Simon v ADP. Pracujte s [týmem podpory ADP](https://www.adp.com/contact-us/overview.aspx) a přidejte uživatele do účtu ADP. 

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici ADP na přístupovém panelu, měli byste se automaticky přihlásit ke ADP, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)