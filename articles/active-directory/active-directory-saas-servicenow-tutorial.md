---
title: 'Kurz: Azure Active Directory integrace s ServiceNow | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: bc5b41da83f183aaf62723212f7197fb50dc536d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Kurz: Azure Active Directory integrace s ServiceNow

V tomto kurzu zjistěte, jak integrovat ServiceNow s Azure Active Directory (Azure AD).

Integrace ServiceNow s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k ServiceNow.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ServiceNow (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ServiceNow, potřebujete následující položky:

- Předplatné služby Azure AD
- Pro ServiceNow, instanci nebo klienta ServiceNow Calgary verze nebo vyšší
- Pro instance ServiceNow Express, Helsinkách verze ServiceNow Express nebo vyšší
- Musí mít klienta ServiceNow [více jeden znak na modul Plugin poskytovatele](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) povolena. To lze provést [odesílá se žádost o službu](https://hi.service-now.com).
- Pro automatickou konfiguraci povolení modulu plug-in více zprostředkovatele pro ServiceNow.

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ServiceNow z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-servicenow-from-the-gallery"></a>Přidání ServiceNow z Galerie
Při konfiguraci integrace ServiceNow do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS ServiceNow z galerie.

**Pokud chcete přidat ServiceNow z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **ServiceNow**, vyberte **ServiceNow** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![ServiceNow v seznamu výsledků](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s ServiceNow podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v ServiceNow je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v ServiceNow musí navázat.

V ServiceNow, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ServiceNow, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování pro ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Konfigurovat Azure AD jednotné přihlašování pro expresní ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow-express)**  – Pokud chcete povolit uživatelům tuto funkci používat.
3. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytvoření zkušebního uživatele ServiceNow](#create-a-servicenow-test-user)**  – Pokud chcete mít protějšek Britta Simon v ServiceNow propojeném s Azure AD reprezentace daného uživatele.
5. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
6. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Konfigurace Azure AD jednotné přihlášení pro ServiceNow

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci ServiceNow.

**Ke konfiguraci Azure AD jednotné přihlašování s ServiceNow, proveďte následující kroky:**

1. Na portálu Azure na **ServiceNow** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. Na **ServiceNow domény a adresy URL** část, proveďte následující kroky:

    ![ServiceNow domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<instance-name>.service-now.com/navpage.do`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<instance-name>.service-now.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Budete muset aktualizovat tyto hodnoty z skutečné přihlašovací adresa URL a identifikátor, který je vysvětlen později v tomto kurzu.

4. Na **SAML podpisový certifikát** část, proveďte následující kroky: 

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificate.png)

    a. Klikněte na tlačítko Kopírovat kopírování **adresu Url aplikace federační Metadata** a vložte do poznámkového bloku, jak tuto adresu Url aplikace federační Metadata se použije později v tomto kurzu.

    b. Klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. Přihlásit se k vaší aplikaci ServiceNow jako správce.

7. Aktivovat **integrace - více jeden přihlašování instalační program zprostředkovatele** modulu plug-in podle následující další kroky:

    a. V navigačním podokně na levé straně, hledání **definice systému** z panelu Hledat a pak klikněte na **modulů plug-in**.

    ![Aktivovat modul plug-in](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "aktivovat modulu plug-in")

     b. Vyhledejte **integrace - více jeden přihlašování instalační program zprostředkovatele**.

     ![Aktivovat modul plug-in](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "aktivovat modulu plug-in")

    c. Vyberte modul plug-in. Klikněte pravým tlačítkem a vyberte **aktivovat nebo upgradovat**.

    d. Klikněte **aktivovat** tlačítko.

8. Existují dva způsoby, ve kterém **ServiceNow** může být nakonfigurovaná automaticky a ručně.

9. Pro konfiguraci **ServiceNow** automaticky podle níže uvedených pokynů

    a. Vraťte se na **ServiceNow** Signle přihlášení na stránce na portálu Azure.

    b. Jedním kliknutím konfigurace služby je zadaný pro ServiceNow tedy mít Azure AD automaticky nakonfigurovat ServiceNow pro ověřování pomocí SAML. Chcete-li povolit tuto službu, přejděte na **ServiceNow konfigurace** klikněte na tlačítko **konfigurace ServiceNow** otevřete konfigurovat přihlašování okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

    c. Zadejte název instance ServiceNow, uživatelské jméno správce a heslo správce v **konfigurovat přihlášení** formuláři a klikněte na tlačítko **konfigurovat**. Všimněte si, že zadané uživatelské jméno správce musí mít **security_admin** role přiřazené v ServiceNow pro tento postup. Jinak, jak ručně nakonfigurovat ServiceNow používat Azure AD jako poskytovatele identity SAML, klikněte na tlačítko **ručně nakonfigurovat jednotné přihlašování** a zkopírujte **Sign-Out URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z části Stručná referenční příručka.

    ![Konfigurovat adresu URL aplikace](./media/active-directory-saas-servicenow-tutorial/configure.png "konfigurovat adresu URL aplikace")

    d. Přihlásit se k vaší aplikaci ServiceNow jako správce.

    e. V automatickou konfiguraci se konfigurují potřebná nastavení na **ServiceNow** straně ale **certifikát X.509** není ve výchozím nastavení povolené. Budete muset ručně mapování na zprostředkovatele identity v ServiceNow. postupujte podle níže uvedených pokynů pro stejný:
    
    * V navigačním podokně na levé straně klikněte na **zprostředkovatelů Identity** pod **jednotného přihlašování k více zprostředkovatele**.

      ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "nakonfigurovat jednotné přihlašování")

    * Klikněte na zprostředkovatele automaticky generované identity

      ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_08.png "nakonfigurovat jednotné přihlašování")

    * Přejděte dolů k položce **certifikát X.509** části. Vyberte **Upravit**.

      ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_09.png "nakonfigurovat jednotné přihlašování")
    
    * Vyberte na certifikátu a klikněte na šipku vpravo přidání certifikátu

      ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_11.png "nakonfigurovat jednotné přihlašování")

    * Klikněte na **Uložit**.

    * Klikněte na **aktivovat** v pravém horním rohu stránky.

10. Pro konfiguraci **ServiceNow** ručně postupujte níže uvedených pokynů

11. Přihlásit se k vaší aplikaci ServiceNow jako správce.

12. V navigačním podokně na levé straně, hledání **jednotného přihlašování k více zprostředkovatele** z panelu Hledat a pak klikněte na **vlastnosti**.

    ![Konfigurovat adresu URL aplikace](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "konfigurovat adresu URL aplikace")

13. Na **více jednotného přihlašování k vlastnosti zprostředkovatele** dialogové okno, proveďte následující kroky:

    ![Konfigurovat adresu URL aplikace](./media/active-directory-saas-servicenow-tutorial/ic7694981.png "konfigurovat adresu URL aplikace")

    a. Jako **povolit zprostředkovatel vícenásobného jednotného přihlašování k**, vyberte **Ano**.

    b. Jako **povolit automatické import uživatelů ze všech zprostředkovatelů identity do tabulky uživatele**, vyberte **Ano**.

    c. Jako **ladění povolení protokolování pro více zprostředkovatele jednotného přihlašování k integraci**, vyberte **Ano**.

    d. V **pole na uživatele, který tabulky...**  textovému poli, typ **uživatelské_jméno**.

    e. Klikněte na **Uložit**.

14. V navigačním podokně na levé straně, hledání **jednotného přihlašování k více zprostředkovatele** z panelu Hledat a pak klikněte na **x509 certifikáty**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "nakonfigurovat jednotné přihlašování")

15. Na **certifikáty X.509** dialogové okno, klikněte na tlačítko **nový**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694974.png "nakonfigurovat jednotné přihlašování")

16. Na **certifikáty X.509** dialogové okno, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "nakonfigurovat jednotné přihlašování")

    a. V **název** textovému poli, zadejte název pro svou konfiguraci (například: **TestSAML2.0**).

    b. Vyberte **Active**.

    c. Jako **formátu**, vyberte **PEM**.

    d. Jako **typ**, vyberte **důvěřovat certifikátu úložiště**.

    e. Otevřete váš certifikát kódovaný v Base64 stahovány ze služby Azure v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte jej do **PEM certifikát** textové pole.

     f. Klikněte na tlačítko **odeslání**.

17. V navigačním podokně na levé straně klikněte na **zprostředkovatelů Identity**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "nakonfigurovat jednotné přihlašování")

18. Na **zprostředkovatelů Identity** dialogové okno, klikněte na tlačítko **nový**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694977.png "nakonfigurovat jednotné přihlašování")

19. Na **zprostředkovatelů Identity** dialogové okno, klikněte na tlačítko **aktualizaci1 typu SAML2?**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694978.png "nakonfigurovat jednotné přihlašování")

20. V dialogovém okně Vlastnosti typu SAML2 aktualizaci1 proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/idp.png "nakonfigurovat jednotné přihlašování")

    a. Vyberte **URL** možnost **Import metadat zprostředkovatelů Identity** dialogové okno.

    b. Zadejte **adresu Url aplikace federační Metadata** který jste zkopírovali z portálu Azure.

    c. Klikněte na **Importovat**.

21. Přečte adresu URL metadat IdP a naplní všechny informace o pole.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694982.png "nakonfigurovat jednotné přihlašování")

    a. V **název** textovému poli, zadejte název pro svou konfiguraci (například **SAML 2.0**).
    
    b. Kopírování **domovské stránky ServiceNow** hodnotu, vložte jej do **přihlašovací adresa URL** textového pole v **ServiceNow domény a adresy URL** části na portálu Azure.

    > [!NOTE]
    > Na domovské stránce instance ServiceNow je tvořen vaše **URL klienta ServieNow** a **/navpage.do** (například:`https://fabrikam.service-now.com/navpage.do`).

    c. Kopírování **Entity ID / vystavitele** hodnotu, vložte jej do **identifikátor** textového pole v **ServiceNow domény a adresy URL** části na portálu Azure.

    d. Klikněte na tlačítko **rozšířené**. V **pole uživatelského** textovému poli, typ **e-mailu** nebo **uživatelské_jméno**, v závislosti na pole, které slouží k jednoznačné identifikaci uživatele ve vašem nasazení ServiceNow.

    > [!NOTE]
    > Můžete konfigurovat Azure AD pro vydávání ID uživatele Azure AD (hlavní název uživatele) nebo e-mailovou adresu jako jedinečný identifikátor v tokenu SAML přechodem na **ServiceNow > atributy > jednotné přihlašování** části portálu Azure a mapování pro požadované pole **nameidentifier** atribut. Hodnota uložená vybraného atributu ve službě Azure AD (například hlavní název uživatele) se musí shodovat s hodnotou uloženou v ServiceNow pro zadané pole (například uživatelské_jméno)

     e. V části **x509 certifikát**, obsahuje seznam certifikátů, které jste vytvořili v předchozím kroku.

     > [!NOTE]
     > ServiceNow neumožňuje aktivace rozšíření Idp bez kliknutím na tlačítko Testovat připojení, k přepsání stejné, postupujte níže uvedených pokynů.

22. Klikněte na ikonu nabídky z nového poskytovatele identity, kterou jste vytvořili jako součást konfigurace a ze seznamu vyberte **zkopírujte sys_id**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694992.png "nakonfigurovat jednotné přihlašování")

23. V levém horním vyhledávacího pole Hledat **sys_properties.list** a stiskněte klávesu enter.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694993.png "nakonfigurovat jednotné přihlašování")

24. Klikněte na možnost **Nové**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694994.png "nakonfigurovat jednotné přihlašování")

