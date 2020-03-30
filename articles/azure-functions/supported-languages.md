---
title: Podporované jazyky ve funkcích Azure
description: Zjistěte, které jazyky jsou podporovány (GA) a které jsou experimentální nebo ve verzi Preview.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 029ea753439dca3093bf214a5adfb6d58a1fe567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74942252"
---
# <a name="supported-languages-in-azure-functions"></a>Podporované jazyky ve funkcích Azure

Tento článek vysvětluje úrovně podpory nabízené pro jazyky, které můžete použít s funkcemi Azure.

## <a name="levels-of-support"></a>Úrovně podpory

Existují tři úrovně podpory:

* **Obecně dostupné (GA)** - Plně podporované a schválené pro použití ve výrobě.
* **Náhled** - Zatím není podporován, ale očekává se, že v budoucnu dosáhne stavu GA.
* **Experimentální** - není podporována a může být opuštěna v budoucnu; žádná záruka případného náhledu nebo stavu GA.

## <a name="languages-by-runtime-version"></a>Jazyky podle verze za běhu 

K dispozici jsou [tři verze runtime Azure Functions.](functions-versions.md) V následující tabulce jsou uvedeny jazyky, které jsou podporovány v každé verzi modulu runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Experimentální jazyky

Experimentální jazyky ve verzi 1.x nejsou dobře škálovat a nepodporují všechny vazby.

Nepoužívejte experimentální funkce pro nic, na co se spoléháte, protože pro ně neexistuje žádná oficiální podpora. Případy podpory by neměly být otevřeny pro problémy s experimentálními jazyky. 

Novější runtime verze nepodporují experimentální jazyky. Podpora pro nové jazyky je přidána pouze v případě, že jazyk může být podporován v produkčním prostředí. 

### <a name="language-extensibility"></a>Rozšiřitelnost jazyka

Počínaje verzí 2.x je runtime navržen tak, aby nabízel [rozšiřitelnost jazyka](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Jazyky JavaScript a Java v běhu 2.x jsou vytvořeny s touto rozšiřitelností.

## <a name="next-steps"></a>Další kroky

Další informace o vývoji funkcí v podporovaných jazycích naleznete v následujících zdrojích:

+ [Odkaz na vývojáře knihovny tříd C#](functions-dotnet-class-library.md)
+ [Odkaz na vývojáře skriptů Jazyka C#](functions-reference-csharp.md)
+ [Referenční příručka pro vývojáře v Jazyce Java](functions-reference-java.md)
+ [Referenční příručka pro vývojáře javascriptu](functions-reference-node.md)
+ [Odkaz na vývojáře prostředí PowerShell](functions-reference-powershell.md)
+ [Odkaz na vývojáře pythonu](functions-reference-python.md)
+ [Odkaz vývojáře jazyka TypeScript](functions-reference-node.md#typescript)
