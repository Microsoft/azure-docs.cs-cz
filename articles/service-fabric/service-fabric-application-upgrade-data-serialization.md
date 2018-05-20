---
title: 'Upgrade aplikace: serializace dat | Microsoft Docs'
description: Osvědčené postupy pro serializaci dat a jak ovlivňuje postupné upgrady aplikací.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: a5f36366-a2ab-4ae3-bb08-bc2f9533bc5a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 2f6fad0ecca09ff9210b5961301fea3446a88f11
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Jak ovlivňuje serializace dat upgradu aplikace
V [vrácení upgradu aplikace](service-fabric-application-upgrade.md), upgradu se použije pro dílčí sadu uzlů, jednu upgradovací doménu najednou. Během tohoto procesu jsou některé domény upgrade na novější verzi aplikace a jsou některé upgradu domény na starší verzi aplikace. Při zavedení nová verze aplikace musí být možné číst stará verze vaše data a starší verzi aplikace musí být možné číst novou verzi vaše data. Pokud formát dat není vpřed a zpětně kompatibilní, upgrade může selhat nebo horší, mohou být data ztrátě nebo poškození. Tento článek popisuje, co se považuje za vaše formát dat a nabízí osvědčené postupy pro zajištění, aby vaše data byla vpřed a zpětně kompatibilní.

## <a name="what-makes-up-your-data-format"></a>Co tvoří vaše formát dat?
V Azure Service Fabric data, která je trvale uložila a replikovala pochází z třídy jazyka C#. Pro aplikace, které používají [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md), že data jsou objekty ve slovnících spolehlivé a fronty. Pro aplikace, které používají [Reliable Actors](service-fabric-reliable-actors-introduction.md), který je základní stav objektu actor. Tyto třídy jazyka C# musí být serializovatelný k trvale uložila a replikovala. Formát dat je proto definované polí a vlastností, které jsou serializovány, a také o tom, že serializovat. Například v `IReliableDictionary<int, MyClass>` data jsou serializovaný seznam `int` a serializovaný seznam `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Kód změny, které způsobily změnu formátu dat
Vzhledem k tomu, že formát dat je určen podle třídy jazyka C#, může způsobit změny třídy změně formátu data. Dbát musí zajistit, že postupného upgradu může zpracovat změny dat formátu. Příklady, které by mohly způsobit změny formátu dat:

* Přidání nebo odebrání vlastnosti nebo pole
* Přejmenování polí a vlastností
* Změny typu polí a vlastností
* Změna názvu třídy nebo obor názvů

### <a name="data-contract-as-the-default-serializer"></a>Kontrakt dat jako výchozí serializátor
Serializátor je obecně odpovědná za čtení dat a deserializaci do aktuální verze i v případě, že data jsou v starší nebo *novější* verze. Výchozí serializátor je [serializátor kontraktu dat](https://msdn.microsoft.com/library/ms733127.aspx), který má pravidla dobře definovaný verzí. Spolehlivé kolekce povolit serializátoru, který má být přepsána, ale Reliable Actors aktuálně nepodporují. Serializátor dat hraje důležitou roli při povolování postupné upgrady. Serializátor kontraktu dat je serializátoru, který doporučujeme pro aplikace Service Fabric.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Jak ovlivňuje formát dat postupného upgradu
Během postupného upgradu, jsou dva základní scénáře, kde serializátor setkat starší nebo *novější* verzi dat:

1. Po uzlu upgradu a spuštění zálohování, nové serializátoru, který načte data, která byla trvalé na pevném disku předchozí verzi aplikace.
2. Během postupného upgradu cluster bude obsahovat kombinací staré a nové verze vašeho kódu. Vzhledem k tomu, že repliky může umístěny v různých doménách upgradu a repliky odesílat data mezi sebou, staré nebo nové verze vaše data mohou být zjištěny staré nebo nové verze vaší serializátor.

> [!NOTE]
> "Nové verze" a "staré verze" Zde naleznete na verzi vašeho kódu, který běží. "Nové serializátor" odkazuje na kód serializátoru, který spouští v nové verzi vaší aplikace. "Nová data" odkazuje na serializovaných třída C# z nové verze aplikace.
> 
> 

Dvě verze formátu kód a data musí být vpřed a zpětně kompatibilní. Pokud nejsou kompatibilní, postupného upgradu může selhat nebo data mohou být ztracena. Postupného upgradu může selhat, protože kód nebo serializátor může výjimku výjimky nebo chybu při zjistí jinou verzi. Data mohou být ztracena, pokud například byla přidána nová vlastnost ale staré serializátor zahodí se během deserializace.

Kontrakt dat je doporučené řešení pro zajištění, aby vaše data byla kompatibilní. Obsahuje pravidla dobře definovaný verzí pro přidání, odebrání a změna pole. Je také podpora zabývají neznámé pole, zapojování do procesu serializace a deserializace a plánování práce s dědičnosti tříd. Další informace najdete v tématu [pomocí kontrakt dat](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Další postup
[Upgrade vaší aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgrade aplikace pomocí sady Visual Studio.

[Upgrade vaší aplikace pomocí prostředí Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgrade aplikace pomocí prostředí PowerShell.

Řídí, jak vaše aplikace upgraduje pomocí [Upgrade parametry](service-fabric-application-upgrade-parameters.md).

Další informace o použití pokročilých funkcí při upgradu vaší aplikace tím, že odkazuje na [Pokročilá témata](service-fabric-application-upgrade-advanced.md).

Řešení běžných potíží v upgradů aplikací podle kroků v části [řešení potíží s aplikací upgrady ](service-fabric-application-upgrade-troubleshooting.md).

