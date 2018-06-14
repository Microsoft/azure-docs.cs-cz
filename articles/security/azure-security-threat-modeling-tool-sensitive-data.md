---
title: Citlivá Data - nástroj Microsoft Threat modelování – Azure | Microsoft Docs
description: způsoby zmírnění hrozeb, které jsou zveřejněné v nástroji pro modelování hrozeb
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 8d7189ea4b01d43cea709e3300d8ed71d266f5c9
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019411"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Zabezpečení rámce: Citlivá Data | Způsoby zmírnění rizik 
| Produktům a službám | Článek |
| --------------- | ------- |
| **Počítač hranice vztahů důvěryhodnosti** | <ul><li>[Ujistěte se, že binární soubory jsou zamaskované pokud obsahují citlivé informace](#binaries-info)</li><li>[Zvažte použití šifrované souborů EFS (Encrypting File System) se používá k ochraně důvěrných dat specifických pro uživatele](#efs-user)</li><li>[Zajistěte, aby byla zašifrovaná citlivá data uložená v aplikaci na systém souborů](#filesystem)</li></ul> | 
| **Webové aplikace** | <ul><li>[Ujistěte se, že není citlivého obsahu do mezipaměti, v prohlížeči](#cache-browser)</li><li>[Šifrování oddíly konfiguračních souborů webové aplikace, které obsahují citlivá data](#encrypt-data)</li><li>[Explicitně zakážete atributu HTML automatického dokončování v citlivých formulářů a vstupy](#autocomplete-input)</li><li>[Ujistěte se, že citlivá data zobrazí na obrazovce uživatele zakryté hvězdičkami](#data-mask)</li></ul> | 
| **Database** | <ul><li>[Implementovat dynamické maskování omezení uživatelů bez ohrožení privilegovaný citlivá data dat](#dynamic-users)</li><li>[Ujistěte se, že hesla jsou uložena ve formátu hashe](#salted-hash)</li><li>[Musí být šifrovaný citlivá data v databázi sloupců](#db-encrypted)</li><li>[Ujistěte se, že je povolené šifrování tuto úroveň databáze (TDE)](#tde-enabled)</li><li>[Zajistěte, aby byly šifrované zálohování databáze](#backup)</li></ul> | 
| **Webové rozhraní API** | <ul><li>[Ujistěte se, že citlivá data, které jsou relevantní pro webového rozhraní API není uložena v prohlížeči úložiště](#api-browser)</li></ul> | 
| Azure Documentdb | <ul><li>[Šifrování citlivých dat, které jsou uloženy v databázi Cosmos Azure](#encrypt-docdb)</li></ul> | 
| **Hranice vztahů důvěryhodnosti virtuálních počítačů Azure IaaS** | <ul><li>[Použít k šifrování disků používaných virtuálními počítači Azure Disk Encryption](#disk-vm)</li></ul> | 
| **Hranice vztahů důvěryhodnosti Service Fabric** | <ul><li>[Šifrování tajných klíčů v aplikace Service Fabric](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Provedení modelování zabezpečení a použít obchodní jednotky nebo týmy případě požadavku](#modeling-teams)</li><li>[Minimalizovat přístup ke sdílení funkcí na důležité entity](#entities)</li><li>[Školení uživatelů na rizika související s funkcí sdílené složky Dynamics CRM a postupy dobrý zabezpečení](#good-practices)</li><li>[Zahrnout pravidla standardy vývoj proscribing zobrazující podrobnosti konfigurace ve správě výjimek](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Používat šifrování služby úložiště Azure (SSE) dat v klidovém stavu (Preview)](#sse-preview)</li><li>[Citlivá data v úložišti Azure pomocí šifrování na straně klienta](#client-storage)</li></ul> | 
| **Mobilního klienta** | <ul><li>[Šifrování velká a malá písmena nebo PII data zapsaná do telefonů místní úložiště](#pii-phones)</li><li>[Obfuskováním binární soubory generované před distribucí koncovým uživatelům](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Nastavit clientCredentialType certifikát nebo systému Windows](#cert)</li><li>[Není povolen režim zabezpečení WCF](#security)</li></ul> | 

## <a id="binaries-info"></a>Ujistěte se, že binární soubory jsou zamaskované pokud obsahují citlivé informace

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Počítač hranice vztahů důvěryhodnosti | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že binární soubory jsou zamaskované pokud obsahují citlivé informace, například obchodních tajemství, citlivé obchodní logiky, která by měla není vrátit zpět. Toto je zastavit zpětnou analýzu sestavení. Nástroje, jako `CryptoObfuscator` mohou být použity pro tento účel. |

## <a id="efs-user"></a>Zvažte použití šifrované souborů EFS (Encrypting File System) se používá k ochraně důvěrných dat specifických pro uživatele

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Počítač hranice vztahů důvěryhodnosti | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Zvažte použití šifrované souborů EFS (Encrypting File System) se používá k ochraně důvěrných dat specifických pro uživatele z nežádoucí s fyzický přístup k počítači. |

## <a id="filesystem"></a>Zajistěte, aby byla zašifrovaná citlivá data uložená v aplikaci na systém souborů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Počítač hranice vztahů důvěryhodnosti | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Zajistěte, aby byla zašifrovaná citlivá data uložená v aplikaci na systém souborů (například pomocí rozhraní DPAPI), pokud systém souborů EFS se nedá vynutit. |

## <a id="cache-browser"></a>Ujistěte se, že není citlivého obsahu do mezipaměti, v prohlížeči

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Generic, Web Forms, MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Prohlížeče můžete ukládat informace pro účely ukládání do mezipaměti a historie. Tyto soubory uložené v mezipaměti jsou uloženy ve složce, jako je složka dočasných souborů Internetu v případě aplikace Internet Explorer. Pokud tyto stránek se označují znovu, budou prohlížeč zobrazí ze své mezipaměti. Pokud citlivé informace se zobrazí uživatelům (například jejich adresy, údaje platební karty, číslo sociálního pojištění nebo uživatelské jméno), pak tyto informace může být uložený v mezipaměti prohlížeče a proto získat prostřednictvím zkoumání mezipaměti prohlížeče nebo jednoduše kliknutím na tlačítko "Zpět" v prohlížeči. Nastavte hodnotu hlavičky cache-control odpovědi na "Ne úložiště" pro všechny stránky. |

### <a name="example"></a>Příklad:
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

### <a name="example"></a>Příklad:
Tato možnost může být implementovaná pomocí filtru. Následující příklad mohou být použity: 
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

## <a id="encrypt-data"></a>Šifrování oddíly konfiguračních souborů webové aplikace, které obsahují citlivá data

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Postupy: Šifrování konfigurační oddíly funkce v technologii ASP.NET 2.0 pomocí rozhraní DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [určení konfigurace poskytovatele chráněné](https://msdn.microsoft.com/library/68ze1hb2.aspx), [pomocí Azure Key Vault k ochraně tajných klíčů aplikace](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Kroky** | Soubory konfigurace, například jako soubor Web.config, appSettings.JSON určený často používané pro udržení citlivé informace, včetně uživatelská jména, hesla, databázové připojovací řetězce a šifrovací klíče. Pokud tyto informace nechrání, vaše aplikace je zranitelný vůči útočníkům nebo uživatelé se zlými úmysly získání citlivé informace, jako je například účet uživatelská jména a hesla, názvy databáze a názvy serverů. V závislosti na typu nasazení (azure nebo místní), šifrování citlivých skupin konfigurační soubory pomocí rozhraní DPAPI nebo služby, jako je Azure Key Vault. |

## <a id="autocomplete-input"></a>Explicitně zakážete atributu HTML automatického dokončování v citlivých formulářů a vstupy

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN: automatického dokončování atribut](http://msdn.microsoft.com/library/ms533486(VS.85).aspx), [pomocí automatického dokončování ve formátu HTML](http://msdn.microsoft.com/library/ms533032.aspx), [zabezpečení čištění HTML](http://technet.microsoft.com/security/bulletin/MS10-071), [Autocomplete., znovu?](http://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Kroky** | Autocomplete atribut určuje, zda formuláře by měl mít automatického dokončování zapnout nebo vypnout. Po automatického dokončování v prohlížeči automaticky dokončit hodnoty na základě hodnot, které uživatel zadal před. Například při zadání nové jméno a heslo ve formuláři a odeslání formuláře, v prohlížeči zobrazí, pokud má být uložen heslo. Po tomto datu když je formulář zobrazen, jméno a heslo se vyplní automaticky nebo jsou dokončeny, jako je název zadán. Útočník s místního přístupu může získat hesla v nešifrovaném textu z mezipaměti prohlížeče. Ve výchozím nastavení je povolená funkce automatického dokončování a musí explicitně zakázat. |

### <a name="example"></a>Příklad:
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>Ujistěte se, že citlivá data zobrazí na obrazovce uživatele zakryté hvězdičkami

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Citlivá data, jako jsou hesla, čísla platebních karet, číslo sociálního pojištění atd by měl maskovat, kdy se zobrazí na obrazovce. Toto je zabraňuje neoprávněným osobám v přístupu k datům (například procházení osazení hesla, zobrazení SSN čísel uživatelů pracovníky technické podpory). Zajistěte, aby tyto datové prvky nejsou viditelné ve formátu prostého textu a jsou správně maskování. Tento postup je postarat při přijímání je jako vstup (např., Typ vstupu = "password") a také zobrazení zpět na obrazovce (například zobrazení pouze poslední 4 číslice čísla platební karty). |

## <a id="dynamic-users"></a>Implementovat dynamické maskování omezení uživatelů bez ohrožení privilegovaný citlivá data dat

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Sql Azure, OnPrem |
| **Atributy**              | MsSQL2016 verze - V12, verze SQL - SQL |
| **Odkazy**              | [Dynamická Data maskování](https://msdn.microsoft.com/library/mt130841) |
| **Kroky** | Účelem dynamické maskování dat je omezení úniku citlivých dat, brání uživatelům, kteří by neměly mít přístup k datům v jejich zobrazení. Dynamické maskování dat není cílem zabránit uživatelům databáze připojovat přímo k databázi a spuštění vyčerpávající dotazů, které zveřejňují kusy citlivá data. Dynamická data maskování je doplňkem k další funkce zabezpečení systému SQL Server (auditování, šifrování, zabezpečení na úrovni řádků...) a důrazně doporučujeme k použití této funkce ve spojení s nimi kromě toho, aby bylo možné lépe chránit citlivá data v databáze. Upozorňujeme, že tato funkce je podporována pouze tak, že počínaje 2016 systému SQL Server a databáze SQL Azure. |

## <a id="salted-hash"></a>Ujistěte se, že hesla jsou uložena ve formátu hashe

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Pomocí rozhraní API technologie .NET kryptografických Hashing heslo](http://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Kroky** | Hesla by neměl být uložena ve vlastní uživatelské úložiště databáze. Hodnoty hash hesla by měly být uložené s hodnoty řetězce salt místo. Zajistěte, aby salt pro daného uživatele je vždy jedinečný a použijete b-crypt, s-crypt nebo PBKDF2 před uložení hesla, s minimální pracovní Multi-Factor iterace počtem 150 000 smyčky omezit možnost hrubou silou.| 

## <a id="db-encrypted"></a>Musí být šifrovaný citlivá data v databázi sloupců

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | Verze SQL – všechny |
| **Odkazy**              | [Šifrování důvěrných osobních údajů v systému SQL server](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [postupy: šifrování sloupec dat v systému SQL Server](https://msdn.microsoft.com/library/ms179331), [šifrovat pomocí certifikátu](https://msdn.microsoft.com/library/ms188061) |
| **Kroky** | Citlivá data, třeba čísla platebních karet má k zašifrování v databázi. Data mohou být šifrována pomocí šifrování na úrovni sloupce nebo funkcí aplikace pomocí funkcí šifrování. |

## <a id="tde-enabled"></a>Ujistěte se, že je povolené šifrování tuto úroveň databáze (TDE)

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Principy SQL serveru transparentní šifrování dat (šifrování TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Kroky** | Transparentní šifrování dat (šifrování TDE) funkce v systému SQL server pomáhá šifrování citlivých dat v databázi a chránit klíčů, které se používají k šifrování dat pomocí certifikátu. Každý, kdo bez klíče zabrání pomocí data. Šifrování TDE chrání data "v klidu", což znamená, soubory protokolu a data. Poskytuje možnost pro dosažení souladu s mnoha právní a pokyny uvedenými v různých odvětví. |

## <a id="backup"></a>Zajistěte, aby byly šifrované zálohování databáze

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | SQL Azure, OnPrem |
| **Atributy**              | MsSQL2014 verze - V12, verze SQL - SQL |
| **Odkazy**              | [Šifrování zálohování databáze SQL](https://msdn.microsoft.com/library/dn449489) |
| **Kroky** | SQL Server má možnost šifrování dat při vytváření zálohy. Zadáním šifrovací algoritmus a modulu pro šifrování (certifikát nebo asymetrický klíč) při vytváření zálohy, můžete jeden vytvořit šifrovaný záložní soubor. |

## <a id="api-browser"></a>Ujistěte se, že citlivá data, které jsou relevantní pro webového rozhraní API není uložena v prohlížeči úložiště

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | MVC 5, MVC 6 |
| **Atributy**              | Poskytovatel Identity identity zprostředkovatel – AD FS, – Azure AD |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>V některých implementacích citlivé artefakty týkající se ověřování webového rozhraní API ukládají v místním úložišti prohlížeče. Například artefakty ověřování Azure AD, například adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key atd.</p><p>Všechny tyto artefakty jsou k dispozici i po odhlášení nebo zavření prohlížeče. Pokud nežádoucí osoba získá přístup k těchto artefaktů, si znovu použít, je přístup k chráněným prostředkům (API). Ujistěte se, že všechny citlivé artefakty související s webového rozhraní API není uložena v prohlížeči úložiště. V případech, kdy je nevyhnutelné použít úložiště na straně klienta (například jedné stránky aplikace (SPA), využívat implicitní OpenIdConnect/OAuth toky potřebovat ukládat místně přístupové tokeny), použijte možnosti úložiště s nemají trvalost. například přednost SessionStorage LocalStorage.</p>| 

### <a name="example"></a>Příklad:
Níže JavaScript fragment kódu je z knihovny vlastního ověřování, která ukládá artefakty ověřování v místní úložiště. Je nutno tyto implementace. 
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

## <a id="encrypt-docdb"></a>Šifrování citlivých dat, které jsou uloženy v databázi systému Cosmos

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Documentdb | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Před uložením v dokumentu DB šifrování citlivých dat na úrovni aplikace nebo uložit všechny citlivá data v jiné řešení úložiště, jako je Azure Storage nebo Azure SQL| 

## <a id="disk-vm"></a>Použít k šifrování disků používaných virtuálními počítači Azure Disk Encryption

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti virtuálních počítačů Azure IaaS | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Pomocí Azure Disk Encryption k šifrování disků používat virtuální počítače](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Kroky** | <p>Azure Disk Encryption je nová funkce, která je aktuálně ve verzi preview. Tato funkce umožňuje šifrování disků operačního systému a datové disky použít podle IaaS virtuálního počítače. Pro systém Windows jsou šifrované jednotky pomocí standardních technologií šifrování nástroje BitLocker. Pro systémy Linux jsou disky šifrované pomocí technologie DM-Crypt. Toto jsou integrované s Azure Key Vault umožňují řídit a spravovat disku šifrovací klíče. Řešení Azure Disk Encryption podporuje následující scénáře šifrování tři zákazníka:</p><ul><li>Povolte šifrování na nové virtuální počítače IaaS vytvořené zákazníka šifrované soubory virtuálního pevného disku a zadaný zákazníka šifrovací klíče, které jsou uložené v Azure Key Vault.</li><li>Povolte šifrování na nové virtuální počítače IaaS vytvořené z Azure Marketplace.</li><li>Povolte šifrování na existující virtuální počítače IaaS už běží v Azure.</li></ul>| 

## <a id="fabric-apps"></a>Šifrování tajných klíčů v aplikace Service Fabric

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Service Fabric | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Odkazy**              | [Správa tajných klíčů v aplikace Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Kroky** | Tajné klíče může být žádné citlivé informace, jako je například úložiště připojovací řetězce, hesla nebo jiné hodnoty, které by neměly být zpracovány v prostém textu. Použití Azure Key Vault pro správu klíčů a tajných klíčů v service fabric aplikace. |

## <a id="modeling-teams"></a>Provedení modelování zabezpečení a použít obchodní jednotky nebo týmy případě požadavku

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Provedení modelování zabezpečení a použít obchodní jednotky nebo týmy případě požadavku |

## <a id="entities"></a>Minimalizovat přístup ke sdílení funkcí na důležité entity

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Minimalizovat přístup ke sdílení funkcí na důležité entity |

## <a id="good-practices"></a>Školení uživatelů na rizika související s funkcí sdílené složky Dynamics CRM a postupy dobrý zabezpečení

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Školení uživatelů na rizika související s funkcí sdílené složky Dynamics CRM a postupy dobrý zabezpečení |

## <a id="exception-mgmt"></a>Zahrnout pravidla standardy vývoj proscribing zobrazující podrobnosti konfigurace ve správě výjimek

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Zahrnout pravidla standardy vývoj proscribing zobrazuje podrobnosti konfigurace ve správě výjimka mimo vývoj. Testování pro tento jako součást recenze kódu nebo pravidelné kontroly.|

## <a id="sse-preview"></a>Používat šifrování služby úložiště Azure (SSE) dat v klidovém stavu (Preview)

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | StorageType – objekt Blob |
| **Odkazy**              | [Šifrování služby úložiště Azure pro Data v klidovém stavu (Preview)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Kroky** | <p>Azure Storage Service šifrování (SSE) pro Data v klidovém stavu pomáhá chránit a ochranu dat, aby splňovaly vaše organizace zabezpečení a dodržování předpisů závazky. Pomocí této funkce Azure Storage automaticky šifruje vaše data před uložením do úložiště a dešifruje před načtení. Šifrování, dešifrování a správu klíčů je pro uživatele zcela transparentní. SSE se vztahuje pouze na objekty BLOB, objekty BLOB stránky, bloků a doplňovací objekty BLOB. Jiné typy dat, včetně tabulek, front a soubory, nebudou šifrována.</p><p>Šifrování a dešifrování pracovního postupu:</p><ul><li>Zákazník povoluje šifrování na účtu úložiště</li><li>Když zákazník zapisuje nová data (PUT objektů Blob, PUT bloku, PUT stránky atd.) do úložiště objektů Blob; každém zápisu je zašifrovaná pomocí šifrování AES 256 bitů, jednu z nejsilnějších šifry bloku k dispozici</li><li>Když zákazník potřebuje přístup k datům (získání objektu Blob atd.), jsou data automaticky dešifrována před vrácením uživatele</li><li>Pokud šifrování je zakázáno, nové zápisy již nejsou zašifrované a existující šifrovaná data zůstává zašifrovaný, dokud přepsaná uživatelem. Když je šifrování povoleno, bude se šifrovat zápisy do úložiště objektů Blob. Stav data nemění se uživatel při přepínání mezi povolení nebo zákaz šifrování pro účet úložiště</li><li>Všechny šifrovací klíče jsou uložené, zašifrovaná a spravován společností Microsoft</li></ul><p>Upozorňujeme, že v tomto okamžiku jsou klíče používané k šifrování spravované microsoftem. Společnost Microsoft generuje klíče původně a spravovat zabezpečené úložiště klíčů, jakož i regulární otočení podle definice interní zásady společnosti Microsoft. V budoucnu, zákazníkům získáte možnost spravovat jejich vlastní > šifrovacích klíčů a zadejte cestu migrace ze spravovaných společností Microsoft klíče spravovaného zákazníkem klíče.</p>| 

## <a id="client-storage"></a>Citlivá data v úložišti Azure pomocí šifrování na straně klienta

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Šifrování na straně klienta a Azure Key Vault pro Microsoft Azure Storage](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [kurz: šifrování a dešifrování objektů BLOB v úložišti Microsoft Azure pomocí Azure Key Vault](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), [bezpečné ukládání dat v Azure Blob Úložiště s příponami Azure šifrování](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Kroky** | <p>Klientská knihovna pro úložiště Azure pro balíček Nuget pro rozhraní .NET podporuje šifrování dat v rámci klientské aplikace před nahráním do úložiště Azure a dešifrování dat při stahování do klienta. Knihovna také podporuje integraci se službou Azure Key Vault pro správu klíčů účtu úložiště. Zde je stručný popis toho, jak funguje šifrování na straně klienta:</p><ul><li>Klient Azure Storage SDK vygeneruje obsahu šifrovací klíč (CEK), což je použití jednoho bránu symetrický klíč</li><li>Data zákazníků se šifruje pomocí této CEK</li><li>CEK je vnořena (šifrované) pomocí klíčů šifrovacího klíče (KEK). Klíče KEK je identifikovaná identifikátorem klíče a může být pár asymetrických klíčů nebo symetrického klíče a mohou být spravovaný místně nebo uloženy v Azure Key Vault. Klient úložiště pro samotné nikdy má přístup k klíče KEK. Vyvolá právě algoritmus zabalení klíče, který zajišťuje Key Vault. Tato volba se pro klíč zabalení/rozbalování Pokud chtějí použít vlastní zprostředkovatelé</li><li>Šifrovaná data se pak odešlou do služby Azure Storage. Zkontrolujte na odkazy v části odkazy podrobnosti implementace nízké úrovně.</li></ul>|

## <a id="pii-phones"></a>Šifrování velká a malá písmena nebo PII data zapsaná do telefonů místní úložiště

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilního klienta | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné, Xamarin  |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Správa nastavení a funkcí v zařízeních pomocí zásad Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies#create-a-configuration-policy), [osobní služby řetězce klíčů](https://components.xamarin.com/view/square.valet) |
| **Kroky** | <p>Pokud aplikace zapíše citlivé informace, jako je PII uživatele (e-mailu, telefonní číslo, křestní jméno, příjmení a předvolby atd.) – v systému souborů pro mobilní, pak jej by se měla šifrovat před zápisem do místního systému souborů. Pokud aplikace je podniková aplikace, pak ověření možnosti publikování aplikací pomocí služby Windows Intune.</p>|

### <a name="example"></a>Příklad:
Intune můžete nakonfigurovat následující zásad zabezpečení, které chrání citlivá data: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Příklad:
Pokud aplikace není podniková aplikace, pak použít platforma poskytovaná úložiště klíčů, řetězce klíčů k ukládání šifrovacích klíčů, pomocí které kryptografické operace může provést v systému souborů. Následující fragment kódu ukazuje, jak přístup ke klíči z řetězce klíčů pomocí xamarin: 
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

## <a id="binaries-end"></a>Obfuskováním binární soubory generované před distribucí koncovým uživatelům

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilního klienta | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Kryptografické maskováním pro .net](http://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Kroky** | Vygenerovaný binárních souborů (sestavení v rámci apk) by měl být matoucí zastavit zpětnou analýzu sestavení. Nástroje, jako `CryptoObfuscator` mohou být použity pro tento účel. |

## <a id="cert"></a>Nastavit clientCredentialType certifikát nebo systému Windows

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Rozhraní .NET framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Obohacení](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Kroky** | Heslo pro útočníky, kteří protokolu SOAP zprávy, můžete sledovat pomocí UsernameToken heslo jako prostý text přes nezašifrované kanály zpřístupní. Poskytovatelé služeb používaných UsernameToken může přijmout hesla odesílána v podobě prostého textu. Odesílání nezašifrovaná hesla přes nezašifrované kanály můžou zpřístupnit přihlašovací údaje pro útočníky, kteří, můžete zachytit zprávu protokolu SOAP. | 

### <a name="example"></a>Příklad:
Následující konfigurace poskytovatele služeb WCF používá UsernameToken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Nastavit clientCredentialType certifikát nebo systému Windows. 

## <a id="security"></a>Není povolen režim zabezpečení WCF

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecná rozhraní .NET Framework 3 |
| **Atributy**              | Zabezpečení režimu – přenosu, režimu zabezpečení – zpráva |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/vulncat/index.html), [časopis CoDe Základy zabezpečení WCF](http://www.codemag.com/article/0611051) |
| **Kroky** | Nebylo definováno žádné zabezpečení přenosu nebo zprávy. Aplikace, které přenosu zpráv bez přenos nebo zpráva, že zabezpečení nemůže zaručit integrity nebo důvěrnosti zpráv. Pokud vazbu zabezpečení WCF je nastavená na hodnotu None, jsou zakázané zabezpečení přenosu a zprávy. |

### <a name="example"></a>Příklad:
Následující konfigurace nastaví režim zabezpečení na hodnotu None. 
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

### <a name="example"></a>Příklad:
Režim zabezpečení přes všechny vazby služeb jsou pět režimy možné zabezpečení: 
* Žádné. Vypne zabezpečení. 
* Přenos. Používá přenosu zabezpečení pro vzájemné ověřování a zpráva ochranu. 
* Zpráva. Zabezpečení zpráv se používá pro vzájemné ověřování a zpráva ochranu. 
* Obě. Umožňuje zadat nastavení přenosu a zabezpečení na úrovni zpráv (MSMQ pouze podporuje to). 
* TransportWithMessageCredential. Přihlašovací údaje se dokončila s zprávu a ochrany zprávy a ověření serveru jsou poskytovány přenosové vrstvy. 
* TransportCredentialOnly. Pověření klienta se dokončila s přenosové vrstvy a použije se žádná zpráva ochrana. Zabezpečení přenosu a zpráv použijte k ochraně integrity a důvěrnosti zpráv. Konfigurace níže informuje službu, která používá zabezpečení přenosu s přihlašovacími údaji zprávy.
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
