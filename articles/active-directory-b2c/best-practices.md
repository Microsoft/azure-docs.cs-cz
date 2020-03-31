---
title: Doporučené postupy pro Azure AD B2C
titleSuffix: Azure AD B2C
description: Doporučení a doporučené postupy, které je třeba zvážit při práci s Azure Active Directory B2C (Azure AD B2C).
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: a76852a6e3cc5ffcdfcac62ce29fe47c97af3df1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136155"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Doporučení a doporučené postupy pro Azure Active Directory B2C

Následující osvědčené postupy a doporučení pokrývají některé primární aspekty integrace služby Azure Active Directory (Azure AD) B2C do existujících nebo nových aplikačních prostředí.

## <a name="fundamentals"></a>Základy

|  |  |
|--|--|
| Výběr toků uživatelů pro většinu scénářů | Rozhraní identity prostředí Azure AD B2C je hlavní síla služby. Zásady plně popisují prostředí identity, jako je registrace, přihlášení nebo úpravy profilu. Abychom vám pomohli nastavit nejběžnější úlohy identity, portál Azure AD B2C obsahuje předdefinované, konfigurovatelné zásady nazývané toky uživatelů. S uživatelskými toky můžete vytvořit skvělé uživatelské prostředí během několika minut pomocí několika kliknutí. [Zjistěte, kdy použít toky uživatelů vs. vlastní zásady](custom-policy-overview.md#comparing-user-flows-and-custom-policies).|
| Registrace aplikací | Každá aplikace (web, nativní) a rozhraní API, které je zabezpečené, musí být registrované v Azure AD B2C. Pokud má aplikace webovou i nativní verzi iOS a Android, můžete je zaregistrovat jako jednu aplikaci v Azure AD B2C se stejným ID klienta. Přečtěte si, jak [zaregistrovat OIDC, SAML, web a nativní aplikace](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications). Další informace o [typech aplikací, které se dá použít v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/application-types). |
| Přechod na měsíční fakturaci aktivních uživatelů | Azure AD B2C se přesunulz měsíčníaktivní ověřování na měsíční aktivní uživatele (MAU) fakturace. Většina zákazníků najde tento model nákladově efektivní. [Přečtěte si další informace o měsíčnífakturaci aktivních uživatelů](https://azure.microsoft.com/updates/mau-billing/). |

## <a name="planning-and-design"></a>Plánování a návrh

Definujte architekturu aplikací a služeb, aktuální systémy inventáře a naplánujte migraci do Azure AD B2C.

|  |  |
|--|--|
| Navrhne komplexní řešení | Při plánování integrace Azure AD B2C zahrňte všechny závislosti vašich aplikací. Zvažte všechny služby a produkty, které jsou aktuálně ve vašem prostředí nebo které může být potřeba přidat do řešení, například Azure Functions, systémy pro správu vztahů se zákazníky (CRM), bránu azure api management a služby úložiště. Vezměte v úvahu zabezpečení a škálovatelnost všech služeb. |
| Dokumentujte prostředí uživatelů | Podrobně uveďte všechny cesty uživatelů, které mohou zákazníci zaznamenat ve vaší aplikaci. Zahrnout všechny obrazovky a všechny toky větvení, které mohou setkat při interakci s identity a profil aspekty vaší aplikace. Zahrnout použitelnost, usnadnění a lokalizaci do plánování. |
| Výběr správného ověřovacího protokolu |  Rozdělení různých scénářů aplikací a jejich doporučených toků ověřování naleznete v [tématu Scénáře a podporované toky ověřování](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows). |
| Pilotní ověření koncepce (POC) od koncového uživatele | Začněte s [ukázkami kódu Microsoftu](code-samples.md) a [ukázkami komunity](https://github.com/azure-ad-b2c/samples). |
| Vytvoření plánu migrace |Plánování dopředu může zajistit, aby migrace probíhala plynuleji. Další informace o [migraci uživatelů](user-migration.md).|
| Použitelnost vs. zabezpečení | Vaše řešení musí najít správnou rovnováhu mezi použitelností aplikací a přijatelnou úrovní rizika vaší organizace. |
| Přesunutí místních závislostí do cloudu | Chcete-li zajistit odolné řešení, zvažte přesunutí existujících závislostí aplikací do cloudu. |
| Migrace existujících aplikací do b2clogin.com | Vyřazení login.microsoftonline.com vstoupilo v platnost pro všechny klienty Azure AD B2C dne 04 Prosinec 2020. [Další informace](b2clogin.md). |

## <a name="implementation"></a>Implementace

Během fáze provádění zvažte následující doporučení.

|  |  |
|--|--|
| Úpravy vlastních zásad s rozšířením Azure AD B2C pro kód Visual Studia | Stáhněte si visual studio kód a toto komunitní [rozšíření z Visual Studio Code Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c). I když není oficiální produkt společnosti Microsoft, rozšíření Azure AD B2C pro Visual Studio Code obsahuje několik funkcí, které pomáhají usnadnit práci s vlastními zásadami. |
| Přečtěte si, jak řešit potíže s Azure AD B2C | Přečtěte si, jak [řešit potíže s vlastními zásadami](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications) během vývoje. Zjistěte, jak vypadá normální tok ověřování, a použijte nástroje pro zjišťování anomálií a chyb. Můžete například použít [Application Insights](troubleshoot-with-application-insights.md) ke kontrole výstupních protokolů cest uživatelů. |
| Využijte naši knihovnu osvědčených vzorů vlastních zásad | Najděte [ukázky](https://github.com/azure-ad-b2c/samples) pro několik rozšířených cest uživatelů Azure AD B2C pro identitu a správu přístupu (CIAM). |


## <a name="testing"></a>Testování

Otestujte a automatizujte implementaci Azure AD B2C.

|  |  |
|--|--|
| Účet pro globální provoz | Pomocí zdrojů návštěvnosti z různých globálních adres otestujte požadavky na výkon a lokalizaci. Ujistěte se, že všechny htmls, CSS a závislosti mohou splňovat vaše potřeby výkonu. |
| Funkční testování a testování uj. | Otestujte toky uživatele od konce do konce. Přidejte syntetické testy každých několik minut pomocí Selenu, VS Web Test, atd. |
| Testování perem | Než začnete pracovat se svým řešením, proveďte testy průniku, abyste ověřili, že všechny součásti jsou zabezpečené, včetně závislostí třetích stran. Ověřte, zda jste zabezpečili rozhraní API pomocí přístupových tokenů a použili správný ověřovací protokol pro váš scénář aplikace. Přečtěte si další informace o [testování průniku](https://docs.microsoft.com/azure/security/fundamentals/pen-testing) a [pravidlech sjednoceného testování pro rozšíření služby Microsoft Cloud .](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) |
| Testování A/B | Před zavedením do celé populace vysuňte své nové funkce s malou náhodnou sadou uživatelů. S povoleným JavaScriptem v Azure AD B2C můžete integrovat s testovacími nástroji A/B, jako je Optimizely, Clarity a další. |
| Zátěžové testování | Azure AD B2C můžete škálovat, ale vaše aplikace můžete škálovat pouze v případě, že všechny jeho závislosti můžete škálovat. Načíst testování vašich api a CDN. |
| Throttling |  Azure AD B2C omezuje provoz, pokud je v krátkém časovém období odesíláno příliš mnoho požadavků ze stejného zdroje. Při testování zatížení používejte několik zdrojů `AADB2C90229` provozu a řádně zpracovat kód chyby ve vašich aplikacích. |
| Automatizace | Pomocí kanálů průběžné integrace a doručování (CI/CD) můžete automatizovat testování a nasazení, například [Azure DevOps](deploy-custom-policies-devops.md). |

## <a name="operations"></a>Provoz

Spravujte prostředí Azure AD B2C.

|  |  |
|--|--|
| Vytvoření více prostředí | Pro snadnější provoz a zavádění nasazení vytvořte samostatná prostředí pro vývoj, testování, předprodukční a produkční prostředí. Vytvořte klienty Azure AD B2C pro každého. |
| Použití správy verzí pro vlastní zásady | Zvažte použití GitHubu, Azure Repos nebo jiného cloudového systému správy verzí pro vlastní zásady Azure AD B2C. |
| Automatizace správy klientů B2C pomocí rozhraní Microsoft Graph API | Rozhraní API microsoft graphu:<br/>Správa [rozhraní Identity Experience Framework](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta) (vlastní zásady)<br/>[Klíče](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[Toky uživatele](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| Integrace s Azure DevOps | Kanál [CI/CD](deploy-custom-policies-devops.md) usnadňuje přesun kódu mezi různými prostředími a zajišťuje provozní připravenost za všech okolností.   |
| Integrace s Azure Monitorem | [Události protokolu auditu](view-audit-logs.md) jsou uchovávány pouze po dobu sedmi dnů. [Integrujte s Azure Monitor](azure-monitor.md) uchovávat protokoly pro dlouhodobé použití, nebo integrovat s nástroji pro správu informací zabezpečení třetích stran a události (SIEM) získat přehled o vašem prostředí. |
| Nastavení aktivního upozorňování a monitorování | [Sledujte chování uživatelů](active-directory-b2c-custom-guide-eventlogger-appins.md) ve službě Azure AD B2C pomocí přehledů aplikací. |


## <a name="support-and-status-updates"></a>Aktualizace podpory a stavu

Zůstaňte v obraze se stavem služby a najděte možnosti podpory.

|  |  |
|--|--|
| [Aktualizace služeb](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Mějte aktuální informace o aktualizacích a oznámeních produktů Azure AD B2C. |
| [Podpora společnosti Microsoft](support-options.md) | Podejte žádost o podporu pro technické problémy Azure AD B2C. Podpora fakturace a správy předplatného je poskytována zdarma. |
| [Stav Azure](https://status.azure.com/status) | Zobrazení aktuálního stavu všech služeb Azure. |