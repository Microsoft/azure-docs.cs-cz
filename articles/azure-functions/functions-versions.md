---
title: Přehled verze modulu runtime Azure Functions
description: Služba Azure Functions podporuje více verzí modulu runtime. Poznejte rozdíly mezi nimi a jak zvolit ten, který je pro vás nejvhodnější.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/29/2018
ms.author: glenga
ms.openlocfilehash: 716f2b537a47c6e721c7393cba340a583c7ed064
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092134"
---
# <a name="azure-functions-runtime-versions-overview"></a>Přehled verze modulu runtime Azure Functions

 Existují dvě hlavní verze modulu runtime Azure Functions: 1.x a 2.x. Pouze 1.x je schválená pro použití v produkčním prostředí. Tento článek vysvětluje, co je nového v 2.x, která je ve verzi preview.

| Modul runtime | Status |
|---------|---------|
|1.x|Obecně dostupná (GA)|
|2.x|Ve verzi Preview<sup>*</sup>|

<sup>*</sup>Důležité aktualizace na verzi 2.x, včetně narušující změně oznámení, podívejte [oznámení služby Azure App Service](https://github.com/Azure/app-service-announcements/issues) úložiště.

> [!NOTE] 
> Tento článek se týká ke cloudové službě Azure Functions. Informace o produktu, která umožňuje spouštění Azure Functions na místním prostředí, najdete v článku [přehled modulu Runtime Azure Functions](functions-runtime-overview.md).

## <a name="cross-platform-development"></a>Vývoj pro různé platformy

Modul runtime 1.x podporuje vývoj a hostování pouze na portálu nebo na Windows. Modul runtime 2.x běží v .NET Core, což znamená, že můžete spustit na všech platformách podporovaných aplikací .NET Core, včetně systému macOS a Linux. To umožňuje vývoj pro různé platformy a scénáře hostingu, které nejsou možné s 1.x.

## <a name="languages"></a>Jazyky

Modul runtime 2.x používá nový model rozšiřitelnosti jazyka. Na začátku jazyka JavaScript a Java jsou výhod tohoto nového modelu. Azure Functions 1.x experimentálních jazyků neaktualizovaly použít nový model, takže nejsou podporovány v 2.x. Následující tabulka uvádí podporované programovací jazyky v každé verzi modulu runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Další informace najdete v tématu [podporované jazyky](supported-languages.md).

## <a name="bindings"></a>Vazby 

Modul runtime používá nový 2.x [model rozšiřitelnosti vazby](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) , který nabízí tyto výhody:

* Podpora pro rozšíření vazby třetích stran.
* Oddělení modulu runtime a vazby. To umožňuje rozšíření vazby se systémovou správou verzí a vydávají se nezávisle na sobě. Můžete například pokud se rozhodnete k upgradu na verzi rozšíření, které spoléhá na novější verzi základní sadu SDK.
* Světlejší spuštění prostředí, kde jsou pouze vazby používá známé a načten modulem runtime.

Všechny integrované vazeb Azure Functions, si osvojila tento model a jsou již zahrnuté ve výchozím nastavení, s výjimkou Časovacího triggeru a triggeru HTTP. Tahle rozšíření jsou nainstalovány automaticky při vytváření funkce prostřednictvím nástrojů, jako je Visual Studio nebo na portálu.

Následující tabulka uvádí, které vazeb jsou podporovány v každé verzi modulu runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>Známé problémy v 2.x

Další informace o podporu vazeb ve službě a dalších funkčních zpoždění při 2.x, naleznete v tématu [Runtime 2.0 – známé problémy](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících materiálech:

* [Místní psaní kódu a testování funkcí Azure Functions](functions-run-local.md)
* [Jak se zaměřit na verze modulu runtime Azure Functions](set-runtime-version.md)
* [Poznámky k verzi](https://github.com/Azure/azure-functions-host/releases)
