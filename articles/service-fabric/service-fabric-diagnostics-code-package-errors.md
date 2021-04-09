---
title: Diagnostikujte běžné chyby balíčku kódu pomocí Service Fabric
description: Přečtěte si, jak řešit běžné chyby balíčků kódu pomocí Azure Service Fabric
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 400651b240f0518a85b9deb7a7293a77a88b0861
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591706"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Diagnostikujte běžné chyby balíčku kódu pomocí Service Fabric

Tento článek popisuje, co znamená, aby balíček kódu neočekávaně ukončil. Poskytuje pohled na možné příčiny běžných chybových kódů spolu s postupem řešení potíží.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Kdy dojde k neočekávanému ukončení procesu nebo kontejneru?

Když Azure Service Fabric obdrží požadavek na spuštění balíčku kódu, začne připravovat prostředí v místním systému podle možností nastavených v manifestech aplikací a služeb. Tyto přípravy můžou zahrnovat rezervaci koncových bodů a prostředků sítě, konfiguraci pravidel brány firewall nebo nastavení omezení zásad správného řízení prostředků. 

Po správném nakonfigurování prostředí se Service Fabric pokusí balíček kódu vyvolat. Tento krok je považován za úspěšný, pokud modul runtime operačního systému nebo kontejneru hlásí, že proces nebo kontejner byl úspěšně aktivován. Pokud se aktivace nezdařila, měla by se zobrazit zpráva o stavu v SFX, která vypadá přibližně takto:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Po úspěšné aktivaci balíčku kódu Service Fabric začne sledovat jeho životnost. V tomto okamžiku může být proces nebo kontejner kdykoli ukončen z několika důvodů. Může se například stát, že se nepovedlo inicializovat knihovnu DLL, nebo když se operační systém vyčerpal z prostoru haldy plochy. Pokud byl balíček kódu ukončen, měla by se zobrazit následující zpráva o stavu v SFX:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

Ukončovací kód v této zprávě o stavu je jediný, který znamená, že proces nebo kontejner poskytuje informace o tom, proč se ukončil. Může být vygenerována libovolnou úrovní zásobníku. Například tento kód ukončení může souviset s chybou operačního systému nebo .NET nebo může být vyvolán vaším kódem. Tento článek slouží jako výchozí bod pro diagnostiku zdroje ukončovacích kódů ukončení a možných řešení. Mějte ale na paměti, že se jedná o obecná řešení běžných scénářů a nemusí se vztahovat na chybu, kterou si vidíte.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Jak poznám, jestli Service Fabric ukončil balíček kódu?

Service Fabric může být zodpovědný za ukončení balíčku kódu z nejrůznějších důvodů. Může se například rozhodnout umístit balíček kódu na jiný uzel pro účely vyrovnávání zatížení. Můžete ověřit, zda balíček kódu Service Fabric ukončil, pokud se zobrazí libovolný ukončovací kód v následující tabulce.

>[!NOTE]
> Pokud se proces nebo kontejner ukončí s ukončovacím kódem jiným než kódy v následující tabulce, Service Fabric není zodpovědný za jeho ukončení.

Ukončovací kód | Description
--------- | -----------
7147 | Indikuje, že Service Fabric řádným vypnutím procesu nebo kontejneru odesláním signálu CTRL + C.
7148 | Indikuje, že Service Fabric ukončil proces nebo kontejner. Někdy tento kód chyby indikuje, že proces nebo kontejner neodpověděl včas po odeslání signálu CTRL + C a musel být ukončen.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Další běžné kódy chyb a jejich možné opravy

Ukončovací kód | Hexadecimální hodnota | Krátký popis | Původní příčina | Potenciální Oprava
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Tato chyba někdy znamená, že počítač má nedostatek prostoru v haldě plochy. Tato příčina je obzvláště pravděpodobná v případě, že máte mnoho procesů, které patří do vaší aplikace spuštěné na uzlu. | Pokud váš program není sestavený tak, aby reagoval na signály CTRL + C, můžete povolit nastavení **EnableActivateNoWindow** v manifestu clusteru. Povolení tohoto nastavení znamená, že se balíček kódu spustí bez okna grafického uživatelského rozhraní a neobdrží signály CTRL + C. Tato akce také snižuje množství prostoru haldy plochy, který každý proces spotřebovává. Pokud váš balíček kódu potřebuje přijmout signály CTRL + C, můžete zvětšit velikost haldy plochy uzlu.
3762504530 | 0xe0434352 | – | Tato hodnota představuje kód chyby pro neošetřenou výjimku ze spravovaného kódu (tj. .NET). | Tento ukončovací kód označuje, že vaše aplikace vyvolala výjimku, která zůstává neošetřená a která ukončila proces. Jako první krok při určení toho, co aktivoval tuto chybu, proveďte ladění protokolů aplikace a souborů výpisu paměti.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [diagnostice jiných běžných scénářů](service-fabric-diagnostics-common-scenarios.md).
* Získejte podrobnější přehled o Azure Monitorch protokolech a možnostech, které nabízí [Azure monitor přehled](../azure-monitor/overview.md).
* Přečtěte si další informace o tom, jak protokoly Azure Monitor [upozorňování](../azure-monitor/alerts/alerts-overview.md) na pomoc při detekci a diagnostice.
* Seznamte se s funkcemi [prohledávání protokolů a dotazování](../azure-monitor/logs/log-query-overview.md) , které nabízí jako součást protokolů Azure monitor.
