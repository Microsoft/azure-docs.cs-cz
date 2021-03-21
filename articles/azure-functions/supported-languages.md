---
title: Podporované jazyky v Azure Functions
description: Seznamte se s podporovanými jazyky (GA), které jsou ve verzi Preview, a způsoby rozšiřování vývoje funkcí do jiných jazyků.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: da3100521faae61e9803aaa82dbf91045a7ecbc9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98108547"
---
# <a name="supported-languages-in-azure-functions"></a>Podporované jazyky v Azure Functions

Tento článek vysvětluje úrovně podpory poskytované pro jazyky, které můžete používat s Azure Functions. Popisuje také strategie pro vytváření funkcí pomocí jazyků, které nejsou nativně podporované.

## <a name="levels-of-support"></a>Úrovně podpory

Existují dvě úrovně podpory:

* **Všeobecně dostupná (GA)** – plně podporované a schválené pro použití v produkčním prostředí.
* **Verze Preview** – zatím není podporovaná, ale očekává se, že bude v budoucnu dostupný stav GA.

## <a name="languages-by-runtime-version"></a>Jazyky podle verze modulu runtime 

K dispozici jsou [tři verze modulu runtime Azure Functions](functions-versions.md) . Následující tabulka uvádí, které jazyky jsou podporovány v každé verzi modulu runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="custom-handlers"></a>Vlastní obslužné rutiny

Vlastní obslužné rutiny jsou jednoduché webové servery, které přijímají události od hostitele Azure Functions. Libovolný jazyk, který podporuje primitivní prvky HTTP, může implementovat vlastní obslužnou rutinu. To znamená, že vlastní obslužné rutiny lze použít k vytvoření funkcí v jazycích, které nejsou oficiálně podporovány. Další informace najdete v tématu [Azure Functions vlastní obslužné rutiny](functions-custom-handlers.md).

## <a name="language-extensibility"></a>Jazyková rozšíření

Počínaje verzí 2. x je modul runtime navržený tak, aby nabízel [jazykové rozšíření](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Jazyky JavaScript a Java v modulu runtime 2. x jsou sestaveny s tímto rozšířením.

## <a name="next-steps"></a>Další kroky

Další informace o vývoji funkcí v podporovaných jazycích najdete v následujících zdrojích informací:

+ [Referenční dokumentace pro vývojáře knihovny tříd C#](functions-dotnet-class-library.md)
+ [Referenční informace pro vývojáře skriptů v jazyce C#](functions-reference-csharp.md)
+ [Referenční dokumentace pro vývojáře v jazyce Java](functions-reference-java.md)
+ [JavaScript – reference pro vývojáře](functions-reference-node.md)
+ [Referenční informace pro vývojáře PowerShellu](functions-reference-powershell.md)
+ [Referenční dokumentace pro vývojáře v Pythonu](functions-reference-python.md)
+ [Referenční dokumentace pro vývojáře TypeScript](functions-reference-node.md#typescript)
