---
title: 'Kurz: Azure Active Directory integrace s Enterprise smysl Qlik | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Qlik smysl Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: 8cca4fb3ee10d56481cefccb7ea869b6bf13109c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352646"
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Kurz: Azure Active Directory integrace s Qlik smysl Enterprise

V tomto kurzu zjistěte, jak integrovat Qlik smysl Enterprise s Azure Active Directory (Azure AD).

Integrace Qlik smysl Enterprise s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup do firemní sítě Qlik smysl.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Qlik smysl Enterprise (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Qlik smysl Enterprise, potřebujete následující položky:

- Předplatné služby Azure AD
- Qlik smysl Enterprise jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat a jeden měsíc zkušební: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Qlik smysl Enterprise z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Přidání Qlik smysl Enterprise z Galerie
Při konfiguraci integrace Qlik smysl Enterprise do služby Azure AD potřebujete přidat Qlik smysl Enterprise z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Qlik smysl Enterprise z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Qlik smysl Enterprise**, vyberte **Qlik smysl Enterprise** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Enterprise Qlik smysl v seznamu výsledků](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Enterprise smysl Qlik podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Qlik smysl Enterprise je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatele v podniku Qlik smysl, je nutné stanovit.

V Qlik smysl Enterprise, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování k Qlik smysl organizace, budete muset provést následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Qlik smysl Enterprise](#create-a-qlik-sense-enterprise-test-user)**  – Pokud chcete mít protějšek Britta Simon v Qlik smysl organizace, která je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Qlik smysl Enterprise.

**Ke konfiguraci Azure AD jednotného přihlašování k Qlik smysl organizace, proveďte následující kroky:**

1. Na portálu Azure na **Qlik smysl Enterprise** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. Na **Qlik smysl podnikové domény a adresy URL** část, proveďte následující kroky:

    ![Qlik smysl podnikové domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<Qlik Sense Fully Qualifed Hostname>:443//samlauthn/`
    
    > [!NOTE]
    > Všimněte si do adresy koncové lomítko na konci tento identifikátor URI. Je vyžadována.
    
    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizovat se skutečné přihlašovací adresa URL a identifikátor, který je popsán později v tomto kurzu nebo kontaktujte [tým podpory klient systému Enterprise smysl Qlik](https://www.qlik.com/us/services/support) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_400.png)

6. Příprava souboru XML federační Metadata tak, aby, můžete nahrát na server Qlik smysl.
   
    > [!NOTE]
    > Před nahráním IdP metadata k serveru Qlik smysl, je třeba soubor upravit, chcete-li odebrat informace, které zajistit správnou funkci mezi službou Azure AD a server Qlik smysl.
    
    ![QlikSense][qs24]
   
    a. Otevřete soubor FederationMetaData.xml, který jste si stáhli z portálu Azure v textovém editoru.
   
    b. Vyhledejte hodnotu **RoleDescriptor**.  Existují čtyři záznamy (dvě dvojice otevření a zavření značky elementu).
   
    c. Odstraníte RoleDescriptor značky a všechny informace v rozmezí ze souboru.
   
    d. Uložte soubor a uložte nedaleko pro použití později v tomto dokumentu.

7. Přejděte k Qlik smysl Qlik Management Console (QMC) jako uživatel, který můžete vytvořit virtuální proxy konfigurace.

8. V QMC, klikněte na **virtuální proxy** položku nabídky.
   
    ![QlikSense][qs6] 

9. V dolní části obrazovky klepněte **vytvořit nový** tlačítko.
   
    ![QlikSense][qs7]

10. Zobrazí se obrazovka virtuální proxy upravit.  Na pravé straně obrazovky je nabídka pro viditelnosti možnosti konfigurace.
   
    ![QlikSense][qs9]

11. S možností nabídky identifikace zaškrtnuto zadejte identifikační informace pro konfiguraci Azure virtuální proxy serveru.
    
    ![QlikSense][qs8]  
    
    a. **Popis** pole je popisný název pro konfiguraci virtuálního proxy serveru.  Zadejte hodnotu pro popis.
    
    b. **Předpony** pole identifikuje koncový bod virtuálního proxy pro připojení k Qlik smysl s Azure AD jednotné přihlašování.  Zadejte název jedinečnou předponu pro tento virtuální server proxy.
    
    c. **Časový limit nečinnosti relace (minuty)** vypršel časový limit pro připojení přes tuto virtuální proxy serveru.
    
    d. **Název hlavičky souboru cookie relace** je název souboru cookie pro relaci Qlik smysl uživatel obdrží po úspěšném ověření ukládání identifikátor relace.  Tento název musí být jedinečný.

12. Klikněte na možnost nabídky ověřování vytvořit viditelné.  Zobrazí se obrazovka ověřování.
    
    ![QlikSense][qs10]
    
    a. **Anonymní přístup režimu** rozevíracího seznamu určuje Pokud anonymní uživatelé mohou přistupovat k Qlik smysl přes virtuální proxy serveru.  Výchozí nastavení je anonymní uživatel.
    
    b. **Metodu ověřování** rozevíracího seznamu určuje schéma ověřování proxy virtuální použije.  V rozevíracím seznamu vyberte SAML.  Proto se zobrazí další možnosti.
    
    c. V **pole URI hostitel SAML**, zadejte název hostitele uživatele zadejte přístup k Qlik smysl prostřednictvím tento proxy server virtuální SAML.  Název hostitele je identifikátor uri serveru Qlik smysl.
    
    d. V **SAML entity ID**, zadejte stejné hodnota zadaná pro pole SAML hostitel identifikátoru URI.
    
    e. **SAML IdP metadata** je soubor upravit dříve v **upravit federačních metadat z konfigurace služby Azure AD** části.  **Před nahráním IdP metadata, soubor musí být upravena** odebrat informace o pracovat správně mezi službou Azure AD a server Qlik smysl.  **Naleznete pokyny výše, pokud má soubor ještě k provádění úprav.**  Pokud soubor byl upraven, klikněte na tlačítko Procházet a vyberte soubor upravená metadata nahrát do konfigurace virtuálního serveru proxy.
    
    f. Zadejte odkaz na atribut název nebo schéma pro představující atribut SAML **UserID** Azure AD odešle na server Qlik smysl.  Informace o schématu odkaz je k dispozici v konfiguraci aplikace Azure obrazovky post.  Chcete-li použít atribut názvu, zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    g. Zadejte hodnotu pro **adresář uživatelského** uživatelům, který bude připojen v při ověření Qlik smysl serveru prostřednictvím služby Azure AD.  Pevně zakódované hodnoty musí být uzavřena do **hranaté závorky []**.  Chcete-li použít atribut odeslány ve výrazu Azure AD SAML, zadejte název atributu v tohoto textového pole **bez** hranaté závorky.
    
    h. **SAML podpisový algoritmus** nastaví podpis pro konfiguraci proxy serveru virtuální certifikát služby zprostředkovatele (v tomto případu Qlik smysl serveru).  Pokud Qlik smysl serveru využívá důvěryhodné certifikát generovaný pomocí Microsoft Enhanced RSA a AES Cryptographic Provider, změňte SAML podpisový algoritmus, který se **SHA-256**.
    
    i. V části mapování atributů SAML umožňuje další atributy, jako jsou skupiny k odeslání do Qlik smysl pro použití v pravidla zabezpečení.

13. Klikněte na **Vyrovnávání zatížení** nabídky možnost vytvořit viditelné.  Zobrazí se obrazovka Vyrovnávání zatížení.
    
    ![QlikSense][qs11]

14. Klikněte na **přidat nový uzel serveru** tlačítko, vyberte modul uzlu nebo uzlů Qlik smysl odešle relací pro účely Vyrovnávání zatížení, a klikněte na **přidat** tlačítko.
    
    ![QlikSense][qs12]

15. Klikněte na možnost Pokročilé nabídky zviditelnit. Zobrazí se obrazovka Upřesnit.
    
    ![QlikSense][qs13]
    
    Seznamu povolených hostitele identifikuje názvy hostitelů, které jsou přijaty při připojování k serveru Qlik smysl.  **Zadejte název hostitele, které budou uživatelé zadávat při připojování k serveru Qlik smysl.** Název hostitele je stejnou hodnotu jako identifikátor uri SAML hostitele bez https://.

16. Klikněte **použít** tlačítko.
    
    ![QlikSense][qs14]

17. Kliknutím na tlačítko OK přijímat upozornění, které stavy proxy propojené s virtuální proxy server se restartuje.
    
    ![QlikSense][qs15]

18. Na pravé straně obrazovky zobrazí se v nabídce přidružené položky.  Klikněte na **proxy** možnost nabídky.
    
    ![QlikSense][qs16]

19. Zobrazí se obrazovka proxy serveru.  Klikněte **odkaz** tlačítko dole propojení proxy server na virtuální server proxy.
    
    ![QlikSense][qs17]

20. Vyberte uzel proxy serveru, který bude podporovat toto připojení virtuální proxy serveru a klikněte na **odkaz** tlačítko.  Po propojení, se objeví pod přidružené proxy proxy serveru.
    
    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

21. Po přibližně pět až deset sekund zobrazí se zpráva QMC aktualizovat.  Klikněte **aktualizovat QMC** tlačítko.
    
    ![QlikSense][qs20]

22. Když QMC aktualizuje, klikněte na **virtuální proxy** položku nabídky. Nové položky virtuální proxy SAML je uvedené v tabulce na obrazovce.  Jedním kliknutím na položku virtuální proxy.
    
    ![QlikSense][qs51]

23. V dolní části obrazovky se budou aktivovat tlačítko Stáhnout SP metadat.  Klikněte **stáhnout SP metadata** tlačítko Uložit metadata do souboru.
    
    ![QlikSense][qs52]

24. Otevřete soubor metadat poskytovatele služeb.  Sledovat **entityID** položku a **AssertionConsumerService** položku.  Tyto hodnoty jsou rovnocenná **identifikátor** a **přihlásit na adrese URL** v konfiguraci aplikace Azure AD. Vložte v tyto hodnoty **Qlik smysl podnikové domény a adresy URL** část v konfiguraci aplikace Azure AD, pokud jejich nejsou párování, pak by měl nahradíte je v Průvodci konfigurací aplikace Azure AD.
    
    ![QlikSense][qs53]

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

   ![Tlačítko Azure Active Directory](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

   !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

   ![Tlačítko Přidat](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

   ![Dialogové okno uživatele](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. V **název** zadejte **BrittaSimon**.

   b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

   c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

   d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-qlik-sense-enterprise-test-user"></a>Vytvoření zkušebního uživatele Qlik smysl Enterprise

V této části vytvoříte uživatele volal Britta Simon v Qlik smysl Enterprise. Práce s [tým podpory klient systému Enterprise smysl Qlik](https://www.qlik.com/us/services/support) přidejte uživatele v platformě Qlik smysl Enterprise. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup do firemní sítě Qlik smysl.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Qlik smysl Enterprise, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Qlik smysl Enterprise**.

    ![V seznamu aplikací na odkaz Qlik smysl Enterprise](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Qlik smysl Enterprise na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Qlik smysl Enterprise. 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png

