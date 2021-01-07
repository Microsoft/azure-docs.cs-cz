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
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: f6066997b5a63a9ffefc1371851c7200d7655c9c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97962563"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Kurz: Azure Active Directory integrace s SAP HANA

V tomto kurzu se naučíte, jak integrovat SAP HANA s Azure Active Directory (Azure AD). Když integrujete SAP HANA s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SAP HANA.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k SAP HANA se svými účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

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

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.


## <a name="adding-sap-hana-from-the-gallery"></a>Přidání SAP HANA z Galerie

Pokud chcete nakonfigurovat integraci SAP HANA do služby Azure AD, musíte přidat SAP HANA z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **SAP HANA** .
1. Na panelu výsledků vyberte **SAP HANA** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro SAP HANA

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SAP HANA pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SAP HANA.

K nakonfigurování a testování jednotného přihlašování služby Azure AD pomocí SAP HANA proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
2. **[Nakonfigurujte SAP HANA SSO](#configure-sap-hana-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    1. **[Vytvořte SAP HANA testovacího uživatele](#create-sap-hana-test-user)** – Britta Simon v SAP HANA, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **SAP HANA** integrace aplikací najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:  `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není reálné číslo. Aktualizujte hodnotu skutečnou adresou URL odpovědi. Pokud chcete získat hodnoty, kontaktujte [tým podpory SAP HANA klientů](https://cloudplatform.sap.com/contact.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k SAP HANA.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **SAP HANA**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-sap-hana-sso"></a>Konfigurace SAP HANA jednotného přihlašování

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

### <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k SAP HANA, pro kterou jste si nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici SAP HANA v okně moje aplikace, měli byste se automaticky přihlásit k SAP HANA, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování SAP HANA můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).