25. V **vlastnost systému** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694995.png "nakonfigurovat jednotné přihlašování")

    a. Zadejte `glide.authenticate.sso.redirect.idp` hodnotu do textového pole název.

    b. V **hodnotu** textovému poli, vložte sys_id kopírování hodnoty, které jste zkopírovali v předchozím postupu.

    c. Vyberte **privátní**.

    d. Klikněte na tlačítko **odeslání**.

26. Klikněte na možnost **Nové**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694994.png "nakonfigurovat jednotné přihlašování")

27. V **vlastnost systému** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694996.png "nakonfigurovat jednotné přihlašování")

    a. Zadejte `glide.authenticate.multisso.test.connection.mandatory` hodnotu do textového pole název.

    b. V **hodnotu** textovému poli, zadejte **false**.

    c. Klikněte na tlačítko **odeslání**.

28. Po to výše krok, teď bude moci uživatel aktivovat nové zprostředkovatele identity a vaše jednotné přihlašování, by měly fungovat

> [!NOTE]
> Také Upozorňujeme, že máte testování konfigurace nové Idp v novém okně incognito

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Konfigurace Azure AD jednotné přihlášení pro ServiceNow Express

1. Na portálu Azure na **ServiceNow** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. Na **ServiceNow domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte hodnotu pomocí následujícího vzorce: `https://<instance-name>.service-now.com/navpage.do`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory ServiceNow klienta](https://www.servicenow.com/support/contact-support.html) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificates.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. Jedním kliknutím konfigurace služby je zadaný pro ServiceNow tedy mít Azure AD automaticky nakonfigurovat ServiceNow pro ověřování pomocí SAML. Chcete-li povolit tuto službu, přejděte na **ServiceNow konfigurace** klikněte na tlačítko **konfigurace ServiceNow** otevřete konfigurovat přihlašování okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

7. Zadejte název instance ServiceNow, uživatelské jméno správce a heslo správce v **konfigurovat přihlášení** formuláři a klikněte na tlačítko **konfigurovat**. Všimněte si, že zadané uživatelské jméno správce musí mít **security_admin** role přiřazené v ServiceNow pro tento postup. Jinak, jak ručně nakonfigurovat ServiceNow používat Azure AD jako poskytovatele identity SAML, klikněte na tlačítko **ručně nakonfigurovat jednotné přihlašování** a zkopírujte **Sign-Out URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z části Stručná referenční příručka.

    ![Konfigurovat adresu URL aplikace](./media/active-directory-saas-servicenow-tutorial/configure.png "konfigurovat adresu URL aplikace")

8. Přihlásit se do aplikace ServiceNow Express jako správce.

9. V navigačním podokně na levé straně klikněte na **jednotné přihlašování**.

    ![Konfigurovat adresu URL aplikace](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "konfigurovat adresu URL aplikace")

10. Na **jednotné přihlašování** dialogové okno, klikněte na ikonu konfigurace v pravém horním rohu a nastavte následující vlastnosti:

    ![Konfigurovat adresu URL aplikace](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "konfigurovat adresu URL aplikace")

    a. Přepnutí **povolit zprostředkovatel vícenásobného jednotného přihlašování k** vpravo.
    
    b. Přepnutí **ladění povolení protokolování pro více zprostředkovatele jednotného přihlašování k integraci** vpravo.
    
    c. V **pole na uživatele, který tabulky...**  textovému poli, typ **uživatelské_jméno**.

11. Na **jednotné přihlašování** dialogové okno, klikněte na tlačítko **přidat nový certifikát**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "nakonfigurovat jednotné přihlašování")

