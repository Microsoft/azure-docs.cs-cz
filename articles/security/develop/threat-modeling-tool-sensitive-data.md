---
title: Citlivá data – nástroj Microsoft Threat Modeling Tool – Azure | Dokumenty společnosti Microsoft
description: zmírnění hrozeb vystavených v nástroji pro modelování hrozeb
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: ab22e9843ca133274361838eeb49abbe326588dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502228"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Bezpečnostní rámec: Citlivé údaje | Skutečnosti snižující závažnost rizika 
| Produkt/služba | Článek |
| --------------- | ------- |
| **Hranice důvěryhodnosti počítače** | <ul><li>[Ujistěte se, že binární soubory jsou zamlžené, pokud obsahují citlivé informace](#binaries-info)</li><li>[Zvažte použití šifrovaného systému souborů (EFS) se používá k ochraně důvěrných dat specifických pro uživatele](#efs-user)</li><li>[Ujistěte se, že citlivá data uložená aplikací v systému souborů jsou šifrována](#filesystem)</li></ul> | 
| **Webová aplikace** | <ul><li>[Ujistěte se, že citlivý obsah není v prohlížeči ukládán do mezipaměti](#cache-browser)</li><li>[Šifrování částí konfiguračních souborů Webové aplikace, které obsahují citlivá data](#encrypt-data)</li><li>[Explicitně zakázat atribut HTML automatického dokončování v citlivých formách a vstupech](#autocomplete-input)</li><li>[Ujistěte se, že citlivá data zobrazená na obrazovce uživatele jsou maskována](#data-mask)</li></ul> | 
| **Databáze** | <ul><li>[Implementace dynamického maskování dat pro omezení vystavení citlivých dat neprivilegovaným uživatelům](#dynamic-users)</li><li>[Ujistěte se, že hesla jsou uložena ve slaném formátu hash](#salted-hash)</li><li>[Zajištění šifrování citlivých dat ve sloupcích databáze](#db-encrypted)</li><li>[Ujistěte se, že je povoleno šifrování na úrovni databáze (TDE)](#tde-enabled)</li><li>[Ujistěte se, že jsou zálohy databáze šifrovány](#backup)</li></ul> | 
| **Web API** | <ul><li>[Ujistěte se, že citlivá data relevantní pro webové rozhraní API nejsou uložena v úložišti prohlížeče](#api-browser)</li></ul> | 
| Databáze dokumentů Azure | <ul><li>[Šifrování citlivých dat uložených v Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Hranice důvěryhodnosti virtuálního počítače Azure IaaS** | <ul><li>[Šifrování disků Azure k šifrování disků používaných virtuálními počítači](#disk-vm)</li></ul> | 
| **Hranice vztahu důvěryhodnosti služby Fabric** | <ul><li>[Šifrování tajných kódů v aplikacích Service Fabric](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Provádění modelování zabezpečení a v případě potřeby používejte organizační jednotky/týmy](#modeling-teams)</li><li>[Minimalizace přístupu ke funkci sdílení na kritických entitách](#entities)</li><li>[Školení uživatelů o rizicích spojených s funkcí Dynamics CRM Share a osvědčenými postupy zabezpečení](#good-practices)</li><li>[Zahrnout pravidlo pro nastavení standardů vývoje, které zobrazuje podrobnosti konfigurace ve správě výjimek](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Použití šifrování služby Azure Storage Service (SSE) pro data v klidovém stavu (preview)](#sse-preview)</li><li>[Ukládání citlivých dat ve službě Azure Storage pomocí šifrování na straně klienta](#client-storage)</li></ul> | 
| **Mobilní klient** | <ul><li>[Šifrování citlivých nebo osobních údajů zapsaná do místního úložiště telefonů](#pii-phones)</li><li>[Obfuscate generované binární soubory před distribucí koncovým uživatelům](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Nastavení typu clientCredentialType na certifikát nebo systém Windows](#cert)</li><li>[Režim wCF-security není povolen.](#security)</li></ul> | 

## <a name="ensure-that-binaries-are-obfuscated-if-they-contain-sensitive-information"></a><a id="binaries-info"></a>Ujistěte se, že binární soubory jsou zamlžené, pokud obsahují citlivé informace

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Ujistěte se, že binární soubory jsou zamlžené, pokud obsahují citlivé informace, jako jsou obchodní tajemství, citlivá obchodní logika, která by neměla být obrácena. Toto je zastavení zpětného inženýrství sestav. K `CryptoObfuscator` tomuto účelu mohou být použity nástroje, jako je tento účel. |

## <a name="consider-using-encrypted-file-system-efs-is-used-to-protect-confidential-user-specific-data"></a><a id="efs-user"></a>Zvažte použití šifrovaného systému souborů (EFS) se používá k ochraně důvěrných dat specifických pro uživatele

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Zvažte použití šifrovaného systému souborů (EFS) se používá k ochraně důvěrných dat specifických pro uživatele před protivníky s fyzickým přístupem k počítači. |

## <a name="ensure-that-sensitive-data-stored-by-the-application-on-the-file-system-is-encrypted"></a><a id="filesystem"></a>Ujistěte se, že citlivá data uložená aplikací v systému souborů jsou šifrována

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Ujistěte se, že citlivá data uložená aplikací v systému souborů jsou šifrována (např. pomocí DPAPI), pokud systém souborů EFS nelze vynutit |

## <a name="ensure-that-sensitive-content-is-not-cached-on-the-browser"></a><a id="cache-browser"></a>Ujistěte se, že citlivý obsah není v prohlížeči ukládán do mezipaměti

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, webové formuláře, MVC5, MVC6 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Prohlížeče mohou ukládat informace pro účely ukládání do mezipaměti a historie. Tyto soubory uložené v mezipaměti jsou uloženy ve složce, jako je složka Dočasné soubory Internetu v případě aplikace Internet Explorer. Když jsou tyto stránky znovu odkazovány, prohlížeč je zobrazí ze své mezipaměti. Pokud se uživateli zobrazí citlivé informace (například jeho adresa, údaje o kreditní kartě, číslo sociálního zabezpečení nebo uživatelské jméno), mohou být tyto informace uloženy v mezipaměti prohlížeče, a proto je lze získat prostřednictvím kontroly mezipaměti prohlížeče nebo pouhým stisknutím tlačítka prohlížeče "Zpět". Nastavte hodnotu hlavičky odpovědi řízení mezipaměti na hodnotu "no-store" pro všechny stránky. |

### <a name="example"></a>Příklad
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-cache" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>Příklad
To může být implementováno prostřednictvím filtru. Lze použít následující příklad: 
```csharp
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a name="encrypt-sections-of-web-apps-configuration-files-that-contain-sensitive-data"></a><a id="encrypt-data"></a>Šifrování částí konfiguračních souborů Webové aplikace, které obsahují citlivá data

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Postup: Šifrování oddílů konfigurace v ASP.NET 2.0 pomocí dpapi](https://msdn.microsoft.com/library/ff647398.aspx), [určení zprostředkovatele chráněné konfigurace](https://msdn.microsoft.com/library/68ze1hb2.aspx), použití [trezoru klíčů Azure k ochraně tajných kódů aplikací](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Kroky** | Konfigurační soubory, jako je například web.config, appsettings.json se často používají k uložení citlivých informací, včetně uživatelských jmen, hesel, připojovacích řetězců databáze a šifrovacích klíčů. Pokud tyto informace nezaštítíte, je aplikace ohrožena útočníky nebo uživateli se zlými úmysly, kteří zpřístupňují citlivé informace, jako jsou uživatelská jména a hesla účtu, názvy databází a názvy serverů. Na základě typu nasazení (azure/on-prem) zašifrujte citlivé části konfiguračních souborů pomocí DPAPI nebo služeb, jako je Azure Key Vault. |

## <a name="explicitly-disable-the-autocomplete-html-attribute-in-sensitive-forms-and-inputs"></a><a id="autocomplete-input"></a>Explicitně zakázat atribut HTML automatického dokončování v citlivých formách a vstupech

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [MSDN: atribut automatického dokončování](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [Použití automatického dokončování v HTML](https://msdn.microsoft.com/library/ms533032.aspx), Chyba zabezpečení [sanitace HTML](https://technet.microsoft.com/security/bulletin/MS10-071), Automatické [dokončování.,znovu?!](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Kroky** | Atribut automatického dokončování určuje, zda má být formulář automaticky dokončován zapnutý nebo vypnutý. Je-li automatické dokončování zapnuto, prohlížeč automaticky dokončí hodnoty na základě hodnot, které uživatel zadal dříve. Pokud je například do formuláře zadáno nové jméno a heslo a formulář je odeslán, prohlížeč se zeptá, zda má být heslo uloženo. Poté, co je formulář zobrazen, jméno a heslo jsou vyplněny automaticky nebo jsou vyplněny jako jméno. Útočník s místním přístupem by mohl získat heslo prostého textu z mezipaměti prohlížeče. Ve výchozím nastavení je automatické dokončování povoleno a musí být explicitně zakázáno. |

### <a name="example"></a>Příklad
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a name="ensure-that-sensitive-data-displayed-on-the-user-screen-is-masked"></a><a id="data-mask"></a>Ujistěte se, že citlivá data zobrazená na obrazovce uživatele jsou maskována

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Při zobrazení na obrazovce by měly být maskovány citlivé údaje, jako jsou hesla, čísla kreditních karet, SSN atd. Tím se zabrání neoprávněným osobám v přístupu k datům (např. hesla pro procházení ramen, pracovníci podpory, kteří si prohlíží počet uživatelů SSN). Ujistěte se, že tyto datové prvky nejsou viditelné ve formátu prostého textu a jsou vhodně maskované. To je třeba dbát při jejich přijímání jako vstup (např., type="password") a také zobrazení zpět na obrazovce (např. zobrazení pouze posledních 4 číslic čísla kreditní karty). |

## <a name="implement-dynamic-data-masking-to-limit-sensitive-data-exposure-non-privileged-users"></a><a id="dynamic-users"></a>Implementace dynamického maskování dat pro omezení vystavení citlivých dat neprivilegovaným uživatelům

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Sql Azure, OnPrem |
| **Atributy**              | VERZE SQL - V12, VERZE SQL - MSSQL2016 |
| **Odkazy**              | [Maskování dynamických dat](https://msdn.microsoft.com/library/mt130841) |
| **Kroky** | Účelem dynamického maskování dat je omezit expozici citlivých dat a zabránit uživatelům, kteří by neměli mít přístup k datům, aby je viděli. Dynamické maskování dat nemá za cíl zabránit uživatelům databáze v připojení přímo k databázi a spuštění vyčerpávající dotazy, které zveřejňují části citlivých dat. Dynamické maskování dat doplňuje další funkce zabezpečení serveru SQL Server (auditování, šifrování, zabezpečení na úrovni řádků...) a důrazně doporučujeme používat tuto funkci ve spojení s nimi navíc, aby bylo možné lépe chránit citlivá data v Databáze. Upozorňujeme, že tuto funkci podporuje jenom SQL Server počínaje 2016 a Azure SQL Database. |

## <a name="ensure-that-passwords-are-stored-in-salted-hash-format"></a><a id="salted-hash"></a>Ujistěte se, že hesla jsou uložena ve slaném formátu hash

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Zahasování hesla pomocí rozhraní API rozhraní API rozhraní .NET Crypto](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Kroky** | Hesla by neměla být uložena v databázích úložiště vlastních uživatelů. Hodnoty hashe hesla by měly být uloženy s hodnotami soli. Ujistěte se, že sůl pro uživatele je vždy unikátní a použijete b-crypt, s-crypt nebo PBKDF2 před uložením hesla, s minimálním počtem iteraci pracovního faktoru 150,000 smyček, abyste vyloučili možnost hrubého vynucení.| 

## <a name="ensure-that-sensitive-data-in-database-columns-is-encrypted"></a><a id="db-encrypted"></a>Zajištění šifrování citlivých dat ve sloupcích databáze

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | VERZE SQL - vše |
| **Odkazy**              | [Šifrování citlivých dat na serveru SQL](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [Jak: Šifrovat sloupec dat na serveru SQL Server](https://msdn.microsoft.com/library/ms179331), [Šifrovat podle certifikátu](https://msdn.microsoft.com/library/ms188061) |
| **Kroky** | V databázi musí být zašifrována citlivá data, například čísla kreditních karet. Data lze šifrovat pomocí šifrování na úrovni sloupců nebo pomocí funkce aplikace pomocí šifrovacích funkcí. |

## <a name="ensure-that-database-level-encryption-tde-is-enabled"></a><a id="tde-enabled"></a>Ujistěte se, že je povoleno šifrování na úrovni databáze (TDE)

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Principy transparentního šifrování dat serveru SQL Server (TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Kroky** | Funkce Transparentní šifrování dat (TDE) na serveru SQL pomáhá při šifrování citlivých dat v databázi a chrání klíče, které se používají k šifrování dat pomocí certifikátu. Tím zabráníte komukoli bez klíčů v použití dat. TDE chrání data "v klidovém stavu", což znamená data a soubory protokolu. Poskytuje schopnost dodržovat mnoho zákonů, předpisů a pokynů stanovených v různých průmyslových odvětvích. |

## <a name="ensure-that-database-backups-are-encrypted"></a><a id="backup"></a>Ujistěte se, že jsou zálohy databáze šifrovány

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | SQL Azure, OnPrem |
| **Atributy**              | Verze SQL - V12, VERZE SQL - MsSQL2014 |
| **Odkazy**              | [Šifrování zálohování databáze SQL](https://msdn.microsoft.com/library/dn449489) |
| **Kroky** | SQL Server má schopnost šifrovat data při vytváření zálohy. Zadáním šifrovacího algoritmu a šifrovacího zařízení (certifikát nebo asymetrický klíč) při vytváření zálohy lze vytvořit šifrovaný záložní soubor. |

## <a name="ensure-that-sensitive-data-relevant-to-web-api-is-not-stored-in-browsers-storage"></a><a id="api-browser"></a>Ujistěte se, že citlivá data relevantní pro webové rozhraní API nejsou uložena v úložišti prohlížeče

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC 5, MVC 6 |
| **Atributy**              | Zprostředkovatel identity – ADFS, Zprostředkovatel identity – Azure AD |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>V některých implementacích jsou citlivé artefakty relevantní pro ověřování webového rozhraní API uloženy v místním úložišti prohlížeče. Například artefakty ověřování Azure AD jako adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key atd.</p><p>Všechny tyto artefakty jsou k dispozici i po odhlášení nebo zavření prohlížeče. Pokud protivník získá přístup k těmto artefaktům, může je znovu použít pro přístup k chráněným prostředkům (API). Ujistěte se, že všechny citlivé artefakty související s webovým rozhraním API nejsou uloženy v úložišti prohlížeče. V případech, kdy je úložiště na straně klienta nevyhnutelné (např. jednostránkové aplikace (SPA), které využívají implicitní toky OpenIdConnect/OAuth k ukládání přístupových tokenů místně), použijte možnosti úložiště s nemají trvalost. například preferují SessionStorage před LocalStorage.</p>| 

### <a name="example"></a>Příklad
Níže uvedený fragment JavaScriptu pochází z vlastní ověřovací knihovny, která ukládá artefakty ověřování v místním úložišti. Těmto prováděním je třeba se vyhnout. 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a name="encrypt-sensitive-data-stored-in-cosmos-db"></a><a id="encrypt-docdb"></a>Šifrování citlivých dat uložených v Cosmos DB

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze dokumentů Azure | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Šifrujte citlivá data na úrovni aplikace před uložením do databáze dokumentů nebo uložte citlivá data do jiných úložných řešení, jako je Azure Storage nebo Azure SQL| 

## <a name="use-azure-disk-encryption-to-encrypt-disks-used-by-virtual-machines"></a><a id="disk-vm"></a>Šifrování disků Azure k šifrování disků používaných virtuálními počítači

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti virtuálního počítače Azure IaaS | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Šifrování disků Azure k šifrování disků používaných vašimi virtuálními počítači](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Kroky** | <p>Azure Disk Encryption je nová funkce, která je aktuálně ve verzi Preview. Tato funkce umožňuje šifrovat disky operačního systému a datové disky používané virtuálním počítačem IaaS. V systému Windows jsou jednotky šifrovány pomocí standardní technologie šifrování nástroje BitLocker. Pro Linux jsou disky šifrovány pomocí technologie DM-Crypt. To je integrován s Azure Key Vault, které vám umožní řídit a spravovat šifrovací klíče disku. Řešení Azure Disk Encryption podporuje následující tři scénáře šifrování zákazníků:</p><ul><li>Povolte šifrování na nových virtuálních počítačích IaaS vytvořených ze souborů Virtuálního pevného disku zašifrované zákazníky a šifrovacích klíčů poskytnutých zákazníkem, které jsou uložené v úložišti Azure Key Vault.</li><li>Povolte šifrování na nových virtuálních počítačích IaaS vytvořených z Azure Marketplace.</li><li>Povolte šifrování na existujících virtuálních počítačích IaaS, které už běží v Azure.</li></ul>| 

## <a name="encrypt-secrets-in-service-fabric-applications"></a><a id="fabric-apps"></a>Šifrování tajných kódů v aplikacích Service Fabric

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahu důvěryhodnosti služby Fabric | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Odkazy**              | [Správa tajných kódů v aplikacích Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Kroky** | Tajné klíče mohou být jakékoli citlivé informace, jako jsou řetězce připojení úložiště, hesla nebo jiné hodnoty, které by neměly být zpracovány ve formátu prostého textu. Azure Key Vault slouží ke správě klíčů a tajných kódů v aplikacích infrastruktury služeb. |

## <a name="perform-security-modeling-and-use-business-unitsteams-where-required"></a><a id="modeling-teams"></a>Provádění modelování zabezpečení a v případě potřeby používejte organizační jednotky/týmy

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Provádění modelování zabezpečení a v případě potřeby používejte organizační jednotky/týmy |

## <a name="minimize-access-to-share-feature-on-critical-entities"></a><a id="entities"></a>Minimalizace přístupu ke funkci sdílení na kritických entitách

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Minimalizace přístupu ke funkci sdílení na kritických entitách |

## <a name="train-users-on-the-risks-associated-with-the-dynamics-crm-share-feature-and-good-security-practices"></a><a id="good-practices"></a>Školení uživatelů o rizicích spojených s funkcí Dynamics CRM Share a osvědčenými postupy zabezpečení

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Školení uživatelů o rizicích spojených s funkcí Dynamics CRM Share a osvědčenými postupy zabezpečení |

## <a name="include-a-development-standards-rule-proscribing-showing-config-details-in-exception-management"></a><a id="exception-mgmt"></a>Zahrnout pravidlo pro nastavení standardů vývoje, které zobrazuje podrobnosti konfigurace ve správě výjimek

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Zahrňte pravidlo pro nastavení standardů vývoje, které zobrazuje podrobnosti konfigurace ve správě výjimek mimo vývoj. Vyzkoušejte to jako součást kontroly kódu nebo pravidelné kontroly.|

## <a name="use-azure-storage-service-encryption-sse-for-data-at-rest-preview"></a><a id="sse-preview"></a>Použití šifrování služby Azure Storage Service (SSE) pro data v klidovém stavu (preview)

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | StorageType – objekt blob |
| **Odkazy**              | [Šifrování služby Azure Storage Service pro data v klidovém stavu (preview)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Kroky** | <p>Azure Storage Service Encryption (SSE) pro data v klidovém stavu vám pomůže chránit a chránit vaše data tak, aby splňovala vaše závazky v oblasti zabezpečení a dodržování předpisů v organizaci. Pomocí této funkce služba Azure Storage automaticky šifruje vaše data před zachováním v úložišti a dešifruje před jejich načtením. Šifrování, dešifrování a správa klíčů je pro uživatele zcela transparentní. SSE platí pouze pro blokové objekty BLOB, objekty BLOB stránky a objekty BLOB připojit. Ostatní typy dat, včetně tabulek, front a souborů, nebudou šifrovány.</p><p>Pracovní postup šifrování a dešifrování:</p><ul><li>Zákazník povolí šifrování na účtu úložiště</li><li>Když zákazník zapíše nová data (PUT Blob, PUT Block, PUT Page, atd.) do úložiště objektů Blob; každý zápis je šifrován pomocí 256bitového šifrování AES, což je jedna z nejsilnějších blokových šifer, které jsou k dispozici</li><li>Když zákazník potřebuje přístup k datům (OBJEKT BLOBLO GET atd.), data se automaticky dešifrují před návratem k uživateli</li><li>Pokud je šifrování zakázáno, nové zápisy již nejsou šifrovány a existující šifrovaná data zůstanou šifrována, dokud je uživatel nepřepíše. Pokud je šifrování povoleno, zápisy do úložiště objektů Blob budou zašifrovány. Stav dat se nemění s přepínáním uživatele mezi povolením nebo zakázáním šifrování pro účet úložiště</li><li>Všechny šifrovací klíče jsou uloženy, šifrovány a spravovány společností Microsoft</li></ul><p>Vezměte prosím na vědomí, že v tomto okamžiku jsou klíče používané pro šifrování spravovány společností Microsoft. Společnost Microsoft generuje klíče původně a spravovat zabezpečené úložiště klíčů, jakož i pravidelné střídání, jak je definováno vnitřní zásady společnosti Microsoft. V budoucnu budou mít zákazníci možnost spravovat vlastní >šifrovací klíče a poskytovat cestu migrace z klíčů spravovaných společností Microsoft na klíče spravované zákazníkem.</p>| 

## <a name="use-client-side-encryption-to-store-sensitive-data-in-azure-storage"></a><a id="client-storage"></a>Ukládání citlivých dat ve službě Azure Storage pomocí šifrování na straně klienta

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Šifrování na straně klienta a Azure Key Vault pro úložiště Microsoft Azure](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [kurz: Šifrování a dešifrování objektů BLOB v úložišti Microsoft Azure pomocí azure trezoru klíčů](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), bezpečné ukládání dat v [úložišti objektů Blob Azure s rozšířeními o šifrování Azure](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Kroky** | <p>Klientská knihovna úložiště Azure pro balíček .NET Nuget podporuje šifrování dat v klientských aplikacích před odesláním do služby Azure Storage a dešifrování dat při stahování do klienta. Knihovna také podporuje integraci se službou Azure Key Vault pro správu klíčů účtu úložiště. Zde je stručný popis toho, jak funguje šifrování na straně klienta:</p><ul><li>Sada Azure Storage client SDK generuje šifrovací klíč obsahu (CEK), což je jednorázový symetrický klíč</li><li>Zákaznická data jsou šifrována pomocí tohoto</li><li>CEK je pak zabalen (šifrován) pomocí šifrovacího klíče (KEK). KEK je identifikován identifikátor klíče a může být asymetrický pár klíčů nebo symetrický klíč a lze spravovat místně nebo uloženy v trezoru klíčů Azure. Samotný klient úložiště nikdy nemá přístup k KEK. Pouze vyvolá algoritmus obtékání klíčů, který je k dispozici trezoru klíčů. Zákazníci si mohou vybrat, zda budou používat vlastní poskytovatele pro obtékání/rozbalení klíčů, pokud chtějí</li><li>Šifrovaná data se pak nahrají do služby Azure Storage. Podrobnosti implementace nižší úrovně naleznete v odkazech v části odkazy.</li></ul>|

## <a name="encrypt-sensitive-or-pii-data-written-to-phones-local-storage"></a><a id="pii-phones"></a>Šifrování citlivých nebo osobních údajů zapsaná do místního úložiště telefonů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilní klient | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecný, Xamarin  |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Správa nastavení a funkcí na svých zařízeních pomocí zásad Microsoft Intune](https://docs.microsoft.com/mem/intune/configuration/), [Keychain Valet](https://components.xamarin.com/view/square.valet) |
| **Kroky** | <p>Pokud aplikace zapisuje citlivé informace, jako je OSOBNÍ údaje uživatele (e-mail, telefonní číslo, jméno, příjmení, předvolby atd.) - v systému souborů mobilního telefonu, pak by měl být šifrován před zápisem do místního souborového systému. Pokud je aplikace podniková aplikace, prozkoumejte možnost publikování aplikace pomocí Windows Intune.</p>|

### <a name="example"></a>Příklad
Intune lze nakonfigurovat pomocí následujících zásad zabezpečení pro zabezpečení citlivých dat: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Příklad
Pokud aplikace není podniková aplikace, použijte platformu za předpokladu, keystore, klíčenky pro ukládání šifrovacích klíčů, pomocí kterého kryptografické operace mohou být prováděny v systému souborů. Následující fragment kódu ukazuje, jak získat přístup ke klíči z klíčenky pomocí xamarinu: 
```csharp
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a name="obfuscate-generated-binaries-before-distributing-to-end-users"></a><a id="binaries-end"></a>Obfuscate generované binární soubory před distribucí koncovým uživatelům

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilní klient | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Crypto obfuskace pro .Net](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Kroky** | Generované binární soubory (sestavení v apk) by měly být zamlžené zastavit reverzní inženýrství sestavení. K `CryptoObfuscator` tomuto účelu mohou být použity nástroje, jako je tento účel. |

## <a name="set-clientcredentialtype-to-certificate-or-windows"></a><a id="cert"></a>Nastavení typu clientCredentialType na certifikát nebo systém Windows

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Rozhraní .NET Framework 3 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Posílit](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Kroky** | Použití UsernameToken s heslem ve formátu prostého textu přes nešifrovaný kanál zpřístupní heslo útočníkům, kteří mohou čichat zprávy SOAP. Zprostředkovatelé služeb, kteří používají UsernameToken může přijímat hesla odeslaná ve formátu prostého textu. Odesílání hesel ve formátu prostého textu přes nešifrovaný kanál může vystavit pověření útočníkům, kteří mohou čichat zprávu SOAP. | 

### <a name="example"></a>Příklad
Následující konfigurace poskytovatele služeb WCF používá Token UsernameToken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Nastavte typ klienta CredentialType na certifikát nebo systém Windows. 

## <a name="wcf-security-mode-is-not-enabled"></a><a id="security"></a>Režim wCF-security není povolen.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecný, rozhraní .NET Framework 3 |
| **Atributy**              | Režim zabezpečení – přenos, režim zabezpečení – zpráva |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [Základy WCF Security CoDe Magazine](https://www.codemag.com/article/0611051) |
| **Kroky** | Nebyl definován žádný přenos ani zabezpečení zpráv. Aplikace, které přenášejí zprávy bez zabezpečení přenosu nebo zprávy, nemohou zaručit integritu nebo důvěrnost zpráv. Pokud je vazba zabezpečení WCF nastavena na žádnou, jsou zakázány zabezpečení přenosu i zprávy. |

### <a name="example"></a>Příklad
Následující konfigurace nastaví režim zabezpečení na žádný. 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>Příklad
Režim zabezpečení Napříč všemi vazbami služeb existuje pět možných režimů zabezpečení: 
* Žádné. Vypne zabezpečení. 
* Dopravy. Používá zabezpečení přenosu pro vzájemné ověřování a ochranu zpráv. 
* Zprávu. Používá zabezpečení zpráv pro vzájemné ověřování a ochranu zpráv. 
* Oboje. Umožňuje zadat nastavení pro přenos a zabezpečení na úrovni zpráv (podporuje to pouze služba MSMQ). 
* TransportwithMessageCredential. Pověření jsou předávány s ochranou zpráv a ověřováním serveru jsou poskytovány transportní vrstvou. 
* TransportCredentialOnly. Pověření klienta jsou předány s transportní vrstvou a není použita žádná ochrana zpráv. K ochraně integrity a důvěrnosti zpráv používejte zabezpečení přenosu a zpráv. Níže uvedená konfigurace říká službě, aby používala zabezpečení přenosu s pověřeními zprávy.
  ```
  <system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
  </system.serviceModel> 
  ```
