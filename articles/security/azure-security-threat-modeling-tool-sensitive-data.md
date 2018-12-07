---
title: Citlivá Data – nástroj pro modelování hrozeb Microsoftu – Azure | Dokumentace Microsoftu
description: zmírnění rizik pro hrozby v nástroj pro modelování hrozeb
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 7f90fccd062826493b7add1a90406fa9244e00b2
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53002050"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Zabezpečení Frame: Citlivá Data | Zmírnění rizik 
| Produkt nebo službu | Článek |
| --------------- | ------- |
| **Hranice vztahů důvěryhodnosti počítače** | <ul><li>[Ujistěte se, že binární soubory jsou zakódovány, pokud obsahují citlivé informace](#binaries-info)</li><li>[Vezměte v úvahu, že pomocí šifrované File System (EFS) se používá k ochraně důvěrných údajů konkrétního uživatele](#efs-user)</li><li>[Ujistěte se, že citlivá data uložená v aplikaci na systém souborů je šifrovaná](#filesystem)</li></ul> | 
| **Webové aplikace** | <ul><li>[Ujistěte se, že není citlivý obsah do mezipaměti, v prohlížeči](#cache-browser)</li><li>[Šifrování oddíly konfiguračních souborů webové aplikace, které obsahují citlivá data](#encrypt-data)</li><li>[Explicitně zakážete automatické dokončování atributu HTML ve formulářích citlivé a vstupy](#autocomplete-input)</li><li>[Ujistěte se, že je zakryté hvězdičkami citlivá data zobrazí na obrazovce pro uživatele](#data-mask)</li></ul> | 
| **Database** | <ul><li>[Maskování dynamických dat implementují omezení citlivá data bez ohrožení privilegovaných uživatelů](#dynamic-users)</li><li>[Ujistěte se, že hesla jsou uložena ve formátu hashe](#salted-hash)</li><li>[ Ujistěte se, že je zašifrovaný citlivá data v databázi sloupce](#db-encrypted)</li><li>[Ujistěte se, že je povoleno šifrování databáze (TDE)](#tde-enabled)</li><li>[Ujistěte se, že jsou šifrovaná zálohování databáze](#backup)</li></ul> | 
| **Webové rozhraní API** | <ul><li>[Ujistěte se, že citlivá data, které jsou relevantní pro webové rozhraní API není uložený v úložišti v prohlížeči](#api-browser)</li></ul> | 
| Azure Documentdb | <ul><li>[Šifrovat citlivá data uložená ve službě Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Hranice vztahů důvěryhodnosti virtuálních počítačů Azure IaaS** | <ul><li>[Použití Azure Disk Encryption pro šifrování disků, které jsou používány virtuálními počítači](#disk-vm)</li></ul> | 
| **Hranice vztahů důvěryhodnosti Service Fabric** | <ul><li>[Šifrování tajných kódů aplikace Service Fabric](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Provádět modelování zabezpečení a použití obchodní jednotky nebo týmy vyžadováno](#modeling-teams)</li><li>[Minimalizovat přístup ke sdílení funkcí na důležité entity](#entities)</li><li>[Naučím uživatele na rizicích spojených s postupy vhodné zabezpečení a funkce sdílení Dynamics CRM](#good-practices)</li><li>[Zahrnout pravidlo standardů vývoje proscribing zobrazující podrobnosti konfigurace v Správa výjimek](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Použijte šifrování služby Azure Storage (SSE) pro Data v klidovém stavu (Preview)](#sse-preview)</li><li>[Šifrování na straně klienta používá k ukládání citlivých dat ve službě Azure Storage](#client-storage)</li></ul> | 
| **Mobilního klienta** | <ul><li>[Šifrování citlivé nebo identifikovatelné osobní údaje data zapsaná do místního úložiště telefony](#pii-phones)</li><li>[Před distribucí koncovým uživatelům obfuskaci generovaný binární soubory](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[ Typ clientCredentialType nastaven na certifikátu nebo Windows](#cert)</li><li>[Není povolen režim zabezpečení WCF](#security)</li></ul> | 

## <a id="binaries-info"></a>Ujistěte se, že binární soubory jsou zakódovány, pokud obsahují citlivé informace

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti počítače | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že binární soubory jsou zakódovány, pokud obsahují citlivé informace například obchodních tajemství, citlivé obchodní logika, která by měla nelze vrátit zpět. Toto je zastavit zpětné analýze sestavení. Nástroje, jako je `CryptoObfuscator` mohou být použity pro tento účel. |

## <a id="efs-user"></a>Vezměte v úvahu, že pomocí šifrované File System (EFS) se používá k ochraně důvěrných údajů konkrétního uživatele

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti počítače | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Zvažte použití šifrované File System (EFS) se používá k ochraně důvěrných údajů konkrétního uživatele z nežádoucí osoby mají fyzický přístup k počítači. |

## <a id="filesystem"></a>Ujistěte se, že citlivá data uložená v aplikaci na systém souborů je šifrovaná

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti počítače | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že je zašifrovaný citlivá data uložená v aplikaci na systém souborů (např. pomocí rozhraní DPAPI), pokud systém souborů EFS se nedá vynutit. |

## <a id="cache-browser"></a>Ujistěte se, že není citlivý obsah do mezipaměti, v prohlížeči

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné, webových formulářů, MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Prohlížeči můžete ukládat informace pro účely ukládání do mezipaměti a historie. Tyto soubory uložené v mezipaměti jsou uloženy ve složce, jako je složka dočasných souborů Internetu v případě aplikace Internet Explorer. Tyto stránky se označují znovu, prohlížeč je zobrazí uloženou v mezipaměti. Pokud citlivých informací se zobrazí uživateli (například jejich adresy, údajů z platební karty, číslo sociálního pojištění nebo uživatelské jméno), pak tyto informace může být uložený v mezipaměti prohlížeče a proto retrievable prostřednictvím zkoušení mezipaměť prohlížeče nebo jednoduše stisknutím tlačítka "Zpět" prohlížeče. Nastavte hodnotu hlavičky cache-control odpovědi na "no-store" pro všechny stránky. |

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
To může implementovat pomocí filtru. Následující příklad může být použit: 
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

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Postupy: Šifrování konfigurační oddíly funkce v ASP.NET 2.0 pomocí DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [určení konfigurace poskytovatele chráněné](https://msdn.microsoft.com/library/68ze1hb2.aspx), [pomocí služby Azure Key Vault k ochraně tajných klíčů aplikací](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Kroky** | Konfigurační soubory, například jako soubor Web.config, appsettings.json se často používají k uložení citlivé informace, včetně uživatelská jména, hesla, připojovací řetězce databáze a šifrovací klíče. Pokud tyto informace not protect, vaše aplikace je zranitelný vůči útočníci nebo uživatelé se zlými úmysly získání citlivé informace, jako je například účet uživatelská jména a hesla, názvy databáze a názvy serverů. Podle typu nasazení (azure nebo v místním prostředí), Zašifrujte citlivá části konfigurační soubory pomocí rozhraní DPAPI nebo službám, jako je Azure Key Vault. |

## <a id="autocomplete-input"></a>Explicitně zakážete automatické dokončování atributu HTML ve formulářích citlivé a vstupy

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN: atribut automatického dokončování](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [pomocí automatické dokončování ve formátu HTML](https://msdn.microsoft.com/library/ms533032.aspx), [ohrožení zabezpečení Sanitizace HTML](https://technet.microsoft.com/security/bulletin/MS10-071), [automatické dokončování., znovu?](http://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Kroky** | Atribut automatického dokončování Určuje, zda formulář má mít automatické dokončování zapnutí nebo vypnutí. Po zapnutí automatického dokončování v prohlížeči automatického dokončení hodnoty na základě hodnot, které uživatel zadal před. Například když se odešle formulář je ve formě zadat nový název a heslo, prohlížeč požádá, pokud by měla uložit heslo. Po tomto datu když je formulář zobrazen, uživatelské jméno a heslo se vyplní automaticky, nebo jsou dokončit, protože název je zadán. Útočník s místním přístupem lze získat hesla v nešifrovaném textu z mezipaměti prohlížeče. Ve výchozím nastavení je povoleno automatické dokončování a musí explicitně zakázat. |

### <a name="example"></a>Příklad:
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>Ujistěte se, že je zakryté hvězdičkami citlivá data zobrazí na obrazovce pro uživatele

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Citlivá data, jako jsou hesla, čísla platebních karet, číslo sociálního pojištění atd by měl maskovaná, když se zobrazí na obrazovce. Toto je zabránit neoprávněným přístup k datům (například procházení rameno, bude hesla, pracovníci podpory zobrazení SSN počtu uživatelů). Ujistěte se, že tyto údaje nejsou viditelné ve formátu prostého textu a jsou maskována odpovídajícím způsobem. Tato akce nemá se postarat při přijímá jako vstup (například. Typ vstupu = "password") a také zobrazení zpět na obrazovce (například zobrazení pouze poslední 4 číslice čísla platební karty). |

## <a id="dynamic-users"></a>Maskování dynamických dat implementují omezení citlivá data bez ohrožení privilegovaných uživatelů

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Sql Azure, OnPrem |
| **Atributy**              | MsSQL2016 verze - V12, verze SQL - SQL |
| **Odkazy**              | [Dynamické maskování dat](https://msdn.microsoft.com/library/mt130841) |
| **Kroky** | Účelem dynamické maskování dat je omezení rizika ohrožení citlivých dat, zabráníte uživatelům, kteří by neměly mít přístup k datům v jejich zobrazení. Dynamické maskování dat není cílem Znemožněte uživatelům připojovat přímo k databázi a spustit vyčerpávající dotazů, které zveřejňují citlivých dat databáze. Dynamické maskování dat je pouze doplnění jiných funkcích zabezpečení SQL serveru (auditování, šifrování, zabezpečení na úrovni řádků...) a je důrazně doporučujeme používat tuto funkci ve spojení s nimi kromě toho, aby bylo možné lépe chránit citlivá data v databáze. Mějte prosím na paměti, že tato funkce je podporována pouze v systému SQL Server, počínaje 2016 a Azure SQL Database. |

## <a id="salted-hash"></a>Ujistěte se, že hesla jsou uložena ve formátu hashe

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Heslo Hashing pomocí rozhraní Crypto API pro .NET](http://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Kroky** | Hesla by neměly být uloženy ve vlastní uživatelské databáze úložiště. Hodnoty hash hesel místo toho by měla být uložena s hodnoty řetězce salt. Ujistěte se, že je vždy jedinečná hodnota salt pro uživatele a použít b-crypt, s-crypt nebo PBKDF2 před níž byla hesla uložená, a minimální pracovní faktor iterace počet 150 000 smyčky pro eliminaci možnosti výskytu hrubou vynucení.| 

## <a id="db-encrypted"></a>Ujistěte se, že je zašifrovaný citlivá data v databázi sloupce

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | Verze SQL - všechny |
| **Odkazy**              | [Šifrování citlivých dat v systému SQL server](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [postupy: šifrování sloupce dat v systému SQL Server](https://msdn.microsoft.com/library/ms179331), [šifrovat pomocí certifikátu](https://msdn.microsoft.com/library/ms188061) |
| **Kroky** | Citlivá data, jako jsou čísla kreditních karet má šifrování v databázi. Data lze zašifrovat pomocí šifrování na úrovni sloupce nebo funkcí aplikace pomocí funkcí šifrování. |

## <a id="tde-enabled"></a>Ujistěte se, že je povoleno šifrování databáze (TDE)

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Principy SQL Server transparentní šifrování dat (TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Kroky** | Transparentní šifrování dat (TDE) funkce v systému SQL server pomáhá při šifrování citlivých dat v databázi a ochranu klíčů, které slouží k šifrování dat pomocí certifikátu. To brání všem bez klíčů používat data. Transparentní šifrování dat chrání data "neaktivní", což znamená, dat a souborů protokolu. Poskytuje možnost v souladu s mnoha zákonům, předpisům a pokyny uvedenými v různých oborech. |

## <a id="backup"></a>Ujistěte se, že jsou šifrovaná zálohování databáze

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | SQL Azure, OnPrem |
| **Atributy**              | MsSQL2014 verze - V12, verze SQL - SQL |
| **Odkazy**              | [Šifrování záloh SQL database](https://msdn.microsoft.com/library/dn449489) |
| **Kroky** | SQL Server má možnost šifrování dat při vytváření zálohy. Zadáním šifrovací algoritmus a encryptoru (certifikátem nebo Asymentrickým klíčem) při vytváření zálohy, můžete jeden vytvořit šifrovaný záložní soubor. |

## <a id="api-browser"></a>Ujistěte se, že citlivá data, které jsou relevantní pro webové rozhraní API není uložený v úložišti v prohlížeči

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | MVC 5, MVC 6 |
| **Atributy**              | Poskytovatel Identity poskytovatele – služby AD FS, identity – Azure AD |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>V některých implementacích jsou uloženy citlivé artefakty, které jsou relevantní pro webové rozhraní API ověřování v místním úložišti v prohlížeči. Například artefakty ověřování Azure AD, jako je adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key atd.</p><p>Všechno, co tyto artefakty jsou k dispozici i po odhlášení nebo prohlížeč je zavřený. Nežádoucí osoba získá přístup k tyto artefakty, učitelského je mohli znovu použít pro přístup k chráněným prostředkům (API). Ujistěte se, že všechny citlivé artefakty související s webového rozhraní API není uložený v úložišti v prohlížeči. V případech, kdy nevyhnutelné úložiště na straně klienta (například jeden stránka aplikace (SPA), které využívají implicitní OpenIdConnect/OAuth toky potřeba ukládat místně přístupové tokeny), použití možností úložiště nemají trvalosti. například raději SessionStorage LocalStorage.</p>| 

### <a name="example"></a>Příklad:
Níže JavaScript fragment kódu je z knihovny vlastní ověřování, která ukládá ověřovací artefakty v místním úložišti. Mělo by se vyhnout takové implementace. 
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

## <a id="encrypt-docdb"></a>Šifrovat citlivá data uložená ve službě Cosmos DB

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Documentdb | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Šifrování citlivých dat na úrovni aplikace před uložením v documentdb nebo ukládání citlivých dat do jiných řešení úložiště, jako je Azure Storage nebo Azure SQL| 

## <a id="disk-vm"></a>Použití Azure Disk Encryption pro šifrování disků, které jsou používány virtuálními počítači

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti virtuálních počítačů Azure IaaS | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Použití Azure Disk Encryption pro šifrování disků, které jsou používány virtuálními počítači](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Kroky** | <p>Azure Disk Encryption je nová funkce, který je aktuálně ve verzi preview. Tato funkce umožňuje šifrovat disky s operačním systémem a datové disky použít tak virtuální počítač IaaS. Pro Windows jsou šifrované jednotky pomocí standardní technologie šifrování nástrojem BitLocker. Pro Linux disky jsou šifrované pomocí technologii DM-Crypt. To je integrovaná s Azure Key Vault, aby bylo možné řídit a spravovat klíče pro šifrování disků. Řešení Azure Disk Encryption podporuje následující scénáře šifrování tři zákazníka:</p><ul><li>Povoluje šifrování na nové virtuální počítače IaaS vytvořené z zákazníka šifrované soubory virtuálního pevného disku a poskytnuté zákazníkem šifrovací klíče, které jsou uložené ve službě Azure Key Vault.</li><li>Povoluje šifrování na nové virtuální počítače IaaS vytvořené z Azure Marketplace.</li><li>Povoluje šifrování na existující virtuální počítače IaaS, které už běží v Azure.</li></ul>| 

## <a id="fabric-apps"></a>Šifrování tajných kódů aplikace Service Fabric

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Service Fabric | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Odkazy**              | [Správa tajných kódů aplikace Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Kroky** | Tajné klíče může být žádné citlivé údaje, jako je například úložiště připojovací řetězce, hesla nebo jiné hodnoty, které by neměly být zpracovat ve formátu prostého textu. Použití Azure Key Vault pro správu klíčů a tajných kódů v aplikace service fabric. |

## <a id="modeling-teams"></a>Provádět modelování zabezpečení a použití obchodní jednotky nebo týmy vyžadováno

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Provádět modelování zabezpečení a použití obchodní jednotky nebo týmy vyžadováno |

## <a id="entities"></a>Minimalizovat přístup ke sdílení funkcí na důležité entity

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Minimalizovat přístup ke sdílení funkcí na důležité entity |

## <a id="good-practices"></a>Naučím uživatele na rizicích spojených s postupy vhodné zabezpečení a funkce sdílení Dynamics CRM

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Naučím uživatele na rizicích spojených s postupy vhodné zabezpečení a funkce sdílení Dynamics CRM |

## <a id="exception-mgmt"></a>Zahrnout pravidlo standardů vývoje proscribing zobrazující podrobnosti konfigurace v Správa výjimek

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Zahrnout pravidlo standardů vývoje proscribing s podrobnostmi konfigurace ve správě výjimka mimo vývoje. Test jako součást revize kódu nebo pravidelné kontroly.|

## <a id="sse-preview"></a>Použijte šifrování služby Azure Storage (SSE) pro Data v klidovém stavu (Preview)

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | StorageType – objekt Blob |
| **Odkazy**              | [Šifrování služby Azure Storage pro Data v klidovém stavu (Preview)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Kroky** | <p>Azure šifrování služby Storage (SSE) pro Data v klidovém stavu pomáhá zabezpečit a chránit vaše data pro splnění vaší organizace na zabezpečení a závazky dodržování předpisů. Pomocí této funkce služba Azure Storage automaticky šifruje vaše data před zachováním v úložišti a dešifruje před jejich načtením. Šifrování, dešifrování a správu klíčů je pro uživatele zcela transparentní. SSE platí pouze pro objekty BLOB bloku, objekty BLOB stránky a doplňovací objekty BLOB. Jiné typy dat, včetně tabulky, fronty a soubory, nebudou šifrována.</p><p>Šifrování a dešifrování pracovního postupu:</p><ul><li>Povoluje šifrování na účet úložiště zákazníka</li><li>Když zákazník zapíše nová data (PUT Blob, UMÍSTĚTE blok, VLOŽTE stránku atd.) do úložiště objektů Blob; Při každém zápisu se šifrují pomocí 256bitového šifrování AES, jeden nejsilnějších dostupných variant blokového šifrování</li><li>Pokud zákazník potřebuje pro přístup k datům (získat objekt Blob, atd.), jsou data automaticky dešifrována před vrácením uživateli</li><li>Pokud šifrování je zakázané, nové zápisy jsou již šifrované a existující šifrovaná data zůstává zašifrovaný, dokud uživatel přepsán. Když je šifrování povoleno, budou šifrovány zápisy do úložiště objektů Blob. S uživatelem, při přepínání mezi povolení nebo zákaz šifrování pro účet úložiště se nezmění stav dat</li><li>Všechny šifrovací klíče jsou uložené, zašifrované a spravováno společností Microsoft</li></ul><p>Mějte prosím na paměti, že v tuto chvíli klíče používané k šifrování spravuje Microsoft. Microsoft původně vygeneruje klíče a správě zabezpečeného úložiště klíčů a také pravidelné otočení určené zásadami Microsoftu pro interní. V budoucnu zákazníci můžou získat možnost spravovat jejich vlastní > šifrovací klíče a zadejte cesty migrace z klíčů spravovaných microsoftem na klíče spravované zákazníkem.</p>| 

## <a id="client-storage"></a>Šifrování na straně klienta používá k ukládání citlivých dat ve službě Azure Storage

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Šifrování na straně klienta a služby Azure Key Vault pro Microsoft Azure Storage](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [kurz: šifrování a dešifrování objektů BLOB ve službě Microsoft Azure Storage pomocí Azure Key Vault](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), [bezpečné ukládání dat do objektů Blob v Azure Úložiště s rozšířeními Azure šifrování](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Kroky** | <p>Klientská knihovna pro úložiště Azure pro balíček Nuget pro rozhraní .NET podporuje šifrování dat v rámci klientské aplikace před nahráním do služby Azure Storage a dešifrování dat při stahování do klienta. Knihovna také podporuje integraci se službou Azure Key Vault pro správu klíčů účtu úložiště. Tady je stručný popis toho, jak funguje šifrování na straně klienta:</p><ul><li>Klient služby Azure Storage SDK generuje obsahu šifrovací klíč (CEK), což je použití jednoho jednorázových symetrický klíč</li><li>Zákaznická data se šifrují pomocí této CEK</li><li>CEK je vnořena (zašifrovaný) pomocí šifrovací klíč klíče (KEK). Klíče KEK je identifikovaná identifikátorem klíče a mohou být asymetrický pár klíčů nebo symetrický klíč a může být spravovaný místně nebo uloženy ve službě Azure Key Vault. Klienta úložiště samotné nikdy má přístup k klíče KEK. Vyvolá pouze algoritmus klíče zabalení, která je k dispozici ve službě Key Vault. Zákazníci můžou zvolit pro klíč zabalení a rozbalení chtějí-li použít vlastní zprostředkovatelé</li><li>Šifrovaná data se pak nahrají do služby Azure Storage. Zkontrolujte na odkazy v části odkazy pro podrobnosti nízké úrovně implementace.</li></ul>|

## <a id="pii-phones"></a>Šifrování citlivé nebo identifikovatelné osobní údaje data zapsaná do místního úložiště telefony

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilního klienta | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné, Xamarin  |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Správa nastavení a funkcí v zařízeních pomocí zásad Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies#create-a-configuration-policy), [osobního řetězce klíčů](https://components.xamarin.com/view/square.valet) |
| **Kroky** | <p>Pokud aplikace zapíše citlivé informace, jako jsou identifikovatelné osobní údaje uživatele (e-mailu, telefonní číslo, křestní jméno, příjmení, předvolby atd.) – v systému souborů pro mobilní zařízení, pak se by se měla šifrovat před zápisem do místního systému souborů. Pokud aplikace je podniková aplikace, pak si projděte možnost publikování aplikací pomocí služby Windows Intune.</p>|

### <a name="example"></a>Příklad:
Intune můžete nakonfigurovat následující zásady zabezpečení k ochraně citlivých dat: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Příklad:
Pokud aplikace není podniková aplikace, pak použijte platformu poskytuje úložiště klíčů, řetězce klíčů pro ukládání šifrovacích klíčů, pomocí které kryptografické operaci může provést v systému souborů. Následující fragment kódu ukazuje, jak přístupový klíč z řetězce klíčů pomocí xamarin: 
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

## <a id="binaries-end"></a>Před distribucí koncovým uživatelům obfuskaci generovaný binární soubory

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilního klienta | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Kryptografie obfuskace pro .net](http://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Kroky** | Generovaný binární soubory (sestavení v rámci apk) by měl být matoucí zastavit zpětné analýze sestavení. Nástroje, jako je `CryptoObfuscator` mohou být použity pro tento účel. |

## <a id="cert"></a>Typ clientCredentialType nastaven na certifikátu nebo Windows

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Rozhraní .NET framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Obohacení](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Kroky** | Heslo pro útočníky, kteří zprávy protokolu SOAP, můžete sledovat pomocí hesla ve formátu prostého textu UsernameToken přes nezašifrované kanály zpřístupňuje. Poskytovatelé služeb používajících UsernameToken může přijmout hesla odesílají v prostém textu. Odesílání nezašifrovaná hesla přes nezašifrované kanály mohou vystavit přihlašovací údaje, které útočníci, kteří si zprávu protokolu SOAP, můžete zachytit. | 

### <a name="example"></a>Příklad:
Následující konfigurace poskytovatele služeb WCF používá UsernameToken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Typ clientCredentialType nastaven na certifikátu nebo Windows. 

## <a id="security"></a>Není povolen režim zabezpečení WCF

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecná rozhraní .NET Framework 3 |
| **Atributy**              | Zpráva o režim – přenosu, režimu zabezpečení – zabezpečení |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [Magazine kódu Základy zabezpečení WCF](http://www.codemag.com/article/0611051) |
| **Kroky** | Nebyla definována žádná zabezpečení transport nebo zprávy. Aplikace, které odesílání zpráv bez přenosu nebo zprávy, že zabezpečení zaručit integritu a důvěrnost zpráv. Pokud vazby zabezpečení WCF je nastaven na hodnotu None, přenosu a zprávu zabezpečení jsou zakázané. |

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
Režim zabezpečení přes všechny vazby služby jsou režimy pět možné zabezpečení: 
* Žádné. Vypne zabezpečení. 
* Přenos. Zabezpečení pro vzájemné ověřování a zpráva ochranu přenosu používá. 
* zpráva. Zabezpečení zpráv používá pro vzájemné ověřování a zpráva ochranu. 
* Obojí. Umožňuje zadat nastavení přenosu a zabezpečení na úrovni zpráv (MSMQ pouze podporuje). 
* TransportWithMessageCredential. Přihlašovací údaje jsou předány zprávou a ochrany zprávy a ověření serveru jsou k dispozici v přenosové vrstvě. 
* TransportCredentialOnly. Přihlašovací údaje pro klienta se předávají pomocí přenosové vrstvy a použití ochrany žádné zprávy. Pomocí zabezpečení přenosu a zprávy můžete chránit integritu a důvěrnost zpráv. Konfigurace níže informuje službu service pro použití zabezpečení přenosu s přihlašovacími údaji zprávy.
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
