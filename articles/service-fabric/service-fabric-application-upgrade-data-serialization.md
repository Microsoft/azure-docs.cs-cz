---
title: 'Upgrade aplikací: serializace dat | Dokumentace Microsoftu'
description: Osvědčené postupy pro serializaci dat a o jejím dopadu na postupné upgrady aplikací.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: a5f36366-a2ab-4ae3-bb08-bc2f9533bc5a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 55cbd869e7434469ebddd7af493c91bfedafc594
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614450"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Jak ovlivňuje serializaci dat upgrade aplikace
V [aplikace upgrade se zajištěním provozu](service-fabric-application-upgrade.md), upgrade se použije pro dílčí sadu uzlů, jednu upgradovací doménu najednou. Během tohoto procesu jsou některé upgradovacích domén na novější verzi aplikace a jsou některé upgradu domény na starší verzi aplikace. Během zavádění novou verzi vaší aplikace musí být schopni číst staré verze vašich dat a starou verzi vaší aplikace musí být schopni číst novou verzi vaše data. Pokud formát dat není kompatibilní s vpřed a zpět, může upgrade selhat nebo horší, mohou být data ztrátě nebo poškození. Tento článek popisuje, co představuje datový formát a nabízí osvědčené postupy pro zajištění, že vaše data jsou dopředné a zpětné kompatibilní.

## <a name="what-makes-up-your-data-format"></a>Co tvoří datový formát?
V Azure Service Fabric, který je trvale uložila a replikovala data pochází z vašeho C# třídy. Pro aplikace, které používají [Reliable Collections](service-fabric-reliable-services-reliable-collections.md), že data jsou objekty v reliable slovníky a fronty. Pro aplikace, které používají [Reliable Actors](service-fabric-reliable-actors-introduction.md), to znamená stavu zálohování pro objekt actor. Tyto C# tříd musí být serializovatelný k trvale uložila a replikovala. Proto formát dat je definován tak, že pole a vlastnosti, které se serializují a jak se serializují. Například v `IReliableDictionary<int, MyClass>` data jsou serializovaného `int` a serializovaného `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Změní kód, jejichž výsledkem je změnit formát dat
Vzhledem k tomu, že formát dat je určeno C# třídy, změny tříd mohou způsobit změnu formátu data. Musí se dbát na to, že upgradu se zajištěním provozu může zpracovat změnu formátu data. Příklady, které by mohly způsobit změny formátu dat:

* Přidáním nebo odebráním vlastnosti nebo pole
* Přejmenování pole nebo vlastnosti
* Změna typy polí a vlastností
* Změna názvu třídy nebo oboru názvů

### <a name="data-contract-as-the-default-serializer"></a>Kontrakt dat jako výchozí serializátor
Serializátor je obecně zodpovědní za načtení dat a deserializovat ho do aktuální verze i v případě, data jsou ve starší nebo *novější* verze. Výchozí serializátor je [serializátor kontraktu dat. není](https://msdn.microsoft.com/library/ms733127.aspx), který má pravidla jasně definované správy verzí. Spolehlivé kolekce umožňují serializátoru, který má být přepsána, ale Reliable Actors v tuto chvíli nepodporují. Serializátor dat hraje důležitou roli při povolování postupné upgrady. Serializátor kontraktu dat. není je serializátoru, který doporučujeme pro aplikace Service Fabric.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Jak ovlivňuje formát dat upgradu se zajištěním provozu
Během upgradu se zajištěním provozu, existují dva základní scénáře, kde může dojít serializátoru, který je starší verze nebo *novější* verze vašich dat:

1. Poté, co uzel se upgraduje a opětovném spuštění, nové serializátoru, který načte data, která je trvalý disk pomocí staré verze.
2. Během upgradu se zajištěním provozu cluster bude obsahovat kombinaci staré a nové verze kódu. Protože repliky můžete umístit v jiné doméně upgradu a repliky odesílání dat mezi sebou, setkat nové a/nebo staré verze vašich dat původní nebo novou verzi vaší serializátor.

> [!NOTE]
> "Nové verze" a "starý" zde odkazovat na verzi kódu, na kterém běží. "Nové serializátor" odkazuje na kód serializátoru, který spouští v nové verzi vaší aplikace. "Nová data" odkazuje na serializovaný C# třídy z novou verzi vaší aplikace.
> 
> 

Dvě verze formátu kód a data musí být dopředu i dozadu kompatibilní. Pokud nejsou kompatibilní, upgradu se zajištěním provozu může selhat nebo může dojít ke ztrátě dat. Upgradu se zajištěním provozu může selhat, protože kód nebo serializátor může vyvolat výjimky nebo chybu Pokud se setká s jinou verzi. Pokud například byla přidána nová vlastnost ale staré serializátor zahodí ji během deserializace, může dojít ke ztrátě dat.

Kontrakt dat je doporučené řešení pro zajištění, že vaše data jsou kompatibilní. Obsahuje pravidla jasně definované správy verzí pro přidání, odebrání a změna polí. Je také podpora zabývají neznámé pole, zapojení do procesu serializace a deserializace a zabývajících se dědičnost tříd. Další informace najdete v tématu [pomocí kontraktu dat](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Další postup
[Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgrade aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí Powershellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgrade aplikace pomocí Powershellu.

Řídí, jak vaše aplikace upgradovala pomocí [parametry upgradu](service-fabric-application-upgrade-parameters.md).

Zjistěte, jak používat pokročilé funkce při upgradu aplikace rekapitulací [Pokročilá témata](service-fabric-application-upgrade-advanced.md).

Vyřešit běžné problémy v upgradech aplikací odkazující na kroky v [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md).