12. Na **certifikáty X.509** dialogové okno, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "nakonfigurovat jednotné přihlašování")

    a. V **název** textovému poli, zadejte název pro svou konfiguraci (například: **TestSAML2.0**).

    b. Vyberte **Active**.

    c. Jako **formátu**, vyberte **PEM**.

    d. Jako **typ**, vyberte **důvěřovat certifikátu úložiště**.

    e. Otevřete váš certifikát kódovaný v Base64 stáhli z portálu Azure v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte jej do **PEM certifikát** textové pole.

    f. Klikněte na tlačítko **aktualizace**

13. Na **jednotné přihlašování** dialogové okno, klikněte na tlačítko **přidat nové rozšíření IdP**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "nakonfigurovat jednotné přihlašování")

14. Na **přidat nového poskytovatele Identity** dialogové okno v části **konfigurace zprostředkovatele Identity**, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "nakonfigurovat jednotné přihlašování")

    a. V **název** textovému poli, zadejte název pro svou konfiguraci (například: **SAML 2.0**).

    b. V **adresa URL poskytovatele Identity** pole, vložte hodnotu **ID zprostředkovatele Identity**, který jste zkopírovali z portálu Azure.
    
    c. V **zprostředkovatele Identity AuthnRequest** pole, vložte hodnotu **adresa URL žádosti o ověření**, který jste zkopírovali z portálu Azure.

    d. V **zprostředkovatele Identity SingleLogoutRequest** pole, vložte hodnotu **jednu adresu URL služby Sign-Out**, který jste zkopírovali z portálu Azure

    e. Jako **certifikát zprostředkovatele Identity**, vyberte certifikát, který jste vytvořili v předchozím kroku.

