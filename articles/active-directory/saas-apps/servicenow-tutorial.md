---
title: 'Kurz: Integrace Azure Active Directory s použitím ServiceNow | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 1963c2f45bc37741a6d00c8dae985bfa5237ce7f
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259224"
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Kurz: Integrace Azure Active Directory s použitím ServiceNow

V tomto kurzu se dozvíte, jak integrovat ServiceNow s Azure Active Directory (Azure AD).

ServiceNow integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ServiceNow.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ServiceNow (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s použitím ServiceNow, potřebujete následující položky:

- S předplatným služby Azure AD
- Pro ServiceNow, instanci nebo tenanta ServiceNow, Calgary verze nebo novější
- Pro ServiceNow Express, instance ServiceNow Express, Helsinki verze nebo novější
- ServiceNow klient musí mít [více jednotné přihlašování na modul Plugin poskytovatele](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) povolena. To můžete udělat [odesílá se žádost o službu](https://hi.service-now.com).
- Pro automatickou konfiguraci povolení modulu plug-in více zprostředkovatele pro ServiceNow.

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ServiceNow z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-servicenow-from-the-gallery"></a>Přidání ServiceNow z Galerie
Konfigurace integrace ServiceNow do služby Azure AD, budete muset přidat ServiceNow z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ServiceNow z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **ServiceNow**vyberte **ServiceNow** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![ServiceNow v seznamu výsledků](./media/servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s použitím ServiceNow podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v ServiceNow je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v ServiceNow.

V ServiceNow, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s použitím ServiceNow, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On pro ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurovat Azure AD Single Sign-On pro ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytvoření zkušebního uživatele ServiceNow](#create-a-servicenow-test-user)**  – Pokud chcete mít protějšek Britta Simon ServiceNow, který je propojený s Azure AD reprezentace uživatele.
5. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Konfigurace Azure AD Single Sign-On pro ServiceNow

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci ServiceNow.

**Ke konfiguraci Azure AD jednotné přihlašování s použitím ServiceNow, proveďte následující kroky:**

1. Na webu Azure Portal na **ServiceNow** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. Na **ServiceNow domény a adresy URL** části, proveďte následující kroky:

    ![ServiceNow domény a adresy URL jednotného přihlašování – informace](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<instance-name>.service-now.com/navpage.do`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<instance-name>.service-now.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Budete muset aktualizovat tyto hodnoty od skutečné přihlašovací adresu URL a identifikátor, který je vysvětlen později v tomto kurzu.

4. Na **podpisový certifikát SAML** části, proveďte následující kroky: 

    ![Odkaz ke stažení certifikátu](./media/servicenow-tutorial/tutorial_servicenow_certificate.png)

    a. Klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku, protože tato adresa Url metadat federace aplikace se použije v pozdější části kurzu.

    b. Klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/servicenow-tutorial/tutorial_general_400.png)

6. Přihlaste se k ServiceNow aplikaci jako správce.

7. Aktivovat **integrace – více jednotné přihlašování – instalační program zprostředkovatele** modulu plug-in podle následující kroků:

    a. V navigačním podokně na levé straně, vyhledejte **definice systému** části z panelu hledání a potom klikněte na tlačítko **moduly plug-in**.

    ![Aktivace modulu plug-in](./media/servicenow-tutorial/tutorial_servicenow_03.png "aktivace modulu plug-in")

     b. Vyhledejte **integrace – více jednotné přihlašování – instalační program zprostředkovatele**.

     ![Aktivace modulu plug-in](./media/servicenow-tutorial/tutorial_servicenow_04.png "aktivace modulu plug-in")

    c. Vyberte modul plug-in. Klikněte pravým tlačítkem a vyberte **aktivovat/Upgrade**.

    d. Klikněte na tlačítko **aktivovat** tlačítko.

8. Existují dva způsoby, ve kterém **ServiceNow** může být nakonfigurované automatické a ruční.

9. Pro konfiguraci **ServiceNow** automaticky postupujte níže uvedený postup

    a. Vraťte se **ServiceNow** jednotného přihlašování na stránce na webu Azure Portal.

    b. Jedním kliknutím konfigurace služby je k dispozici pro ServiceNow tedy bude Azure AD automaticky nakonfigurovat ServiceNow pro ověřování založené na SAML. Pokud chcete povolit tuto službu, přejděte na **ServiceNow konfigurace** klikněte na tlačítko **nakonfigurovat ServiceNow** konfigurovat přihlašování – okno.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    c. Zadejte název instance ServiceNow, uživatelské jméno admin a heslo správce **nakonfigurovat přihlašování** formuláři a klikněte na tlačítko **nakonfigurovat nyní**. Všimněte si, že musíte mít uživatelské jméno správce zadané **security_admin** v ServiceNow pro tuto práci přiřazenou roli. V opačném případě ručně nakonfigurovat ServiceNow, které používají Azure AD jako zprostředkovatele identity SAML, klikněte na **ručně nakonfigurovat jednotné přihlašování** a zkopírujte **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z části Stručná referenční příručka.

    ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/configure.png "konfigurovat adresu URL aplikace")

    d. Přihlaste se k ServiceNow aplikaci jako správce.

    e. V Automatická konfigurace jsou všechna potřebná nastavení nakonfigurované na **ServiceNow** straně ale **certifikát X.509** není ve výchozím nastavení povolená. Budete muset ručně namapovat na poskytovatele identity v ServiceNow. postupujte podle níže uvedeného postupu u stejné:
    
    * V navigačním podokně na levé straně klikněte na tlačítko **zprostředkovatelé Identity** pod **více poskytovatele jednotného přihlašování**.

      ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_07.png "nakonfigurovat jednotné přihlašování")

    * Klikněte na zprostředkovatele identity automaticky generované

      ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_08.png "nakonfigurovat jednotné přihlašování")

    * Přejděte dolů k položce **certifikát X.509** oddílu. Vyberte **Upravit**.

      ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_09.png "nakonfigurovat jednotné přihlašování")
    
    * Vyberte na certifikátu a klikněte na ikonu se šipkou doprava přidejte certifikát

      ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_11.png "nakonfigurovat jednotné přihlašování")

    * Klikněte na **Uložit**.

    * Klikněte na **aktivovat** v pravém horním rohu stránky.

10. Pro konfiguraci **ServiceNow** ručně postupujte níže uvedený postup

11. Přihlaste se k ServiceNow aplikaci jako správce.

12. V navigačním podokně na levé straně, vyhledejte **více poskytovatele jednotného přihlašování** části z panelu hledání a potom klikněte na tlačítko **vlastnosti**.

    ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/tutorial_servicenow_06.png "konfigurovat adresu URL aplikace")

13. Na **více vlastností poskytovatele jednotného přihlašování** dialogového okna, proveďte následující kroky:

    ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/ic7694981.png "konfigurovat adresu URL aplikace")

    a. Jako **povolit více poskytovatele jednotného přihlašování**vyberte **Ano**.

    b. Jako **povolit automatický import uživatelů ze všech poskytovatelů identity v tabulce uživatelských**vyberte **Ano**.

    c. Jako **protokolování pro více zprostředkovatele integraci jednotného přihlašování k povolení ladění**vyberte **Ano**.

    d. V **pole na uživatele, který tabulky...**  textové pole, typ **uživatelské_jméno**.

    e. Klikněte na **Uložit**.

14. V navigačním podokně na levé straně, vyhledejte **více poskytovatele jednotného přihlašování** části z panelu hledání a potom klikněte na tlačítko **certifikáty x509**.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_05.png "nakonfigurovat jednotné přihlašování")

15. Na **certifikáty X.509** dialogového okna, klikněte na tlačítko **nový**.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694974.png "nakonfigurovat jednotné přihlašování")

16. Na **certifikáty X.509** dialogového okna, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694975.png "nakonfigurovat jednotné přihlašování")

    a. V **název** textového pole zadejte název pro vaši konfiguraci (například: **TestSAML2.0**).

    b. Vyberte **aktivní**.

    c. Jako **formátu**vyberte **PEM**.

    d. Jako **typ**vyberte **důvěřovat certifikátu Store**.

    e. Otevřete váš certifikát kódovaný v Base64 stáhli z Azure v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte jej do **PEM certifikátu** textového pole.

     f. Klikněte na tlačítko **odeslat**.

17. V navigačním podokně na levé straně klikněte na tlačítko **zprostředkovatelé Identity**.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_07.png "nakonfigurovat jednotné přihlašování")

18. Na **zprostředkovatelé Identity** dialogového okna, klikněte na tlačítko **nový**.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694977.png "nakonfigurovat jednotné přihlašování")

19. Na **zprostředkovatelé Identity** dialogového okna, klikněte na tlačítko **SAML2 Update1?**.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694978.png "nakonfigurovat jednotné přihlašování")

20. V dialogovém okně Vlastnosti Update1 SAML2 proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/idp.png "nakonfigurovat jednotné přihlašování")

    a. Vyberte **URL** možnost **Import metadat zprostředkovatele Identity** dialogové okno.

    b. Zadejte **adresa Url federačních metadat aplikace** zkopírovanou z webu Azure portal.

    c. Klikněte na **Importovat**.

21. Načte adresu URL metadat zprostředkovatele identity a naplní všechny informace o pole.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694982.png "nakonfigurovat jednotné přihlašování")

    a. V **název** textového pole zadejte název pro vaši konfiguraci (například **SAML 2.0**).
    
    b. Kopírování **domovské stránky ServiceNow** hodnota a vložte ji **přihlašovací adresa URL** textového pole v **ServiceNow domény a adresy URL** části na webu Azure portal.

    > [!NOTE]
    > Na domovské stránce instance ServiceNow je tvořen vaše **adresy URL tenanta ServieNow** a **/navpage.do** (například:`https://fabrikam.service-now.com/navpage.do`).

    c. Kopírování **Entity ID / vystavitele** hodnota a vložte ji **identifikátor** textového pole v **ServiceNow domény a adresy URL** části na webu Azure portal.

    d. Klikněte na tlačítko **Advanced**. V **pole uživatel** textové pole, typ **e-mailu** nebo **uživatelské_jméno**podle toho, jaké pole se používá pro jednoznačnou identifikaci uživatelů ve vašem nasazení ServiceNow.

    > [!NOTE]
    > Můžete nakonfigurovat služby Azure AD ke generování ID uživatele služby Azure AD (hlavní název uživatele) nebo e-mailovou adresu jako jedinečný identifikátor v tokenu SAML tak, že přejdete **ServiceNow > atributy > Single Sign-On** části webu Azure portal a mapování na požadované pole **nameidentifier** atribut. Hodnota uložená ve službě Azure AD (například hlavní název uživatele) pro vybraný atribut musí odpovídat hodnotě uložené v ServiceNow pro zadané pole (například uživatelské_jméno)

    e. V části **x509 certifikát**, obsahuje seznam certifikátů, které jste vytvořili v předchozím kroku.

    > [!NOTE]
    > ServiceNow neumožňuje aktivace zprostředkovatele identity bez kliknutím na tlačítko Testovat připojení, k přepsání stejné, postupujte následujících kroků.

22. Klikněte na ikonu nabídky od vašeho zprostředkovatele identity, který jste vytvořili jako součást konfigurace a v seznamu vyberte **zkopírujte sys_id**

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694992.png "nakonfigurovat jednotné přihlašování")

23. V horním levém vyhledávacího pole vyhledejte **sys_properties.list** a stiskněte klávesu enter.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694993.png "nakonfigurovat jednotné přihlašování")

24. Klikněte na možnost **Nové**.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694994.png "nakonfigurovat jednotné přihlašování")

25. V **vlastnost systému** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694995.png "nakonfigurovat jednotné přihlašování")

    a. Zadejte `glide.authenticate.sso.redirect.idp` hodnota v textovém poli název.

    b. V **hodnotu** textové pole, vložit sys_id kopie hodnoty, které jste zkopírovali v předchozích krocích.

    c. Vyberte **privátní**.

    d. Klikněte na tlačítko **odeslat**.

