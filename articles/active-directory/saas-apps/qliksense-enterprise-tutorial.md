---
title: 'Kurz: Integrace Azure Active Directory Qlik Sense Enterprise | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Qlik Sense Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.openlocfilehash: 1bf11bc93065c4c590b5224a805b8e1dbeadbb61
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54816904"
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Kurz: Integrace Azure Active Directory Qlik Sense Enterprise

V tomto kurzu se dozvíte, jak integrovat Qlik Sense organizace Azure Active Directory (Azure AD).
Qlik Sense podnikové integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Qlik Sense Enterprise.
* Uživatelům se automaticky přihlášeni k Qlik Sense Enterprise (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Qlik Sense Enterprise, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Qlik Sense podnikové jednotné přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Qlik Sense Enterprise **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Přidání organizace Qlik Sense z Galerie

Ke konfiguraci integrace Qlik Sense organizace do služby Azure AD, budete muset přidat Qlik Sense Enterprise na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Qlik Sense organizace z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Qlik Sense Enterprise**vyberte **Qlik Sense Enterprise** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Qlik Sense Enterprise v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s Enterprise Qlik Sense podle testovacího uživatele, volá se, **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Qlik Sense Enterprise.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Qlik Sense organizace, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Qlik Sense Enterprise Single Sign-On](#configure-qlik-sense-enterprise-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Qlik Sense Enterprise](#create-qlik-sense-enterprise-test-user)**  – Pokud chcete mít protějšek Britta Simon v sadě Qlik Sense Enterprise, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Qlik Sense organizace, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Qlik Sense Enterprise** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Qlik Sense podnikové domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

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

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-qlik-sense-enterprise-single-sign-on"></a>Konfigurace Qlik Sense Enterprise Single Sign-On

1. Příprava souboru XML metadat federace tak, aby, který můžete nahrát na server Qlik Sense.

    > [!NOTE]
    > Před nahráním na server Qlik Sense metadat zprostředkovatele identity, musí tento soubor upravit odebrat informace, chcete-li zajistit správnou funkci mezi službami Azure AD a Qlik Sense serveru.

    ![QlikSense][qs24]

    a. Otevřete soubor FederationMetaData.xml, který jste si stáhli z webu Azure portal v textovém editoru.

    b. Vyhledejte hodnotu **RoleDescriptor**.  Existují čtyři záznamy (dvě dvojice otevření a zavření značky elementu).

    c. Odstraňte RoleDescriptor značky a všech informací mezi ze souboru.

    d. Uložte soubor a zachování jejich okolních pro pozdější použití v tomto dokumentu.

2. Přejděte k Qlik Sense Qlik správy konzoly (QMC) jako uživatel, který můžete vytvořit konfigurace virtuálního serveru proxy.

3. V QMC, klikněte na **virtuální proxy** položky nabídky.

    ![QlikSense][qs6]

4. V dolní části obrazovky klikněte na tlačítko **vytvořit nový** tlačítko.

    ![QlikSense][qs7]

5. Zobrazí se na obrazovce pro úpravy proxy virtuální.  Na pravé straně obrazovky je nabídka pro zviditelnění možnosti konfigurace.

    ![QlikSense][qs9]

6. Možnost nabídky identifikace zaškrtnuté zadejte identifikační informace pro konfiguraci proxy služby Azure virtual.

    ![QlikSense][qs8]  

    a. **Popis** pole je popisný název pro konfiguraci virtuálního proxy serveru.  Zadejte hodnotu pro popis.

    b. **Předpony** pole identifikuje koncový bod virtuální proxy pro připojení k Qlik Sense s Azure AD jednotného přihlašování.  Zadejte jedinečnou předponu názvu pro tento virtuální server proxy.

    c. **Časový limit nečinnosti relace (minuty)** je časový limit pro připojení přes tuto virtuální proxy serveru.

    d. **Název hlavičky souboru cookie relace** je název souboru cookie uložením identifikátor relace pro relaci Qlik Sense uživateli se zobrazí po úspěšném ověření.  Tento název musí být jedinečný.

7. Klikněte na možnost nabídky ověřování, aby ji viděli.  Zobrazí se obrazovka ověřování.

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

8. Klikněte na **Vyrovnávání zatížení** možnost nabídky, aby byla viditelná.  Zobrazí se obrazovka Vyrovnávání zatížení.

    ![QlikSense][qs11]

9. Klikněte na **přidat nový uzel serveru** tlačítka, vyberte modul uzel nebo uzly Qlik Sense relace pro účely Vyrovnávání zatížení a klikněte na tlačítko pošle **přidat** tlačítko.

    ![QlikSense][qs12]

10. Klikněte na možnost rozšířené nabídky a aktivujte ji. Pokročilé obrazovka se zobrazí.

    ![QlikSense][qs13]

    Prázdný seznam hostitele určuje názvy hostitelů, které přijímá při připojování k serveru Qlik Sense.  **Zadejte název hostitele, které budou uživatelé zadávat při připojování k serveru Qlik Sense.** Název hostitele se stejnou hodnotu jako identifikátoru uri hostitele SAML bez https://.

11. Klikněte na tlačítko **použít** tlačítko.

    ![QlikSense][qs14]

12. Klikněte na tlačítko OK potvrďte zprávu upozornění oznamující proxy propojené s virtuální proxy server se restartuje.

    ![QlikSense][qs15]

13. Na pravé straně obrazovky zobrazí se nabídka přidružené položky.  Klikněte na **proxy** nabídky.

    ![QlikSense][qs16]

14. Zobrazí se obrazovka proxy serveru.  Klikněte na tlačítko **odkaz** tlačítko v dolní části můžete propojit virtuální proxy serveru proxy.

    ![QlikSense][qs17]

15. Vyberte uzel proxy server, který bude podporovat toto připojení virtuální proxy serveru a klikněte na tlačítko **odkaz** tlačítko.  Po propojení, budou uvedeny proxy pod přidružené servery proxy.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

16. Po přibližně pět až deset sekund zobrazí se zpráva QMC aktualizovat.  Klikněte na tlačítko **aktualizovat QMC** tlačítko.

    ![QlikSense][qs20]

17. Když se aktualizuje QMC, klikněte na **virtuální proxy** položky nabídky. Nová položka virtuální proxy SAML je uveden v tabulce na obrazovce.  Jediným kliknutím na položku virtuální proxy.

    ![QlikSense][qs51]

18. V dolní části obrazovky se budou aktivovat tlačítko Stáhnout SP metadat.  Klikněte na tlačítko **SP stáhnout metadata** tlačítko Uložit metadata do souboru.

    ![QlikSense][qs52]

19. Otevření souboru sp metadat.  Podívejte se **entityID** položku a **AssertionConsumerService** položka.  Tyto hodnoty odpovídají **identifikátor**, **přihlašovací adresa URL** a **adresy URL odpovědi** v konfiguraci aplikace Azure AD. Vložte tyto hodnoty **Qlik Sense podnikové domény a adresy URL** části v konfiguraci Azure AD aplikace, pokud tyto neodpovídají, pak byste měli vyměnit v Průvodci konfigurací aplikací Azure AD.

    ![QlikSense][qs53]

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do firemní sítě Qlik Sense.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Qlik Sense Enterprise**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Qlik Sense Enterprise**.

    ![Odkaz Qlik Sense Enterprise v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-qlik-sense-enterprise-test-user"></a>Vytvořit testovacího uživatele Qlik Sense Enterprise

V této části vytvořte uživatele Britta Simon v Qlik Sense Enterprise. Práce s [tým podpory Qlik Sense Enterprise](https://www.qlik.com/us/services/support) přidat uživatele na platformě Qlik Sense Enterprise. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Qlik Sense Enterprise na přístupovém panelu, můžete by měl být automaticky přihlášeni k Enterprise Qlik Sense, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

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

