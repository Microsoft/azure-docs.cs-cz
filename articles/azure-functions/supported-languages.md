---
title: Podporované jazyky v Azure Functions
description: Další jazyky, které jsou podporovány (GA) a které jsou experimentální nebo v náhledu.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: tdykstra
ms.openlocfilehash: 5786a206b258cfe7c48f52ead9b5a4cceb64cd5f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2017
ms.locfileid: "24879437"
---
# <a name="supported-languages-in-azure-functions"></a>Podporované jazyky v Azure Functions

Tento článek vysvětluje, že nabízí úrovně podpory pro jazyky, které můžete použít s Azure Functions.

## <a name="levels-of-support"></a>Úrovně podpory

Existují tři úrovně podpory:

* **Obecně dostupná (GA)** – plně podporované a schváleny pro použití v provozním prostředí.
* **Náhled** – ještě nebyla podporována, ale očekává se dosáhne stavu GA v budoucnu.
* **Experimentální** – není podporované a může být opuštěny v budoucnu; žádná záruka případné preview nebo GA stav.

## <a name="languages-in-runtime-1x-and-2x"></a>Jazyky v modulu runtime 1.x a 2.x

[Dvě verze modulu runtime Azure Functions](functions-versions.md) jsou k dispozici. Modul runtime 1.x je všeobecné Je pouze modul runtime, který je schválená pro výrobní aplikace. Modul runtime 2.x je aktuálně ve verzi preview, takže jazyky, které podporuje jsou ve verzi preview. V následující tabulce jsou uvedeny podporované jazyky v každé verzi modulu runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Experimentální jazyky

Experimentální jazyků 1.x nemáte škálovat a nepodporují všechny vazby. Python je třeba pomalé, protože běží Functions runtime *python.exe* s každým vyvolání funkce. A při Python podporuje vazby HTTP, nemůže přistupovat k objektu žádosti.

Experimentální podpora pro PowerShell je omezený na verze 4.0, protože se jedná o tom, co je nainstalované na virtuálních počítačích, které funkce aplikace běží na. Pokud chcete spouštět skripty prostředí PowerShell, zvažte [Azure Automation](https://azure.microsoft.com/services/automation/).

Modul runtime 2.x nepodporuje experimentální jazyky. V 2.x přidáme podporu pro jazyk, pouze v případě, že se dobře Škáluje a podporuje rozšířené aktivační události.

Pokud chcete použít jeden z jazyků, které jsou dostupné jenom v 1.x, Zůstaňte na 1.x runtime. Ale nepoužívejte experimentální jazyky pro všechno, co byste tedy spoléhat na, protože neexistuje žádná podpora oficiální pro ně. Může požádat o pomoc ve [vytváření Githubu problémy](https://github.com/Azure/azure-webjobs-sdk-script/issues), ale případů podpory by neměl být otevřené pro problémy s povolenými experimentálními jazyky. 

### <a name="language-extensibility"></a>Jazyková rozšíření

Modul runtime 2.x je určená k poskytování [rozšiřitelnosti jazyka](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Model mezi první jazyky, které chcete být založené na toto rozšíření je Java, které je ve verzi preview v 2.x.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat jeden z jazyků GA nebo preview v Azure Functions, najdete v následujících zdrojích informací:

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)