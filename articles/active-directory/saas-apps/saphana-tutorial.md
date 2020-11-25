---
title: 'Kurz: Azure Active Directory integrace s SAP HANA | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP HANA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: 748566cf5b25157fcb8a2938a3f4bd307c930874
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993219"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Kurz: Azure Active Directory integrace s SAP HANA

V tomto kurzu se naučíte, jak integrovat SAP HANA s Azure Active Directory (Azure AD).
Integrace SAP HANA se službou Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k SAP HANA.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k SAP HANA (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s SAP HANA potřebujete následující položky:

- Předplatné Azure AD
- Předplatné SAP HANA s povoleným jednotným přihlašováním (SSO)
- Instance HANA, která běží na všech veřejných IaaS, místních virtuálních počítačích Azure nebo ve velkých instancích SAP v Azure
- Webové rozhraní pro správu XSA i HANA Studio nainstalované na instanci HANA

> [!NOTE]
> Nedoporučujeme používat provozní prostředí SAP HANA k otestování kroků v tomto kurzu. Nejprve otestujte integraci ve vývojovém nebo přípravném prostředí aplikace a pak použijte provozní prostředí.

K otestování kroků v tomto kurzu použijte tato doporučení:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* SAP HANA odběr povoleného jednotného přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SAP HANA podporuje jednotné přihlašování (SSO) iniciované **IDP**
* SAP HANA podporuje zřizování uživatelů **za běhu**

## <a name="adding-sap-hana-from-the-gallery"></a>Přidání SAP HANA z Galerie

Pokud chcete nakonfigurovat integraci SAP HANA do služby Azure AD, musíte přidat SAP HANA z Galerie do svého seznamu spravovaných aplikací SaaS.

**Chcete-li přidat SAP HANA z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAP HANA**, vyberte **SAP HANA** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![SAP HANA v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí SAP HANA na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán odkaz na odkaz mezi uživatelem služby Azure AD a souvisejícím uživatelem v SAP HANA.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SAP HANA, je nutné dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte SAP HANA jednotné přihlašování](#configure-sap-hana-single-sign-on)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte SAP HANA testovacího uživatele](#create-sap-hana-test-user)** – Britta Simon v SAP HANA, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí SAP HANA, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **SAP HANA** integrace aplikací vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    ![Informace o jednotném přihlašování k doméně a adresám URL SAP HANA](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte následující text: `HA100`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory SAP HANA klientů](https://cloudplatform.sap.com/contact.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. SAP HANA aplikace očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jeden Sign-On se** stránkou SAML kliknutím na tlačítko **Upravit** otevřete dialogové okno **atributy uživatele** .

    ![Snímek obrazovky, který zobrazuje oddíl "atributy uživatele" se zvolenou ikonou "Upravit".](common/edit-attribute.png)

6. V části **atributy uživatele** v dialogovém okně **atributy uživatele & deklarací** proveďte následující kroky:
 
    a. Kliknutím na **ikonu Upravit** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![Snímek obrazovky, který zobrazuje dialog "atributy uživatele & deklarace identity" s vybranou ikonou "Upravit".](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. V seznamu **transformace** vyberte **ExtractMailPrefix ()**.

    c. V seznamu **parametr 1** vyberte **User. mail**.

    d. Klikněte na **Uložit**.

7. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>Konfigurace SAP HANA jednoho Sign-On

1. Pokud chcete nakonfigurovat jednotné přihlašování na straně SAP HANA, přihlaste se k **webové konzole Hana XSA**  tak, že nasadíte na příslušný koncový bod HTTPS.

    > [!NOTE]
    > Ve výchozí konfiguraci adresa URL přesměruje požadavek na přihlašovací obrazovku, která vyžaduje přihlašovací údaje uživatele ověřené databáze SAP HANA. Uživatel, který se přihlásí, musí mít oprávnění k provádění úloh správy SAML.

2. Ve webovém rozhraní XSA přejít na **zprostředkovatele identity SAML**. Odtud vyberte **+** tlačítko v dolní části obrazovky, aby se zobrazilo podokno **Přidat informace o poskytovateli identity** . Pak proveďte následující kroky:

    ![Přidat zprostředkovatele identity](./media/saphana-tutorial/sap1.png)

    a. V podokně **Přidat informace o zprostředkovateli identity** vložte obsah XML metadat (který jste stáhli z Azure Portal) do pole **metadata** .

    ![Snímek obrazovky, který zobrazuje podokno přidat informace o zprostředkovateli identity se zvýrazněnými poli metadata a název](./media/saphana-tutorial/sap2.png)

    b. Pokud je obsah dokumentu XML platný, proces analýzy extrahuje informace požadované pro pole **předmět, ID entity a vystavitele** v oblasti obrazovka **Obecné data** . Také extrahuje informace, které jsou nezbytné pro pole adresy URL v **cílové** oblasti obrazovky, například jako **základní adresu URL a pole SingleSignon URL (*)** .

    ![Přidat nastavení zprostředkovatele identity](./media/saphana-tutorial/sap3.png)

    c. Do pole **název** v oblasti **Obecná data** zadejte název nového poskytovatele identity jednotného přihlašování SAML.

    > [!NOTE]
    > Název IDP SAML je povinný a musí být jedinečný. Zobrazí se v seznamu dostupných zprostředkovatelů identity SAML, který se zobrazí, když jako metodu ověřování pro SAP HANA aplikace typu XS vyberete SAML. To můžete provést například v oblasti obrazovka **ověřování** nástroje pro správu artefaktů xs.

3. Výběrem **Uložit** uložte podrobnosti zprostředkovatele identity SAML a přidejte nový IDP SAML do seznamu známých zprostředkovatelů identity SAML.

    ![Tlačítko Uložit](./media/saphana-tutorial/sap4.png)

4. V HANA Studio v části Vlastnosti systému na kartě **Konfigurace** vyfiltrujte nastavení pomocí **SAML**. Pak upravte **assertion_timeout** z **10 sec** na **120 s**.

    ![nastavení assertion_timeout](./media/saphana-tutorial/sap7.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k SAP HANA.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte možnost **SAP HANA**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **SAP HANA**.

    ![Odkaz SAP HANA v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-sap-hana-test-user"></a>Vytvořit SAP HANA testovacího uživatele

Pokud chcete uživatelům Azure AD povolit, aby se k SAP HANA přihlásili, musíte je zřídit v SAP HANA.
SAP HANA podporuje **zřizování za běhu**, což je ve výchozím nastavení povoleno.

Pokud potřebujete ručně vytvořit uživatele, proveďte následující kroky:

>[!NOTE]
>Můžete změnit externí ověřování, které uživatel používá. Můžou se ověřovat pomocí externího systému, jako je třeba Kerberos. Podrobné informace o externích identitách získáte od [správce domény](https://cloudplatform.sap.com/contact.html).

1. Otevřete [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) jako správce a pak povolte DB-User SSO pro jednotné přihlašování SAML.

    ![Vytvořit uživatele](./media/saphana-tutorial/sap5.png)

2. Vyberte neviditelné zaškrtávací políčko nalevo od **SAML** a pak vyberte odkaz **Konfigurovat** .

3. Výběrem **Přidat** přidejte IDP SAML.  Vyberte odpovídající IDP SAML a pak vyberte **OK**.

4. Přidejte **externí identitu** (v tomto případě BrittaSimon) nebo vyberte **libovolnou**. Pak vyberte **OK**.

   > [!Note]
   > Pokud políčko **není zaškrtnuté** , musí se uživatelské jméno v Hana přesně shodovat s názvem uživatele v hlavní název uživatele (UPN) před příponou domény. (Například se BrittaSimon@contoso.com v Hana stávají BrittaSimon.)

5. Pro účely testování přiřaďte uživateli všechny role typu **xs** .

    ![Přiřazování rolí](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Měli byste udělit oprávnění, která jsou vhodná jenom pro vaše případy použití.

6. Uložte uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici SAP HANA na přístupovém panelu byste měli být automaticky přihlášení do SAP HANA, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)