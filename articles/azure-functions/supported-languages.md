---
title: Podporované jazyky ve službě Azure Functions
description: Zjistěte, jaké jazyky jsou podporovány (GA) a které jsou experimentální nebo ve verzi preview.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: 15d2e40127579fbd278cc3dc18653d782a515caa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957308"
---
# <a name="supported-languages-in-azure-functions"></a>Podporované jazyky ve službě Azure Functions

Tento článek vysvětluje, že nabízí úrovně podpory pro jazyky, které můžete použít s využitím Azure Functions.

## <a name="levels-of-support"></a>Úrovně podpory

Existují tři úrovně podpory:

* **Všeobecně dostupná (GA)** – plně podporované a schválena pro použití v produkčním prostředí.
* **Ve verzi Preview** – ještě není podporované, ale očekávala se pro dosažení všeobecné dostupnosti stav v budoucnu.
* **Experimentální** – není podporované a může být opuštěných v budoucnu; neposkytujeme záruku její stav GA nebo konečné verze preview.

## <a name="languages-in-runtime-1x-and-2x"></a>Jazyky v modulu runtime 1.x a 2.x

[Dvě verze modulu runtime Azure Functions](functions-versions.md) jsou k dispozici. V následující tabulce jsou uvedeny podporované jazyky v každé verzi modulu runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Experimentálních jazyků

Experimentálních jazyků ve verzi 1.x dobře neškálují a nepodporují všechny vazby. 1.x Python je například pomalá, protože modul runtime služby Functions spustí *python.exe* s každým vyvolání funkce. A i když Python podporuje vazby protokolu HTTP, nemá přístup k objektu žádosti.

Experimentální podpora pro prostředí PowerShell v 1.x je omezena na verze 5.1, protože to je, co se instaluje standardně na virtuálních počítačích, na které aplikace function App, spusťte. Pokud chcete spustit Powershellové skripty, vezměte v úvahu [Azure Automation](https://azure.microsoft.com/services/automation/).

Nepoužívejte experimentálních jazyků pro všechno, co, které využívají, protože neexistuje žádné oficiální podpora pro ně. Případy podpory by měl být otevřen pro problémy s experimentálních jazyků. 

Modul runtime verze 2.x nepodporuje experimentálních jazyků. Pouze v případě, že v produkčním prostředí může být podporovaný jazyk je přidána podpora pro nové jazyky. 

### <a name="language-extensibility"></a>Rozšíření pro jazyk

Modul runtime verze 2.x je určená k [rozšíření jazyk](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Tato rozšíření jsou vybaveny jazyků JavaScript a Java v modul runtime verze 2.x.

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
