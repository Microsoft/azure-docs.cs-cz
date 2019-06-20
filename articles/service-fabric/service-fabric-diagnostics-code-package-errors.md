---
title: Azure Service Fabric diagnostikovat chyby balíčku kódu | Dokumentace Microsoftu
description: Zjistěte, jak řešit běžné chyby balíčku kódu s Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: grzuber
manager: gkhanna
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 235952388d2c044cc141b3020c67944c4250ea3d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276948"
---
# <a name="diagnose-common-code-package-errors-with-service-fabric"></a>Diagnostikovat běžné chyby balíčku kódu s využitím Service Fabric

Tento článek ukazuje, co znamená pro balíček kódu k neočekávanému ukončení a poskytuje podrobné informace o možných příčin běžné kódy chyb, jakož i řešení potíží, které mohou potenciálně tyto problémy zmírnit.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Pokud procesů nebo kontejnerů dojde k neočekávanému ukončení?

Když Service Fabric obdrží požadavek na spuštění kódu balíčku, začne Příprava prostředí na místní systém založený na možnosti konfigurace, nastavte v manifesty služby a aplikace. Rezervace koncové body sítě nebo prostředky, konfigurace pravidel brány firewall nebo nastavení omezení zásad správného řízení prostředků může zahrnovat tyto přípravy. Po prostředí, že správně nakonfigurujete, pokusí se Service Fabric se balíček kódu. Tento krok je považován za úspěšné, pokud modul runtime operačního systému nebo kontejner hlásí, že procesů nebo kontejnerů se aktivoval úspěšně. Pokud se aktivace neproběhla úspěšně, měla zobrazit zpráva stavu v SFX s upozorněním

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Jakmile balíček kódu má byly úspěšně aktualizovány, Service Fabric zahájí monitorování svého životního cyklu. V tomto okamžiku procesu nebo kontejneru můžete ukončit kdykoli z několika důvodů. Může se nepodařilo inicializovat knihovnu DLL operačního systému může vyčerpala volné místo v haldě klasické pracovní plochy, atd. Pokud se váš balíček kódu, měla zobrazit zpráva stavu v SFX s upozorněním

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

Ukončovací kód procesu k dispozici v tomto stavu zpráva je pouze naznačuje poskytované procesu nebo kontejneru, proč byl ukončen a by byly vytvořeny libovolné úrovni zásobníku. Je obtížné pochopit, pokud tento kód ukončení byl v relaci, například chybu operačního systému, problém .NET, nebo byla vyvolána pomocí kódu. Proto tento článek slouží jako výchozí bod pro diagnostiku zdrojového ukončovací kódy ukončení a možná řešení s vědomím, že tyto představují obecná řešení běžných scénářů a nemusí vztahovat na chybu, můžete se sdělením.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Jak poznám, že pokud Service Fabric ukončen Můj balíček kódu?

Service Fabric může také zodpovídat za ukončení vašeho balíčku kódu pro celou řadu důvodů. Například může rozhodnout umístěte balíček kódu v jiném uzlu pro vyrovnávání zatížení účely. Poznáte, pokud váš balíček kódu byl ukončen Service Fabric, pokud se zobrazí z ukončovacích kódů v následující tabulce:

>[!NOTE]
> Pokud váš proces/kontejner skončí s ukončovacím kódem než těch, které jsou v následující tabulce, Service Fabric nezodpovídá za zrušení.

Ukončovací kód | Popis
--------- | -----------
7147 | Tyto kódy chyb znamenat, že Service Fabric řádné ukončení procesu nebo kontejneru a odeslat ho Ctrl + C signál.
7148 | Tyto kódy chyb znamenat, že Service Fabric byl ukončen proces/kontejneru. Tento kód chyby v některých případech může znamenat, že je po odeslání Ctrl + C signál, takže musí být ukončen proces/kontejneru neodpověděl včas.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Další běžné kódy chyb a jejich možné opravy

Ukončovací kód | Šestnáctková hodnota | Krátký popis | Původní příčina | Potenciální oprava
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Tato chyba může znamenat potenciálně počítač nemá dostatek místa v haldě klasické pracovní plochy. Tato příčina je zvláště pravděpodobné, pokud máte velký počet procesů, které patří k aplikaci spuštěné na uzlu. | Pokud váš program nebyl vytvořen na kombinaci kláves Ctrl + C signál, můžete povolit nastavení "EnableActivateNoWindow" v manifestu clusteru. Povolení tohoto nastavení by znamenal, že váš balíček kódu běžet bez okno s grafickým uživatelským rozhraním a nebude přijímat signály Ctrl + C, ale snižuje množství místa klasické pracovní plochy haldy, které každý proces spotřebovává. Pokud váš kód balíček musí přijímat signály Ctrl + C, můžete zvýšit velikost haldy váš uzel klasické pracovní plochy.
3762504530 | 0xe0434352 | neuvedeno | Tato hodnota je kód chyby pro nezpracovanou výjimku ze spravovaného kódu (.NET). | Pokud dojde k této ukončovací kód, předpokládá, že vaše aplikace vyvolá výjimku, která zůstala neošetřené a byl ukončen proces. Ladění aplikace protokolů a výpisů paměti by měla být prvním krokem při určování, co způsobilo chybu.

## <a name="next-steps"></a>Další postup

* Další informace o [Diagnostika další běžné scénáře](service-fabric-diagnostics-common-scenarios.md)
* Získejte podrobnější přehled o protokoly Azure monitoru a nabízí načtením [co je Azure Monitor protokoly?](../operations-management-suite/operations-management-suite-overview.md)
* Další informace o protokolech Azure Monitor [upozorňování](../log-analytics/log-analytics-alerts.md) pro usnadnění detekce a Diagnostika.
* Seznamte se s [prohledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) funkce nabízí jako součást protokoly Azure monitoru
