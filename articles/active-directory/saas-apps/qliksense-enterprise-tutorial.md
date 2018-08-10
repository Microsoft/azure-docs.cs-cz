---
title: 'Kurz: Integrace Azure Active Directory Qlik Sense Enterprise | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Qlik Sense Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/06/2018
ms.author: jeedes
ms.openlocfilehash: a8816451b45171e0ba8cbd7acc937201c587c481
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627946"
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Kurz: Integrace Azure Active Directory Qlik Sense Enterprise

V tomto kurzu se dozvíte, jak integrovat Qlik Sense organizace Azure Active Directory (Azure AD).

Qlik Sense podnikové integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Qlik Sense Enterprise.
- Uživatele, aby automaticky získat přihlášení k Qlik Sense Enterprise (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Qlik Sense Enterprise, budete potřebovat následující položky:

- Předplatné Azure AD
- Qlik Sense podnikové jednotné přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání organizace Qlik Sense z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Přidání organizace Qlik Sense z Galerie
Ke konfiguraci integrace Qlik Sense organizace do služby Azure AD, budete muset přidat Qlik Sense Enterprise na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Qlik Sense organizace z galerie, postupujte následovně:**

1. V ** [webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Qlik Sense Enterprise**vyberte **Qlik Sense Enterprise** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Qlik Sense Enterprise v seznamu výsledků](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Qlik Sense Enterprise na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Qlik Sense Enterprise je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Qlik Sense organizace je potřeba navázat.

V sadě Qlik Sense Enterprise, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Qlik Sense organizace, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on) ** – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) ** – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Qlik Sense Enterprise](#create-a-qlik-sense-enterprise-test-user) ** – Pokud chcete mít protějšek Britta Simon v sadě Qlik Sense Enterprise, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) ** – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on) ** – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Qlik Sense Enterprise.

**Ke konfiguraci Azure AD jednotné přihlašování s Qlik Sense organizace, proveďte následující kroky:**

1. Na webu Azure Portal na **Qlik Sense Enterprise** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. Na **Qlik Sense podnikové domény a adresy URL** části, proveďte následující kroky:

    ![Qlik Sense podnikové domény a adresy URL jednotného přihlašování – informace](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<Qlik Sense Fully Qualifed Hostname>:4443/azure/hub`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|
    | |

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    `https://<Qlik Sense Fully Qualifed Hostname>:4443/samlauthn/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizovat skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi, které jsou vysvětleny dále v tomto kurzu nebo se obraťte na [tým podpory Qlik Sense Enterprise Client](https://www.qlik.com/us/services/support) k získání těchto hodnot.

4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/qliksense-enterprise-tutorial/tutorial_general_400.png)

6. Příprava souboru XML metadat federace tak, aby, který můžete nahrát na server Qlik Sense.

    > [!NOTE]
    > Před nahráním na server Qlik Sense metadat zprostředkovatele identity, musí tento soubor upravit odebrat informace, chcete-li zajistit správnou funkci mezi službami Azure AD a Qlik Sense serveru.

    ![QlikSense][qs24]

    a. Otevřete soubor FederationMetaData.xml, který jste si stáhli z webu Azure portal v textovém editoru.

    b. Vyhledejte hodnotu **RoleDescriptor**.  Existují čtyři záznamy (dvě dvojice otevření a zavření značky elementu).

    c. Odstraňte RoleDescriptor značky a všech informací mezi ze souboru.

    d. Uložte soubor a zachování jejich okolních pro pozdější použití v tomto dokumentu.

7. Přejděte k Qlik Sense Qlik správy konzoly (QMC) jako uživatel, který můžete vytvořit konfigurace virtuálního serveru proxy.

8. V QMC, klikněte na **virtuální proxy** položky nabídky.

    ![QlikSense][qs6]

9. V dolní části obrazovky klikněte na tlačítko **vytvořit nový** tlačítko.

    ![QlikSense][qs7]

10. Zobrazí se na obrazovce pro úpravy proxy virtuální.  Na pravé straně obrazovky je nabídka pro zviditelnění možnosti konfigurace.

    ![QlikSense][qs9]

11. Možnost nabídky identifikace zaškrtnuté zadejte identifikační informace pro konfiguraci proxy služby Azure virtual.

    ![QlikSense][qs8]  

    a. **Popis** pole je popisný název pro konfiguraci virtuálního proxy serveru.  Zadejte hodnotu pro popis.

    b. **Předpony** pole identifikuje koncový bod virtuální proxy pro připojení k Qlik Sense s Azure AD jednotného přihlašování.  Zadejte jedinečnou předponu názvu pro tento virtuální server proxy.

    c. **Časový limit nečinnosti relace (minuty)** je časový limit pro připojení přes tuto virtuální proxy serveru.

    d. **Název hlavičky souboru cookie relace** je název souboru cookie uložením identifikátor relace pro relaci Qlik Sense uživateli se zobrazí po úspěšném ověření.  Tento název musí být jedinečný.

12. Klikněte na možnost nabídky ověřování, aby ji viděli.  Zobrazí se obrazovka ověřování.

    ![QlikSense][qs10]

    a. **Anonymní přístup režimu** rozevírací seznam určuje, pokud anonymní uživatelé mohou přistupovat k Qlik Sense přes virtuální proxy serveru.  Výchozí možnost je žádný uživatel anonymní.

    b. **Metodu ověřování** rozevíracího seznamu určuje schéma ověřování proxy virtuální použije.  Z rozevíracího seznamu vyberte SAML.  Díky tomu se zobrazí další možnosti.

    c. V **pole identifikátoru URI hostitele SAML**, zadejte název hostitele uživatele enter zpřístupníte Qlik Sense přes toto virtuální proxy SAML.  Název hostitele je identifikátor uri serveru Qlik Sense.

    d. V **SAML entity ID**, zadejte stejnou hodnotu pro pole identifikátoru URI hostitele SAML.

    e. **Metadat SAML zprostředkovatele identity** je soubor upravovat v **upravit federačních metadat z konfigurace služby Azure AD** oddílu.  **Před nahráním metadat zprostředkovatele identity, je potřeba soubor upravit** odebrat informace, chcete-li zajistit správnou funkci mezi službami Azure AD a Qlik Sense serveru.  **Najdete na výše uvedených pokynů, pokud má soubor ještě upravit.**  Pokud byl upraven soubor klikněte na tlačítko Procházet a vyberte soubor upravený metadat k nahrání do konfigurace virtuálního serveru proxy.

    f. Zadejte odkaz na atribut name nebo schéma pro zastoupení SAML atribut **UserID** Azure AD odešle na server Qlik Sense.  Referenční informace o schématu je k dispozici v konfiguraci příspěvek obrazovky aplikace Azure.  Chcete-li použít atribut name, zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    g. Zadejte hodnotu **adresář uživatele** pro uživatele, který bude připojen v při ověřování Qlik Sense server prostřednictvím služby Azure AD.  Pevně zakódované hodnoty musí být uzavřeny do **hranaté závorky []**.  Pokud chcete použít atribut poslaná kontrolní výraz Azure AD SAML, zadejte název atributu do tohoto textového pole **bez** hranaté závorky.

    h. **Podpisový algoritmus SAML** nastaví certifikát služby zprostředkovatele (v tomto případě Qlik Sense serveru), podepisování pro konfiguraci virtuálního proxy serveru.  Pokud server Qlik Sense používá důvěryhodný certifikát vytvořený pomocí Microsoft Enhanced RSA a AES Cryptographic Provider, změňte podpisový algoritmus SAML **SHA-256**.

    i. Mapování oddílu SAML atribut umožňuje další atributy, jako jsou skupiny k odeslání do Qlik Sense pro použití v pravidlech zabezpečení.

13. Klikněte na **Vyrovnávání zatížení** možnost nabídky, aby byla viditelná.  Zobrazí se obrazovka Vyrovnávání zatížení.

    ![QlikSense][qs11]

14. Klikněte na **přidat nový uzel serveru** tlačítka, vyberte modul uzel nebo uzly Qlik Sense relace pro účely Vyrovnávání zatížení a klikněte na tlačítko pošle **přidat** tlačítko.

    ![QlikSense][qs12]

15. Klikněte na možnost rozšířené nabídky a aktivujte ji. Pokročilé obrazovka se zobrazí.

    ![QlikSense][qs13]

    Prázdný seznam hostitele určuje názvy hostitelů, které přijímá při připojování k serveru Qlik Sense.  **Zadejte název hostitele, které budou uživatelé zadávat při připojování k serveru Qlik Sense.** Název hostitele se stejnou hodnotu jako identifikátoru uri hostitele SAML bez https://.

16. Klikněte na tlačítko **použít** tlačítko.

    ![QlikSense][qs14]

17. Klikněte na tlačítko OK potvrďte zprávu upozornění oznamující proxy propojené s virtuální proxy server se restartuje.

    ![QlikSense][qs15]

18. Na pravé straně obrazovky zobrazí se nabídka přidružené položky.  Klikněte na **proxy** nabídky.

    ![QlikSense][qs16]

19. Zobrazí se obrazovka proxy serveru.  Klikněte na tlačítko **odkaz** tlačítko v dolní části můžete propojit virtuální proxy serveru proxy.

    ![QlikSense][qs17]

20. Vyberte uzel proxy server, který bude podporovat toto připojení virtuální proxy serveru a klikněte na tlačítko **odkaz** tlačítko.  Po propojení, budou uvedeny proxy pod přidružené servery proxy.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

21. Po přibližně pět až deset sekund zobrazí se zpráva QMC aktualizovat.  Klikněte na tlačítko **aktualizovat QMC** tlačítko.

    ![QlikSense][qs20]

22. Když se aktualizuje QMC, klikněte na **virtuální proxy** položky nabídky. Nová položka virtuální proxy SAML je uveden v tabulce na obrazovce.  Jediným kliknutím na položku virtuální proxy.

    ![QlikSense][qs51]

23. V dolní části obrazovky se budou aktivovat tlačítko Stáhnout SP metadat.  Klikněte na tlačítko **SP stáhnout metadata** tlačítko Uložit metadata do souboru.

    ![QlikSense][qs52]

24. Otevření souboru sp metadat.  Podívejte se **entityID** položku a **AssertionConsumerService** položka.  Tyto hodnoty odpovídají **identifikátor**, **přihlašovací adresa URL** a **adresy URL odpovědi** v konfiguraci aplikace Azure AD. Vložte tyto hodnoty **Qlik Sense podnikové domény a adresy URL** části v konfiguraci Azure AD aplikace, pokud tyto neodpovídají, pak byste měli vyměnit v Průvodci konfigurací aplikací Azure AD.

    ![QlikSense][qs53]

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

   ![Tlačítko Azure Active Directory](./media/qliksense-enterprise-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

   !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/qliksense-enterprise-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

   ![Tlačítko Přidat](./media/qliksense-enterprise-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

   ![Dialogové okno uživatele](./media/qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. V **název** zadejte **BrittaSimon**.

   b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

   c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

   d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-qlik-sense-enterprise-test-user"></a>Vytvoření zkušebního uživatele Qlik Sense Enterprise

V této části vytvořte uživatele Britta Simon v Qlik Sense Enterprise. Práce s [tým podpory Qlik Sense Enterprise Client](https://www.qlik.com/us/services/support) přidat uživatele na platformě Qlik Sense Enterprise. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do firemní sítě Qlik Sense.

![Přiřazení role uživatele][200]

**Britta Simon přiřadit Qlik Sense organizace, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Qlik Sense Enterprise**.

    ![Odkaz Qlik Sense Enterprise v seznamu aplikací](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Qlik Sense Enterprise na přístupovém panelu, vám by měl získat automaticky přihlášení k Qlik Sense podnikové aplikace.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/qliksense-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/qliksense-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/qliksense-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/qliksense-enterprise-tutorial/tutorial_general_203.png

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png