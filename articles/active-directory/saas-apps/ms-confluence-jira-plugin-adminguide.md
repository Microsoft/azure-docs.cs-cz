---
title: Příručka pro správce Atlassian JIRA/Confluence – Azure Active Directory | Microsoft Docs
description: Průvodce pro správu, který používá Atlassian JIRA a Confluence s Azure Active Directory (Azure AD)..
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 4b0d2c6c3d70e73be25038c763f05ef572b56ebe
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515675"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Atlassian JIRA a příručka pro správce Confluence pro Azure Active Directory

## <a name="overview"></a>Přehled

Modul plug-in Azure Active Directory (Azure AD) jednotného přihlašování (SSO) umožňuje Microsoft Azure AD zákazníkům používat pracovní nebo školní účet pro přihlašování k produktům Atlassian JIRA a Confluence serverem. Implementuje jednotné přihlašování založené na SAML 2,0.

## <a name="how-it-works"></a>Jak to funguje

Když se uživatelé chtějí přihlašovat k aplikaci Atlassian JIRA nebo Confluence, uvidí na přihlašovací stránce tlačítko **Přihlásit se pomocí Azure AD** . Když je vyberou, musí se přihlásit pomocí přihlašovací stránky organizace Azure AD (tj. pracovní nebo školní účet).

Po ověření uživatelů by se měli být schopni přihlásit k aplikaci. Pokud jsou už ověřené pomocí ID a hesla pro svůj pracovní nebo školní účet, přihlaste se přímo k aplikaci. 

Přihlašování funguje napříč JIRA a Confluence. Pokud se uživatelé přihlásí do aplikace JIRA a ve stejném okně prohlížeče se otevře Confluence, nemusí zadávat přihlašovací údaje pro jinou aplikaci. 

Uživatelé se také můžou k Atlassian produktu dostat prostřednictvím svých aplikací v pracovním nebo školním účtu. Měly by být přihlášeni bez výzvy k zadání přihlašovacích údajů.

> [!NOTE]
> Zřizování uživatelů se neprovádí pomocí modulu plug-in.

## <a name="audience"></a>Cílová skupina

Správci JIRA a Confluence můžou pomocí tohoto modulu plug-in povolit jednotné přihlašování pomocí služby Azure AD.

## <a name="assumptions"></a>Předpoklady

* Instance JIRA a Confluence mají povolený protokol HTTPS.
* Uživatelé jsou již vytvořeni v JIRA nebo Confluence.
* Uživatelé mají přiřazené role v JIRA nebo Confluence.
* Správci mají přístup k informacím vyžadovaným ke konfiguraci modulu plug-in.
* JIRA nebo Confluence je k dispozici i mimo podnikovou síť.
* Modul plug-in funguje jenom s místní verzí JIRA a Confluence.

## <a name="prerequisites"></a>Předpoklady

Před instalací modulu plug-in si všimněte následujících informací:

* JIRA a Confluence jsou nainstalovány v 64 verze systému Windows.
* Verze JIRA a Confluence jsou povoleny pomocí protokolu HTTPS.
* JIRA a Confluence jsou k dispozici na internetu.
* Přihlašovací údaje správce jsou pro JIRA a Confluence.
* Pro Azure AD jsou k dismístě přihlašovací údaje správce.
* WebSudo je v JIRA a Confluence zakázaný.

## <a name="supported-versions-of-jira-and-confluence"></a>Podporované verze systémů JIRA a Confluence

Modul plug-in podporuje následující verze JIRA a Confluence:

* JIRA Core a software: 6,0 až 7,12
* Oddělení služeb JIRA: 3.0.0 na 3.5.0
* JIRA také podporuje 5,2. Další podrobnosti získáte, když kliknete na [Microsoft Azure Active Directory jednotné přihlašování pro JIRA 5,2](./jira52microsoft-tutorial.md) .
* Confluence: 5,0 až 5,10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

## <a name="installation"></a>Instalace

Chcete-li nainstalovat modul plug-in, postupujte podle následujících kroků:

1. Přihlaste se ke své instanci JIRA nebo Confluence jako správce.

2. Otevřete konzolu pro správu JIRA/Confluence a vyberte možnost **Doplňky**.

