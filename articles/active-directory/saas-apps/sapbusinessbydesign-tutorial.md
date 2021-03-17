---
title: 'Kurz: Azure Active Directory integrace se SAP Business ByDesign | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP Business ByDesign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 6db863f43deb6eb2787cda60650a267a62076aad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654332"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Kurz: Integrace Azure Active Directory se službou SAP Business ByDesign

V tomto kurzu se dozvíte, jak integrovat SAP Business ByDesign s Azure Active Directory (Azure AD). Když integrujete SAP Business ByDesign s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SAP Business ByDesign.
* Umožněte uživatelům, aby se k SAP Business ByDesign automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné SAP Business ByDesign s podporou jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SAP Business ByDesign podporuje jednotné přihlašování spouštěné v **SP**

## <a name="add-sap-business-bydesign-from-the-gallery"></a>Přidání SAP Business ByDesign z Galerie

Pokud chcete nakonfigurovat integraci SAP Business ByDesign do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat SAP Business ByDesign z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **SAP Business ByDesign** .
1. Z panelu výsledků vyberte **SAP Business ByDesign** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SAP Business ByDesign pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SAP Business ByDesign.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SAP Business ByDesign, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte jednotné přihlašování SAP Business ByDesign](#configure-sap-business-bydesign-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
    1. **[Vytvořte testovacího uživatele SAP Business ByDesign](#create-sap-business-bydesign-test-user)** , abyste měli protějšek Britta Simon v SAP Business ByDesign, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **SAP Business ByDesign** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<servername>.sapbydesign.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory pro klienta SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Aplikace SAP Business ByDesign očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jeden Sign-On se** stránkou SAML kliknutím na tlačítko **Upravit** otevřete dialogové okno **atributy uživatele** .

    ![image1](common/edit-attribute.png)

6. Kliknutím na ikonu **Upravit** upravíte **hodnotu identifikátoru názvu**.

    ![image2](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. V části **Spravovat deklarace identity uživatelů** proveďte následující kroky:

    ![image3](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Jako **zdroj** vyberte **transformovat** .

    b. V rozevíracím seznamu **transformace** vyberte **ExtractMailPrefix ()**.

    > [!NOTE]
    > Pro mapování uživatele používá ByD ve výchozím nastavení **nespecifikovaný** formát NameId. ByD mapuje NameID kontrolních výrazů SAML na aliasu uživatele ByD. Navíc ByD podporuje formát ID **EmailAddress**. V tomto případě ByD mapuje NameID pro příkaz SAM-assertion na e-mailovou adresu ByD uživatele kontaktní data ByD zaměstnanců. Další podrobnosti najdete na [tomto blogu SAP](https://blogs.sap.com/2017/05/24/single-sign-on-sso-with-sap-business-bydesign/).

8. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

9. V části **nastavení SAP Business ByDesign** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k SAP Business ByDesign.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SAP Business ByDesign**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.

1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-sap-business-bydesign-sso"></a>Konfigurace jednotného přihlašování SAP Business ByDesign

1. Přihlaste se k portálu SAP Business ByDesign s právy správce.

2. Přejděte na **běžný úkol správy aplikací a uživatelů** a klikněte na kartu **poskytovatel identity** .

3. Klikněte na **Nový zprostředkovatel identity** a vyberte soubor XML s metadaty, který jste stáhli z Azure Portal. Po importu metadat systém automaticky odešle požadovaný podpisový certifikát a šifrovací certifikát.

    ![Konfigurace jednoho Sign-On1](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Pokud chcete do žádosti SAML zahrnout **adresu URL služby pro příjemce kontrolního výrazu** , vyberte **zahrnout adresu URL služby pro příjemce kontrolního výrazu**.

5. Klikněte na **aktivovat jednotné přihlašování**.

6. Uložte provedené změny.

7. Klikněte na kartu **můj systém** .

    ![Konfigurace jednoho Sign-On2](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. V poli **přihlašovací adresa URL pro přihlášení k Azure AD** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    ![Konfigurace jednoho Sign-On3](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Určete, jestli si zaměstnanec může ručně vybrat přihlášení pomocí ID uživatele a hesla nebo jednotného přihlašování. vybere **Výběr ručního zprostředkovatele identity**.

10. V části **Adresa URL jednotného přihlašování** zadejte adresu URL, kterou by měl zaměstnanec použít k jednotnéhoí systému.
    V rozevíracím seznamu Adresa URL odeslání na zaměstnanec si můžete vybrat z následujících možností:

    **Adresa URL bez jednotného přihlašování**

    Systém pošle zaměstnanci pouze normální adresu URL systému. Zaměstnanec se nemůže jednotného pomocí jednotného přihlašování a musí místo toho použít heslo nebo certifikát.

    **ADRESA URL JEDNOTNÉHO PŘIHLAŠOVÁNÍ**

    Systém pošle zaměstnanci pouze adresu URL jednotného přihlašování. Zaměstnanec může jednotného pomocí jednotného přihlašování. Požadavek na ověření se přesměruje přes IdP.

    **Automatický výběr**

    Pokud jednotné přihlašování není aktivní, pošle se zaměstnanci obvyklá systémová adresa URL. Pokud je jednotné přihlašování aktivní, systém zkontroluje, jestli má zaměstnanec heslo. Pokud je k dispozici heslo, adresa URL jednotného přihlašování a adresa URL bez jednotného přihlašování se odešlou zaměstnanci. Pokud ale nemá zaměstnanec žádné heslo, pošle se zaměstnanci jenom adresa URL jednotného přihlašování.

11. Uložte provedené změny.

### <a name="create-sap-business-bydesign-test-user"></a>Vytvořit testovacího uživatele SAP Business ByDesign

V této části vytvoříte uživatele s názvem Britta Simon v SAP Business ByDesign. Pokud chcete přidat uživatele na platformě SAP Business ByDesign, obraťte se na [tým podpory klientů SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) . 

> [!NOTE]
> Ujistěte se prosím, že hodnota NameID by se měla shodovat s polem username v platformě SAP Business ByDesign.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

1. Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení SAP Business ByDesign, kde můžete spustit tok přihlášení. 

2. Přejít na adresu URL pro přihlášení k SAP Business ByDesign přímo a zahájit tok přihlášení.

3. Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici SAP Business ByDesign v části Moje aplikace, přesměruje se na adresu URL pro přihlášení SAP Business ByDesign. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

* Jakmile nakonfigurujete SAP Business ByDesign, můžete vymáhat ovládací prvky relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace se rozšíří z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).