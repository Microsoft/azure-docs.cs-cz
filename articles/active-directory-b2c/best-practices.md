---
title: Osvědčené postupy pro Azure AD B2C
titleSuffix: Azure AD B2C
description: Doporučení a osvědčené postupy, které je potřeba vzít v úvahu při práci s Azure Active Directory B2C (Azure AD B2C).
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: d8c0a5ce6f3befd41c0e1399363fd73726693837
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96750216"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Doporučení a osvědčené postupy pro Azure Active Directory B2C

Následující osvědčené postupy a doporučení se týkají některých primárních aspektů Integrace Azure Active Directory (Azure AD) B2C do stávajících nebo nových aplikačních prostředí.

## <a name="fundamentals"></a>Základy

| Osvědčený postup | Description |
|--|--|
| Volba toků uživatelů pro většinu scénářů | Architektura prostředí identit Azure AD B2C je základní silou služby. Zásady plně popisují prostředí identity, jako je registrace, přihlašování nebo úpravy profilu. Aby vám pomohly nastavit nejběžnější úkoly identity, Azure AD B2C portál obsahuje předdefinované a konfigurovatelné zásady nazývané uživatelské toky. Díky tokům uživatelů můžete vytvořit skvělé uživatelské prostředí během několika minut. stačí jenom několik kliknutí. [Naučte se používat toky uživatelů a vlastní zásady](custom-policy-overview.md#comparing-user-flows-and-custom-policies).|
| Registrace aplikací | Všechny aplikace (web, nativní) a rozhraní API, které je třeba zabezpečit, musí být registrovány v Azure AD B2C. Pokud má aplikace jak webovou, tak nativní verzi iOS a Android, můžete je zaregistrovat jako jednu aplikaci v Azure AD B2C se stejným ID klienta. Naučte se [Registrovat OIDC, SAML, web a nativní aplikace](./tutorial-register-applications.md?tabs=applications). Další informace o [typech aplikací, které lze použít v Azure AD B2C](./application-types.md). |
| Fakturace po měsících aktivních uživatelů | Azure AD B2C přesunula z měsíčních aktivních ověřování na měsíční fakturace aktivních uživatelů (MAU). Většina zákazníků bude tento model nákladově efektivní. [Přečtěte si další informace o fakturaci měsíčně aktivních uživatelů](https://azure.microsoft.com/updates/mau-billing/). |

## <a name="planning-and-design"></a>Plánování a návrh

Definujte architekturu aplikací a služeb, aktuální systémy inventáře a naplánujte migraci na Azure AD B2C.

| Osvědčený postup | Description |
|--|--|
| Architekt kompletního řešení | Při plánování integrace Azure AD B2C Zahrňte všechny závislosti vašich aplikací. Vezměte v úvahu všechny služby a produkty, které jsou aktuálně ve vašem prostředí, nebo které je potřeba přidat do řešení, například Azure Functions, systémy správy vztahů se zákazníky (CRM), služby Azure API Management Gateway a služby úložiště. Vezměte v úvahu zabezpečení a škálovatelnost všech služeb. |
| Zdokumentujte uživatelské prostředí | Podrobné informace o všech uživatelích cestují vaše zákazníci, kteří můžou pracovat ve vaší aplikaci. Zahrňte každou obrazovku a všechny toky větve, které se můžou setkat při interakci s aspekty identity a profilů vaší aplikace. Zahrňte do plánování použitelnost, přístupnost a lokalizace. |
| Výběr správného ověřovacího protokolu |  Rozpis různých scénářů aplikací a jejich doporučených ověřovacích toků najdete v tématu [scénáře a podporované toky ověřování](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows). |
| Pilotní nasazení komplexního uživatelského rozhraní pro testování koncepce (koncepce) | Začněte s našimi ukázkami kódu a [komunitou](https://github.com/azure-ad-b2c/samples) [Microsoftu](code-samples.md) . |
| Vytvoření plánu migrace |Plánování se může udělat plynule, aby migrace proběhla rychleji. Přečtěte si další informace o [migraci uživatelů](user-migration.md).|
| Použitelnost a zabezpečení | Vaše řešení musí mít správné rovnováhu mezi použitelností aplikace a přijatelnou úrovní rizika vaší organizace. |
| Přesunutí místních závislostí do cloudu | Pro zajištění odolného řešení zvažte přesunutí stávajících závislostí aplikace do cloudu. |
| Migrace stávajících aplikací na b2clogin.com | Vyřazení login.microsoftonline.com se projeví pro všechny klienty Azure AD B2C v 04. prosince 2020. [Další informace](b2clogin.md). |
| Použití Identity Protection a podmíněného přístupu | Využijte tyto možnosti pro významně větší kontrolu nad rizikovým ověřováním a zásadami přístupu. Vyžaduje se Azure AD B2C Premium P2. [Další informace](conditional-access-identity-protection-overview.md). |

## <a name="implementation"></a>Implementace

Během fáze implementace Vezměte v úvahu následující doporučení.

| Osvědčený postup | Description |
|--|--|
| Úprava vlastních zásad pomocí rozšíření Azure AD B2C pro Visual Studio Code | Stáhněte si Visual Studio Code a toto rozšíření postavené na komunitě [z webu Visual Studio Code Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c). I když není oficiální produkt společnosti Microsoft, Azure AD B2C rozšíření pro Visual Studio Code obsahuje několik funkcí, které usnadňují práci s vlastními zásadami. |
| Postup řešení potíží s Azure AD B2C | Naučte se [řešit potíže s vlastními zásadami](./troubleshoot-custom-policies.md?tabs=applications) během vývoje. Naučte se, jak vypadá normální tok ověřování, a používejte nástroje pro zjišťování anomálií a chyb. Můžete například použít [Application Insights](troubleshoot-with-application-insights.md) ke kontrole výstupních protokolů cest uživatelů. |
| Využijte naši knihovnu prověřených vlastních vzorů zásad. | Najděte si [ukázky](https://github.com/azure-ad-b2c/samples) pro několik vylepšených Azure AD B2Cch cest uživatelů pro správu identit a přístupu (CIAM) zákazníků. |

## <a name="testing"></a>Testování

Testování a automatizace implementace Azure AD B2C.

| Osvědčený postup | Description |
|--|--|
| Účet pro globální provoz | K testování požadavků na výkon a lokalizaci použijte zdroje přenosů z jiné globální adresy. Ujistěte se, že všechny HTML, CSS a závislosti můžou splňovat vaše požadavky na výkon. |
| Funkční testování uživatelského rozhraní | Otestujte toky uživatelů na konci. Přidejte syntetické testy každých několik minut pomocí programu selen, VS Web test atd. |
| Testování perem | Než začnete pracovat s vaším řešením, provedete testování průniku, abyste ověřili, že všechny komponenty jsou zabezpečené, včetně všech závislostí třetích stran. Ověřte, že jste své rozhraní API zabezpečili pomocí přístupových tokenů a použili jste správný ověřovací protokol pro váš scénář aplikace. Přečtěte si další informace o [testování průniku](../security/fundamentals/pen-testing.md) a o [pravidlech pro testování](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)průniku v rámci zapojení do Microsoft Cloud Unified. |
| Testování a/B | Zastavte nové funkce s malou náhodnou sadou uživatelů, než se zavedete k celému naplnění. Pomocí JavaScriptu povoleného v Azure AD B2C můžete integrovat s testovacími nástroji/B, jako je optimalizace, přehlednost a další. |
| Zátěžové testování | Azure AD B2C se můžou škálovat, ale vaše aplikace se může škálovat jenom v případě, že se můžou škálovat všechny jeho závislosti. Zátěžové testování vašich rozhraní API a sítě CDN. |
| Throttling |  Azure AD B2C omezuje provoz, pokud je v krátké době odesíláno příliš mnoho požadavků ze stejného zdroje. Při zátěžovém testování použijte několik zdrojů přenosů a `AADB2C90229` v aplikacích proveďte řádné zpracování kódu chyby. |
| Automation | Pomocí kanálů průběžné integrace a doručování (CI/CD) můžete automatizovat testování a nasazení, například [Azure DevOps](deploy-custom-policies-devops.md). |

## <a name="operations"></a>Operace

Spravujte své Azure AD B2C prostředí.

| Osvědčený postup | Description |
|--|--|
| Vytvoření více prostředí | Pro snazší operace a zavedení nasazení vytvořte samostatná prostředí pro vývoj, testování, předprodukční prostředí a produkci. Pro každou Vytvořte Azure AD B2C klienty. |
| Použití správy verzí pro vlastní zásady | Zvažte použití GitHubu, Azure Repos nebo jiného cloudového systému správy verzí pro vlastní zásady Azure AD B2C. |
| Použití rozhraní Microsoft Graph API k automatizaci správy klientů B2C | Rozhraní API pro Microsoft Graph:<br/>Správa [architektury identity Experience Framework](/graph/api/resources/trustframeworkpolicy?preserve-view=true&view=graph-rest-beta) (vlastní zásady)<br/>[Klíče](/graph/api/resources/trustframeworkkeyset?preserve-view=true&view=graph-rest-beta)<br/>[Toky uživatelů](/graph/api/resources/identityuserflow?preserve-view=true&view=graph-rest-beta) |
| Integrace s Azure DevOps | [Kanál CI/CD](deploy-custom-policies-devops.md) usnadňuje přesouvání kódu mezi různými prostředími a zajišťuje připravenost na produkční prostředí.   |
| Integrace s Azure Monitor | [Události protokolu auditu](view-audit-logs.md) se uchovávají jenom po dobu sedmi dnů. [Integrujte s Azure monitor](azure-monitor.md) , abyste zachovali protokoly pro dlouhodobé použití, nebo integraci s nástroji pro zabezpečení a správu událostí třetích stran (Siem), abyste získali přehled o vašem prostředí. |
| Nastavení aktivního upozorňování a monitorování | [Sledovat chování uživatele](./analytics-with-application-insights.md) v Azure AD B2C pomocí Application Insights. |

## <a name="support-and-status-updates"></a>Aktualizace stavu a podpory

Udržujte si přehled o stavu služby a vyhledejte možnosti podpory.

| Osvědčený postup | Description |
|--|--|
| [Aktualizace služeb](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Udržujte si přehled o Azure AD B2C aktualizacích produktů a oznámeních. |
| [Podpora společnosti Microsoft](support-options.md) | Soubor a žádost o podporu pro Azure AD B2C technické problémy. Podpora fakturace a správy předplatného se poskytuje bez jakýchkoli nákladů. |
| [Stav Azure](https://status.azure.com/status) | Podívejte se na aktuální stav všech služeb Azure. |