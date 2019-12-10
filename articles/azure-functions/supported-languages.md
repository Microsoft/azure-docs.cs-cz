---
title: Podporované jazyky v Azure Functions
description: Zjistěte, které jazyky jsou podporované (GA) a které jsou experimentální nebo ve verzi Preview.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 029ea753439dca3093bf214a5adfb6d58a1fe567
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74942252"
---
# <a name="supported-languages-in-azure-functions"></a>Podporované jazyky v Azure Functions

Tento článek vysvětluje úrovně podpory poskytované pro jazyky, které můžete používat s Azure Functions.

## <a name="levels-of-support"></a>Úrovně podpory

Existují tři úrovně podpory:

* **Všeobecně dostupná (GA)** – plně podporované a schválené pro použití v produkčním prostředí.
* **Verze Preview** – zatím není podporovaná, ale očekává se, že bude v budoucnu dostupný stav GA.
* **Experimentální** – nepodporované a můžou být v budoucnu opuštěné; bez záruky na konečný stav Preview nebo GA.

## <a name="languages-by-runtime-version"></a>Jazyky podle verze modulu runtime 

K dispozici jsou [tři verze modulu runtime Azure Functions](functions-versions.md) . Následující tabulka uvádí, které jazyky jsou podporovány v každé verzi modulu runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Experimentální jazyky

Experimentální jazyky verze 1. x se neškálují správně a nepodporují všechny vazby.

Nepoužívejte experimentální funkce pro cokoli, co jste se spoléhali, protože pro ně není oficiální podpora k dispozici. Pro problémy s experimentálními jazyky by se neměly otevírat případy podpory. 

Novější verze modulu runtime nepodporují experimentální jazyky. Podpora pro nové jazyky se přidá jenom v případě, že je možné jazyk v produkčním prostředí podporovat. 

### <a name="language-extensibility"></a>Jazyková rozšíření

Počínaje verzí 2. x je modul runtime navržený tak, aby nabízel [jazykové rozšíření](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Jazyky JavaScript a Java v modulu runtime 2. x jsou sestaveny s tímto rozšířením.

## <a name="next-steps"></a>Další kroky

Další informace o vývoji funkcí v podporovaných jazycích najdete v následujících zdrojích informací:

+ [C#Referenční dokumentace pro vývojáře knihovny tříd](functions-dotnet-class-library.md)
+ [C#Reference pro vývojáře skriptů](functions-reference-csharp.md)
+ [Referenční dokumentace pro vývojáře v jazyce Java](functions-reference-java.md)
+ [JavaScript – reference pro vývojáře](functions-reference-node.md)
+ [Referenční informace pro vývojáře PowerShellu](functions-reference-powershell.md)
+ [Referenční dokumentace pro vývojáře v Pythonu](functions-reference-python.md)
+ [Referenční dokumentace pro vývojáře TypeScript](functions-reference-node.md#typescript)
