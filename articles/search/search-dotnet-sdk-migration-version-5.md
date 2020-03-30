---
title: Upgrade na Azure Search .NET SDK verze 5
titleSuffix: Azure Cognitive Search
description: Migrace kódu do Azure Search .NET SDK verze 5 ze starších verzí. Zjistěte, co je nového a jaké změny kódu jsou požadovány.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bb0cd191ba7e5939c55d11b484ed7a2c422f8c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793022"
---
# <a name="upgrade-to-azure-search-net-sdk-version-5"></a>Upgrade na Azure Search .NET SDK verze 5

Pokud používáte verzi 4.0-preview nebo starší azure [search .NET SDK](https://aka.ms/search-sdk), tento článek vám pomůže upgradovat aplikaci na verzi 5.

Obecnější návod sady SDK včetně příkladů naleznete v tématu [Jak používat Azure Search z aplikace .NET](search-howto-dotnet-sdk.md).

Verze 5 sady Azure Search .NET SDK obsahuje některé změny z předchozích verzí. Jedná se většinou o menší, takže změna kódu by měla vyžadovat pouze minimální úsilí. Pokyny, jak změnit kód tak, aby používal novou verzi sady SDK, najdete v tématu [Kroky pro upgrade.](#UpgradeSteps)

> [!NOTE]
> Pokud používáte verzi 2.0-preview nebo starší, měli byste nejprve upgradovat na verzi 3 a potom upgradovat na verzi 5. Pokyny najdete [v tématu Upgrade na Azure Search .NET SDK verze 3.](search-dotnet-sdk-migration.md)
>
> Vaše instance služby Azure Search podporuje několik verzí rozhraní REST API, včetně nejnovější. Verzi můžete nadále používat, pokud již není nejnovější, ale doporučujeme migrovat kód a používat nejnovější verzi. Při použití rozhraní REST API, musíte zadat verzi rozhraní API v každém požadavku prostřednictvím parametru api-version. Při použití sady .NET SDK určuje verze sady SDK, kterou používáte, odpovídající verzi rozhraní REST API. Pokud používáte starší sadu SDK, můžete pokračovat v spouštění tohoto kódu bez evidenčních změn, i když je služba upgradována na podporu novější verze rozhraní API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Co je nového ve verzi 5
Verze 5 sady Azure Search .NET SDK se zaměřuje na nejnovější obecně dostupnou verzi rozhraní AZURE Search REST API, konkrétně 2017-11-11. To umožňuje používat nové funkce Azure Search z aplikace .NET, včetně následujících:

* [Synonyma](search-synonyms.md).
* Nyní můžete programově přistupovat k upozorněním v `Warning` historii `IndexerExecutionResult` provádění indexeru (další podrobnosti naleznete ve vlastnosti v [odkazu .NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)
* Podpora pro .NET Core 2.
* Nová struktura balíčku podporuje použití pouze části sady SDK, které potřebujete (podrobnosti naleznete v tématu [Rozdělení změn ve verzi 5).](#ListOfChanges)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Postup upgradu
Nejprve aktualizujte odkaz `Microsoft.Azure.Search` NuGet pro použití konzoly NuGet Package Manager nebo kliknutím pravým tlačítkem myši na odkazy na projekt a výběrem možnosti "Spravovat balíčky NuGet..." v sadě Visual Studio.

Jakmile NuGet stáhla nové balíčky a jejich závislosti, znovu sestavit projekt. V závislosti na tom, jak je váš kód strukturován, může být úspěšně znovu sestavit. Pokud ano, jste připraveni jít!

Pokud se sestavení nezdaří, měla by se zobrazit chyba sestavení, jako je následující:

    The name 'SuggesterSearchMode' does not exist in the current context

Dalším krokem je oprava této chyby sestavení. Podrobnosti o tom, co chybu způsobuje a jak ji opravit, najdete v tématu [Nejnovější změny ve verzi 5.](#ListOfChanges)

Vezměte prosím na vědomí, že kvůli změnám v balení azure search .NET SDK, je nutné znovu sestavit aplikaci, aby bylo možné použít verzi 5. Tyto změny jsou podrobně [popsány](#ListOfChanges)v nejnovější změny ve verzi 5 .

Může se zobrazit další sestavení upozornění týkající se zastaralé metody nebo vlastnosti. Upozornění budou obsahovat pokyny k použití namísto zastaralé funkce. Například pokud vaše aplikace `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` používá metodu, měli byste získat upozornění, že "Toto chování je nyní povolena ve výchozím nastavení, takže volání této metody již není nutné."

Jakmile opravíte všechny chyby sestavení nebo upozornění, můžete v aplikaci provést změny a využít tak nové funkce, pokud chcete. Nové funkce sady SDK jsou podrobně popsány v části [Co je nového ve verzi 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Nejnovější změny ve verzi 5

### <a name="new-package-structure"></a>Nová struktura balíčků

Nejpodstatnější narušující změnou ve `Microsoft.Azure.Search` verzi 5 je, že sestavení a jeho obsah byly rozděleny do čtyř samostatných sestavení, které jsou nyní distribuovány jako čtyři samostatné balíčky NuGet:

 - `Microsoft.Azure.Search`: Toto je meta balíček, který zahrnuje všechny ostatní balíčky Azure Search jako závislosti. Pokud upgradujete ze starší verze sady SDK, stačí upgradovat tento balíček a znovu vytvořit, abyste začali používat novou verzi.
 - `Microsoft.Azure.Search.Data`: Tento balíček použijte, pokud vyvíjíte aplikaci .NET pomocí Azure Search a stačí zadat dotaz nebo aktualizovat dokumenty v indexech. Pokud také potřebujete vytvořit nebo aktualizovat indexy, mapy synonym nebo jiné `Microsoft.Azure.Search` prostředky na úrovni služeb, použijte balíček.
 - `Microsoft.Azure.Search.Service`: Tento balíček použijte, pokud vyvíjíte automatizaci v rozhraní .NET ke správě indexů Azure Search, map synonym, indexerů, zdrojů dat nebo jiných prostředků na úrovni služeb. Pokud potřebujete pouze dotaz nebo aktualizaci dokumentů v `Microsoft.Azure.Search.Data` indexech, použijte balíček místo. Pokud potřebujete všechny funkce Azure Search, `Microsoft.Azure.Search` použijte balíček místo.
 - `Microsoft.Azure.Search.Common`: Běžné typy, které potřebují knihovny Azure Search .NET. Tento balíček byste neměli používat přímo ve vaší aplikaci. Je určen pouze pro použití jako závislost.
 
Tato změna je technicky porušení, protože mnoho typů byly přesunuty mezi sestaveními. To je důvod, proč opětovné sestavení aplikace je nezbytné pro upgrade na verzi 5 sady SDK.

Existuje malý počet dalších narušující změny ve verzi 5, které mohou vyžadovat změny kódu kromě opětovnésestavení aplikace.

### <a name="change-to-suggesters"></a>Změnit na návrhy 

Konstruktor `Suggester` již nemá `enum` parametr `SuggesterSearchMode`pro . Tento výčet měl pouze jednu hodnotu, a proto byl redundantní. Pokud se v důsledku toho zobrazí chyby sestavení, `SuggesterSearchMode` jednoduše odeberte odkazy na parametr.

### <a name="removed-obsolete-members"></a>Odebrané zastaralé členy

Mohou se zobrazit chyby sestavení související s metodami nebo vlastnostmi, které byly v dřívějších verzích označeny jako zastaralé a následně odebrány ve verzi 5. Pokud narazíte na takové chyby, zde je návod, jak je vyřešit:

- Pokud jste metodu `IndexingParametersExtensions.IndexStorageMetadataOnly` používali, použijte `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` místo ní.
- Pokud jste metodu `IndexingParametersExtensions.SkipContent` používali, použijte `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` místo ní.

### <a name="removed-preview-features"></a>Odebrané funkce náhledu

Pokud upgradujete z verze 4.0-preview na verzi 5, uvědomte si, že podpora analyzování json a csv analýzy pro indexery objektů Blob byla odebrána, protože tyto funkce jsou stále ve verzi preview. Konkrétně byly odebrány `IndexingParametersExtensions` následující metody třídy:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Pokud vaše aplikace má pevné závislosti na těchto funkcích, nebude možné upgradovat na verzi 5 Azure Search .NET SDK. Můžete pokračovat v používání verze 4.0-preview. Mějte však na paměti, že **nedoporučujeme používat sady Preview SDK v produkčních aplikacích**. Funkce náhledu jsou pouze pro vyhodnocení a mohou se změnit.

## <a name="conclusion"></a>Závěr
Pokud potřebujete další podrobnosti o používání sady Azure Search .NET SDK, podívejte [se na .NET Návod](search-howto-dotnet-sdk.md).

Uvítáme vaši zpětnou vazbu k sdk. Pokud narazíte na problémy, neváhejte a požádejte nás o pomoc na [Stack Přetečení](https://stackoverflow.com/questions/tagged/azure-search). Pokud najdete chybu, můžete podat problém v [úložišti GitHub Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Nezapomeňte předponu název problému s "[Azure Search]".

Děkujeme vám, že používáte Azure Search!
