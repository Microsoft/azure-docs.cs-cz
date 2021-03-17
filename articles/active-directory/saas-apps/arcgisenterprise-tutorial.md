---
title: 'Kurz: Azure Active Directory integrace s ArcGIS Enterprise | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ArcGIS Enterprise.
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
ms.openlocfilehash: 9cde75440292fffb830656c32181d7be64a55f3d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645488"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Kurz: Azure Active Directory integrace s ArcGIS Enterprise

V tomto kurzu se dozvíte, jak integrovat ArcGIS Enterprise s Azure Active Directory (Azure AD). Když integrujete ArcGIS Enterprise s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k ArcGIS Enterprise.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k ArcGIS Enterprise pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné ArcGIS Enterprise s povoleným jednotným přihlašováním (SSO).

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ArcGIS Enterprise podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.
* ArcGIS Enterprise podporuje **jenom během** zřizování uživatelů.

## <a name="add-arcgis-enterprise-from-the-gallery"></a>Přidání ArcGIS Enterprise z Galerie

Pokud chcete nakonfigurovat integraci ArcGIS Enterprise do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat ArcGIS Enterprise z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **ArcGIS Enterprise** .
1. Z panelu výsledků vyberte **ArcGIS Enterprise** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-enterprise"></a>Konfigurace a testování jednotného přihlašování Azure AD pro ArcGIS Enterprise

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s ArcGIS Enterprise pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ArcGIS Enterprise.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ArcGIS Enterprise, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte ArcGIS Enterprise SSO](#configure-arcgis-enterprise-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte ArcGIS Enterprise Test User](#create-arcgis-enterprise-test-user)** – abyste měli protějšek B. Simon v ArcGIS Enterprise, který se odkazuje na reprezentaci uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace **podnikových aplikací ArcGIS** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky, pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** :

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `<EXTERNAL_DNS_NAME>.portal`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory ArcGIS Enterprise Client](mailto:support@esri.com) . Zobrazí se hodnota identifikátoru z **části nastavit zprostředkovatele identity**, která je vysvětlena dále v tomto kurzu.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k ArcGIS Enterprise.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **ArcGIS Enterprise**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-arcgis-enterprise-sso"></a>Konfigurace ArcGIS Enterprise SSO

1. Pokud chcete automatizovat konfiguraci v rámci ArcGIS Enterprise, je potřeba nainstalovat **rozšíření prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **Nastavení ArcGIS Enterprise** , které vás přesměruje na podnikovou aplikaci ArcGIS. Odtud zadejte přihlašovací údaje správce, které se budou přihlašovat do ArcGIS Enterprise. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit ArcGIS Enterprise ručně, přihlaste se k webu ArcGIS podnikové společnosti jako správce.


1. Vyberte **organizace >upravit nastavení**.

    ![Snímek obrazovky se zobrazí na kartě organizace ArcGIS Enterprise s nastavením upravit s názvem.](./media/arcgisenterprise-tutorial/configure-1.png)

1. Vyberte kartu **Zabezpečení**.

    ![Snímek obrazovky se zobrazí karta zabezpečení.](./media/arcgisenterprise-tutorial/configure-2.png)

1. Přejděte dolů do části **podnikové přihlášení prostřednictvím SAML** a vyberte **nastavit podnikové přihlášení**.

    ![Snímek obrazovky zobrazuje podnikové přihlašovací údaje přes SAML, kde můžete vybrat nastavit přihlášení k podnikové síti.](./media/arcgisenterprise-tutorial/configure-3.png)

1. V části **nastavit zprostředkovatele identity** proveďte následující kroky:

    ![Snímek obrazovky s nastavením poskytovatele identity zobrazí, kde provedete kroky popsané tady.](./media/arcgisenterprise-tutorial/configure-4.png)

    a. Do textového pole **název** zadejte název, třeba **Azure Active Directory test** .

    b. Do textového pole **Adresa URL** vložte hodnotu **adresy URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

    c. Klikněte na **Zobrazit upřesňující nastavení** a ZKOPÍRUJTE hodnotu **ID entity** a vložte ji do textového pole **identifikátor** v části **doména ArcGIS Enterprise a adresy URL** v Azure Portal.

    ![Snímek obrazovky s informacemi o tom, kde získat entitu I a aktualizovat poskytovatele identifikace](./media/arcgisenterprise-tutorial/configure-5.png)

    d. Klikněte na **aktualizovat zprostředkovatele identity**.

### <a name="create-arcgis-enterprise-test-user"></a>Vytvořit ArcGIS Enterprise Test User

V této části se v ArcGIS Enterprise vytvoří uživatel s názvem Britta Simon. ArcGIS Enterprise podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v ArcGIS Enterprise neexistuje, vytvoří se po ověření nový.

> [!Note]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory ArcGIS Enterprise](mailto:support@esri.com).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL ArcGIS Enterprise Sign on, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlašování na ArcGIS Enterprise přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k ArcGIS podniku, pro který jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici ArcGIS Enterprise v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k ArcGIS podniku, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování ArcGIS Enterprise můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).