---
title: Auditování a protokolování Azure - modelování nástroj Microsoft Threat - | Microsoft Docs
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
ms.openlocfilehash: 8837dfaf156e5a4d07598f2c58694663a9ff5580
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029977"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Zabezpečení rámce: Auditování a protokolování | Způsoby zmírnění rizik 
| Produktům a službám | Článek |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identifikovat citlivé entity ve vašem řešení a implementovat auditování změn](#sensitive-entities)</li></ul> |
| **Webové aplikace** | <ul><li>[Ujistěte se, že auditování a protokolování je vynucená u aplikace](#auditing)</li><li>[Zkontrolujte, zda jsou na místě oběh protokolu a oddělení](#log-rotation)</li><li>[Ujistěte se, aplikace nejsou přihlašujete citlivá uživatelská data](#log-sensitive-data)</li><li>[Ujistěte se, že auditu a soubory protokolů mají omezený přístup](#log-restricted-access)</li><li>[Ujistěte se, že jsou zaznamenány události správy uživatelů](#user-management)</li><li>[Zkontrolujte, zda má systém integrované obranu proti zneužití](#inbuilt-defenses)</li><li>[Povolit protokolování diagnostiky pro webové aplikace v Azure App Service](#diagnostics-logging)</li></ul> |
| **Database** | <ul><li>[Ujistěte se, že přihlášení auditování je povolena v systému SQL Server](#identify-sensitive-entities)</li><li>[Povolení detekce hrozeb v Azure SQL](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Použití Azure Storage Analytics auditovat přístup k Azure Storage](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementace dostatečná protokolování](#sufficient-logging)</li><li>[Implementace dostatečná auditu selhání zpracování](#audit-failure-handling)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Ujistěte se, že se vynucuje auditování a protokolování u webového rozhraní API](#logging-web-api)</li></ul> |
| **Brána pole IoT** | <ul><li>[Ujistěte se, že odpovídající auditování a protokolování se vynucuje u brána pole](#logging-field-gateway)</li></ul> |
| **Brána IoT cloudu** | <ul><li>[Ujistěte se, že odpovídající auditování a protokolování se vynucuje u brány cloudu](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>Identifikovat citlivé entity ve vašem řešení a implementovat auditování změn

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky**                   | Určit entity ve vašem řešení obsahující citlivá data a implementovat auditování změn v těchto entit a polí |

## <a id="auditing"></a>Ujistěte se, že auditování a protokolování je vynucená u aplikace

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky**                   | Povolte auditování a protokolování na všechny součásti. Protokoly auditu měli zaznamenat kontextu uživatele. Identifikujte všechny důležité události a zaznamenat tyto události do protokolu. Implementace centralizované protokolování |

## <a id="log-rotation"></a>Zkontrolujte, zda jsou na místě oběh protokolu a oddělení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky**                   | <p>Oběh protokolu se používá v systému správy, ve kterém datem protokolu soubory jsou archivovány automatizovaný proces. Servery, které používají velké aplikace často protokolovat každou žádost: při krátkodobém objemné protokoly oběh protokolu je způsob, jak omezit celkovou velikost protokolů zároveň umožňuje analýzu poslední události. </p><p>Oddělení protokolu v podstatě znamená, že máte k uložení protokolu souborů do různých oddílů jako kde operačního systému nebo aplikace běží, aby zabránily útoku DOS nebo Downgrade aplikace jeho výkon</p>|

## <a id="log-sensitive-data"></a>Ujistěte se, aplikace nejsou přihlašujete citlivá uživatelská data

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky**                   | <p>Zkontrolujte, jestli připojovat všechny důvěrné osobní údaje, které uživatel odešle na váš web. Kontrola úmyslné protokolování, jakož i vedlejší účinky, které jsou způsobeny problémy návrhu. Příklady citlivých dat:</p><ul><li>Přihlašovací údaje uživatele</li><li>Číslo sociálního pojištění nebo jiné identifikační údaje</li><li>Čísla platebních karet nebo jiné finanční informace</li><li>Informace o stavu</li><li>Privátní klíče nebo jiná data, která by mohla být použité k dešifrování šifrovaných informací</li><li>Systém nebo aplikace, informace, které umožňuje efektivněji útokům aplikace</li></ul>|

## <a id="log-restricted-access"></a>Ujistěte se, že auditu a soubory protokolů mají omezený přístup

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky**                   | <p>Zkontrolujte přístupová práva do souborů protokolu jsou správně nastaveny. Účty aplikace by měl mít přístup jen pro zápis a operátory a pracovníky technické podpory by měl mít přístup jen pro čtení, podle potřeby.</p><p>Účty správců jsou pouze účty, které by měl mít úplný přístup. Zkontrolujte seznamů ACL systému Windows pro soubory protokolu pro jistotu, že jsou správně s omezeným přístupem:</p><ul><li>Účty aplikace mají mít přístup jen pro zápis</li><li>Operátory a pracovníky technické podpory musí mít oprávnění jen pro čtení, podle potřeby</li><li>Správci jsou pouze účty, které by měl mít úplný přístup</li></ul>|

## <a id="user-management"></a>Ujistěte se, že jsou zaznamenány události správy uživatelů

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky**                   | <p>Zkontrolujte, zda aplikace monitoruje událostí správy uživatele jako je přihlášení úspěšné i neúspěšné uživatelů, resetování hesel, změny hesel, uzamčení účtu, registrace uživatele. Díky této pomáhají zjistit a reagovat na potenciálně podezřelého chování. Taky umožňuje shromažďovat data operace; například sledovat, kdo přistupuje k aplikaci</p>|

## <a id="inbuilt-defenses"></a>Zkontrolujte, zda má systém integrované obranu proti zneužití

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky**                   | <p>Ovládací prvky musí být na místě, které vyvolávají výjimka zabezpečení v případě zneužití aplikací. Například pokud je ověření vstupu na svém místě a útočník pokusí vložit škodlivého kódu, který neodpovídá regulární výraz, vyvolána výjimka zabezpečení může být může být předběžné zneužít systému</p><p>Například se doporučuje mít výjimky zabezpečení přihlášení a akce prováděné na následující problémy:</p><ul><li>Ověření vstupu</li><li>Porušení proti útokům CSRF</li><li>Útok hrubou silou (horní limit pro počet požadavků na uživatele na prostředků)</li><li>Narušení nahrávání souborů</li><ul>|

## <a id="diagnostics-logging"></a>Povolit protokolování diagnostiky pro webové aplikace v Azure App Service

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Azure nabízí integrovanou diagnostiku, která vám pomůže s laděním webových aplikací App Service. Platí také pro rozhraní API a mobilní aplikace. Webové aplikace služby App Service poskytují diagnostické funkce pro protokolování informací z webového serveru a webové aplikace.</p><p>Tyto jsou logicky rozdělené do webového serveru diagnostics a application diagnostics</p>|

## <a id="identify-sensitive-entities"></a>Ujistěte se, že přihlášení auditování je povolena v systému SQL Server

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Konfigurace auditování přihlášení](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Kroky** | <p>Auditování databáze serveru přihlášení musí být povolena pro detekci potvrďte za účelem uhodnutí hesla útoky. Je důležité pro zachycení neúspěšných pokusů o přihlášení. Další výhodou zaznamenávání obou pokusů o přihlášení úspěšné i neúspěšné poskytuje během forenzního vyšetřování</p>|

## <a id="threat-detection"></a>Povolení detekce hrozeb v Azure SQL

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | SQL Azure |
| **Atributy**              | Verze SQL - V12 |
| **Odkazy**              | [Začínáme s detekce hrozeb databáze SQL](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Kroky** |<p>Detekce hrozeb zjistila nezvyklé databázové aktivity, které indikují potenciální ohrožení databáze. Poskytuje novou vrstvu zabezpečení, která uživatelům umožňuje zjistit a reagovat na potenciální hrozby, kdy k nim dojde tím, že poskytuje výstrahy zabezpečení na neobvyklé aktivity.</p><p>Uživatele můžete prozkoumat podezřelé události pomocí audity Azure SQL Database k určení, pokud vyplývají z pokus o přístup, porušení nebo využívat data v databázi.</p><p>Detekce hrozeb zjednodušuje na potenciální hrozby adres do databáze bez nutnosti odborné zabezpečení nebo spravovat pokročilým zabezpečením monitorování systémů</p>|

## <a id="analytics"></a>Použití Azure Storage Analytics auditovat přístup k Azure Storage

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno |
| **Odkazy**              | [Pomocí Storage Analytics k monitorování typ autorizace](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Kroky** | <p>Pro každý účet úložiště jeden můžete povolit Azure Storage Analytics k provedení protokolování a ukládání dat metriky. Protokoly analýzy úložiště obsahují důležité informace, jako je například metodu ověřování používanou někdo při přístupu k úložišti.</p><p>To může být velmi užitečné, pokud jsou úzce ochrana přístup k úložišti. V úložišti objektů Blob můžete například nastavit všechny kontejnery do privátního a implementovat použití služby SAS v celé vaší aplikace. Zkontrolujte protokoly pravidelně zobrazíte Pokud objektů BLOB ke kterým se přistupuje pomocí klíče účtu úložiště, které mohou indikovat porušení zabezpečení, nebo pokud jsou veřejné objekty BLOB, ale nemělo by být.</p>|

## <a id="sufficient-logging"></a>Implementace dostatečná protokolování

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | .NET Framework |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Kroky** | <p>Nedostatečná správné kontrolní záznam incidentu zabezpečení může zabránit spuštění forenzní úsilí. Windows Communication Foundation (WCF) nabízí schopnost protokolovat pokusy o ověření úspěšné nebo neúspěšné.</p><p>Protokolování neúspěšných pokusů o přihlášení může upozornit, že správci potenciální útoky hrubou silou. Protokolování událostí úspěšné ověření podobně, zadejte záznam pro audit užitečné při ohrožení legitimní účtu. Povolení funkce auditu zabezpečení služby WCF na |

### <a name="example"></a>Příklad:
Tady je příklad konfigurace auditování povolené
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a id="audit-failure-handling"></a>Implementace dostatečná auditu selhání zpracování

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | .NET Framework |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Kroky** | <p>Vyvinuté řešení je nakonfigurován tak, aby se vygeneruje výjimka, když se nepodaří zápis do protokolu auditu. Pokud WCF je nakonfigurován tak, aby způsobí výjimku, pokud nelze zapisovat do protokolu auditování, program nebude informováni o selhání a může přestat docházet k auditování událostí zabezpečení.</p>|

### <a name="example"></a>Příklad:
`<behavior/>` Prvek konfiguračního souboru WCF, které jsou níže dá pokyn WCF není upozornit aplikaci, když selže WCF k zápisu do protokolu auditování.
````
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
````
Nakonfigurujte WCF oznámit program vždy, když nelze zapisovat do protokolu auditování. Program by měl mít příslušné schéma alternativní oznámení výstrahy nejsou neudržují organizace, která audit záznamům. 

## <a id="logging-web-api"></a>Ujistěte se, že se vynucuje auditování a protokolování u webového rozhraní API

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Povolte auditování a protokolování na webovým rozhraním API. Protokoly auditu měli zaznamenat kontextu uživatele. Identifikujte všechny důležité události a zaznamenat tyto události do protokolu. Implementace centralizované protokolování |

## <a id="logging-field-gateway"></a>Ujistěte se, že odpovídající auditování a protokolování se vynucuje u brána pole

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána pole IoT | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Když více zařízení připojit k bráně pole, zajistěte, aby pokusy o připojení a stavu ověření (úspěch nebo neúspěch) pro jednotlivá zařízení jsou přihlášení a udržuje na bráně pole.</p><p>Také v případech, kde je brána pole zachování IoT Hub přihlašovací údaje pro jednotlivá zařízení, ujistěte se, že auditování se provádí tyto přihlašovací údaje jsou načteny. Vytvořte proces pro protokoly pravidelně nahrát do Azure IoT Hub za úložiště pro dlouho období uchování.</p> |

## <a id="logging-cloud-gateway"></a>Ujistěte se, že odpovídající auditování a protokolování se vynucuje u brány cloudu

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT cloudu | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Úvod do služby IoT Hub operations monitorování](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Kroky** | <p>Návrh pro shromažďování a ukládání dat auditu shromáždit prostřednictvím monitorování nástroje Operations IoT Hub. Povolte monitorování následujících kategorií:</p><ul><li>Operace identity zařízení</li><li>Komunikace zařízení cloud</li><li>Komunikace cloud zařízení</li><li>Připojení</li><li>Nahrávání souborů</li></ul>|