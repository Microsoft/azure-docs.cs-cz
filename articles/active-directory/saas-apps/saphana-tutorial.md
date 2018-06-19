---
title: 'Kurz: Azure Active Directory integrace s SAP HANA | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP HANA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 65d474e78cf9a2a504b2e573c96f3cbf818bb017
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899260"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Kurz: Azure Active Directory integrace s SAP HANA

V tomto kurzu zjistěte, jak integrovat SAP HANA s Azure Active Directory (Azure AD).

Pokud integrujete SAP HANA s Azure AD, získáte následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k SAP HANA.
- Můžete povolit uživatelům automaticky získat přihlášeni k SAP HANA s účty služby Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě – portál Azure.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SAP HANA, potřebujete následující položky:

- Předplatné služby Azure AD
- SAP HANA předplatné, které je jednotné přihlašování (SSO) povoleno
- HANA instance, která běží na všechny veřejné IaaS, místní, virtuální počítač Azure nebo SAP velké instancemi v Azure
- Správa XSA webové rozhraní, jakož i HANA Studio instalovaného na instanci HANA

> [!NOTE]
> Nedoporučujeme použití provozním prostředí SAP HANA k testování kroky v tomto kurzu. Nejdřív otestovat integrace v vývoj nebo pracovní prostředí aplikace a pak pomocí produkčního prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- [Získat bezplatnou zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/) Azure AD, pokud ještě nemáte prostředí zkušební verze Azure AD.

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář, který je popsané v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SAP HANA z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-sap-hana-from-the-gallery"></a>Přidat SAP HANA z Galerie
Při konfiguraci integrace SAP HANA do služby Azure AD přidáte do seznamu spravovaných aplikací SaaS SAP HANA z galerie.

**Pokud chcete přidat SAP HANA z galerie, proveďte následující kroky:**

