---
title: 'Upgrade aplikace: serializace dat'
description: Osvědčené postupy pro serializaci dat a jejich vliv na postupné upgrady aplikací.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: d502e74139c543d4183a75faa6bea1948d9f3e56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86247978"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Vliv serializace dat na upgrade aplikace
V rámci [upgradu aplikace](service-fabric-application-upgrade.md)se upgrade aplikuje na podmnožinu uzlů, jednu upgradovací doménu v jednom okamžiku. Během tohoto procesu se některé domény upgradu nacházejí v novější verzi aplikace a některé domény upgradu se nacházejí ve starší verzi aplikace. Při zavedení musí být v nové verzi aplikace možné číst starou verzi vašich dat a stará verze vaší aplikace musí být schopná přečíst si novou verzi vašich dat. Pokud formát dat není předáván dál a zpětně kompatibilní, upgrade může selhat nebo může dojít ke ztrátě nebo poškození dat. Tento článek popisuje, co znamená váš formát dat, a nabízí osvědčené postupy pro zajištění, že vaše data jsou předávána a zpětně kompatibilní.

## <a name="what-makes-up-your-data-format"></a>Čím je váš formát dat?
V Azure Service Fabric data, která jsou trvalá a replikovaná, pocházejí z vašich tříd C#. Pro aplikace, které používají [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md), jsou tato data objekty ve spolehlivých slovnících a frontách. Pro aplikace, které používají [Reliable Actors](service-fabric-reliable-actors-introduction.md), je to stav zálohování objektu actor. Aby bylo možné tyto třídy jazyka C# zachovat a replikovat, musí být serializovatelné. Proto je formát dat definován pomocí polí a vlastností, které jsou serializovány, a také způsobem jejich serializace. Například v `IReliableDictionary<int, MyClass>` datech jsou serializovaná `int` a serializovaná `MyClass` .

### <a name="code-changes-that-result-in-a-data-format-change"></a>Změny kódu, které mají za následek změnu formátu dat
Vzhledem k tomu, že formát dat je určen třídami jazyka C#, mohou změny v třídách způsobit změnu formátu dat. Je nutné dbát na to, aby se změny formátu dat mohly zpracovávat v rámci postupného upgradu. Příklady, které mohou způsobit změny formátu dat:

* Přidání nebo odebrání polí nebo vlastností
* Přejmenování polí nebo vlastností
* Změna typů polí nebo vlastností
* Změna názvu nebo oboru názvů třídy

### <a name="data-contract-as-the-default-serializer"></a>Kontrakt dat jako výchozí serializátor
Serializátor je obecně zodpovědný za čtení dat a jejich deserializaci do aktuální verze, a to i v případě, že jsou data ve starší nebo *novější* verzi. Výchozí serializátor je [serializátor kontraktu dat](/dotnet/framework/wcf/feature-details/using-data-contracts), který má dobře definovaná pravidla správy verzí. Spolehlivé kolekce umožňují přepsat serializátor, ale Reliable Actors aktuálně ne. Serializátor dat hraje důležitou roli při povolování postupné inovace. Serializátor kontraktu dat je serializátor, který doporučujeme pro Service Fabric aplikace.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Vliv formátu dat na postupný upgrade
Během postupného upgradu existují dva hlavní scénáře, kde serializátor může narazit na starší nebo *novější* verzi vašich dat:

1. Po upgradu uzlu a spuštění zálohování nový serializátor načte data, která byla trvalá na disk se starou verzí.
2. Během postupného upgradu bude cluster obsahovat kombinaci staré a nové verze kódu. Vzhledem k tomu, že repliky mohou být umístěny v různých upgradovacích doménách a repliky odesílají data vzájemně, je možné, že nová a/nebo stará verze vašeho serializátoru může být k dispozici pro novou a/nebo starou verzi vašich dat.

> [!NOTE]
> "Nová verze" a "stará verze" zde odkazuje na verzi kódu, na kterém je spuštěný. "Nový serializátor" odkazuje na kód serializátoru, který je spuštěn v nové verzi aplikace. "Nová data" odkazují na serializovanou třídu C# z nové verze vaší aplikace.
> 
> 

Obě verze kódu a formátu dat musí být kompatibilní s dopředně i zpět. Pokud nejsou kompatibilní, může upgrade za provozu selhat nebo může dojít ke ztrátě dat. Postupná inovace může selhat, protože kód nebo serializátor může vyvolat výjimky nebo chybu, když nalezne jinou verzi. Data mohou být ztracena, pokud například byla přidána nová vlastnost, ale starý serializátor je během deserializace zahozen.

Pro zajištění kompatibility vašich dat je doporučeným řešením kontraktu dat. Má jasně definovaná pravidla správy verzí pro přidání, odebrání a změnu polí. Také podporuje pro práci s neznámými poli, zapojení do procesu serializace a deserializace a řešit dědičnost tříd. Další informace najdete v tématu [použití kontraktu dat](/dotnet/framework/wcf/feature-details/using-data-contracts).

## <a name="next-steps"></a>Další kroky
[Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgradem aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí PowerShellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgradem aplikace pomocí PowerShellu.

Pomocí [parametrů upgradu](service-fabric-application-upgrade-parameters.md)lze řídit, jak se vaše aplikace upgradují.

Naučte se používat pokročilé funkce při upgradu vaší aplikace, které odkazují na [Pokročilá témata](service-fabric-application-upgrade-advanced.md).

Pomocí postupu v části [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md)můžete opravit běžné problémy s upgrady aplikací.
