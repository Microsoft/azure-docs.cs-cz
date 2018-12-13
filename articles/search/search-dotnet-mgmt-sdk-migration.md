---
title: Upgrade na Azure Search .NET Management SDK verze 2 – Azure Search
description: Upgradovat z předchozí verze Azure Search .NET Management SDK verze 2. Zjistěte, co je nového a jaké změny kódu jsou povinné.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.custom: seodec2018
ms.openlocfilehash: 369e5283d7350729e0d8cc44f94f1f2e71b133c3
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316861"
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version-2"></a>Upgrade na Azure Search .NET Management SDK verze 2
Pokud používáte verzi 1.0.2 nebo starší aplikace [Management SDK služby Azure Search .NET](https://aka.ms/search-mgmt-sdk), tento článek vám pomůže při upgradu aplikace použije verzi 2.

Verze 2 služby Azure Search .NET Management SDK obsahuje některé změny z předchozích verzí. Toto jsou většinou dílčí, takže změna kódu by měla trvat pouze minimálním úsilím. Zobrazit [kroky pro upgrade](#UpgradeSteps) pokyny o tom, jak změnit váš kód k použití nové verze sady SDK.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Co je nového ve verzi 2
Verze 2 služby Azure Search .NET Management SDK cílí na stejné obecně dostupnou verzi REST API pro správu Azure Search jako předchozí verze sady SDK, konkrétně 2015-08-19. Změny v sadě SDK jsou striktně klientské změny ke zlepšení použitelnosti sama sada SDK. Tyto změny patří:

* `Services.CreateOrUpdate` a jeho asynchronní verze teď automaticky vyžádají zřizování `SearchService` a nevrátí až do dokončení zřizování služby. To vám ušetří nemusíte psát sami takový kód cyklického dotazování.
* Pokud chcete dotazovat služby zřizování ručně, můžete použít novou `Services.BeginCreateOrUpdate` metody nebo jeden z jeho asynchronní verze.
* Nové metody `Services.Update` a jeho asynchronní verze byly přidány k sadě SDK. Tyto metody použít pro podporu přírůstkové aktualizace služby HTTP PATCH. Například teď můžete škálovat služby pomocí předání `SearchService` instance těchto metod, která obsahuje jenom požadované `partitionCount` a `replicaCount` vlastnosti. Starý způsob volání `Services.Get`, úprava vráceného `SearchService`a předá se `Services.CreateOrUpdate` stále podporovány, ale již není nezbytné. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroky pro upgrade
Nejprve aktualizujte referenci NuGet pro `Microsoft.Azure.Management.Search` pomocí konzoly Správce balíčků NuGet nebo tím, že pravým tlačítkem myši na odkazy projektu a vyberte "Spravovat NuGet balíčky..." v sadě Visual Studio.

Po stažení nové balíčky a jejich závislostí NuGet znovu sestavte projekt. V závislosti na tom, jak váš kód strukturovaná ji může znovu sestavit úspěšně. Pokud ano, jste připraveni začít!

Pokud se sestavení nezdaří, je možné, protože jsme implementovali některá rozhraní SDK (například pro účely testování jednotek), které byly změněny. Chcete-li tento problém vyřešit, budete muset implementovat nové metody jako `BeginCreateOrUpdateWithHttpMessagesAsync`.

Po jste opravě případných chyb sestavení, můžete aplikace výhod nových funkcí, pokud chcete změnit. Nové funkce v sadě Windows SDK jsou podrobně popsány v [co je nového ve verzi 2](#WhatsNew).

## <a name="conclusion"></a>Závěr
Vítáme váš názor na sadě SDK. Pokud narazíte na problémy, neváhejte nás požádat o pomoc na [fórum Azure Search na webu MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Pokud zjistíte chybu, můžete založit problém v [úložiště Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Ujistěte se, že jako předpona váš problém název s "[Azure Search]".

Děkujeme vám za použití Azure Search.
