---
title: Podporované jazyky ve službě Azure Functions
description: Zjistěte, jaké jazyky jsou podporovány (GA) a které jsou experimentální nebo ve verzi preview.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: glenga
ms.openlocfilehash: 00f291e903948bf43bc997816b6072186cf1f889
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343079"
---
# <a name="supported-languages-in-azure-functions"></a>Podporované jazyky ve službě Azure Functions

Tento článek vysvětluje, že nabízí úrovně podpory pro jazyky, které můžete použít s využitím Azure Functions.

## <a name="levels-of-support"></a>Úrovně podpory

Existují tři úrovně podpory:

* **Všeobecně dostupná (GA)** – plně podporované a schválena pro použití v produkčním prostředí.
* **Ve verzi Preview** – ještě není podporované, ale očekávala se pro dosažení všeobecné dostupnosti stav v budoucnu.
* **Experimentální** – není podporované a může být opuštěných v budoucnu; neposkytujeme záruku její stav GA nebo konečné verze preview.

## <a name="languages-in-runtime-1x-and-2x"></a>Jazyky v modulu runtime 1.x a 2.x

[Dvě verze modulu runtime Azure Functions](functions-versions.md) jsou k dispozici. Modul runtime verze 1.x je zavedení všeobecné dostupnosti Je pouze modul runtime, který je schválená pro produkční aplikace. Modul runtime verze 2.x je aktuálně ve verzi preview, takže jazyky, které podporuje, jsou ve verzi preview. V následující tabulce jsou uvedeny podporované jazyky v každé verzi modulu runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Experimentálních jazyků

Experimentálních jazyků v 1.x dobře neškálují a nepodporují všechny vazby. Python je například pomalá, protože modul runtime služby Functions spustí *python.exe* s každým vyvolání funkce. A i když Python podporuje vazby protokolu HTTP, nemá přístup k objektu žádosti.

Experimentální podporu pro prostředí PowerShell je omezena na verzi 4.0, protože to je, co je nainstalován na virtuálních počítačích, na kterých běží aplikace Function App na. Pokud chcete spustit Powershellové skripty, vezměte v úvahu [Azure Automation](https://azure.microsoft.com/services/automation/).

Modul runtime verze 2.x nepodporuje experimentálních jazyků. V 2.x přidáme podporu jazyka pouze v případě, že se bude dobře škálovat a podporuje rozšířené aktivační události.

Pokud chcete použít jeden z těchto jazyků, které jsou dostupné jenom v 1.x, Zůstaňte na modul runtime verze 1.x. Ale nepoužívejte experimentálních jazyků pro všechno, co, které využívají, protože neexistuje žádné oficiální podpora pro ně. Si můžete vyžádat pomoc podle [vytvoření problémů Githubu](https://github.com/Azure/azure-webjobs-sdk-script/issues), ale případy podpory by měl být otevřen pro problémy s experimentálních jazyků. 

### <a name="language-extensibility"></a>Rozšíření pro jazyk

Modul runtime verze 2.x je určená k [rozšíření jazyk](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Mezi první jazyky, které chcete být podle tohoto rozšíření je model Javu, která je ve verzi preview v 2.x.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak používat jeden z těchto jazyků GA nebo ve verzi preview ve službě Azure Functions, najdete v následujících zdrojích:

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)