26. Klikněte na možnost **Nové**.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694994.png "nakonfigurovat jednotné přihlašování")

27. V **vlastnost systému** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694996.png "nakonfigurovat jednotné přihlašování")

    a. Zadejte `glide.authenticate.multisso.test.connection.mandatory` hodnota v textovém poli název.

    b. V **hodnotu** textového pole zadejte **false**.

    c. Klikněte na tlačítko **odeslat**.

28. Po nějaké době nad krok, teď bude moci uživatel aktivovat nového zprostředkovatele identity a jednotného přihlašování k vaší by mělo fungovat.

> [!NOTE]
> Také Upozorňujeme, že máte k otestování vaší nové konfigurace zprostředkovatele identity v nové anonymní okno

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Konfigurovat Azure AD Single Sign-On pro ServiceNow Express

1. Na webu Azure Portal na **ServiceNow** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. Na **ServiceNow domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte hodnotu pomocí následujícího vzorce: `https://<instance-name>.service-now.com/navpage.do`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory klienta ServiceNow](https://www.servicenow.com/support/contact-support.html) k získání těchto hodnot.

4. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_certificates.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_general_400.png)

6. Jedním kliknutím konfigurace služby je k dispozici pro ServiceNow tedy bude Azure AD automaticky nakonfigurovat ServiceNow pro ověřování založené na SAML. Pokud chcete povolit tuto službu, přejděte na **ServiceNow konfigurace** klikněte na tlačítko **nakonfigurovat ServiceNow** konfigurovat přihlašování – okno.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Zadejte název instance ServiceNow, uživatelské jméno admin a heslo správce **nakonfigurovat přihlašování** formuláři a klikněte na tlačítko **nakonfigurovat nyní**. Všimněte si, že musíte mít uživatelské jméno správce zadané **security_admin** v ServiceNow pro tuto práci přiřazenou roli. V opačném případě ručně nakonfigurovat ServiceNow, které používají Azure AD jako zprostředkovatele identity SAML, klikněte na **ručně nakonfigurovat jednotné přihlašování** a zkopírujte **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z části Stručná referenční příručka.

    ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/configure.png "konfigurovat adresu URL aplikace")

