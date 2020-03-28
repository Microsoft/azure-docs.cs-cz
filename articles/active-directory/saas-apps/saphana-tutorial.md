---
title: 'Kurz: Integrace služby Azure Active Directory se SAP HANA | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a SAP HANA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44a34fe5637e895ea69b6fc4c277b7722b306c97
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161185"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Kurz: Integrace služby Azure Active Directory se SAP HANA

V tomto kurzu se dozvíte, jak integrovat SAP HANA s Azure Active Directory (Azure AD).
Integrace SAP HANA s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k SAP HANA.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k SAP HANA (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s SAP HANA, potřebujete následující položky:

- Předplatné Azure AD
- Předplatné SAP HANA, které je povoleno jednotné přihlašování (SSO)
- Instance HANA, která běží na všech veřejných iaas, místních, virtuálních počítačích Azure nebo velkých instancích SAP v Azure
- Webové rozhraní Správy XSA a hana studio nainstalované v instanci HANA

> [!NOTE]
> Nedoporučujeme používat produkční prostředí SAP HANA k testování kroků v tomto kurzu. Otestujte integraci nejprve ve vývojovém nebo přípravném prostředí aplikace a potom použijte produkční prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Sap HANA povolení jednotného přihlášení předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SAP HANA podporuje **iniciované** přihlašované mši inicioval idiiii IDP
* SAP HANA podporuje zřizování uživatelů **just-in-time**

## <a name="adding-sap-hana-from-the-gallery"></a>Přidání SAP HANA z galerie

Chcete-li nakonfigurovat integraci SAP HANA do Azure AD, musíte přidat SAP HANA z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat SAP HANA z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAP HANA**, z panelu výsledků vyberte **SAP HANA** a klepnutím na **tlačítko Přidat** přidejte aplikaci.

     ![SAP HANA v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí SAP HANA na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v SAP HANA.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SAP HANA, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace sap hana jednotné přihlášení](#configure-sap-hana-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele SAP HANA](#create-sap-hana-test-user)** – chcete-li mít protějšek Britta Simon v SAP HANA, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí SAP HANA, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **SAP HANA** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** proveďte následující kroky:

    ![Sap HANA Domény a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte následující příkaz:`HA100`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou adresou URL identifikátoru a odpovědi. Obraťte se na [tým podpory klienta SAP HANA](https://cloudplatform.sap.com/contact.html) a získejte tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Aplikace SAP HANA očekává kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Hodnoty těchto atributů můžete spravovat v části **Atributy uživatele** na stránce integrace aplikace. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na **tlačítko Upravit** otevřete dialogové okno **Atributy uživatele.**

    ![image](common/edit-attribute.png)

6. V části **Atributy uživatele** v dialogovém okně **Atributy uživatele & deklarace** identity proveďte následující kroky:
 
    a. Kliknutím na **ikonu Upravit** otevřete dialogové okno **Spravovat deklarace identity uživatelů.**

    ![image](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Ze seznamu **Transformace** vyberte **ExtractMailPrefix()**.

    c. Ze seznamu **Parametr 1** vyberte **user.mail**.

    d. Klikněte na **Uložit**.

7. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>Konfigurace jednotného přihlašování SAP HANA

1. Chcete-li nakonfigurovat jednotné přihlašování na straně SAP HANA, přihlaste se k **webové konzoli HANA XSA** tak, že přejdete na příslušný koncový bod HTTPS.

    > [!NOTE]
    > Ve výchozí konfiguraci adresa URL přesměruje požadavek na přihlašovací obrazovku, která vyžaduje pověření ověřeného uživatele databáze SAP HANA. Uživatel, který se přihlásí, musí mít oprávnění k provádění úloh správy SAML.

2. Ve webovém rozhraní XSA přejděte na **web ovou službu SAML .** Odtud vyberte **+** tlačítko v dolní části obrazovky a zobrazte podokno **Přidat informace o poskytovateli identity.** Pak proveďte následující kroky:

    ![Přidat zprostředkovatele identity](./media/saphana-tutorial/sap1.png)

    a. V podokně **Přidat informace o poskytovateli identity** vložte obsah XML metadat (který jste stáhli z portálu Azure) do pole **Metadata.**

    ![Přidat nastavení zprostředkovatele identity](./media/saphana-tutorial/sap2.png)

    b. Pokud je obsah dokumentu XML platný, proces analýzy extrahuje informace, které jsou požadovány pro pole **Předmět, ID entity a Vystavittele** v oblasti **Obecné datové** obrazovky. Extrahuje také informace, které jsou nezbytné pro pole URL v oblasti **obrazovky Cíl,** například **pole Základní adresa URL a Adresa URL singlesignon (*).**

    ![Přidat nastavení zprostředkovatele identity](./media/saphana-tutorial/sap3.png)

    c. Do pole **Název** oblasti obrazovky **Obecná data** zadejte název nového poskytovatele identity při spo.up.

    > [!NOTE]
    > Název SAML IDP je povinný a musí být jedinečný. Zobrazí se v seznamu dostupných idp SAML, který se zobrazí, když vyberete SAML jako metodu ověřování pro aplikace SAP HANA XS, které chcete použít. Můžete to například provést v oblasti **obrazovky Ověřování** nástroje Správa artefaktů XS.

3. Výběrem **možnosti Uložit** uložíte podrobnosti o zprostředkovateli identity SAML a přidáte nový idp SAML do seznamu známých idp SAML.

    ![Tlačítko Uložit](./media/saphana-tutorial/sap4.png)

4. V HANA Studio, v rámci vlastností systému **konfigurace** karty, filtrovat nastavení **saml**. Poté upravte **assertion_timeout** z **10 sekund** na **120 sekund**.

    ![assertion_timeout nastavení](./media/saphana-tutorial/sap7.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** zadejte **\@brittasimon vašecompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k SAP HANA.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **SAP HANA**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **SAP HANA**.

    ![Odkaz SAP HANA v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-sap-hana-test-user"></a>Vytvořit testovacího uživatele SAP HANA

Chcete-li povolit uživatelům Azure AD k přihlášení k SAP HANA, musíte je zřídit v SAP HANA.
SAP HANA podporuje **zřizování za čas**, což je ve výchozím nastavení povoleno.

Pokud potřebujete vytvořit uživatele ručně, postupujte takto:

>[!NOTE]
>Můžete změnit externí ověřování, které uživatel používá. Mohou se ověřit pomocí externího systému, jako je například protokol Kerberos. Podrobné informace o externích identitách získáte od [správce domény](https://cloudplatform.sap.com/contact.html).

1. Otevřete [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) jako správce a povolte DB-User pro SAML SSO.

    ![Vytvořit uživatele](./media/saphana-tutorial/sap5.png)

2. Zaškrtněte políčko neviditelné nalevo od **saml**a pak vyberte **konfigurovat** odkaz.

3. Vyberte **Přidat,** chcete-li přidat IDP SAML.  Vyberte příslušnou položku SAML IDP a pak vyberte **ok**.

4. Přidejte **externí identitu** (v tomto případě BrittaSimon) nebo zvolte **Any**. Pak vyberte **OK**.

   > [!Note]
   > Pokud není zaškrtnuto políčko **Any,** musí uživatelské jméno v hana přesně odpovídat jménu uživatele v hlavní názvu uživatele před příponou domény. (Například BrittaSimon@contoso.com se stane BrittaSimon v HANA.)

5. Pro účely testování přiřaďte uživateli všechny role **XS.**

    ![Přiřazení rolí](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Měli byste udělit oprávnění, která jsou vhodná pouze pro vaše případy použití.

6. Uložte uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici SAP HANA na přístupovém panelu, můžete by měl být automaticky přihlášeni k SAP HANA, pro které nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

