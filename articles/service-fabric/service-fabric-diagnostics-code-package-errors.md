---
title: Diagnostikovat běžné chyby balíčku kódu pomocí service fabric
description: Zjistěte, jak řešit běžné chyby balíčku kódu pomocí Azure Service Fabric
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 344fef70522240da2236a020c96308c472c9c545
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463110"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Diagnostikovat běžné chyby balíčku kódu pomocí service fabric

Tento článek popisuje, co to znamená pro balíček kódu neočekávaně ukončit. Poskytuje přehled o možných příčinách běžných kódů chyb spolu s kroky řešení potíží.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Kdy proces nebo kontejner neočekávaně skončí?

Když Azure Service Fabric obdrží požadavek na spuštění balíčku kódu, začne připravovat prostředí v místním systému podle možností nastavených v manifestech aplikací a služeb. Tyto přípravy mohou zahrnovat rezervaci koncových bodů sítě nebo prostředků, konfiguraci pravidel brány firewall nebo nastavení omezení zásad správného řízení prostředků. 

Po správné konfiguraci prostředí Service Fabric pokusí vyvolat balíček kódu. Tento krok je považován za úspěšný, pokud operační systém nebo zaběhový čas kontejneru hlásí, že proces nebo kontejner byl úspěšně aktivován. Pokud aktivace je neúspěšná, měli byste vidět zprávu o stavu v SFX, která se podobá následující:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Po úspěšné aktivaci balíčku kódu service fabric začne sledovat jeho životnost. V tomto okamžiku proces nebo kontejner může ukončit kdykoli z mnoha důvodů. Například se nemusí inicializovat dll nebo os mohl mít nedostatek místa haldy plochy. Pokud váš balíček kódu ukončen, měli byste vidět následující zprávu o stavu v SFX:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

Ukončovací kód v této zprávě o stavu je pouze vodítko, které poskytuje proces nebo kontejner o tom, proč byl ukončen. Může být generována libovolnou úrovní zásobníku. Tento ukončovací kód může například souviset s chybou operačního spoje nebo problémem s rozhraním .NET nebo byl vyvolán vaším kódem. Tento článek slouží jako výchozí bod pro diagnostiku zdroje ukončovacích kódů a možných řešení. Mějte však na paměti, že se jedná o obecná řešení běžných scénářů a nemusí se vztahovat na chybu, kterou vidíte.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Jak poznám, že Service Fabric ukončil můj balíček kódu?

Service Fabric může být zodpovědný za ukončení balíčku kódu z různých důvodů. Například může rozhodnout umístit balíček kódu na jiný uzel pro účely vyrovnávání zatížení. Můžete ověřit, že Service Fabric ukončil balíček kódu, pokud se zobrazí některý z ukončovacích kódů v následující tabulce.

>[!NOTE]
> Pokud proces nebo kontejner ukončí s ukončovací kód jiný než kódy v následující tabulce, Service Fabric není zodpovědný za jeho ukončení.

Ukončovací kód | Popis
--------- | -----------
7147 | Označuje, že Service Fabric řádně vypnout proces nebo kontejner odesláním Ctrl + C signál.
7148 | Označuje, že Service Fabric ukončil proces nebo kontejner. V některých případě tento kód chyby označuje, že proces nebo kontejner neodpověděl včas po odeslání signálu Ctrl +C a musel být ukončen.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Další běžné kódy chyb a jejich potenciální opravy

Ukončovací kód | Šestnáctková hodnota | Stručný popis | Původní příčina | Potenciální oprava
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Tato chyba někdy znamená, že počítač má nedostatek místa haldy plochy. Tato příčina je zvláště pravděpodobné, pokud máte mnoho procesů, které patří do vaší aplikace spuštěné na uzlu. | Pokud váš program nebyl vytvořen tak, aby reagoval na signály Ctrl+C, můžete v manifestu clusteru povolit nastavení **EnableActivateNoWindow.** Povolení tohoto nastavení znamená, že balíček kódu bude spuštěn bez okna grafického uživatelského rozhraní a nebude přijímat signály Ctrl+C. Tato akce také snižuje množství místa haldy plochy, které každý proces spotřebovává. Pokud váš balíček kódu potřebuje přijímat signály Ctrl+C, můžete zvětšit velikost haldy plochy vašeho uzlu.
3762504530 | 0xe0434352 | Není dostupné. | Tato hodnota představuje kód chyby pro neošetřenou výjimku ze spravovaného kódu (tj. .NET). | Tento ukončovací kód označuje, že aplikace vyvolala výjimku, která zůstává neošetřena a která ukončila proces. Jako první krok při určování, co vyvolalo tuto chybu, ladění protokolů aplikace a soubory výpisu stavu paměti.

## <a name="next-steps"></a>Další kroky

* Další informace o [diagnostice dalších běžných scénářů](service-fabric-diagnostics-common-scenarios.md).
* Získejte podrobnější přehled protokolů Azure Monitoru a co nabízejí na čtení [přehledu Azure Monitoru](../operations-management-suite/operations-management-suite-overview.md).
* Další informace o azure monitoru protokoly [upozornění](../log-analytics/log-analytics-alerts.md) na pomoc při zjišťování a diagnostiky.
* Seznamte se s funkcemi [vyhledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) nabízených jako součást protokolů Azure Monitor.
