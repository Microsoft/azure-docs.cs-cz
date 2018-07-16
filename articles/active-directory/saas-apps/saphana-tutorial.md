---
title: 'Kurz: Integrace Azure Active Directory se SAP HANA | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP HANA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: c466e811d868403c59d6615882422996442d792a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045823"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Kurz: Integrace Azure Active Directory se SAP HANA

V tomto kurzu se dozvíte, jak integrovat SAP HANA s Azure Active Directory (Azure AD).

Když integrujete SAP HANA v Azure AD, získáte následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SAP HANA.
- Můžete povolit uživatelům, aby automaticky získat přihlášeni k SAP HANA pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se SAP HANA, potřebujete následující položky:

- S předplatným služby Azure AD
- SAP HANA předplatné, které je jednotné přihlašování (SSO) povoleno
- Instanci HANA, která běží na libovolné veřejné IaaS on-premises, virtuální počítač Azure nebo velké instance SAP v Azure
- Správa XSA webové rozhraní, jakož i HANA Studio nainstalovaná v instanci HANA

> [!NOTE]
> Nedoporučujeme použití produkční prostředí SAP Hana pro testování kroky v tomto kurzu. Nejdřív otestovat integraci v vývojové nebo testovací prostředí aplikace a pak používat produkčním prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte vaše produkční prostředí, pokud to není nutné.
- [Získat bezplatnou zkušební verzi měsíčního](https://azure.microsoft.com/pricing/free-trial/) služby Azure AD, pokud ještě nemáte prostředí zkušební verzi Azure AD.

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář, který je popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SAP HANA z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-sap-hana-from-the-gallery"></a>Přidat z Galerie SAP HANA
Pokud chcete nakonfigurovat integraci SAP HANA do služby Azure AD, přidáte z Galerie na váš seznam spravovaných aplikací SaaS SAP HANA.

**Chcete-li přidat z Galerie SAP HANA, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci** tlačítko nahoře dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **SAP HANA**. Potom vyberte **SAP HANA** na panelu výsledků. Nakonec vyberte **přidat** tlačítko pro přidání aplikace. 

    ![Nová aplikace](./media/saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotného přihlašování se SAP HANA na základě testovací uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, kdo je uživatel protějšky v SAP HANA na uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit propojení mezi uživatele služby Azure AD a související uživatel v SAP HANA.

V SAP HANA, zadejte **uživatelské jméno** hodnota stejnou hodnotu **uživatelské jméno** ve službě Azure AD. Tento krok vytvoří propojení mezi dva uživatele.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se SAP HANA, proveďte následující stavebních bloků:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configuring-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
2. [Vytvořit testovacího uživatele Azure AD](#creating-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
3. [Vytvoření zkušebního uživatele SAP HANA](#creating-a-sap-hana-test-user) mít protějšek Britta Simon v SAP HANA, který je propojený s Azure AD zastoupení uživatele.
4. [Přiřadit uživatele Azure AD](#assigning-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. [Otestovat jednotné přihlašování](#testing-single-sign-on) ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování aplikace SAP HANA.

**Ke konfiguraci Azure AD jednotného přihlašování se SAP HANA, proveďte následující kroky:**

1. Na webu Azure Portal na **SAP HANA** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. V **jednotného přihlašování** dialogovém okně **přihlašování na základě SAML**vyberte **režimu**.
 
    ![Jednotné přihlašování – dialogové okno](./media/saphana-tutorial/tutorial_saphana_samlbase.png)

3. V **SAP HANA domény a adresy URL** části, proveďte následující kroky:

    ![Domény a adresy URL jednotného přihlašování – informace](./media/saphana-tutorial/tutorial_saphana_url.png)

    a. V **identifikátor** pole, zadejte následující příkaz: `HA100` 

    b. V **adresy URL odpovědi** pole, zadejte adresu URL s následujícím vzorem: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Obraťte se [tým podpory SAP HANA klienta](https://cloudplatform.sap.com/contact.html) k získání těchto hodnot. 

4. V **podpisový certifikát SAML** vyberte **soubor XML s metadaty**. Uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Pokud certifikát není aktivní, pak ji nastavíte jako aktivní tak, že vyberete **nastavit nový certifikát jako aktivní** zaškrtávací políčko ve službě Azure AD. 

5. Aplikace SAP HANA očekává, že kontrolní výrazy SAML v určitém formátu. Následující snímek obrazovky ukazuje příklad tohoto formátu. 

    Tady jsme změnili **identifikátor uživatele** s **ExtractMailPrefix()** funkce **user.mail**. Díky tomu získá hodnotu předpony e-mail uživatele, který je jedinečné ID uživatele. Toto ID uživatele je odeslat do aplikace SAP HANA v každé úspěšné odpovědi.

    ![Konfigurace jednotného přihlašování](./media/saphana-tutorial/attribute.png)

6. V **atributy uživatele** část **jednotného přihlašování** dialogové okno pole, proveďte následující kroky:

    a. V **identifikátor uživatele** rozevíracího seznamu vyberte **ExtractMailPrefix**.
    
    b. V **e-mailu** rozevíracího seznamu vyberte **user.mail**.

7. Vyberte tlačítko **Uložit**.

    ![Konfigurace jednotné přihlašování tlačítko Uložit](./media/saphana-tutorial/tutorial_general_400.png)
    
8. Pokud chcete nakonfigurovat jednotné přihlašování na straně SAP HANA, přihlaste se k vaší **HANA XSA Webová konzola** tak, že přejdete do příslušného koncového bodu HTTPS.

    > [!NOTE]
    > Ve výchozím nastavení adresa URL přesměruje požadavek přihlašovací obrazovku, která vyžaduje přihlašovací údaje ověřeného uživatele databáze SAP HANA. Uživatel, který se přihlásí, musí mít oprávnění k provádění úloh správy SAML.

9. Přejděte ve webovém rozhraní XSA **zprostředkovatele Identity SAML**. Tam pak vyberete **+** tlačítko v dolní části obrazovky zobrazte **přidat informace o poskytovateli Identity** podokně. Pak proveďte následující kroky:

    ![Přidat zprostředkovatele Identity](./media/saphana-tutorial/sap1.png)

    a. V **přidat informace o poskytovateli Identity** podokně do vložte obsah XML metadat (který jste si stáhli z portálu Azure portal) **metadat** pole.

    ![Přidat nastavení zprostředkovatele Identity](./media/saphana-tutorial/sap2.png)

    b. Pokud obsah dokumentu XML platný, proces analýzy extrahuje informace, které je vyžadováno pro **předmět, Entity ID a vystavitele** pole v **obecná data** obrazovky oblasti. Extrahuje informace, které je nezbytné pro pole adresy URL v **cílové** obrazovky oblasti, například **základní adresu URL a adresy URL SingleSignOn (*)** pole.

    ![Přidat nastavení zprostředkovatele Identity](./media/saphana-tutorial/sap3.png)

    c. V **název** pomocí boxingu **obecná Data** obrazovky oblasti, zadejte název nového zprostředkovatele identity SAML SSO.

    > [!NOTE]
    > Název zprostředkovatele identity SAML je povinná a musí být jedinečný. Zobrazí se v seznamu dostupných zprostředkovatelů identity SAML, který se zobrazí, když vyberete jako metodu ověřování pro použití aplikacemi SAP HANA XS SAML. Například to můžete provést **ověřování** obrazovky oblasti nástroje pro správu XS artefaktů.

10. Vyberte **Uložit** uložit podrobnosti zprostředkovatele identity SAML a přidat nového zprostředkovatele identity SAML do seznamu známých zprostředkovatelů identity SAML.

    ![Tlačítko Uložit](./media/saphana-tutorial/sap4.png)

11. V HANA Studio v systému vlastností **konfigurace** kartu, nastavení pomocí filtru **saml**. Potom upravte **assertion_timeout** z **10 sekund** k **120 sekundu**.

    ![nastavení assertion_timeout](./media/saphana-tutorial/sap7.png)

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com) při nastavení aplikace! Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace** vyberte **Single Sign-On** kartu a přístup k vložený dokumentace ke službě prostřednictvím **konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace v [dokumentace ke službě Azure AD embedded](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **webu Azure portal**, v levém podokně, vyberte **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/saphana-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**. Potom vyberte **všichni uživatelé**.
    
    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/saphana-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogu **přidat** v horní části dialogového okna.
 
    ![Tlačítko Přidat](./media/saphana-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno pole, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/saphana-tutorial/create_aaduser_04.png) 

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo**a zapište si heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-sap-hana-test-user"></a>Vytvoření zkušebního uživatele SAP HANA

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k SAP HANA, musíte zřídit v SAP HANA.
SAP HANA podporuje just-in-time zřizování, což je tak ve výchozím nastavení povolené.

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

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k SAP HANA.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon k SAP HANA, postupujte následovně:**

1. Na webu Azure Portal otevřete zobrazení aplikace. Přejděte do adresáře zobrazení a přejít na **podnikové aplikace**. Vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SAP HANA**.

    ![Přiřadit uživatele](./media/saphana-tutorial/tutorial_saphana_app.png) 

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202] 

4. Vyberte **přidat** tlačítko. V **přidat přiřazení** dialogu **uživatelů a skupin**.

    ![Podokno Přidat přiřazení][203]

5. V **uživatelů a skupin** dialogu **Britta Simon** v **uživatelé** seznamu.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Vyberte **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když vyberete dlaždici SAP HANA na přístupovém panelu, by měl získat automaticky přihlásí aplikace SAP HANA.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
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

