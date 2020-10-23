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
ms.date: 12/28/2018
ms.author: jeedes
ms.openlocfilehash: 61920b7c5356b6e1fa5683ac0553060c85e256d3
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457805"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Kurz: Azure Active Directory integrace s ArcGIS Enterprise

V tomto kurzu se dozvíte, jak integrovat ArcGIS Enterprise s Azure Active Directory (Azure AD).
Integrace ArcGIS Enterprise s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k ArcGIS Enterprise.
* Uživatelům můžete povolit, aby se automaticky přihlásili k ArcGIS Enterprise (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s ArcGIS Enterprise budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné ArcGIS Enterprise s povoleným jednotným přihlašováním

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ArcGIS Enterprise podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.
* ArcGIS Enterprise podporuje zřizování uživatelů **jenom v čase**


## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Přidání ArcGIS Enterprise z Galerie

Pokud chcete nakonfigurovat integraci ArcGIS Enterprise do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat ArcGIS Enterprise z galerie.

**Pokud chcete přidat ArcGIS Enterprise z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ArcGIS Enterprise**, vyberte **ArcGIS Enterprise** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![ArcGIS Enterprise v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí [název aplikace] na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán vztah odkazu mezi uživatelem služby Azure AD a souvisejícím uživatelem v [název aplikace].

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí [název aplikace], musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování ArcGIS Enterprise](#configure-arcgis-enterprise-single-sign-on)** , abyste na straně aplikace nakonfigurovali nastavení jednoho Sign-On.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte ArcGIS Enterprise Test User](#create-arcgis-enterprise-test-user)** – abyste měli protějšek Britta Simon v ArcGIS Enterprise, který se odkazuje na reprezentaci uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí [název aplikace], proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **podnikových aplikací ArcGIS** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky, pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** :

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat identifikátor, odpovědět U R L a vybrat Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `<EXTERNAL_DNS_NAME>.portal`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory ArcGIS Enterprise Client](mailto:support@esri.com) . Zobrazí se hodnota identifikátoru z **části nastavit zprostředkovatele identity**, která je vysvětlena dále v tomto kurzu.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-arcgis-enterprise-single-sign-on"></a>Konfigurace ArcGIS Enterprise Single Sign-On

1. Pokud chcete automatizovat konfiguraci v rámci ArcGIS Enterprise, je potřeba nainstalovat **rozšíření prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **Nastavení ArcGIS Enterprise** , které vás přesměruje na podnikovou aplikaci ArcGIS. Odtud zadejte přihlašovací údaje správce, které se budou přihlašovat do ArcGIS Enterprise. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit ArcGIS Enterprise ručně, přihlaste se k webu ArcGIS podnikové společnosti jako správce.


1. Vyberte **organizace >upravit nastavení**.

    ![Snímek obrazovky se zobrazí na kartě organizace ArcGIS Enterprise s nastavením upravit s názvem.](./media/arcgisenterprise-tutorial/configure1.png)

1. Vyberte kartu **Zabezpečení**.

    ![Snímek obrazovky se zobrazí karta zabezpečení.](./media/arcgisenterprise-tutorial/configure2.png)

1. Přejděte dolů do části **podnikové přihlášení prostřednictvím SAML** a vyberte **nastavit podnikové přihlášení**.

    ![Snímek obrazovky zobrazuje podnikové přihlašovací údaje přes SAML, kde můžete vybrat nastavit přihlášení k podnikové síti.](./media/arcgisenterprise-tutorial/configure3.png)

1. V části **nastavit zprostředkovatele identity** proveďte následující kroky:

    ![Snímek obrazovky s nastavením poskytovatele identity zobrazí, kde provedete kroky popsané tady.](./media/arcgisenterprise-tutorial/configure4.png)

    a. Do textového pole **název** zadejte název, třeba **Azure Active Directory test** .

    b. Do textového pole **Adresa URL** vložte hodnotu **adresy URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

    c. Klikněte na **Zobrazit upřesňující nastavení** a ZKOPÍRUJTE hodnotu **ID entity** a vložte ji do textového pole **identifikátor** v části **doména ArcGIS Enterprise a adresy URL** v Azure Portal.
    
    ![Snímek obrazovky s informacemi o tom, kde získat entitu I a aktualizovat poskytovatele identifikace](./media/arcgisenterprise-tutorial/configure5.png)

    d. Klikněte na **aktualizovat zprostředkovatele identity**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k ArcGIS Enterprise.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **ArcGIS Enterprise**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **ArcGIS Enterprise**.

    ![Odkaz ArcGIS Enterprise v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-arcgis-enterprise-test-user"></a>Vytvořit ArcGIS Enterprise Test User

V této části se v ArcGIS Enterprise vytvoří uživatel s názvem Britta Simon. ArcGIS Enterprise podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v ArcGIS Enterprise neexistuje, vytvoří se po ověření nový.

> [!Note]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory ArcGIS Enterprise](mailto:support@esri.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici ArcGIS Enterprise na přístupovém panelu, měli byste se automaticky přihlásit k ArcGIS podniku, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)