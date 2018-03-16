---
title: "Microsoft Azure Active Directory jednu přihlašování modulu plug-in Správce Průvodce | Microsoft Docs"
description: "Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Microsoft Azure Active Directory jednotné přihlašování pro JIRA."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: af949d1db8af37a534a16364f9f0763479c436e4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-admin-guide"></a>Microsoft Azure Active Directory jednu přihlašování modulu plug-in Správce Průvodce

## <a name="table-of-contents"></a>Obsah

1. **[PŘEHLED](#overview)**
2. **[JAK TO FUNGUJE](#how-it-works)**
3. **[CÍLOVÁ SKUPINA](#audience)**
4. **[PŘEDPOKLADY](#assumptions)**
5. **[POŽADAVKY](#prerequisites)**
6. **[PODPOROVANÉ VERZE JIRA A SOUTOKU](#supported-versions-of-jira-and-confluence)**
7. **[INSTALACE](#installation)**
8. **[KONFIGURACE MODULU PLUG-IN](#plugin-configuration)**
9. **[VYSVĚTLENÍ POLE PRO ROZŠÍŘENÍ KONFIGURAČNÍ OBRAZOVCE:](#field-explanation-for-add---on-configuration-screen:)**
10. **[ŘEŠENÍ POTÍŽÍ](#troubleshooting)**

## <a name="overview"></a>Přehled

Tyto doplňky povolte Microsoft Azure AD zákazníkům používat jejich organizace uživatelské jméno a heslo pro přihlášení do Atlassian Jira a na serveru soutoku produkty. Implementuje SAML 2.0 na základě jednotné přihlašování.

## <a name="how-it-works"></a>Jak to funguje

Pokud chtějí uživatelé přihlášení do aplikace Atlassian Jira nebo soutoku, zobrazí se jim **přihlášení s Azure AD** tlačítko na přihlašovací stránku. Po kliknutí na něm, jsou vyžadovány při přihlašování pomocí Azure AD organizace přihlašovací stránku.

Jakmile jsou uživatelé ověřeni, že byste měli mít přihlášení do aplikace. Pokud je již ověřen pomocí ID organizace a hesla, pak se přímo přihlásit do aplikace. Mějte také na paměti, že přihlášení funguje napříč JIRA a soutoku. Pokud uživatele se protokolují do JIRA aplikace a soutoku je také otevřete v okně prohlížeče, budou muset přihlásit jednou a není nutné znovu zadat přihlašovací údaje pro ostatní aplikace. Uživatele můžete získat také na produkt Atlassian prostřednictvím myapps v rámci účtu Azure a že mají být protokolovány bez se zobrazí dotaz, pro přihlašovací údaje.

> [!NOTE]
> Zřizování uživatelů se provádí pomocí tohoto doplňku.

## <a name="audience"></a>Cílová skupina

JIRA a soutoku správci, kteří jsou v úmyslu používat tento modul plug-in umožňující jednotného přihlašování pomocí služby Azure AD.

## <a name="assumptions"></a>Předpoklady

* Instance JIRA/soutoku je povolen protokol HTTPS
* Uživatelé jsou už vytvořené v JIRA/soutoku
* Uživatelé mají přiřazenou v JIRA/soutoku roli
* Správci mají přístup k požadované informace o konfiguraci.
* JIRA/soutoku jsou k dispozici mimo do podnikové sítě
* Přidat na funguje pouze na místní verzi JIRA a soutoku

## <a name="prerequisites"></a>Požadavky

Poznámka: následující požadavky před pokračovat v instalaci doplňku:

* JIRA/soutoku jsou nainstalovány na verzi Windows 64-bit
* Povolit protokol HTTPS jsou JIRA nebo soutoku verze
* Poznámka: podporovaná verze modulu plug-in v části "Podporované verze".
* JIRA/soutoku je k dispozici na Internetu.
* Přihlašovací údaje správce pro JIRA/soutoku
* Přihlašovací údaje správce pro Azure AD
* WebSudo by mělo být zakázáno v JIRA a soutoku

## <a name="supported-versions-of-jira-and-confluence"></a>Podporované verze JIRA a soutoku

Od nyní jsou podporovány následující verze JIRA a soutoku:

* Základní JIRA a Software: 6.0 na 7.2.0
* JIRA technickou podporu: 3.2 k 3.0
* Soutoku: 5.0 pro 5.10

## <a name="installation"></a>Instalace

Správce postupujte podle kroků k instalaci modulu plug-in uvedená níže:

1. Přihlaste se k instanci JIRA/soutoku jako správce
    
2. Přejít na JIRA/soutoku Správa a klikněte na rozšíření.
    
3. Z Atlassian Marketplace vyhledejte **Microsoft zásuvný modul jednotné přihlašování SAML**
 
4. Příslušnou verzi rozšíření se zobrazí ve vyhledávání
 
5. Vyberte modul plug-in a nainstaluje UPM stejné.
 
6. Po instalaci modulu plug-in, zobrazí se v části doplňky uživatel nainstaloval spravovat rozšíření oddílu
 
7. Je nutné nakonfigurovat modul plug-in, než začnete používat ho.
 
8. Klikněte na tento modul plug-in a zobrazí tlačítko Konfigurovat.
 
9. Kliknutím zadejte vstupy konfigurace
    
## <a name="plugin-configuration"></a>Konfigurace modulu plug-in

Následující obrázek znázorňuje obrazovce konfigurace rozšíření JIRA a soutoku
    
![Konfigurace rozšíření](./media/ms-confluence-jira-plugin-adminguide/jira.png)

### <a name="field-explanation-for-add-on-configuration-screen"></a>Vysvětlení pole pro rozšíření konfigurační obrazovce:

1.   Adresu URL metadat: Adresa URL Chcete-li získat federačních metadat z Azure AD
 
2.   Identifikátor: Používá Azure AD ověřit zdroj žádosti. To se mapuje na element identifikátoru ve službě Azure AD. Tím se automaticky vypočítá modulu plug-in jako https://<domain:port>/
 
3.   Adresa URL odpovědi: Adresa URL použití odpovědi v vaší IdP zahájíte SAML přihlášení. To se mapuje na element adresa URL odpovědi ve službě Azure AD. Tím se automaticky vypočítá modulu plug-in jako https://<domain:port>/plugins/servlet/saml/auth
 
4.   Adresa URL přihlašování: Použijte přihlašovací na adresu URL v vaší IdP zahájíte SAML přihlášení. To se mapuje na element přihlašování ve službě Azure AD. Tím se automaticky vypočítá modulu plug-in jako https://<domain:port>/plugins/servlet/saml/auth
 
5.   ID IdP Entity: ID Entity používající vaše deklarací identity. To se zaplní při adresy URL metadat.
 
6.   Přihlašovací adresa URL: Přihlašovací adresa URL z vaší deklarací identity. To se naplní ze služby Azure AD, když je adresa URL metadat vyřešený.
 
7.   Adresa URL odhlašovací: adresa URL odhlašovací z vaší deklarací identity. To se naplní ze služby Azure AD, když je adresa URL metadat vyřešený.
 
8.   Certifikátu X.509: Certifikát X.509 vaše deklarací identity. To se naplní ze služby Azure AD, když je adresa URL metadat vyřešený.
 
9.   Název tlačítka přihlášení: Název tlačítka pro přihlášení, které vaše organizace chce vidět. Tento text se zobrazí uživatelům na tlačítko přihlašovací obrazovka pro přihlášení.
 
10.   SAML uživatele ID umístění: Kde id uživatele je očekávána v odpovědi SAML. Může být buď v NameID nebo název vlastního atributu. Toto ID musí být JIRA/soutoku id uživatele.
 
11.   Název atributu: název atributu, kde je možné očekávat Id uživatele.
 
12.   Zjišťování povolit domovské sféry: Zkontrolujte tento příznak, zda společnosti pomocí přihlášení na základě služby AD FS.
 
13.   Název domény: Zadejte název domény zde v případě přihlášení na základě služby AD FS
 
14.   Povolit jednotné přihlašování se: Zaškrtněte toto office, pokud chcete se odhlásit z Azure AD, když uživatel neodhlásí z JIRA/soutoku.

### <a name="troubleshooting"></a>Řešení potíží

* Pokud se zobrazuje několik chyb certifikáty
    
    * Přihlášení k Azure AD a odebrání více certifikátů, které jsou k dispozici pro aplikaci. Zkontrolujte, zda je přítomen pouze jeden certifikát.

* Certifikát je vyprší ve službě Azure AD.
    
    * Doplňky postará o automatické výměny certifikátu. Když certifikát je vyprší, nový certifikát by měl být označen jako aktivní a nepoužívané certifikátu měla by být odstraněna. Když se uživatel pokusí o přihlášení k JIRA v tomto scénáři, rozšíření načte nový certifikát a uložte v modulu plug-in.

* Zakázání WebSudo (zakázat relace zabezpečené správce)
    
    * JIRA: Zabezpečte správce, které jsou ve výchozím nastavení povolené relace (tedy potvrzení hesla před přístupem k funkcím pro správu). Pokud chcete zakázat toto v instanci JIRA, můžete tuto funkci zakázat zadáním následujícího řádku v souboru jira config.properties: "ira.websudo.is.disabled = true"
    
    * Soutoku: Postupujte podle kroků uvedených uvádí následující adresu URL https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html

* Získávání nezobrazí pole, které by mohly být obsazeny adresu URL metadat
    
    * Zkontrolujte, jestli je adresa URL správná. Zkontrolujte, pokud jsou namapovány na správný klienta a id aplikace.
    
    * Stiskněte tlačítko adresu URL z prohlížeče, abyste viděli, pokud jste obdrželi, federačních metadat XML.

* Vnitřní chyba serveru:
    
    * Projděte protokoly nachází v adresáři protokoly instalace. Pokud se zobrazuje chyba při pokusu uživatele o přihlášení pomocí jednotného přihlašování Azure AD, můžete sdílet protokoly s informace podpory, které jsou uvedeny níže v tomto dokumentu.

* ID uživatele nebyla nalezena chyba, když se uživatel pokusí o přihlášení
    
    * Uživatel není vytvořená ve JIRA/soutoku, takže vytvořte stejné.

* Aplikace nebyla nalezena chyba ve službě Azure AD
    
    * V tématu, pokud příslušnou adresou URL je namapovaný na aplikaci ve službě Azure AD.

* Podrobnosti o podpoře: oslovení nám na: [týmu Integrace Azure AD jednotného přihlašování k](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Jsme odpověď ve 24 48 pracovní dobu.
    
    * Můžete taky zvýšit lístek podpory se společností Microsoft prostřednictvím portálu Azure kanálu.