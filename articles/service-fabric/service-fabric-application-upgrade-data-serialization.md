---
title: 'Upgrade aplikace: serializace dat'
description: Doporučené postupy pro serializaci dat a jeho vliv na postupné upgrady aplikací.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 7dc60c28b56982f82c1ac90db55ac752977ea2d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457492"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Jak serializace dat ovlivňuje upgrade aplikace
Při [upgradu postupných aplikací](service-fabric-application-upgrade.md)je upgrade použit na podmnožinu uzlů po jedné inovované doméně. Během tohoto procesu jsou některé upgradovací domény v novější verzi aplikace a některé upgradovací domény jsou ve starší verzi aplikace. Během zavádění musí být nová verze aplikace schopna číst starou verzi dat a stará verze aplikace musí být schopna číst novou verzi dat. Pokud formát dat není kompatibilní dopředu a zpět, upgrade může selhat nebo horší, data mohou být ztracena nebo poškozena. Tento článek popisuje, co představuje formát dat a nabízí osvědčené postupy pro zajištění, že vaše data jsou dopředné a zpětně kompatibilní.

## <a name="what-makes-up-your-data-format"></a>Co tvoří formát dat?
Ve službě Azure Fabric data, která je trvalá a replikována pochází z tříd jazyka C#. Pro aplikace, které používají [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md), že data jsou objekty ve spolehlivé slovníky a fronty. Pro aplikace, které používají [spolehlivé objekty actor](service-fabric-reliable-actors-introduction.md), to je stav zálohování pro objekt actor. Tyto třídy Jazyka C# musí být serializovatelné, aby byly trvalé a replikované. Proto je formát dat definován pole a vlastnosti, které jsou serializovány, jakož i jak jsou serializovány. Například v `IReliableDictionary<int, MyClass>` data je serializované `int` a serializované `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Změny kódu, které vedou ke změně formátu dat
Vzhledem k tomu, že formát dat je určen c# třídy, změny tříd může způsobit změnu formátu dat. Je třeba dbát na to, aby postupná inovace mohla zpracovat změnu formátu dat. Příklady, které mohou způsobit změny formátu dat:

* Přidání nebo odebrání polí nebo vlastností
* Přejmenování polí nebo vlastností
* Změna typů polí nebo vlastností
* Změna názvu třídy nebo oboru názvů

### <a name="data-contract-as-the-default-serializer"></a>Kontrakt dat jako výchozí serializátor
Serializátor je obecně zodpovědný za čtení dat a jejich rekonstrukci do aktuální verze, i když jsou data ve starší nebo *novější* verzi. Výchozí serializátor je [serializátor kontraktů dat](https://msdn.microsoft.com/library/ms733127.aspx), který má dobře definovaná pravidla správy verzí. Spolehlivé kolekce umožňují serializátor upřednostnit, ale reliable actors aktuálně ne. Serializátor dat hraje důležitou roli při povolení postupné inovace. Serializátor kontraktu dat je serializátor, který doporučujeme pro aplikace Service Fabric.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Vliv datového formátu na postupnou inovaci
Během postupné inovace existují dva hlavní scénáře, kde serializátor může dojít starší nebo *novější* verzi dat:

1. Po upgradu uzlu a spustí zálohovat, nový serializátor načte data, která byla uložena na disk starou verzí.
2. Během postupné inovace bude cluster obsahovat kombinaci staré a nové verze kódu. Vzhledem k tomu, že repliky mohou být umístěny v různých doménách upgradu a repliky si navzájem odesílají data, může nová nebo stará verze dat narazit na novou nebo starou verzi serializátoru.

> [!NOTE]
> "Nová verze" a "stará verze" zde odkazují na verzi kódu, který je spuštěn. "Nový serializátor" odkazuje na serializační kód, který je spuštěn v nové verzi aplikace. "Nová data" odkazuje na serializované třídy C# z nové verze aplikace.
> 
> 

Dvě verze kódu a formátu dat musí být kompatibilní dopředu i zpětně. Pokud nejsou kompatibilní, postupná inovace může selhat nebo mohou být ztracena data. Postupná inovace může selhat, protože kód nebo serializátor může vyvolat výjimky nebo chybu, když narazí na druhou verzi. Data mohou být ztraceny, pokud byla například přidána nová vlastnost, ale starý serializátor je během deserializace zahodí.

Data Contract je doporučené řešení pro zajištění kompatibility vašich dat. Obsahuje dobře definovaná pravidla správy verzí pro přidávání, odebírání a změnu polí. Má také podporu pro práci s neznámými poli, hákování do procesu serializace a deserializace a řešení dědičnosti třídy. Další informace naleznete [v tématu Using Data Contract](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Další kroky
[Inovace aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgradem aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí powershellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgradem aplikace pomocí PowerShellu.

Pomocí funkce [Parametry upgradu](service-fabric-application-upgrade-parameters.md)můžete řídit způsob upgradu aplikace .

Naučte se používat pokročilé funkce při inovaci aplikace odkazem na [upřesnit témata](service-fabric-application-upgrade-advanced.md).

Běžné problémy při upgradu aplikací opravíte odkazem na kroky při [řešení potíží s inovacemi aplikací](service-fabric-application-upgrade-troubleshooting.md).

