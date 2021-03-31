---
title: Auditování a protokolování – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Přečtěte si o omezeních auditování a protokolování v Threat Modeling Tool. Podívejte se na informace o zmírnění rizik a zobrazte příklady kódu.
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
ms.openlocfilehash: 9d3f3ca7b5d4516c2ad5dc9cb19a2eaed0a8a4a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518273"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Rámec zabezpečení: auditování a protokolování | Hrozeb 

| Produkt/služba | Článek |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identifikujte citlivé entity ve vašem řešení a implementujte auditování změn.](#sensitive-entities)</li></ul> |
| **Webová aplikace** | <ul><li>[Ujistěte se, že se v aplikaci vynutilo auditování a protokolování.](#auditing)</li><li>[Ujistěte se, že jsou zavedeny rotace protokolu a oddělení.](#log-rotation)</li><li>[Ujistěte se, že aplikace neprotokoluje citlivá uživatelská data.](#log-sensitive-data)</li><li>[Ujistěte se, že soubory auditu a protokolu mají omezený přístup.](#log-restricted-access)</li><li>[Zajistěte, aby byly události správy uživatelů protokolovány](#user-management)</li><li>[Zajistěte, aby systém měl zabudované obrany před zneužitím.](#inbuilt-defenses)</li><li>[Povolit protokolování diagnostiky pro webové aplikace v Azure App Service](#diagnostics-logging)</li></ul> |
| **Databáze** | <ul><li>[Ujistěte se, že auditování přihlášení je povolené v SQL Server](#identify-sensitive-entities)</li><li>[Povolení detekce hrozeb v Azure SQL](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Auditování přístupu Azure Storage pomocí Analýza úložiště Azure](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementace dostatečného protokolování](#sufficient-logging)</li><li>[Implementace dostatečného zpracování selhání auditu](#audit-failure-handling)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Ujistěte se, že je na webovém rozhraní API vynutilo auditování a protokolování.](#logging-web-api)</li></ul> |
| **Brána pole IoT** | <ul><li>[Zajistěte, aby se pro bránu pole vynutilo vhodné auditování a protokolování.](#logging-field-gateway)</li></ul> |
| **Cloudová brána IoT** | <ul><li>[Zajistěte, aby se v cloudové bráně vynutilo vhodné auditování a protokolování.](#logging-cloud-gateway)</li></ul> |

## <a name="identify-sensitive-entities-in-your-solution-and-implement-change-auditing"></a><a id="sensitive-entities"></a>Identifikujte citlivé entity ve vašem řešení a implementujte auditování změn.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky**                   | Identifikujte entity ve vašem řešení obsahující citlivá data a implementujte auditování změn u těchto entit a polí. |

## <a name="ensure-that-auditing-and-logging-is-enforced-on-the-application"></a><a id="auditing"></a>Ujistěte se, že se v aplikaci vynutilo auditování a protokolování.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky**                   | Povolte auditování a protokolování pro všechny součásti. Protokoly auditu by měly zachytit kontext uživatele. Identifikujte všechny důležité události a protokolujte tyto události. Implementace centralizovaného protokolování |

## <a name="ensure-that-log-rotation-and-separation-are-in-place"></a><a id="log-rotation"></a>Ujistěte se, že jsou zavedeny rotace protokolu a oddělení.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky**                   | <p>Rotace protokolu je automatizovaný proces používaný při správě systému, ve kterém jsou archivovány soubory protokolu s datem. Servery, které spouštějí velké aplikace, často protokolují každý požadavek: na tvář hromadných protokolů je rotace protokolu způsob, jak omezit celkovou velikost protokolů, a přitom stále povolit analýzu posledních událostí. </p><p>Oddělení log v podstatě znamená, že budete muset ukládat soubory protokolů na jiný oddíl, ve kterém je spuštěný operační systém/aplikace, aby nemohlo způsobit útok na útok DoS (Denial of Service) nebo downgrade vaší aplikace na výkon.</p>|

## <a name="ensure-that-the-application-does-not-log-sensitive-user-data"></a><a id="log-sensitive-data"></a>Ujistěte se, že aplikace neprotokoluje citlivá uživatelská data.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky**                   | <p>Ověřte, že nechcete protokolovat žádná citlivá data, která uživatel odešle do vašeho webu. Kontrolovat úmyslné protokolování a také vedlejší účinky způsobené problémy s návrhem. Mezi příklady citlivých dat patří:</p><ul><li>Přihlašovací údaje uživatele</li><li>Číslo sociálního pojištění nebo jiné identifikační údaje</li><li>Čísla platebních karet nebo jiné finanční informace</li><li>Informace o stavu</li><li>Soukromé klíče nebo jiná data, která lze použít k dešifrování šifrovaných informací</li><li>Informace o systému nebo aplikaci, které lze použít k efektivnějšímu útoku aplikace</li></ul>|

## <a name="ensure-that-audit-and-log-files-have-restricted-access"></a><a id="log-restricted-access"></a>Ujistěte se, že soubory auditu a protokolu mají omezený přístup.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky**                   | <p>Zkontrolujte, jestli jsou správně nastavená přístupová práva k souborům protokolu. Účty aplikací by měly mít přístup jen pro zápis a operátoři technické podpory by měli mít přístup jen pro čtení podle potřeby.</p><p>Účty správců jsou jediné účty, které by měly mít úplný přístup. Zkontrolujte seznam řízení přístupu systému Windows pro soubory protokolu, abyste měli jistotu, že jsou správně omezené:</p><ul><li>Účty aplikací by měly mít přístup jen pro zápis.</li><li>Operátoři a pracovníci podpory by měli mít přístup jen pro čtení podle potřeby.</li><li>Jedinými účty, které by měly mít úplný přístup, jsou správci.</li></ul>|

## <a name="ensure-that-user-management-events-are-logged"></a><a id="user-management"></a>Zajistěte, aby byly události správy uživatelů protokolovány

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky**                   | <p>Ujistěte se, že aplikace monitoruje události správy uživatelů, jako jsou úspěšná a Nezdařená přihlášení uživatelů, resetování hesla, změny hesla, uzamčení účtu, registrace uživatele. To pomáhá detekovat a reagovat na potenciálně podezřelé chování. Umožňuje taky shromažďovat data o operacích. Chcete-li například sledovat, kdo přistupuje k aplikaci</p>|

## <a name="ensure-that-the-system-has-inbuilt-defenses-against-misuse"></a><a id="inbuilt-defenses"></a>Zajistěte, aby systém měl zabudované obrany před zneužitím.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky**                   | <p>Ovládací prvky by měly být zavedeny, které vyvolávají výjimku zabezpečení v případě zneužití aplikace. Například pokud je vstupní ověřování na místě a útočník se pokusí vložit škodlivý kód, který se neshoduje s regulárním výrazem, může být vyvolána výjimka zabezpečení, která může být orientačním zneužitím systému.</p><p>Doporučuje se například zaprotokolovat výjimky zabezpečení a akce provedené v následujících problémech:</p><ul><li>Ověřování vstupu</li><li>CSRF porušení</li><li>Hrubá síla (horní limit počtu požadavků na uživatele na prostředek)</li><li>Narušení nahrávání souborů</li><ul>|

## <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a><a id="diagnostics-logging"></a>Povolit protokolování diagnostiky pro webové aplikace v Azure App Service

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Reference**              | –  |
| **Kroky** | <p>Azure poskytuje integrovanou diagnostiku, která vám pomůže s laděním App Service webové aplikace. Platí to i pro aplikace API a mobilní aplikace. App Service webové aplikace poskytují diagnostické funkce pro protokolování informací z webového serveru i z webové aplikace.</p><p>Tyto jsou logicky rozděleny na diagnostiku webových serverů a Application Diagnostics</p>|

## <a name="ensure-that-login-auditing-is-enabled-on-sql-server"></a><a id="identify-sensitive-entities"></a>Ujistěte se, že auditování přihlášení je povolené v SQL Server

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Konfigurace auditování přihlašování](/sql/ssms/configure-login-auditing-sql-server-management-studio) |
| **Kroky** | <p>Aby bylo možné zjistit nebo potvrdit útoky, které se odhadují, musí být povoleno auditování přihlášení k databázovému serveru. Je důležité zachytit neúspěšné pokusy o přihlášení. Zachycení úspěšných a neúspěšných pokusů o přihlášení přináší další výhody během Forenzní vyšetřování.</p>|

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Povolení detekce hrozeb v Azure SQL

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | SQL Azure |
| **Atributy**              | SQL verze – V12 |
| **Reference**              | [Začínáme se zjišťováním hrozeb SQL Database](../../azure-sql/database/threat-detection-configure.md)|
| **Kroky** |<p>Detekce hrozeb detekuje neobvyklé databázové aktivity, které indikují potenciální ohrožení zabezpečení databáze. Poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje rozpoznávat a reagovat na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro aktivity neobvyklé.</p><p>Uživatelé můžou prozkoumat podezřelé události pomocí auditování Azure SQL Database, aby zjistili, jestli jsou výsledkem pokusu o přístup, porušení nebo zneužití dat v databázi.</p><p>Detekce hrozeb usnadňuje řešení potenciálních hrozeb pro databázi, aniž by bylo nutné být odborníkem na zabezpečení nebo spravovali pokročilé systémy monitorování zabezpečení.</p>|

## <a name="use-azure-storage-analytics-to-audit-access-of-azure-storage"></a><a id="analytics"></a>Auditování přístupu Azure Storage pomocí Analýza úložiště Azure

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | – |
| **Reference**              | [Monitorování typu autorizace pomocí Analýza úložiště](../../storage/blobs/security-recommendations.md#loggingmonitoring) |
| **Kroky** | <p>U každého účtu úložiště může jedna povolit Analýza úložiště Azure provádět protokolování a ukládat data metrik. Protokoly služby Storage Analytics poskytují důležité informace, jako například metodu ověřování, kterou používá někdo při přístupu k úložišti.</p><p>To může být opravdu užitečné v případě, že úzce chráníte přístup k úložišti. Například v Blob Storage můžete nastavit všechny kontejnery na soukromé a implementovat používání služby SAS v rámci svých aplikací. Pak můžete protokoly pravidelně kontrolovat a zjistit, jestli k objektům blob přistupovali pomocí klíčů účtu úložiště, což může znamenat porušení zabezpečení, nebo pokud jsou objekty blob veřejné, ale neměly by být.</p>|

## <a name="implement-sufficient-logging"></a><a id="sufficient-logging"></a>Implementace dostatečného protokolování

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | .NET Framework |
| **Atributy**              | –  |
| **Reference**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Kroky** | <p>Chybějící správný záznam pro audit po incidentu zabezpečení může zabránit forenzní úsilí. Windows Communication Foundation (WCF) nabízí možnost protokolování úspěšných nebo neúspěšných pokusů o ověření.</p><p>Protokolování neúspěšných pokusů o ověření může varovat správců potenciálních útoků hrubou silou. Podobně protokolování úspěšných událostí ověření může poskytnout užitečný záznam pro audit, když dojde k ohrožení bezpečnosti legitimního účtu. Povolit funkci audit zabezpečení služby WCF |

### <a name="example"></a>Příklad
Následuje příklad konfigurace s povoleným auditováním.
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
| **Atributy**              | –  |
| **Reference**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Kroky** | <p>Vytvořené řešení je nakonfigurované tak, aby negenerovalo výjimku, když se nepovede zapsat do protokolu auditu. Pokud je WCF nakonfigurované tak, aby nevyvolávají výjimku, když nemůže zapisovat do protokolu auditu, program nebude upozorněn na selhání a nemusí dojít k auditování kritických událostí zabezpečení.</p>|

### <a name="example"></a>Příklad
`<behavior/>`Element konfiguračního souboru WCF vydá pokyn, aby WCF neinformovalo aplikaci, když se službě WCF nepovede zapisovat do protokolu auditu.
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
Nakonfigurujte WCF tak, aby programu oznámil program, kdykoli nebude moct zapisovat do protokolu auditu. Program by měl mít k dispozici alternativní schéma oznámení, aby bylo možné upozornit organizaci na to, že nejsou udržovány záznamy auditu. 

## <a name="ensure-that-auditing-and-logging-is-enforced-on-web-api"></a><a id="logging-web-api"></a>Ujistěte se, že je na webovém rozhraní API vynutilo auditování a protokolování.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webové rozhraní API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Povolí auditování a protokolování webových rozhraní API. Protokoly auditu by měly zachytit kontext uživatele. Identifikujte všechny důležité události a protokolujte tyto události. Implementace centralizovaného protokolování |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-field-gateway"></a><a id="logging-field-gateway"></a>Zajistěte, aby se pro bránu pole vynutilo vhodné auditování a protokolování.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána pole IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | <p>Když se k bráně pole připojí více zařízení, zajistěte, aby se pokusy o připojení a stav ověřování (úspěch nebo neúspěch) pro jednotlivá zařízení evidovaly a udržovaly v bráně pole.</p><p>V případech, kdy brána Field uchovává IoT Hub přihlašovací údaje pro jednotlivá zařízení, zajistěte, aby se auditování provádělo při načítání těchto přihlašovacích údajů. Vytvořte proces pro pravidelné nahrávání protokolů do Azure IoT Hub/úložiště pro dlouhodobé uchovávání.</p> |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-cloud-gateway"></a><a id="logging-cloud-gateway"></a>Zajistěte, aby se v cloudové bráně vynutilo vhodné auditování a protokolování.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Cloudová brána IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Seznámení s IoT Hub monitorování provozu](../../iot-hub/iot-hub-operations-monitoring.md) |
| **Kroky** | <p>Navrhněte shromažďování a ukládání dat auditu shromážděných prostřednictvím monitorování IoT Hubch operací. Povolte následující kategorie monitorování:</p><ul><li>Operace identity zařízení</li><li>Komunikace mezi zařízeními a cloudem</li><li>Komunikace z cloudu na zařízení</li><li>Připojení</li><li>Nahrání souborů</li></ul>|