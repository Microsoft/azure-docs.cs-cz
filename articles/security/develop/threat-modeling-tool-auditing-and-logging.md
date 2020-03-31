---
title: Auditování a protokolování – Nástroj pro modelování hrozeb společnosti Microsoft – Azure | Dokumenty společnosti Microsoft
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
ms.openlocfilehash: c9d20b3259cf4ea7af263d5e31145ad372db0c77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728416"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Rámec zabezpečení: Auditování a protokolování | Skutečnosti snižující závažnost rizika 

| Produkt/služba | Článek |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identifikace citlivých entit ve vašem řešení a implementace auditování změn](#sensitive-entities)</li></ul> |
| **Webová aplikace** | <ul><li>[Ujistěte se, že auditování a protokolování je vynuceno na aplikaci](#auditing)</li><li>[Ujistěte se, že je na místě otáčení a separace protokolu](#log-rotation)</li><li>[Ujistěte se, že aplikace nezaznamenává citlivá uživatelská data](#log-sensitive-data)</li><li>[Ujistěte se, že soubory auditu a protokolu mají omezený přístup](#log-restricted-access)</li><li>[Ujistěte se, že jsou zaznamenány události správy uživatelů](#user-management)</li><li>[Ujistěte se, že systém má vestavěnou obranu proti zneužití](#inbuilt-defenses)</li><li>[Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service](#diagnostics-logging)</li></ul> |
| **Databáze** | <ul><li>[Ujistěte se, že je na serveru SQL Server povoleno auditování přihlášení.](#identify-sensitive-entities)</li><li>[Povolení zjišťování hrozeb v Azure SQL](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Použití Azure Storage Analytics k auditování přístupu k Azure Storage](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementace dostatečného protokolování](#sufficient-logging)</li><li>[Implementace dostatečného zpracování selhání auditu](#audit-failure-handling)</li></ul> |
| **Web API** | <ul><li>[Zajištění vynucení auditování a protokolování ve webovém rozhraní API](#logging-web-api)</li></ul> |
| **Brána ioT pole** | <ul><li>[Zajištění vynucení příslušného auditování a protokolování v bráně pole](#logging-field-gateway)</li></ul> |
| **Cloudová brána IoT** | <ul><li>[Zajistěte, aby se v cloudové bráně vynucoval yvhodný auditování a protokolování.](#logging-cloud-gateway)</li></ul> |

## <a name="identify-sensitive-entities-in-your-solution-and-implement-change-auditing"></a><a id="sensitive-entities"></a>Identifikace citlivých entit ve vašem řešení a implementace auditování změn

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky**                   | Identifikujte entity ve vašem řešení obsahující citlivá data a implementujte auditování změn u těchto entit a polí. |

## <a name="ensure-that-auditing-and-logging-is-enforced-on-the-application"></a><a id="auditing"></a>Ujistěte se, že auditování a protokolování je vynuceno na aplikaci

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky**                   | Povolte auditování a protokolování všech součástí. Protokoly auditu by měly zachytit kontext uživatele. Identifikujte všechny důležité události a protokolujte tyto události. Implementace centralizovaného protokolování |

## <a name="ensure-that-log-rotation-and-separation-are-in-place"></a><a id="log-rotation"></a>Ujistěte se, že je na místě otáčení a separace protokolu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky**                   | <p>Střídání protokolů je automatizovaný proces používaný při správě systému, ve kterém jsou archivovány datované soubory protokolu. Servery, které spouštějí velké aplikace často protokolu každý požadavek: tváří v tvář objemné protokoly, log rotace je způsob, jak omezit celkovou velikost protokolů a zároveň umožňuje analýzu nedávných událostí. </p><p>Oddělení protokolu v podstatě znamená, že budete muset uložit soubory protokolu na jiný oddíl, protože kde váš operační systém / aplikace běží na s cílem odvrátit útok odmítnutí služby nebo snížení stupně utajení vaší aplikace jeho výkon</p>|

## <a name="ensure-that-the-application-does-not-log-sensitive-user-data"></a><a id="log-sensitive-data"></a>Ujistěte se, že aplikace nezaznamenává citlivá uživatelská data

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky**                   | <p>Zkontrolujte, zda nezaznamenáváte žádná citlivá data, která uživatel odešle na váš web. Zkontrolujte, zda záměrné protokolování, stejně jako vedlejší účinky způsobené problémy s návrhem. Příklady citlivých údajů zahrnují:</p><ul><li>Pověření uživatele</li><li>Číslo sociálního pojištění nebo jiné identifikační údaje</li><li>Čísla kreditních karet nebo jiné finanční informace</li><li>Informace o zdravotním stavu</li><li>Soukromé klíče nebo jiná data, která by mohla být použita k dešifrování šifrovaných informací</li><li>Informace o systému nebo aplikaci, které lze použít k efektivnějšímu útoku na aplikaci</li></ul>|

## <a name="ensure-that-audit-and-log-files-have-restricted-access"></a><a id="log-restricted-access"></a>Ujistěte se, že soubory auditu a protokolu mají omezený přístup

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky**                   | <p>Zkontrolujte, zda jsou správně nastavena přístupová práva k souborům protokolu. Účty aplikací by měly mít přístup jen pro zápis a operátoři a pracovníci podpory by měli mít přístup jen pro čtení podle potřeby.</p><p>Účty správců jsou jedinými účty, které by měly mít plný přístup. Zkontrolujte, zda windows ACL na soubory protokolu, aby se ujistil, že jsou řádně omezeny:</p><ul><li>Účty aplikací by měly mít přístup pouze pro zápis</li><li>Operátoři a pracovníci podpory by měli mít podle potřeby přístup jen pro čtení</li><li>Správci jsou jediné účty, které by měly mít úplný přístup</li></ul>|

## <a name="ensure-that-user-management-events-are-logged"></a><a id="user-management"></a>Ujistěte se, že jsou zaznamenány události správy uživatelů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky**                   | <p>Ujistěte se, že aplikace monitoruje události správy uživatelů, jako jsou úspěšná a neúspěšná přihlášení uživatelů, resetování hesla, změny hesel, uzamčení účtu, registrace uživatelů. To pomáhá odhalit a reagovat na potenciálně podezřelé chování. Umožňuje také shromažďovat provozní data; například sledovat, kdo přistupuje k aplikaci</p>|

## <a name="ensure-that-the-system-has-inbuilt-defenses-against-misuse"></a><a id="inbuilt-defenses"></a>Ujistěte se, že systém má vestavěnou obranu proti zneužití

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky**                   | <p>Měly by být zavedeny ovládací prvky, které v případě zneužití aplikace vyvádí výjimku zabezpečení. Je-li například zavedeno ověření vstupu a útočník se pokusí vložit škodlivý kód, který neodpovídá regulárnímu výrazu, může být vyvolána výjimka zabezpečení, která může svědčit o zneužití systému.</p><p>Například se doporučuje mít protokolované výjimky zabezpečení a akce provedené pro následující problémy:</p><ul><li>Ověření vstupu</li><li>CsRF porušení</li><li>Hrubá síla (horní limit pro počet požadavků na uživatele na prostředek)</li><li>Porušení nahrávání souborů</li><ul>|

## <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a><a id="diagnostics-logging"></a>Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Azure poskytuje integrovanou diagnostiku, která vám pomůže s laděním webové aplikace Služby aplikací. Platí také pro aplikace rozhraní API a mobilní aplikace. Webové aplikace Služby App Service poskytují diagnostické funkce pro protokolování informací z webového serveru i webové aplikace.</p><p>Ty jsou logicky rozděleny do diagnostiky webových serverů a diagnostiky aplikací.</p>|

## <a name="ensure-that-login-auditing-is-enabled-on-sql-server"></a><a id="identify-sensitive-entities"></a>Ujistěte se, že je na serveru SQL Server povoleno auditování přihlášení.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Konfigurace auditování přihlášení](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Kroky** | <p>Auditování přihlášení databázového serveru musí být povoleno, aby bylo možné rozpoznat/potvrdit útoky na hádání hesel. Je důležité zachytit neúspěšné pokusy o přihlášení. Zachycení úspěšných i neúspěšných pokusů o přihlášení poskytuje další výhodu během forenzního vyšetřování</p>|

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Povolení zjišťování hrozeb v Azure SQL

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | SQL Azure |
| **Atributy**              | Verze SQL - V12 |
| **Odkazy**              | [Začínáme s detekcí hrozeb databáze SQL](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Kroky** |<p>Detekce hrozeb detekuje neobvyklé databázové aktivity označující potenciální ohrožení zabezpečení databáze. Poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje detekovat potenciální hrozby a reagovat na ně tak, jak se vyskytují, a to poskytováním výstrah zabezpečení na neobvyklé aktivity.</p><p>Uživatelé mohou prozkoumat podezřelé události pomocí Azure SQL Database Auditing k určení, pokud jsou výsledkem pokusu o přístup, porušení nebo zneužití dat v databázi.</p><p>Detekce hrozeb usnadňuje řešení potenciálních hrozeb pro databázi bez nutnosti být odborníkem na zabezpečení nebo spravovat pokročilé systémy monitorování zabezpečení</p>|

## <a name="use-azure-storage-analytics-to-audit-access-of-azure-storage"></a><a id="analytics"></a>Použití Azure Storage Analytics k auditování přístupu k Azure Storage

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné. |
| **Odkazy**              | [Použití analýzy úložiště ke sledování typu autorizace](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Kroky** | <p>Pro každý účet úložiště můžete povolit Azure Storage Analytics k provádění dat protokolování a ukládání metrik. Protokoly analýzy úložiště poskytují důležité informace, jako je například metoda ověřování používaná někým při přístupu k úložišti.</p><p>To může být opravdu užitečné, pokud jste pevně střežit přístup k úložišti. Například v úložišti objektů Blob můžete nastavit všechny kontejnery na soukromé a implementovat použití služby SAS v rámci vašich aplikací. Potom můžete pravidelně kontrolovat protokoly a zjistit, jestli jsou k vašim objektům BLOB přístup pomocí klíčů účtu úložiště, což může znamenat porušení zabezpečení, nebo pokud jsou objekty BLOB veřejné, ale neměly by být.</p>|

## <a name="implement-sufficient-logging"></a><a id="sufficient-logging"></a>Implementace dostatečného protokolování

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | .NET Framework |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Opevnitkrálovství](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Kroky** | <p>Nedostatek řádného auditnístopy po bezpečnostním incidentu může bránit forenznímu úsilí. Windows Communication Foundation (WCF) nabízí možnost protokolovat úspěšné nebo neúspěšné pokusy o ověření.</p><p>Protokolování neúspěšných pokusů o ověření může upozornit správce na potenciální útoky hrubou silou. Podobně protokolování úspěšných událostí ověřování může poskytnout užitečné auditní stopy při ohrožení zabezpečení legitimního účtu. Povolení funkce auditu zabezpečení služby WCF |

### <a name="example"></a>Příklad
Následuje ukázková konfigurace s povoleným auditováním.
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

## <a name="implement-sufficient-audit-failure-handling"></a><a id="audit-failure-handling"></a>Implementace dostatečného zpracování selhání auditu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | .NET Framework |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Opevnitkrálovství](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Kroky** | <p>Vyvinuté řešení je nakonfigurováno tak, aby negenerovalo výjimku, pokud se nepodaří zapsat do protokolu auditu. Pokud wcf je nakonfigurován tak, aby vyvolat výjimku, když není schopen zapisovat do protokolu auditu, program nebude upozorněn na selhání a auditování kritických událostí zabezpečení nemusí dojít.</p>|

### <a name="example"></a>Příklad
Prvek `<behavior/>` konfiguračního souboru WCF níže pokyn WCF neupozorní aplikaci, když WCF nepodaří zapsat do protokolu auditu.
```
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
```
Nakonfigurujte wcf upozornit program vždy, když není schopen zapisovat do protokolu auditu. Program by měl mít alternativní schéma oznámení na místě upozornit organizaci, že audit stezky nejsou udržovány. 

## <a name="ensure-that-auditing-and-logging-is-enforced-on-web-api"></a><a id="logging-web-api"></a>Zajištění vynucení auditování a protokolování ve webovém rozhraní API

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Povolte auditování a protokolování webových api. Protokoly auditu by měly zachytit kontext uživatele. Identifikujte všechny důležité události a protokolujte tyto události. Implementace centralizovaného protokolování |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-field-gateway"></a><a id="logging-field-gateway"></a>Zajištění vynucení příslušného auditování a protokolování v bráně pole

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána ioT pole | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Když se k bráně pole připojí více zařízení, ujistěte se, že pokusy o připojení a stav ověřování (úspěch nebo neúspěch) pro jednotlivá zařízení jsou zaznamenány a udržovány v bráně pole.</p><p>Také v případech, kdy Field Gateway udržuje pověření služby IoT Hub pro jednotlivá zařízení, ujistěte se, že auditování se provádí při načítání těchto pověření. Vytvořte proces, který pravidelně nahrává protokoly do služby Azure IoT Hub/storage pro dlouhodobé uchovávání informací.</p> |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-cloud-gateway"></a><a id="logging-cloud-gateway"></a>Zajistěte, aby se v cloudové bráně vynucoval yvhodný auditování a protokolování.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Cloudová brána IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Úvod do monitorování operací ioT hubu](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Kroky** | <p>Návrh pro shromažďování a ukládání dat auditu shromážděných prostřednictvím monitorování provozu ioT hubu. Povolte následující kategorie monitorování:</p><ul><li>Operace identity zařízení</li><li>Komunikace mezi zařízeními a cloudem</li><li>Komunikace mezi cloudy</li><li>Připojení</li><li>Nahrání souborů</li></ul>|