8. Přihlaste se k ServiceNow Express aplikaci jako správce.

9. V navigačním podokně na levé straně klikněte na tlačítko **Single Sign-On**.

    ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/ic7694980ex.png "konfigurovat adresu URL aplikace")

10. Na **Single Sign-On** dialogového okna, klikněte na ikonu konfigurace v pravém horním rohu a nastavte následující vlastnosti:

    ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/ic7694981ex.png "konfigurovat adresu URL aplikace")

    a. Přepnout **povolit více poskytovatele jednotného přihlašování** vpravo.
    
    b. Přepnout **protokolování pro více zprostředkovatele integraci jednotného přihlašování k povolení ladění** vpravo.
    
    c. V **pole na uživatele, který tabulky...**  textové pole, typ **uživatelské_jméno**.

11. Na **Single Sign-On** dialogového okna, klikněte na tlačítko **přidat nový certifikát**.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694973ex.png "nakonfigurovat jednotné přihlašování")

12. Na **certifikáty X.509** dialogového okna, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694975.png "nakonfigurovat jednotné přihlašování")

    a. V **název** textového pole zadejte název pro vaši konfiguraci (například: **TestSAML2.0**).

    b. Vyberte **aktivní**.

    c. Jako **formátu**vyberte **PEM**.

    d. Jako **typ**vyberte **důvěřovat certifikátu Store**.

    e. Váš certifikát kódovaný v Base64 stáhnout z webu Azure portal v poznámkovém bloku otevřete, zkopírujte obsah ho do schránky a vložte ho do **PEM certifikátu** textového pole.

    f. Klikněte na tlačítko **aktualizace**

