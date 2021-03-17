---
title: Diagnostika a řešení problémů v Azure API Management
description: Naučte se řešit problémy s vaším rozhraním API v Azure API Management pomocí nástroje Diagnostika a řešení v Azure Portal.
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: d8ec04227316088983977f5b487abfa81fb5c525
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652398"
---
# <a name="azure-api-management-diagnostics-overview"></a>Přehled diagnostiky Azure API Management

Když vytváříte a spravujete rozhraní API v Azure API Management, chcete se připravit na případné problémy, které mohou nastat, od 404 nenalezených chyb až 502 chybná chyba brány. Diagnostika API Management je inteligentní a interaktivní prostředí, které vám umožní řešit potíže s rozhraním API publikovaným v APIM bez nutnosti konfigurace. Když provedete problémy s publikovanými rozhraními API, API Management Diagnostika ukazuje, co je špatné a provede vás pokyny k rychlému řešení problému a vyřešení problému.

I když je toto prostředí nejužitečnější, když v posledních 24 hodinách dojde k problémům s vaším rozhraním API, všechny diagnostické grafy jsou k dispozici vždy, když je budete chtít analyzovat.

## <a name="open-api-management-diagnostics"></a>Otevřít diagnostiku API Management

Pokud chcete získat přístup k diagnostice API Management, přejděte v [Azure Portal](https://portal.azure.com)do své instance služby API Management. V levém navigačním panelu vyberte **Diagnostika a řešení problémů**.

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="Snímek obrazovky ukazuje, jak přejít na diagnostiku.":::



## <a name="intelligent-search"></a>Inteligentní hledání

Problémy nebo problémy můžete hledat na panelu hledání v horní části stránky. Hledání také vám pomůže najít nástroje, které vám mohou pomoci při řešení problémů nebo jejich řešení. 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="snímek inteligentního hledání":::


## <a name="troubleshooting-categories"></a>Kategorie řešení potíží

Můžete řešit problémy v kategorii. Běžné problémy, které souvisí s výkonem rozhraní API, bránou, zásadami rozhraní API a úrovní služeb, se dají analyzovat v rámci každé kategorie. Každá kategorie také poskytuje konkrétnější diagnostické kontroly. 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="snímek obrazovky s přehledem kategorií":::


### <a name="availability-and-performance"></a>Dostupnost a výkon

Ověřte dostupnost rozhraní API a problémy s výkonem v této kategorii. Po výběru dlaždice kategorie se v interaktivním rozhraní doporučuje několik běžných kontrol. Klikněte na každé zaškrtávací políčko, abyste podrobněi hloubku konkrétních problémů. Tato kontrolu vám taky poskytne graf, který ukazuje výkon rozhraní API, a souhrn problémů s výkonem. Například vaše služba API mohla mít 5xx chybu a časový limit za poslední hodinu v back-endu. 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="Snímek obrazovky 1 z interaktivní kontroly rozhraní.":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="Snímek obrazovky 2 interaktivní kontroly rozhraní.":::

### <a name="api-policies"></a>Zásady rozhraní API

Tato kategorie detekuje chyby a upozorňuje vás na vaše problémy se zásadami. 

Podobné interaktivní rozhraní vás provede nastavením metriky dat, které vám pomůžou při odstraňování potíží s konfigurací zásad rozhraní API.

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="snímek obrazovky dlaždice kategorie zásad rozhraní API":::

### <a name="gateway-performance"></a>Výkon brány 

V případě požadavků a odpovědí brány nebo jakýchkoli chyb 4xx nebo 5xx v bráně použijte tuto kategorii k monitorování a odstraňování potíží. Podobně můžete pomocí interaktivního rozhraní podrobně hloubku v konkrétní oblasti, kterou chcete ověřit výkon brány rozhraní API. 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="snímek obrazovky dlaždice kategorie výkonu brány":::

### <a name="service-upgrade"></a>Upgrade služby

Tato kategorie kontroluje, jakou úroveň služby (SKU) aktuálně používáte, a připomíná vás k upgradu, aby nedocházelo k problémům, které by mohly souviset s touto vrstvou. Stejné interaktivní rozhraní vám pomůže s větším využitím grafiky a souhrnem výsledků kontroly. 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="obrazovka dlaždice kategorie upgradu služby":::

## <a name="search-documentation"></a>Hledat v dokumentaci

V části Další možnosti diagnostikovat a řešit problémy můžete vyhledat dokumentaci k řešení potíží související s vaším problémem. Po spuštění diagnostiky ve službě vyberte v interaktivním rozhraní **dokumentaci pro hledání** . 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="snímek obrazovky 1, jak používat funkci dokumentace pro hledání.":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="snímek obrazovky 2, jak používat dokumentaci pro hledání.":::


## <a name="next-steps"></a>Další kroky

* K analýze využití a výkonu rozhraní API můžete také použít [analýzu rozhraní API](howto-use-analytics.md) . 
* Chcete řešit potíže s Web Apps problémy s diagnostikou? Přečtěte si ho [tady](../app-service/overview-diagnostics.md)
* Využijte diagnostiku pro kontrolu potíží s Azure Kubernetes Services. Viz [Diagnostika v AKS](../aks/concepts-diagnostics.md)
* Vystavte své dotazy nebo připomínky na webu [UserVoice](https://feedback.azure.com/forums/248703-api-management) přidáním "[diag]" v názvu.
