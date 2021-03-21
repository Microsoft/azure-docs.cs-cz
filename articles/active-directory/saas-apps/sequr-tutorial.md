---
title: 'Kurz: Azure Active Directory integrace s genovou Access Control | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a genovou Access Control.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 82c05f77781abdaea3b2c84aa1071656c206439a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669853"
---
# <a name="tutorial-azure-active-directory-integration-with-genea-access-control"></a>Kurz: Azure Active Directory integrace s genovou a Access Control

V tomto kurzu se dozvíte, jak integrovat Access Control genů pomocí Azure Active Directory (Azure AD). Když integrujete genovou Access Control s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k genové Access Control.
* Umožněte uživatelům, aby se automaticky přihlásili do genové Access Control s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s Access Control genové aplikace potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Genová Access Control předplatné s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Genů Access Control podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.
> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.


## <a name="adding-genea-access-control-from-the-gallery"></a>Přidání Access Control genů z Galerie

Pokud chcete nakonfigurovat integraci genové Access Control do služby Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat Access Control genů z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole typ **genů Access Control** .
1. Z panelu výsledků vyberte možnost **genová Access Control** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-genea-access-control"></a>Konfigurace a testování jednotného přihlašování Azure AD pro genovou a Access Control

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s genovou a Access Control pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v genové Access Control.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Access Control genů, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte genovou Access Control SSO](#configure-genea-access-control-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte si Access Control testovacího uživatele](#create-genea-access-control-test-user)** , abyste měli protějšek B. Simon ve službě genů Access Control, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **genů Access Control** Application Integration, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující krok:

    Do textového pole **identifikátor** zadejte adresu URL:  `https://login.sequr.io`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://login.sequr.io`

    b. Do textového pole **stav přenosu** se zobrazí tato hodnota, která je vysvětleno dále v tomto kurzu.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **nastavit genovou Access Control** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k genové Access Control.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **genová Access Control**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .
## <a name="configure-genea-access-control-sso"></a>Konfigurace genové Access Control jednotného přihlašování

1. V jiném okně webového prohlížeče se přihlaste ke svojí Access Control firemnímu webu jako správce.

1. V levém navigačním panelu klikněte na **integrace** .

    ![Snímek obrazovky znázorňuje integraci vybranou z navigačního panelu.](./media/sequr-tutorial/configure-1.png)

1. Přejděte dolů k části **jednotné přihlašování** a klikněte na **Spravovat**.

    ![Snímek obrazovky s vybraným tlačítkem spravovat zobrazí oddíl jednotného přihlašování.](./media/sequr-tutorial/configure-2.png)

1. V části **Spravovat jednotné přihlašování** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí oddíl spravovat jednu Sign-On, kde můžete zadat hodnoty, které jsou popsány.](./media/sequr-tutorial/configure-3.png)

    a. Do textového pole **URL s jedním Sign-On URL poskytovatele identity** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. Přetáhněte soubor **certifikátu** , který jste stáhli z Azure Portal, nebo ručně zadejte obsah certifikátu.

    c. Po uložení konfigurace se vygeneruje hodnota stav přenosu. Zkopírujte hodnotu **stavu přenosu** a vložte ji do textového pole **stav přenosu** **základního konfiguračního oddílu SAML** v Azure Portal.

    d. Klikněte na **Uložit**.

### <a name="create-genea-access-control-test-user"></a>Vytvořit genovou Access Control testovacího uživatele

V této části vytvoříte uživatele s názvem Britta Simon v genové Access Control. K práci s [genovou a Access Control týmem podpory klientů](mailto:support@sequr.io) můžete přidávat uživatele v Access Control platformě genů. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na genovou adresu Access Control přihlašovací adresa URL, kde můžete spustit tok přihlášení.  

* Přejít na genovou adresu Access Control adresa URL pro přihlášení přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k genové Access Control, pro kterou jste si nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Access Control genů v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro zahájení toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k Access Control genové a pro které jste si nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete genovou Access Control, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).