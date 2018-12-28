---
title: 'Kurz: Integrace Azure Active Directory se SAP HANA | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP HANA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: dc5cad0d4d1919db23e5393f2bb02810a1d7f8f9
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790190"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Kurz: Integrace Azure Active Directory se SAP HANA

V tomto kurzu se dozvíte, jak integrovat SAP HANA s Azure Active Directory (Azure AD).
SAP HANA integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k SAP HANA.
* Uživatelům se automaticky přihlášeni k SAP HANA (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se SAP HANA, potřebujete následující položky:

- Předplatné Azure AD
- SAP HANA předplatné, které je jednotné přihlašování (SSO) povoleno
- Instanci HANA, která běží na libovolné veřejné IaaS on-premises, virtuální počítač Azure nebo velké instance SAP v Azure
- Správa XSA webové rozhraní, jakož i HANA Studio nainstalovaná v instanci HANA

> [!NOTE]
> Nedoporučujeme použití produkční prostředí SAP Hana pro testování kroky v tomto kurzu. Nejdřív otestovat integraci v vývojové nebo testovací prostředí aplikace a pak používat produkčním prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* SAP HANA jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* SAP HANA podporuje **IDP** jednotné přihlašování zahájené pomocí
* SAP HANA podporuje **just-in-time** zřizování uživatelů

## <a name="adding-sap-hana-from-the-gallery"></a>Přidání SAP HANA z Galerie

Ke konfiguraci integrace systému SAP HANA do služby Azure AD, budete muset přidat SAP HANA v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SAP HANA z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAP HANA**vyberte **SAP HANA** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![SAP HANA v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, nakonfigurovat a otestovat Azure AD jednotného přihlašování se SAP HANA založené na test uživateli **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SAP HANA.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se SAP HANA, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace SAP HANA Single Sign-On](#configure-sap-hana-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytvořit testovacího uživatele SAP HANA](#create-sap-hana-test-user)**  – Pokud chcete mít protějšek Britta Simon v SAP HANA, který je propojený s Azure AD reprezentace uživatele.
5. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotného přihlašování se SAP HANA, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **SAP HANA** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránce, proveďte následující kroky:

    ![SAP HANA domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte následující příkaz: `HA100`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory SAP HANA Client](https://cloudplatform.sap.com/contact.html) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Aplikace SAP HANA očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

6. V **atributy uživatele** části na **atributy uživatele a deklarace identity** dialogového okna, proveďte následující kroky:
 
    a. Klikněte na tlačítko **ikonu pro úpravu** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Z **transformace** seznamu vyberte **ExtractMailPrefix()**.

    c. Z **parametr 1** seznamu vyberte **user.mail**.

    d. Klikněte na **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>Konfigurace SAP HANA jednotného přihlašování

1. Pokud chcete nakonfigurovat jednotné přihlašování na straně SAP HANA, přihlaste se k vaší **HANA XSA Webová konzola** tak, že přejdete do příslušného koncového bodu HTTPS.

    > [!NOTE]
    > Ve výchozím nastavení adresa URL přesměruje požadavek přihlašovací obrazovku, která vyžaduje přihlašovací údaje ověřeného uživatele databáze SAP HANA. Uživatel, který se přihlásí, musí mít oprávnění k provádění úloh správy SAML.

2. Přejděte ve webovém rozhraní XSA **zprostředkovatele Identity SAML**. Tam pak vyberete **+** tlačítko v dolní části obrazovky zobrazte **přidat informace o poskytovateli Identity** podokně. Pak proveďte následující kroky:

    ![Přidat zprostředkovatele Identity](./media/saphana-tutorial/sap1.png)

    a. V **přidat informace o poskytovateli Identity** podokně do vložte obsah XML metadat (který jste si stáhli z portálu Azure portal) **metadat** pole.

    ![Přidat nastavení zprostředkovatele Identity](./media/saphana-tutorial/sap2.png)

    b. Pokud obsah dokumentu XML platný, proces analýzy extrahuje informace, které je vyžadováno pro **předmět, Entity ID a vystavitele** pole v **obecná data** obrazovky oblasti. Extrahuje informace, které je nezbytné pro pole adresy URL v **cílové** obrazovky oblasti, například **základní adresu URL a adresy URL SingleSignOn (*)** pole.

    ![Přidat nastavení zprostředkovatele Identity](./media/saphana-tutorial/sap3.png)

    c. V **název** pomocí boxingu **obecná Data** obrazovky oblasti, zadejte název nového zprostředkovatele identity SAML SSO.

    > [!NOTE]
    > Název zprostředkovatele identity SAML je povinná a musí být jedinečný. Zobrazí se v seznamu dostupných zprostředkovatelů identity SAML, který se zobrazí, když vyberete jako metodu ověřování pro použití aplikacemi SAP HANA XS SAML. Například to můžete provést **ověřování** obrazovky oblasti nástroje pro správu XS artefaktů.

3. Vyberte **Uložit** uložit podrobnosti zprostředkovatele identity SAML a přidat nového zprostředkovatele identity SAML do seznamu známých zprostředkovatelů identity SAML.

    ![Tlačítko Uložit](./media/saphana-tutorial/sap4.png)

4. V HANA Studio v systému vlastností **konfigurace** kartu, nastavení pomocí filtru **saml**. Potom upravte **assertion_timeout** z **10 sekund** k **120 sekundu**.

    ![nastavení assertion_timeout](./media/saphana-tutorial/sap7.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k SAP HANA.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **SAP HANA**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **SAP HANA**.

    ![SAP HANA odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-sap-hana-test-user"></a>Vytvořit testovacího uživatele SAP HANA

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k SAP HANA, musíte zřídit v SAP HANA.
SAP HANA podporuje **just-in-time zřizování**, což je tak ve výchozím nastavení povolené.

Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:

>[!NOTE]
>Můžete změnit externího ověřování, které uživatel používá. Mohou se ověřit s externím systémem, jako je třeba Kerberos. Obraťte se na podrobné informace o externí identity vaší [správce domény](https://cloudplatform.sap.com/contact.html).

1. Otevřít [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) jako správce a potom povolit uživatel databáze pro jednotné přihlašování SAML.

    ![Vytvořit uživatele](./media/saphana-tutorial/sap5.png)

2. Neviditelné zaškrtněte políčko nalevo od **SAML**a pak vyberte **konfigurovat** odkaz.

3. Vyberte **přidat** přidat zprostředkovatele identity SAML.  Vyberte odpovídající zprostředkovatele identity SAML a pak vyberte **OK**.

4. Přidat **externí identita** (v tomto případě BrittaSimon) nebo zvolte **jakékoli**. Pak vyberte **OK**.

    >[!Note]
    >Pokud **jakékoli** není zaškrtnuté políčko a potom uživatelské jméno v HANA musí přesně shodovat s názvem uživatele (UPN) před příponu domény. (Například BrittaSimon@contoso.com stane BrittaSimon v HANA.)

5. Pro účely testování, přiřaďte všechny **XS** role pro uživatele.

    ![Přiřazení rolí](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Je třeba přiřadit oprávnění, které jsou vhodné pro vaše případy použití.

6. Uložení uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici SAP HANA na přístupovém panelu, vám by měl být automaticky přihlášeni k SAP HANA, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