13. Na **Single Sign-On** dialogového okna, klikněte na tlačítko **přidat nového zprostředkovatele identity**.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694976ex.png "nakonfigurovat jednotné přihlašování")

14. Na **přidat nového zprostředkovatele Identity** dialogového okna, v části **konfigurace zprostředkovatele Identity**, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694982ex.png "nakonfigurovat jednotné přihlašování")

    a. V **název** textového pole zadejte název pro vaši konfiguraci (například: **SAML 2.0**).

    b. V **adresa URL zprostředkovatele Identity** pole, vložte hodnotu **ID zprostředkovatele Identity**, který jste zkopírovali z portálu Azure portal.
    
    c. V **zprostředkovatele Identity AuthnRequest** pole, vložte hodnotu **adresy URL požadavku ověřování**, který jste zkopírovali z portálu Azure portal.

    d. V **zprostředkovatele Identity SingleLogoutRequest** pole, vložte hodnotu **adresu URL jednotného odhlašování služby**, který jste zkopírovali z portálu Azure portal

    e. Jako **certifikát poskytovatele Identity**, vyberte certifikát, který jste vytvořili v předchozím kroku.

15. Klikněte na tlačítko **Upřesnit nastavení**a v části **další vlastnosti zprostředkovatele Identity**, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694983ex.png "nakonfigurovat jednotné přihlašování")

    a. V **protokol vazby pro zprostředkovatele identity SingleLogoutRequest** textové pole, typ **urn: oasis: názvy: tc: SAML:2.0:bindings:HTTP – přesměrování**.

    b. V **NameID zásady** textové pole, typ **urn: oasis: názvy: tc: SAML:1.1:nameid – formát: neurčené**.

    c. V **AuthnContextClassRef metoda**, typ `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Zrušte výběr **vytvořit AuthnContextClass**.

16. V části **další vlastnosti zprostředkovatele služby**, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694984ex.png "nakonfigurovat jednotné přihlašování")

    a. V **domovské stránky ServiceNow** textového pole zadejte adresu URL vaší domovské stránce instance ServiceNow.

    > [!NOTE]
    > Na domovské stránce instance ServiceNow je tvořen vaše **adresy URL tenanta ServieNow** a **/navpage.do** (například: `https://fabrikam.service-now.com/navpage.do`).

    b. V **Entity ID / vystavitele** textového pole zadejte adresu URL vašeho tenanta ServiceNow.

    c. V **identifikátor URI cílové skupiny** textového pole zadejte adresu URL vašeho tenanta ServiceNow.

    d. V **hodin zkreslit** textové pole, typ **60**.

    e. V **pole uživatel** textové pole, typ **e-mailu** nebo **uživatelské_jméno**podle toho, jaké pole se používá pro jednoznačnou identifikaci uživatelů ve vašem nasazení ServiceNow.

    > [!NOTE]
    > Můžete nakonfigurovat služby Azure AD ke generování ID uživatele služby Azure AD (hlavní název uživatele) nebo e-mailovou adresu jako jedinečný identifikátor v tokenu SAML tak, že přejdete **ServiceNow > atributy > Single Sign-On** části webu Azure portal a mapování na požadované pole **nameidentifier** atribut. Hodnota uložená ve službě Azure AD (například hlavní název uživatele) pro vybraný atribut musí odpovídat hodnotě uložené v ServiceNow pro zadané pole (například uživatelské_jméno)

    f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/servicenow-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/servicenow-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/servicenow-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/servicenow-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-servicenow-test-user"></a>Vytvoření zkušebního uživatele ServiceNow

Cílem této části je vytvořte uživatele Britta Simon v ServiceNow. ServiceNow podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](servicenow-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory klienta ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k ServiceNow.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit k ServiceNow Britta Simon, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **ServiceNow**.

    ![V seznamu aplikací na odkaz ServiceNow](./media/servicenow-tutorial/tutorial_servicenow_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici ServiceNow na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci ServiceNow.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](servicenow-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/servicenow-tutorial/tutorial_general_01.png
[2]: ./media/servicenow-tutorial/tutorial_general_02.png
[3]: ./media/servicenow-tutorial/tutorial_general_03.png
[4]: ./media/servicenow-tutorial/tutorial_general_04.png

[100]: ./media/servicenow-tutorial/tutorial_general_100.png

[200]: ./media/servicenow-tutorial/tutorial_general_200.png
[201]: ./media/servicenow-tutorial/tutorial_general_201.png
[202]: ./media/servicenow-tutorial/tutorial_general_202.png
[203]: ./media/servicenow-tutorial/tutorial_general_203.png