3. Na webu Microsoft Download Center si Stáhněte modul plug [-in Microsoft SAML SSO pro JIRA](https://www.microsoft.com/download/details.aspx?id=56506) /  [Microsoft SAML SSO pro Confluence](https://www.microsoft.com/download/details.aspx?id=56503).

   Ve výsledcích hledání se zobrazí příslušná verze modulu plug-in.

4. Vyberte modul plug-in a správce univerzálního modulu plug-in (UPM) ho nainstaluje.

Po instalaci modulu plug-in se tato položka zobrazí v části **doplňky nainstalované uživatelem** tématu **Správa doplňků**.

## <a name="plug-in-configuration"></a>Konfigurace modulu plug-in

Než začnete používat modul plug-in, musíte ho nakonfigurovat. Vyberte modul plug-in, klikněte na tlačítko **Konfigurovat** a zadejte podrobnosti o konfiguraci.

Následující obrázek znázorňuje konfigurační obrazovku v JIRA i Confluence:

![Obrazovka konfigurace modulu plug-in](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **Adresa URL metadat**: adresa URL pro získání federačních metadat z Azure AD.

* **Identifikátory**: adresa URL, kterou služba Azure AD používá k ověření zdroje žádosti. Mapuje se na element **identifikátoru** ve službě Azure AD. Modul plug-in automaticky odvozuje tuto adresu URL jako https:// *\<domain:port>* /.

* **Adresa URL odpovědi**: adresa URL odpovědi ve Vašem zprostředkovateli identity (IDP), která iniciuje přihlášení SAML. Mapuje se na element **adresy URL odpovědi** ve službě Azure AD. Modul plug-in automaticky odvozuje tuto adresu URL jako https:// *\<domain:port>* /plugins/servlet/SAML/auth..

* **Adresa URL pro přihlášení**: přihlašovací adresa URL v IDP, která iniciuje přihlášení SAML. Mapuje se na element **Signing** v Azure AD. Modul plug-in automaticky odvozuje tuto adresu URL jako https:// *\<domain:port>* /plugins/servlet/SAML/auth..

* **ID entity IDP**: ID entity, kterou používá vaše IDP. Toto pole se vyplní, když se vyřeší adresa URL metadat.

* **Přihlašovací adresa URL**: přihlašovací adresa URL z vaší IDP. Toto pole se vyplní z Azure AD, když se vyřeší adresa URL metadat.

* **Odhlašovací adresa**URL: odhlašovací URL z vaší IDP. Toto pole se vyplní z Azure AD, když se vyřeší adresa URL metadat.

* **Certifikát x. 509**: váš IDP certifikát x. 509. Toto pole se vyplní z Azure AD, když se vyřeší adresa URL metadat.

* **Název tlačítka pro přihlášení**: název přihlašovacího tlačítka, které vaše organizace chce, aby na přihlašovací stránce viděli uživatele.

* **Umístění ID uživatele SAML**: umístění, kde se v odpovědi SAML očekává ID uživatele JIRA nebo Confluence. Může to být v **NameId** nebo ve vlastním názvu atributu.

* **Název atributu**: název atributu, u kterého se očekává ID uživatele.

* **Povolit zjišťování domovské sféry**: výběr, který se má použít k tomu, aby společnost používala přihlašování založené na Active Directory Federation Services (AD FS) (AD FS).

* **Název domény**: název domény, pokud se AD FS na základě přihlášení.

* **Povolit jednotné přihlašování**: výběr, který se má udělat, pokud se chcete odhlásit ze služby Azure AD, když se uživatel odhlásí z JIRA nebo Confluence.

## <a name="troubleshooting"></a>Řešení potíží

* Máte k dispozici **více chyb certifikátu**: Přihlaste se do služby Azure AD a odeberte více certifikátů, které jsou pro aplikaci k dispozici. Zajistěte, aby byl přítomen pouze jeden certifikát.

* **Platnost certifikátu brzy vyprší ve službě Azure AD**: Doplňky se postará o automatický přechod na certifikát. Pokud brzy vyprší platnost certifikátu, měl by být nový certifikát označený jako aktivní a nepoužívané certifikáty by se měly odstranit. Když se uživatel pokusí přihlásit k JIRA v tomto scénáři, modul plug-in načte a uloží nový certifikát.

* **Chcete zakázat WebSudo (zakažte relaci zabezpečeného správce)**:

  * Pro JIRA jsou ve výchozím nastavení povolené relace zabezpečeného správce (tj. potvrzení hesla před přístupem k funkcím pro správu). Pokud chcete tuto schopnost v instanci JIRA odebrat, zadejte v souboru JIRA-config. Properties následující řádek: `jira.websudo.is.disabled = true`

  * V případě Confluence postupujte podle pokynů na [webu podpory Confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Pole, která by měla být naplněna pomocí adresy URL metadat, nejsou naplněna**:

  * Zkontroluje, jestli je adresa URL správná. Ověřte, jestli jste namapovali správné ID tenanta a aplikace.

  * Zadejte adresu URL v prohlížeči a podívejte se, jestli se vám zobrazuje kód XML federačních metadat.

* **Došlo k vnitřní chybě serveru**: Projděte si protokoly v adresáři protokolu instalace. Pokud se zobrazí chyba, když se uživatel pokusí přihlásit pomocí jednotného přihlašování služby Azure AD, můžete protokoly sdílet s týmem podpory.

* **Při pokusu uživatele o přihlášení došlo k chybě "ID uživatele nebylo nalezeno"**: vytvořte ID uživatele v JIRA nebo Confluence.

* **V Azure AD se vyskytla chyba aplikace nenalezena**: Podívejte se, jestli je příslušná adresa URL namapovaná na aplikaci ve službě Azure AD.

* **Potřebujete podporu**: nahlaste se k [integrování týmu jednotného přihlašování v Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Tým odpoví v 24-48 pracovní době.

  Lístek podpory můžete také vyvolat se společností Microsoft prostřednictvím kanálu Azure Portal.

## <a name="plug-in-faq"></a>Nejčastější dotazy k modulu plug-in

Pokud máte dotazy týkající se tohoto modulu plug-in, přečtěte si následující Nejčastější dotazy.

### <a name="what-does-the-plug-in-do"></a>Co modul plug-in dělá?

Modul plug-in poskytuje funkci jednotného přihlašování (SSO) pro Atlassian JIRA (včetně JIRA Core, JIRA softwaru, JIRA oddělení služeb) a Confluence místního softwaru. Modul plug-in funguje s Azure Active Directory (Azure AD) jako poskytovatelem identity (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>U kterých produktů Atlassian funguje modul plug-in?

Modul plug-in funguje s místními verzemi JIRA a Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Funguje modul plug-in ve verzích cloudu?

Ne. Modul plug-in podporuje jenom místní verze JIRA a Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Které verze JIRA a Confluence podporuje modul plug-in?

Modul plug-in podporuje tyto verze:

* JIRA Core a software: 6,0 až 7,12
* Oddělení služeb JIRA: 3.0.0 na 3.5.0
* JIRA také podporuje 5,2. Další podrobnosti získáte, když kliknete na [Microsoft Azure Active Directory jednotné přihlašování pro JIRA 5,2](./jira52microsoft-tutorial.md) .
* Confluence: 5,0 až 5,10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>Je modul plug-in bezplatný nebo placený?

Je to bezplatný doplněk.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Musím po nasazení modulu plug-in restartovat JIRA nebo Confluence?

Restartování se nevyžaduje. Můžete začít používat modul plug-in hned.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Návody získat podporu pro modul plug-in?

Můžete se obrátit na [integrační tým jednotného přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) , kde najdete veškerou podporu pro tento modul plug-in. Tým odpoví v 24-48 pracovní době.

Lístek podpory můžete také vyvolat se společností Microsoft prostřednictvím kanálu Azure Portal.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Funguje modul plug-in na instalaci Mac nebo Ubuntu pro JIRA a Confluence?

Otestovali jsme modul plug-in pouze na 64 instalacích systému Windows Server JIRA a Confluence.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Funguje modul plug-in s jiným zprostředkovatelů identity než Azure AD?

Ne. Funguje pouze se službou Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Jakou verzi SAML funguje modul plug-in?

Funguje s SAML 2,0.

### <a name="does-the-plug-in-do-user-provisioning"></a>Provádí modul plug-in zřizování uživatelů?

Ne. Modul plug-in poskytuje pouze jednotné přihlašování založené na SAML 2,0. Uživatel musí být v aplikaci zřízen před přihlášením jednotného přihlašování.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Podporuje modul plug-in Clusterové verze JIRA a Confluence?

Ne. Modul plug-in funguje s místními verzemi JIRA a Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Funguje modul plug-in s verzemi HTTP JIRA a Confluence?

Ne. Modul plug-in funguje jenom s instalací podporujícími protokol HTTPS.