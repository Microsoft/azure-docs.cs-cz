---
title: Upgrade na azure search .NET Management SDK verze 2
titleSuffix: Azure Cognitive Search
description: Upgradujte na Azure Search .NET Management SDK verze 2 z předchozích verzí. Zjistěte, co je nového a jaké změny kódu jsou požadovány.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b18e9688141ee64eb7dfcb82ce58db198e324b5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847533"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Inovace verzí sady Azure Search .NET Management SDK

> [!Important]
> Tento obsah je stále ve výstavbě. Verze 3.0 sady Azure Search Management .NET SDK je k dispozici na NuGet. Pracujeme na aktualizaci tohoto průvodce migrací, abychom vysvětlili, jak upgradovat na novou verzi. 
>

Pokud používáte verzi 1.0.2 nebo starší sady [Azure Search .NET Management SDK](https://aka.ms/search-mgmt-sdk), tento článek vám pomůže upgradovat aplikaci na verzi 2.

Verze 2 sady Azure Search .NET Management SDK obsahuje některé změny z předchozích verzí. Jedná se většinou o menší, takže změna kódu by měla vyžadovat pouze minimální úsilí. Pokyny, jak změnit kód tak, aby používal novou verzi sady SDK, najdete v tématu [Kroky pro upgrade.](#UpgradeSteps)

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Co je nového ve verzi 2
Verze 2 sady Azure Search .NET Management SDK se zaměřuje na stejnou obecně dostupnou verzi rozhraní REST pro správu azure search api jako předchozí verze sady SDK, konkrétně 2015-08-19. Změny sady SDK jsou přísně změny na straně klienta ke zlepšení použitelnosti samotné sady SDK. Mezi tyto změny patří následující:

* `Services.CreateOrUpdate`a jeho asynchronní verze nyní automaticky `SearchService` dotazování zřizování a nevrátí, dokud zřizování služby je kompletní. To vám ušetří od nutnosti psát takový volební kód sami.
* Pokud stále chcete dotazování zřizování služby `Services.BeginCreateOrUpdate` ručně, můžete použít novou metodu nebo jednu z jeho asynchronní verze.
* Do `Services.Update` sady SDK byly přidány nové metody a její asynchronní verze. Tyto metody používají protokol HTTP PATCH k podpoře přírůstkové aktualizace služby. Například nyní můžete škálovat službu `SearchService` předáním instance tyto metody, které obsahuje pouze požadované `partitionCount` a `replicaCount` vlastnosti. Starý způsob volání `Services.Get`, úprava `SearchService`vrácené a `Services.CreateOrUpdate` předání je stále podporován, ale již není nutné. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Postup upgradu
Nejprve aktualizujte odkaz `Microsoft.Azure.Management.Search` NuGet pro použití konzoly NuGet Package Manager nebo kliknutím pravým tlačítkem myši na odkazy na projekt a výběrem možnosti "Spravovat balíčky NuGet..." v sadě Visual Studio.

Jakmile NuGet stáhla nové balíčky a jejich závislosti, znovu sestavit projekt. V závislosti na tom, jak je váš kód strukturován, může být úspěšně znovu sestavit. Pokud ano, jste připraveni jít!

Pokud se sestavení nezdaří, může to být proto, že jste implementovali některá rozhraní sady SDK (například pro účely testování částí), která se změnila. Chcete-li tento problém vyřešit, budete muset `BeginCreateOrUpdateWithHttpMessagesAsync`implementovat nové metody, jako je například .

Jakmile opravíte všechny chyby sestavení, můžete v aplikaci provést změny a využít tak nové funkce, pokud chcete. Nové funkce sady SDK jsou podrobně popsány v části [Co je nového ve verzi 2](#WhatsNew).

## <a name="next-steps"></a>Další kroky
Uvítáme vaši zpětnou vazbu k sdk. Pokud narazíte na problémy, prosím, pošlete své otázky [stack přetečení](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Pokud najdete chybu, můžete podat problém v [úložišti GitHub Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Název problému nezapomeňte označit "hledat]".
