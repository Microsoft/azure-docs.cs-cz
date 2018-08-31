---
title: Auditování a protokolování – modelování nástroj Microsoft Threat – Azure | Dokumentace Microsoftu
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
ms.openlocfilehash: 990f300055f7c0c7132dd44271dea73044649fc5
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306994"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Zabezpečení Frame: Auditování a protokolování | Zmírnění rizik 
| Produkt nebo službu | Článek |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Určení citlivých entit ve vašem řešení a provádět audit změn](#sensitive-entities)</li></ul> |
| **Webové aplikace** | <ul><li>[Ujistěte se, že auditování a protokolování je vynucená u aplikace](#auditing)</li><li>[Ujistěte se, že jsou splněné rotace protokolů a oddělení](#log-rotation)</li><li>[Ujistěte se, že aplikace neprotokoluje citlivými daty](#log-sensitive-data)</li><li>[Ujistěte se, že auditu a soubory protokolů mají omezený přístup](#log-restricted-access)</li><li>[Ujistěte se, že jsou zaznamenány události správy uživatele](#user-management)</li><li>[Ujistěte se, že systém má integrované obrana proti zneužití](#inbuilt-defenses)</li><li>[Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service](#diagnostics-logging)</li></ul> |
| **Database** | <ul><li>[Ujistěte se, že je povolené auditování přihlášení na SQL serveru](#identify-sensitive-entities)</li><li>[Povolit detekci hrozeb v Azure SQL](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Auditovat přístup k Azure Storage pomocí Azure Storage Analytics](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementace dostatečná protokolování](#sufficient-logging)</li><li>[Implementace dostatečná auditu selhání zpracování](#audit-failure-handling)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Ujistěte se, že je pro webové rozhraní API vynucovat auditování a protokolování](#logging-web-api)</li></ul> |
| **Brána IoT pole** | <ul><li>[Ujistěte se, že pro hraniční brána je vynucovat odpovídající auditování a protokolování](#logging-field-gateway)</li></ul> |
| **Brána IoT cloudu** | <ul><li>[Ujistěte se, že je pro Cloudová brána vynucovat odpovídající auditování a protokolování](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>Určení citlivých entit ve vašem řešení a provádět audit změn

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky**                   | Identifikace entit ve vašem řešení obsahující citlivá data a provádět audit změn u těchto entit a polí |

## <a id="auditing"></a>Ujistěte se, že auditování a protokolování je vynucená u aplikace

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky**                   | Povolte auditování a protokolování pro všechny komponenty. Protokoly auditu by měl vystihnout kontextu uživatele. Identifikujte všechny důležité události a zaznamenávat tyto události. Implementace centralizované protokolování |

## <a id="log-rotation"></a>Ujistěte se, že jsou splněné rotace protokolů a oddělení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky**                   | <p>Rotace protokolů je automatizovaný proces používaných pro správu systému, ve kterém jsou archivovány průkaz s datem protokolu souborů. Servery, které běží velké aplikace často protokolovat každou žádost: i v případě objemné protokoly rotace protokolů je způsob, jak omezit celkovou velikost protokoly zároveň umožní analýzy nedávné události. </p><p>Protokolovat oddělení v podstatě znamená, že budete muset uložit protokol soubory na jiný oddíl jako kde operačního systému/aplikace běží, aby bylo možné zhroutil útoku DOS nebo při downgradu aplikace jeho výkonu</p>|

## <a id="log-sensitive-data"></a>Ujistěte se, že aplikace neprotokoluje citlivými daty

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky**                   | <p>Kontrola protokolu citlivá data, která uživatel odešle na váš web. Zkontrolujte protokolování úmyslné, jakož i vedlejší účinky, které jsou způsobeny problémy návrhu. Příklady citlivá data:</p><ul><li>Přihlašovací údaje uživatele</li><li>Číslo sociálního pojištění nebo identifikovatelné informace</li><li>Čísla platebních karet nebo jiných finančních informací</li><li>Informace o stavu</li><li>Privátní klíče nebo jiná data, která se dá použít k dešifrování šifrovaných informací</li><li>Systém nebo aplikace, informace, které lze použít k efektivněji útoku na aplikace</li></ul>|

## <a id="log-restricted-access"></a>Ujistěte se, že auditu a soubory protokolů mají omezený přístup

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky**                   | <p>Zkontrolujte přístupová práva k soubory protokolu jsou správně nastavené. Aplikace účty by neměly mít přístup jen pro zápis a operátory a pracovníci podpory by měl mít přístup jen pro čtení, podle potřeby.</p><p>Účty správců jsou to jediné účty, které by měl mít úplný přístup. Zkontrolujte Windows seznam ACL pro soubory protokolu ověřte, že jsou správně s omezeným přístupem:</p><ul><li>Aplikace účty by neměly mít přístup jen pro zápis</li><li>Operátory a pracovníci podpory by měly mít přístup jen pro čtení, podle potřeby</li><li>Správci jsou to jediné účty, které by měl mít úplný přístup</li></ul>|

## <a id="user-management"></a>Ujistěte se, že jsou zaznamenány události správy uživatele

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky**                   | <p>Ujistěte se, že aplikace sleduje události správy uživatele jako je přihlášení úspěšné i neúspěšné uživatelů, resetování hesla, změny hesel, uzamčení účtu, registrace uživatele. Provádí se to pomáhá detekovat a reagovat na potenciálně podezřelé chování. Umožňuje také shromažďovat data operace; například chcete sledovat, kdo přistupuje k aplikaci</p>|

## <a id="inbuilt-defenses"></a>Ujistěte se, že systém má integrované obrana proti zneužití

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky**                   | <p>Ovládací prvky, měly by být vestavěny, která výjimku zabezpečení v případě zneužití aplikací. Například pokud ověření vstupu je na místě a útočník pokusí injektovat škodlivý kód, který neodpovídá regulárnímu, že výjimka zabezpečení mohou být vyvolány může být předběžné zneužití systému</p><p>Například se doporučuje mít zabezpečení výjimek zaznamenaných a akce prováděné na následující problémy:</p><ul><li>Ověření vstupu</li><li>Porušení CSRF</li><li>Hrubá síla (horní limit pro počet požadavků za uživatele a prostředků)</li><li>Narušení nahrávání souborů</li><ul>|

## <a id="diagnostics-logging"></a>Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Azure nabízí integrovanou diagnostiku, která vám pomůže s laděním webových aplikací App Service. Platí také pro aplikace API a mobilní aplikace. Aplikace služby App Service web apps poskytují diagnostické funkce pro protokolování informací z webového serveru a webové aplikace.</p><p>Jsou logicky oddělené do webového serveru diagnostiky a application diagnostics</p>|

## <a id="identify-sensitive-entities"></a>Ujistěte se, že je povolené auditování přihlášení na SQL serveru

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Konfigurace auditování přihlášení](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Kroky** | <p>Auditování přihlášení serveru databáze musí být povolena pro zjišťování a potvrďte heslo opakovaně uhodnout útoky. Je třeba zaznamenat neúspěšné pokusy o přihlášení. Zachytávání obě přihlášení úspěšné i neúspěšné pokusy o poskytuje další výhody během forenzní vyšetřování</p>|

## <a id="threat-detection"></a>Povolit detekci hrozeb v Azure SQL

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | SQL Azure |
| **Atributy**              | Verze SQL - V12 |
| **Odkazy**              | [Začínáme s detekcí hrozeb služby SQL Database](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Kroky** |<p>Detekce hrozeb detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení databáze. Poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje detekovat a reagovat na potenciální hrozby, jak se objeví díky poskytování upozornění zabezpečení na neobvyklé aktivity.</p><p>Uživatelé můžou prozkoumat podezřelé události pomocí auditování služby Azure SQL Database k určení, jestli jsou důsledkem pokus o přístup, narušení nebo zneužití dat v databázi.</p><p>Detekce hrozeb usnadňuje řešení potenciálních ohrožení databáze, aniž byste museli být odborné zabezpečení nebo spravovat pokročilé systémy monitorování zabezpečení</p>|

## <a id="analytics"></a>Auditovat přístup k Azure Storage pomocí Azure Storage Analytics

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno |
| **Odkazy**              | [Použití Storage Analytics k monitorování typ autorizace](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Kroky** | <p>Pro každý účet úložiště jeden povolit Azure Storage Analytics provádět protokolování a ukládat data metrik. Protokoly analýzy úložiště obsahují důležité informace, jako je například metodu ověřování používanou někdo při přístupu k úložišti.</p><p>To může být velmi užitečné, pokud jsou úzce důslednější přístup k úložišti. V úložišti objektů Blob můžete například nastavit všechny kontejnery do privátního a implementovat pomocí SAS služby v rámci vašich aplikací. Potom můžete zkontrolovat protokoly pravidelně a zda objektů BLOB jsou přístupné pomocí klíče účtu úložiště, což může znamenat, že porušení zabezpečení, nebo pokud objekty BLOB byly veřejné, ale neměly by být.</p>|

## <a id="sufficient-logging"></a>Implementace dostatečná protokolování

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | .NET Framework |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Kroky** | <p>Nedostatek správné auditu incidentu zabezpečení může omezovat forenzní úsilí. Windows Communication Foundation (WCF) nabízí schopnost protokolovat pokusy o ověření úspěšné nebo neúspěšné.</p><p>Protokolování neúspěšných pokusů o přihlášení může upozornit správce potenciální útoky hrubou silou. Protokolování událostí úspěšného ověření obdobně poskytují užitečné auditu když dojde k narušení legitimní účet. Povolení funkce auditu zabezpečení služby WCF. |

### <a name="example"></a>Příklad:
Tady je příklad konfigurace s auditováním služby povolena
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
| **Použitelných technologiích** | .NET Framework |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Kroky** | <p>Vyvinuté řešení je nakonfigurován tak, aby vygeneruje výjimku, pokud se nezdaří pro zápis do protokolu auditu. Pokud WCF je nakonfigurován tak, aby vyvolat výjimku, pokud nemůže zapisovat do protokolu auditu, program nebudete nijak upozorněni chyby a auditování událostí zabezpečení nedojde.</p>|

### <a name="example"></a>Příklad:
`<behavior/>` Element konfigurační soubor WCF níže dá pokyn WCF není upozornit aplikaci WCF se nepodařilo zapsat do protokolu auditu.
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
Konfigurace WCF oznámení programu pokaždé, když nelze zapisovat do protokolu auditu. Program by měl mít schéma alternativní oznámení výstrahy nejsou dodržovány organizace, která záznamy pro audit. 

## <a id="logging-web-api"></a>Ujistěte se, že je pro webové rozhraní API vynucovat auditování a protokolování

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Povolte auditování a protokolování na webová rozhraní API. Protokoly auditu by měl vystihnout kontextu uživatele. Identifikujte všechny důležité události a zaznamenávat tyto události. Implementace centralizované protokolování |

## <a id="logging-field-gateway"></a>Ujistěte se, že pro hraniční brána je vynucovat odpovídající auditování a protokolování

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT pole | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Když více zařízení připojit k bráně pole, zajistěte, aby pokusy o připojení a stavu ověření (úspěch nebo neúspěch) pro jednotlivá zařízení jsou protokolovány a udržuje na hraniční brána.</p><p>Také v případech, ve kterém je brána pole zachování přihlašovací údaje služby IoT Hub pro jednotlivá zařízení, ujistěte se, že auditování se provádí tyto přihlašovací údaje jsou načteny. Vývoj procesu se pravidelně nahrát protokoly do služby IoT Hub a úložiště Azure pro dlouhodobé uchovávání.</p> |

## <a id="logging-cloud-gateway"></a>Ujistěte se, že je pro Cloudová brána vynucovat odpovídající auditování a protokolování

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT cloudu | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Úvod k monitorování operací služby IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Kroky** | <p>Návrh pro shromažďování a ukládání dat auditu shromážděných prostřednictvím monitorování Operations centra IoT. Povolte monitorování následujících kategorií:</p><ul><li>Operace identity zařízení</li><li>Komunikace typu zařízení cloud</li><li>Komunikaci typu cloud zařízení</li><li>Připojení</li><li>Nahrání souborů</li></ul>|