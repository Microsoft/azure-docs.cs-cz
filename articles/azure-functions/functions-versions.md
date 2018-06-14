---
title: Přehled Azure verze modulu runtime funkce
description: Azure Functions podporuje více verzí modulu runtime. Přečtěte si rozdíly mezi nimi a jak zvolit ten, který je pro vás nejvhodnější.
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 9f916aaa8032ff519709d73a1c1f51195f811686
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28919351"
---
# <a name="azure-functions-runtime-versions-overview"></a>Přehled Azure verze modulu runtime funkce

 Existují dvě hlavní verze modulu runtime Azure Functions.: 1.x a 2.x. Tento článek vysvětluje, jak zvolit které hlavní verze se má použít.

> [!IMPORTANT] 
> Modul runtime 1.x je pouze verze schválené pro použití v provozním prostředí.

| Modul runtime | Status |
|---------|---------|
|1.x|Obecně dostupná (GA)|
|2.x|Preview|

Informace o tom, jak nakonfigurovat aplikaci funkce nebo vývojového prostředí pro konkrétní verzi najdete v tématu [jak mít verze modulu runtime Azure Functions](set-runtime-version.md) a [kódu a testování Azure Functions místně](functions-run-local.md).

## <a name="cross-platform-development"></a>Vývoj pro různé platformy

Modul runtime 1.x podporuje vývoj a hostování jenom na portálu nebo v systému Windows. Modul runtime 2.x běží na .NET Core, což znamená, že ho můžete spustit na všech platformách podporovaných aplikací .NET Core, včetně systému macOS a Linux. To umožňuje vývoj napříč platformami a scénáře hostingu, které nejsou možné pomocí 1.x.

## <a name="languages"></a>Jazyky

Modul runtime 2.x používá nový model rozšiřitelnosti jazyka. JavaScript a Javu jsou standardně využívat výhod tohoto nového modelu. Použít nový model, takže některé funkce nejsou podporovány v 2.x nebyly aktualizovány Azure funkce 1.x experimentální jazyky. Následující tabulka uvádí podporované programovací jazyky v každé verzi modulu runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Další informace najdete v tématu [podporované jazyky](supported-languages.md).

## <a name="bindings"></a>Vazby 

Modul runtime 2.x používá nový [model rozšiřitelnosti vazby](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) , nabízí tyto výhody:

* Podpora pro rozšíření třetích stran vazby.
* Oddělení runtime a vazeb. To umožňuje rozšíření vazby verzí a vydaná nezávisle. Můžete například rozhodnete upgradovat na verzi rozšíření, které závisí na novější verzi základní sady SDK.
* Světlejší prostředí pro spouštění, kde jsou pouze vazby používá známé a načten modulem runtime.

Všechny předdefinované vazeb Azure Functions přijaly tento model a jsou již zahrnuty ve výchozím nastavení, s výjimkou aktivační událost časovače a triggeru protokolu HTTP. Tato rozšíření jsou automaticky nainstalovány při vytváření funkce prostřednictvím nástrojů, například Visual Studio nebo prostřednictvím portálu.

Následující tabulka uvádí, které vazby jsou podporované v každé verzi modulu runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>Známé problémy v 2.x

Další informace o podpoře vazby a dalších funkční mezery v 2.x najdete v tématu [Runtime 2.0 známé problémy](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Cílový modul runtime 2.0 ve vašem místním vývojovém prostředí](functions-run-local.md)

> [!div class="nextstepaction"]
> [Najdete v části poznámky k verzi pro verze modulu runtime](https://github.com/Azure/azure-webjobs-sdk-script/releases)