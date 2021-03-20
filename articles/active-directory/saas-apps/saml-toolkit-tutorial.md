---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s využitím Azure AD SAML Toolkit | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a službou Azure AD SAML Toolkit.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 26618382223a87f779f95452000a39126f37efbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92675430"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí sady nástrojů Azure AD SAML Toolkit

V tomto kurzu se dozvíte, jak integrovat sadu nástrojů Azure AD SAML pomocí Azure Active Directory (Azure AD). Když integrujete sadu nástrojů SAML sady Azure AD s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke službě Azure AD SAML Toolkit.
* Umožněte, aby se vaši uživatelé automaticky přihlásili ke službě Azure AD SAML Toolkit pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Azure AD SAML Toolkit jednotného přihlašování (SSO) s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Sada nástrojů SAML pro Azure AD podporuje jednotné přihlašování na více **aktualizacích**

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Přidání sady nástrojů SAML pro Azure AD z Galerie

Ke konfiguraci integrace služby Azure AD SAML Toolkit do služby Azure AD je nutné přidat sadu nástrojů Azure AD SAML z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **službu Azure AD SAML Toolkit** .
1. Vyberte **Azure AD SAML Toolkit** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-azure-ad-saml-toolkit"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Azure AD SAML Toolkit

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí služby Azure AD SAML Toolkit pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v sadě nástrojů Azure AD SAML Toolkit.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí sady nástrojů SAML v Azure AD, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování sady SAML sady Azure AD](#configure-azure-ad-saml-toolkit-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte testovacího uživatele pro Azure AD SAML Toolkit](#create-azure-ad-saml-toolkit-test-user)** – můžete mít protějšek B. Simon ve službě Azure AD SAML Toolkit, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **SAML sady nástrojů Azure AD** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://samltoolkit.azurewebsites.net/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **certifikát (RAW)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

1. V části **nastavení sady Azure AD SAML Toolkit** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure udělením přístupu ke službě Azure AD SAML Toolkit.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Azure AD SAML Toolkit**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Konfigurace jednotného přihlašování sady Azure AD SAML Toolkit

1. Otevřete nové okno webového prohlížeče (Pokud jste nezaregistrovali na webu Azure AD SAML Toolkit), nejprve se zaregistrujte kliknutím na **registr**. Pokud jste se už zaregistrovali, přihlaste se k webu služby Azure AD SAML Toolkit pomocí zaregistrovaných přihlašovacích údajů pro přihlášení.

    ![Registr sady nástrojů SAML pro Azure AD](./media/saml-toolkit-tutorial/register.png)

1. Klikněte na **konfiguraci SAML**.

    ![Konfigurace SAML sady Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/saml-configure.png)

1. Klikněte na **Vytvořit**.

    ![Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/createsso.png)

1. Na stránce **Konfigurace jednotného přihlašování SAML** proveďte následující kroky:

    ![Služba Azure AD SAML Toolkit vytvoření konfigurace jednotného přihlašování](./media/saml-toolkit-tutorial/fill-details.png)

    1. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    1. Do textového pole **identifikátoru Azure AD** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    1. Do textového pole **Adresa URL pro odhlášení** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    1. Klikněte na **zvolit soubor** a nahrajte soubor **certifikátu (RAW)** , který jste stáhli z Azure Portal.

    1. Klikněte na **Vytvořit**.

    1. Kopírování přihlašovacích adres URL, identifikátorů a hodnot adresy URL služby ACS na stránce konfigurace jednotného přihlašování SAML Toolkit a vložení do v **části základní konfigurační** pole v Azure Portal.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Vytvořit testovacího uživatele pro Azure AD SAML Toolkit

V této části se ve službě Azure AD SAML Toolkit vytvoří uživatel s názvem B. Simon. Vytvořte prosím v nástroji testovacího uživatele tak, že zaregistrujete nového uživatele a zadáte všechny podrobnosti o uživateli. 

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

1. Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení ke službě SAML Toolkit, kde můžete spustit tok přihlášení. 

2. Přejít na adresu URL pro přihlášení ke službě SAML Toolkit přímo a zahájit tok přihlášení.

3. Můžete použít panel Microsoft Access. Když kliknete na dlaždici SAML Toolkit na přístupovém panelu, měli byste být automaticky přihlášeni ke sadě SAML Toolkit, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po konfiguraci sady Azure AD SAML Toolkit můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)