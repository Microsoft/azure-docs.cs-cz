---
title: Upgrade na Azure .NET SDK služby Search správu verze 2 | Microsoft Docs
description: Upgrade na Azure .NET SDK služby Search správu verze 2
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.openlocfilehash: a6b6c01f0cc811abca90139e4c26c27b7bd7119f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version-2"></a>Upgrade na Azure .NET SDK služby Search správu verze 2
Pokud používáte verzi 1.0.2 nebo starší z [SDK služby Azure Search .NET správu](https://aka.ms/search-mgmt-sdk), tento článek vám pomůže při upgradu aplikace k používání verze 2.

Verze 2 správu SDK služby Azure Search .NET obsahuje některé změny z předchozích verzí. Tyto jsou většinou dílčí, takže měnili kód měli vyžadovat jen minimální úsilí. V tématu [kroky pro upgrade](#UpgradeSteps) pokyny o tom, jak upravit svůj kód k použití nové verze sady SDK.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Co je nového v verze 2
Verze 2 správu SDK služby Azure Search .NET cílí stejné všeobecně dostupná verze REST API služby Azure Search správy jako předchozí verze sady SDK, konkrétně 2015-08-19. Změny do sady SDK jsou výhradně klienta změny zlepšují použitelnost sady SDK sám sebe. Tyto změny zahrnují následující:

* `Services.CreateOrUpdate` a její asynchronní verze nyní automaticky dotazování zajišťování `SearchService` a nevrátí až do dokončení zřizování služby. Uloží to jste museli psát sami takový kód cyklického dotazování.
* Pokud jste ještě stále chcete dotazovat služby zřizování ručně, můžete použít nové `Services.BeginCreateOrUpdate` metoda nebo jednoho z jeho asynchronní verze.
* Nové metody `Services.Update` a její asynchronní verze jsou přidané do sady SDK. K podpoře přírůstkové aktualizace služby použít tyto metody HTTP PATCH. Například můžete nyní škálování služby předáním `SearchService` instance pro tyto metody, která obsahuje jenom požadované `partitionCount` a `replicaCount` vlastnosti. Původní způsob volání `Services.Get`, úprava vrácený `SearchService`a předání do `Services.CreateOrUpdate` se pořád podporuje, ale není nutné. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroky pro upgrade
Nejdřív aktualizovat vaše NuGet odkaz pro `Microsoft.Azure.Management.Search` pomocí konzoly Správce balíčků NuGet nebo nástrojem pravým tlačítkem myši na vaše odkazy na projekt a výběrem "Správa NuGet balíčky..." v sadě Visual Studio.

Jakmile NuGet stáhl nové balíčky a jejich závislosti, znovu sestavte projekt. V závislosti na tom, jak je strukturovaná kódu se může znovu sestavit úspěšně. Pokud ano, jste připravení přejít!

Pokud vaše sestavení selže, je možné, protože jste implementovali některá rozhraní SDK (například pro účely testování jednotek), které se změnily. Chcete-li tento problém vyřešili, budete muset implementovat nové metody, jako `BeginCreateOrUpdateWithHttpMessagesAsync`.

Jakmile vyřešili všechny chyby sestavení, vám do vaší aplikace využívat výhod nových funkcí, nechcete-li provádět změny. Nové funkce v sadě SDK jsou podrobně popsané na [co je nového ve verzi 2](#WhatsNew).

## <a name="conclusion"></a>Závěr
Uvítáme vaše názory týkající se sady SDK. Pokud narazíte na potíže, neváhejte nám požádat o pomoc na [fórum Azure Search MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Pokud narazíte na chyby, můžete soubor problém v [úložiště Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Ujistěte se, že předpony název vašeho problému s "[Azure Search]".

Děkujeme za používání služby Azure Search.
