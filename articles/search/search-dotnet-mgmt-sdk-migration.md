---
title: Upgrade na sadu Azure Search .NET Management SDK verze 2 – Azure Search
description: Upgradujte na sadu Azure Search .NET Management SDK verze 2 z předchozích verzí. Podívejte se, co je nového a co je potřeba mít změny kódu.
author: brjohnstmsft
manager: nitinme
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 2a59cff7f5313b0ac5a060d698950a4c82160f67
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182248"
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version"></a>Upgrade na Azure Search .NET Management SDK verze 

> [!Important]
> Tento obsah je stále v konstrukci. V NuGet je dostupná verze 3,0 sady Azure Search Management .NET SDK. Pracujeme na aktualizaci tohoto průvodce migrací, abychom vysvětlují, jak upgradovat na novou verzi. 
>

Pokud používáte verzi 1.0.2 [sady .NET Management SDK nebo Azure Search](https://aka.ms/search-mgmt-sdk)starší, Tento článek vám pomůže při upgradu aplikace na použití verze 2.

Verze 2 sady Azure Search .NET Management SDK obsahuje některé změny z dřívějších verzí. Jsou to většinou méně významné, takže změna kódu by měla vyžadovat jenom minimální úsilí. Pokyny ke změně kódu pro použití nové verze sady SDK najdete v tématu [Postup upgradu](#UpgradeSteps) .

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Co je nového ve verzi 2
Verze 2 sady Azure Search .NET Management SDK cílí na stejnou obecně dostupnou verzi REST API správy Azure Search jako předchozí verze sady SDK, konkrétně 2015-08-19. Změny v sadě SDK jsou čistě změny na straně klienta, aby se zlepšila použitelnost samotné sady SDK. Mezi tyto změny patří následující:

* `Services.CreateOrUpdate`a jeho asynchronní verze nyní automaticky dotazují zřizování `SearchService` a nevrátí se do dokončení zřizování služby. Tím ušetříte, abyste si tento kód cyklického dotazování sami napsali sami.
* Pokud přesto chcete, aby se zřizování služby dotazoval ručně, můžete použít novou `Services.BeginCreateOrUpdate` metodu nebo jednu z jejích asynchronních verzí.
* Nové metody `Services.Update` a její asynchronní verze byly přidány do sady SDK. Tyto metody používají opravu HTTP k podpoře přírůstkové aktualizace služby. Můžete například nyní škálovat službu předáním `SearchService` instance těmto metodám, které obsahují pouze požadované `partitionCount` vlastnosti a `replicaCount` . Starý způsob volání `Services.Get`, úpravy vráceného `SearchService`a jeho předání na `Services.CreateOrUpdate` je stále podporován, ale již není nutný. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Postup upgradu
Nejdřív aktualizujte svůj odkaz na NuGet `Microsoft.Azure.Management.Search` pro použití buď konzoly Správce balíčků NuGet, nebo kliknutím pravým tlačítkem na odkazy na projekt a výběrem možnosti spravovat balíčky NuGet... v aplikaci Visual Studio.

Jakmile NuGet stáhne nové balíčky a jejich závislosti, sestavte projekt znovu. V závislosti na tom, jak je kód strukturovaný, se může úspěšně znovu sestavit. Pokud ano, jste připraveni!

Pokud sestavení selhalo, může to být způsobeno tím, že jste implementovali některá rozhraní sady SDK (například pro účely testování částí), které se změnily. Chcete-li tento problém vyřešit, budete muset implementovat nové metody, jako `BeginCreateOrUpdateWithHttpMessagesAsync`je například.

Jakmile opravíte jakékoli chyby sestavení, můžete v aplikaci provádět změny, abyste mohli využít nové funkce, pokud chcete. Nové funkce v sadě SDK jsou podrobně popsané v části [co je nového ve verzi 2](#WhatsNew).

## <a name="conclusion"></a>Závěr
Vaše názory na sadu SDK jsme uvítá. Pokud narazíte na problémy, požádejte nás o pomoc na [Azure Search Fórum MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Pokud narazíte na chybu, můžete založit problém v [úložišti GitHub Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Ujistěte se, že název problému bude předponou "[Azure Search]".

Děkujeme, že používáte Azure Search!
