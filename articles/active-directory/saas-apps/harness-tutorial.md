---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s využitím kabelů | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednoduchým způsobem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.openlocfilehash: e49fc29f03269096bfc7d6bb6e905187bb8788ff
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91944064"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s využitím

V tomto kurzu se dozvíte, jak integrovat pomocí Azure Active Directory (Azure AD). Když integrujete službu pomocí Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k předanému svazku.
* Umožněte, aby se vaši uživatelé automaticky přihlásili ke svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Využijte předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Kabelový svazek podporuje **SP a IDP** iniciované jednotné přihlašování.

## <a name="adding-harness-from-the-gallery"></a>Přidání kabelů z Galerie

Pokud chcete nakonfigurovat integraci se systémem do služby Azure AD, musíte z Galerie přidat ze seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** **Zadejte do** vyhledávacího pole text.
1. Na panelu výsledků vyberte možnost **svazek** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>Konfigurace a testování jednotného přihlašování Azure AD pro svazek

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí využívajících testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v nástroji.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí nástroje, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat jednotné přihlašování](#configure-harness-sso)** ke službě pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte testovacího uživatele](#create-harness-test-user)** , abyste měli protějšek B. Simon ve svazku, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **integrace aplikace na** více počítačích najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:  `https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://app.harness.io/`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není reálné číslo. V části Konfigurace nástroje pro **jednotné přihlašování** se zobrazí skutečná adresa URL odpovědi, která se vysvětluje později v tomto kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavit svazek** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke svazku.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **převyužívat**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-harness-sso"></a>Konfigurace jednotného přihlašování ke kabelům

1. Chcete-li automatizovat konfiguraci v rámci rozšíření, je nutné nainstalovat **rozšíření prohlížeče zabezpečeného přihlašování aplikace** kliknutím na tlačítko **nainstalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **instalační program** , který vás nasměruje do aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení ke službě. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit vlastní nastavení ručně, otevřete nové okno webového prohlížeče a přihlaste se k webovému serveru společnosti jako správce a proveďte následující kroky:

4. V pravém horním rohu stránky klikněte na **Continuous Security**  >  **Access Management**  >  **nastavení ověřování**pro správu nepřetržitého přístupu zabezpečení.

    ![Snímek obrazovky, který zobrazuje nabídku "průběžné zabezpečení" s vybraným nastavením "Správa přístupu" a "nastavení ověřování".](./media/harness-tutorial/configure01.png)

5. V části **Zprostředkovatelé jednotného přihlašování** klikněte na **+ Přidat poskytovatele jednotného přihlašování**  >  **SAML**.

    ![Snímek obrazovky, který zobrazuje poskytovatele "S S" s "+ Add-s" Providers-S je vybraný A M L.](./media/harness-tutorial/configure03.png)

6. V automaticky otevíraném okně **zprostředkovatele SAML** proveďte následující kroky:

    ![Snímek obrazovky s vybranými poli "U R L" a "zobrazovaného názvu", které obsahuje zvýrazněná pole "U R L" a "zobrazovaný název" a výběr tlačítek "vybrat soubor" a "Odeslat".](./media/harness-tutorial/configure02.png)

    a. Zkopírujte **do svého poskytovatele jednotného přihlašování, povolte prosím přihlašovací jméno založené na SAML a pak zadejte následující instanci adresy URL** a vložte ji do textového pole Adresa URL odpovědi v tématu **základní konfigurace SAML** na Azure Portal.

    b. Do textového pole **Zobrazovaný název** zadejte své zobrazované jméno.

    c. Klikněte na **zvolit soubor** a nahrajte soubor XML federačních metadat, který jste stáhli ze služby Azure AD.

    d. Klikněte na **Odeslat**.

### <a name="create-harness-test-user"></a>Vytvořit testovacího uživatele pro strojové testování

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili ke svým kabelům, musí se zřídit ve svém využívání. Ve svazku je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke službě jako správce.

1. V pravém horním rohu stránky klikněte na **nepřetržitý**  >  Uživatel**pro správu zabezpečení přístupu**  >  **Users**.

    ![Snímek obrazovky, který zobrazuje nabídku průběžného zabezpečení se zvolenými možnostmi Správa přístupu a uživatelé.](./media/harness-tutorial/configure04.png)

1. Na pravé straně stránky klikněte na **+ Přidat uživatele**.

    ![Snímek obrazovky zobrazující stránku Uživatelé s vybranou akcí + přidat uživatele](./media/harness-tutorial/configure05.png)

1. V automaticky otevíraném okně **Přidat uživatele** proveďte následující kroky:

    ![Konfigurace svazku](./media/harness-tutorial/configure06.png)

    a. Do textového pole **e-mailová adresa** zadejte e-maily uživatele, jako je `B.simon@contoso.com` .

    b. Vyberte **skupiny uživatelů**.

    c. Klikněte na **Odeslat**.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici se předepsanou na přístupovém panelu byste měli být automaticky přihlášení ke svazku, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si pomocí Azure AD](https://aad.portal.azure.com/)