15. Klikněte na tlačítko **Upřesnit nastavení**a v části **další vlastnosti zprostředkovatele Identity**, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "nakonfigurovat jednotné přihlašování")

    a. V **protokol vazby pro rozšíření IDP na SingleLogoutRequest** textovému poli, typ **urn: oasis: názvy: tc: SAML:2.0:bindings:HTTP-přesměrování**.

    b. V **NameID zásad** textovému poli, typ **urn: oasis: názvy: tc: SAML:1.1:nameid-formátu: neurčené**.

    c. V **AuthnContextClassRef metoda**, typ `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Zrušte výběr **vytvořit AuthnContextClass**.

16. V části **další vlastnosti zprostředkovatele služby**, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "nakonfigurovat jednotné přihlašování")

    a. V **domovské stránky ServiceNow** textovému poli, zadejte adresu URL domovské stránce instance ServiceNow.

    > [!NOTE]
    > Na domovské stránce instance ServiceNow je tvořen vaše **URL klienta ServieNow** a **/navpage.do** (například: `https://fabrikam.service-now.com/navpage.do`).

    b. V **Entity ID / vystavitele** textovému poli, zadejte adresu URL vašeho klienta ServiceNow.

    c. V **identifikátor URI cílové skupiny** textovému poli, zadejte adresu URL vašeho klienta ServiceNow.

    d. V **hodin zkreslit** textovému poli, typ **60**.

    e. V **pole uživatelského** textovému poli, typ **e-mailu** nebo **uživatelské_jméno**, v závislosti na pole, které slouží k jednoznačné identifikaci uživatele ve vašem nasazení ServiceNow.

    > [!NOTE]
    > Můžete konfigurovat Azure AD pro vydávání ID uživatele Azure AD (hlavní název uživatele) nebo e-mailovou adresu jako jedinečný identifikátor v tokenu SAML přechodem na **ServiceNow > atributy > jednotné přihlašování** části portálu Azure a mapování pro požadované pole **nameidentifier** atribut. Hodnota uložená vybraného atributu ve službě Azure AD (například hlavní název uživatele) se musí shodovat s hodnotou uloženou v ServiceNow pro zadané pole (například uživatelské_jméno)

    f. Klikněte na **Uložit**.
 
### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-servicenow-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-servicenow-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-servicenow-test-user"></a>Vytvoření zkušebního uživatele ServiceNow

V této části vytvoříte uživatele volal Britta Simon v ServiceNow. Pokud si nejste jisti, jak přidat uživatele ve vašem účtu ServiceNow a ServiceNow Express, obraťte se na [tým podpory ServiceNow klienta](https://www.servicenow.com/support/contact-support.html)

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu ServiceNow.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon ServiceNow, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **ServiceNow**.

    ![V seznamu aplikací na ServiceNow odkaz](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici ServiceNow na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci ServiceNow.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png