1. V [portál Azure](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **SAP HANA**. Potom vyberte **SAP HANA** z panelu výsledků. Nakonec vyberte **přidat** tlačítko Přidat aplikaci. 

    ![Nová aplikace](./media/saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s SAP HANA podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, kdo příslušného uživatele v SAP HANA je pro uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit propojení mezi uživatele Azure AD a související uživatelské v SAP HANA.

V SAP HANA poskytnout **uživatelské jméno** hodnotu stejnou hodnotu **uživatelské jméno** ve službě Azure AD. Tento krok vytvoří propojení mezi dvěma uživateli.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SAP HANA, proveďte následující stavební bloky:

1. [Konfigurovat Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on) umožňující uživatelům používat tuto funkci.
2. [Vytvořit testovací uživatele Azure AD](#creating-an-azure-ad-test-user) k testování Azure AD jednotné přihlašování s Britta Simon.
3. [Vytvoření zkušebního uživatele SAP HANA](#creating-a-sap-hana-test-user) tak, aby měl protějšek Britta Simon v SAP HANA propojeném s Azure AD reprezentace uživatele.
4. [Přiřadit testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user) povolit Britta Simon používat Azure AD jednotné přihlašování.
5. [Test jednotného přihlašování](#testing-single-sign-on) ověřit, zda funguje konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci SAP HANA.

**Ke konfiguraci Azure AD jednotné přihlašování s SAP HANA, proveďte následující kroky:**

1. Na portálu Azure na **SAP HANA** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. V **jednotného přihlašování** dialogovém **na základě SAML přihlašování**, vyberte **režimu**.
 
    ![Jediné přihlášení dialogové okno](./media/saphana-tutorial/tutorial_saphana_samlbase.png)

3. V **SAP HANA domény a adresy URL** část, proveďte následující kroky:

    ![Domény a adresy URL jednotné přihlašování informace](./media/saphana-tutorial/tutorial_saphana_url.png)

    a. V **identifikátor** pole, zadejte následující příkaz: `HA100` 

    b. V **adresa URL odpovědi** pole, zadejte adresu URL pomocí vzoru následující: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizovat skutečným identifikátorem a adresa URL odpovědi. Obraťte se [tým podpory klienta SAP HANA](https://cloudplatform.sap.com/contact.html) k získání těchto hodnot. 

4. V **SAML podpisový certifikát** vyberte **soubor XML s metadaty**. Uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Pokud certifikát není aktivní, pak nastavte ji jako aktivní výběrem **aktivujte nový certifikát** políčko ve službě Azure AD. 

5. Aplikace SAP HANA očekává SAML kontrolní výrazy ve specifickém formátu. Následující snímek obrazovky ukazuje příklad tohoto formátu. 

    Zde jsme změnili **uživatelský identifikátor** s **ExtractMailPrefix()** funkce **user.mail**. Díky tomu hodnotu předpony e-mail uživatele, který je jedinečný identifikátor uživatele. Toto ID uživatele je odeslat do aplikace SAP HANA v každé úspěšné odpovědi.

    ![Konfigurovat jednotné přihlašování](./media/saphana-tutorial/attribute.png)

6. V **uživatelské atributy** části **jednotného přihlašování** dialogové okno pole, proveďte následující kroky:

    a. V **uživatelský identifikátor** rozevíracího seznamu vyberte **ExtractMailPrefix**.
    
    b. V **e-mailu** rozevíracího seznamu vyberte **user.mail**.

7. Vyberte tlačítko **Uložit**.

    ![Konfigurace jednotné přihlašování tlačítko Uložit](./media/saphana-tutorial/tutorial_general_400.png)
    
8. Na straně SAP HANA nakonfigurovat jednotné přihlašování, přihlaste se k vaší **HANA XSA webové konzole** přechodem na příslušné koncový bod HTTPS.

    > [!NOTE]
    > Adresa URL ve výchozí konfiguraci, přesměruje požadavek na přihlašovací obrazovce, která vyžaduje přihlašovací údaje ověřeného uživatele databáze SAP HANA. Uživatel, který se přihlásí musí mít oprávnění k provádění úloh správy SAML.

9. Přejděte ve webové rozhraní XSA **poskytovatele Identity SAML**. Tam pak vyberete **+** tlačítko v dolní části obrazovky pro zobrazení **přidat informace o poskytovateli Identity** podokně. Proveďte následující kroky:

    ![Přidejte zprostředkovatele Identity](./media/saphana-tutorial/sap1.png)

    a. V **přidat informace o poskytovateli Identity** podokně umožňuje vložit obsah XML Metadata (který jste si stáhli z portálu Azure) do **Metadata** pole.

    ![Přidejte nastavení zprostředkovatele Identity](./media/saphana-tutorial/sap2.png)

    b. Pokud obsah dokumentu XML platný, proces analýzy extrahuje informace, které je nutné pro **předmět, Entity ID a vystavitele** polí v **obecné data** obrazovky oblasti. Také extrahuje informace, které je nezbytné pro pole adresy URL v **cílové** obrazovky oblasti, například **základní adresu URL a adresy URL SingleSignOn (*)** pole.

    ![Přidejte nastavení zprostředkovatele Identity](./media/saphana-tutorial/sap3.png)

    c. V **název** pole **obecné Data** obrazovky oblasti, zadejte název nového poskytovatele identity SAML jednotné přihlašování.

    > [!NOTE]
    > Název rozšíření IDP SAML je povinná a musí být jedinečný. Zobrazí se v seznamu dostupných IDPs SAML, který se zobrazí, když vyberete jako metodu ověřování pro použití aplikacemi SAP HANA XS SAML. Například to můžete provést **ověřování** obrazovky oblasti nástroje správy Křížky artefaktů.

10. Vyberte **Uložit** uložte podrobnosti o poskytovatele identity SAML a přidat nové rozšíření IDP SAML do seznamu známých IDPs SAML.

    ![Tlačítko Uložit](./media/saphana-tutorial/sap4.png)

11. V nástroji Studio HANA ve vlastnostech systému **konfigurace** kartě, filtrovat podle nastavení **saml**. Upravte **assertion_timeout** z **10 sekund** k **120 sekundu**.

    ![nastavení assertion_timeout](./media/saphana-tutorial/sap7.png)

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com) při nastavujete aplikace! Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace, které** vyberte **jednotné přihlašování** kartě a přístup k vložený dokumentace prostřednictvím **konfigurace** v dolní části. Si můžete přečíst informace o funkci embedded dokumentace v [Azure AD vložených dokumentaci](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém podokně, vyberte **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/saphana-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**. Potom vyberte **všichni uživatelé**.
    
    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/saphana-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, vyberte **přidat** v horní části dialogových oken.
 
    ![Tlačítko Přidat](./media/saphana-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno pole, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/saphana-tutorial/create_aaduser_04.png) 

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla**a zapište si heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-sap-hana-test-user"></a>Vytvoření zkušebního uživatele SAP HANA

Povolit uživatelům Azure AD přihlášení k SAP HANA, musíte zřídit v SAP HANA.
SAP HANA podporuje za běhu zřizování, který je ve povolené ve výchozím nastavení.

Pokud potřebujete ručně vytvořit uživatele, proveďte následující kroky:

>[!NOTE]
>Můžete změnit externího ověřování, které uživatel používá. Mohou se ověřit s externím systémem, jako je třeba Kerberos. Podrobné informace o externí identity, kontaktujte vašeho [správce domény](https://cloudplatform.sap.com/contact.html).

1. Otevřete [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) jako správce a potom povolit uživatele databáze pro jednotné přihlašování SAML.

    ![Vytvořit uživatele](./media/saphana-tutorial/sap5.png)

2. Neviditelná zaškrtněte políčko nalevo od **SAML**a pak vyberte **konfigurace** odkaz.

3. Vyberte **přidat** přidat rozšíření IDP SAML.  Vyberte příslušné rozšíření IDP SAML a pak vyberte **OK**.

4. Přidat **externí Identity** (v tomto případě BrittaSimon), nebo zvolte **žádné**. Pak vyberte **OK**.

    >[!Note]
    >Pokud **žádné** není zaškrtnuté políčko a potom uživatelské jméno v HANA musí přesně shodovat s názvem uživatele v hlavní název uživatele před příponu domény. (Například BrittaSimon@contoso.com stane BrittaSimon v HANA.)

5. Pro účely testování, přiřaďte všechny **XS** role pro uživatele.

    ![přiřazení rolí](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Musíte získat oprávnění, které jsou vhodné pro vaše případy použití.

6. Uložte uživatele.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup k SAP HANA.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon SAP HANA, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikace. Přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které**. Vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SAP HANA**.

    ![Přiřadit uživatele](./media/saphana-tutorial/tutorial_saphana_app.png) 

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202] 

4. Vyberte **přidat** tlačítko. V **přidat přiřazení** dialogové okno, vyberte **uživatelů a skupin**.

    ![V podokně Přidat přiřazení][203]

5. V **uživatelů a skupin** dialogové okno, vyberte **Britta Simon** v **uživatelé** seznamu.

6. Klikněte na tlačítko **vyberte** v tlačítko **uživatelů a skupin** dialogové okno.

7. Vyberte **přiřadit** v tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části otestovat vaše konfigurace Azure AD jeden přihlašování pomocí přístupového panelu.

Když vyberete dlaždici SAP HANA na přístupovém panelu, by měl získat automaticky přihlášeni aplikace SAP HANA.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS v Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saphana-tutorial/tutorial_general_01.png
[2]: ./media/saphana-tutorial/tutorial_general_02.png
[3]: ./media/saphana-tutorial/tutorial_general_03.png
[4]: ./media/saphana-tutorial/tutorial_general_04.png

[100]: ./media/saphana-tutorial/tutorial_general_100.png

[200]: ./media/saphana-tutorial/tutorial_general_200.png
[201]: ./media/saphana-tutorial/tutorial_general_201.png
[202]: ./media/saphana-tutorial/tutorial_general_202.png
[203]: ./media/saphana-tutorial/tutorial_general_203.png

