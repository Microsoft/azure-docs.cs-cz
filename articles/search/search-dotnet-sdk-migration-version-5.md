---
title: Upgrade na Azure Search .NET SDK verze 5
titleSuffix: Azure Cognitive Search
description: Migruje kód do sady Azure Search .NET SDK verze 5 ze starších verzí. Zjistěte, co je nového a kdy se vyžadují změny kódu.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bb0cd191ba7e5939c55d11b484ed7a2c422f8c6d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793022"
---
# <a name="upgrade-to-azure-search-net-sdk-version-5"></a>Upgrade na Azure Search .NET SDK verze 5

Pokud používáte verzi 4,0-Preview nebo starší [sadu Azure Search .NET SDK](https://aka.ms/search-sdk), Tento článek vám pomůže při upgradu aplikace na použití verze 5.

Obecnější návod k sadě SDK, včetně příkladů, najdete v tématu [použití Azure Search z aplikace .NET](search-howto-dotnet-sdk.md).

Verze 5 sady Azure Search .NET SDK obsahuje některé změny z dřívějších verzí. Jsou to většinou méně významné, takže změna kódu by měla vyžadovat jenom minimální úsilí. Pokyny ke změně kódu pro použití nové verze sady SDK najdete v tématu [Postup upgradu](#UpgradeSteps) .

> [!NOTE]
> Pokud používáte verzi 2,0-Preview nebo starší, měli byste nejdřív upgradovat na verzi 3 a pak upgradovat na verzi 5. Pokyny najdete v tématu [upgrade na sadu Azure Search .NET SDK verze 3](search-dotnet-sdk-migration.md) .
>
> Vaše instance služby Azure Search podporuje několik verzí REST API, včetně nejnovějšího. Můžete i nadále používat verzi, pokud již není nejnovější, ale doporučujeme, abyste si kód migrovali na používání nejnovější verze. Při použití REST API musíte v každé žádosti zadat verzi rozhraní API přes parametr verze API. Pokud používáte sadu .NET SDK, verze sady SDK, kterou používáte, určí odpovídající verzi REST API. Pokud používáte starší sadu SDK, můžete pokračovat ve spuštění tohoto kódu bez změny, i když je služba upgradována na podporu novější verze rozhraní API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Co je nového ve verzi 5
Verze 5 rozhraní Azure Search .NET SDK cílí na nejnovější všeobecně dostupnou verzi Azure Search REST API, konkrétně 2017-11-11. Díky tomu je možné využít nové funkce Azure Search z aplikace .NET, včetně následujících:

* [Synonyma](search-synonyms.md).
* Teď můžete programově přistupovat k upozorněním v historii spouštění indexeru (další podrobnosti najdete v tématu `Warning` vlastnosti `IndexerExecutionResult` v [Referenční příručce .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) ).
* Podpora pro .NET Core 2
* Nová struktura balíčku podporuje jenom ty součásti sady SDK, které potřebujete (podrobnosti najdete v tématu zásadní [změny ve verzi 5](#ListOfChanges) ).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Postup upgradu
Nejdřív aktualizujte svůj odkaz na NuGet pro `Microsoft.Azure.Search` pomocí konzoly Správce balíčků NuGet nebo kliknutím pravým tlačítkem na odkazy na projekt a výběrem možnosti spravovat balíčky NuGet... v aplikaci Visual Studio.

Jakmile NuGet stáhne nové balíčky a jejich závislosti, sestavte projekt znovu. V závislosti na tom, jak je kód strukturovaný, se může úspěšně znovu sestavit. Pokud ano, jste připraveni!

Pokud se sestavení nepovede, měla by se zobrazit chyba sestavení podobný následujícímu:

    The name 'SuggesterSearchMode' does not exist in the current context

Dalším krokem je opravit tuto chybu sestavení. Podrobné informace o tom, co způsobuje chybu a jak ji opravit, najdete v tématu zásadní [změny ve verzi 5](#ListOfChanges) .

Všimněte si, že kvůli změnám v balení sady Azure Search .NET SDK je nutné aplikaci znovu sestavit, aby bylo možné použít verzi 5. Tyto změny jsou podrobně popsány v podrobnějších [změnách verze 5](#ListOfChanges).

Může se zobrazit další upozornění sestavení související s zastaralými metodami nebo vlastnostmi. Upozornění budou obsahovat pokyny k používání funkce místo zastaralé funkce. Například pokud vaše aplikace používá metodu `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType`, měla by se zobrazit upozornění, že toto chování je nyní ve výchozím nastavení povoleno, takže volání této metody již není nutné. "

Jakmile budete chtít opravit chyby nebo upozornění sestavení, můžete v aplikaci provádět změny, abyste mohli využít nové funkce, pokud chcete. Nové funkce v sadě SDK jsou podrobně popsané v části [novinky verze 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Přerušující změny ve verzi 5

### <a name="new-package-structure"></a>Nová struktura balíčku

Závažná zásadní změna verze 5 je, že `Microsoft.Azure.Search` sestavení a jeho obsah byly rozděleny do čtyř samostatných sestavení, která jsou nyní distribuována jako čtyři samostatné balíčky NuGet:

 - `Microsoft.Azure.Search`: Jedná se o meta balíček, který obsahuje všechny ostatní balíčky Azure Search jako závislosti. Pokud upgradujete ze starší verze sady SDK, stačí upgradovat tento balíček a opětovné sestavení by mělo být dostatečné, aby bylo možné začít používat novou verzi.
 - `Microsoft.Azure.Search.Data`: Tento balíček použijte v případě, že vyvíjíte aplikaci .NET pomocí Azure Search a potřebujete dotazovat nebo aktualizovat dokumenty v indexech. Pokud potřebujete také vytvořit nebo aktualizovat indexy, mapy synonym nebo jiné prostředky na úrovni služby, použijte místo toho balíček `Microsoft.Azure.Search`.
 - `Microsoft.Azure.Search.Service`: Tento balíček použijte v případě, že vyvíjíte automatizaci v rozhraní .NET, abyste mohli spravovat Azure Search indexy, mapy synonym, indexery, zdroje dat nebo jiné prostředky na úrovni služby. Pokud potřebujete dotazovat nebo aktualizovat dokumenty ve svých indexech, použijte místo toho balíček `Microsoft.Azure.Search.Data`. Pokud potřebujete všechny funkce Azure Search, použijte místo toho balíček `Microsoft.Azure.Search`.
 - `Microsoft.Azure.Search.Common`: společné typy vyžadované knihovnami Azure Search .NET. Tento balíček byste neměli muset používat přímo v aplikaci. Má sloužit pouze jako závislost.
 
Tato změna je technicky rozbitá, protože mezi sestaveními se přesunulo mnoho typů. K tomu je potřeba znovu sestavit aplikaci, aby bylo možné upgradovat na verzi 5 sady SDK.

Existuje malý počet dalších přerušujících změn ve verzi 5, které mohou vyžadovat změny kódu kromě opakovaného sestavení aplikace.

### <a name="change-to-suggesters"></a>Změnit na moduly pro návrhy 

Konstruktor `Suggester` už neobsahuje parametr `enum` pro `SuggesterSearchMode`. Tento výčet má pouze jednu hodnotu, a proto byl redundantní. Pokud se v důsledku toho zobrazí chyby sestavení, jednoduše odeberte odkazy na parametr `SuggesterSearchMode`.

### <a name="removed-obsolete-members"></a>Odebrané zastaralé členy

Můžou se zobrazit chyby sestavení související s metodami nebo vlastnostmi, které byly označeny jako zastaralé v dřívějších verzích a následně odebrány ve verzi 5. Pokud dojde k takovým chybám, můžete je vyřešit:

- Pokud jste používali metodu `IndexingParametersExtensions.IndexStorageMetadataOnly`, použijte místo toho `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)`.
- Pokud jste používali metodu `IndexingParametersExtensions.SkipContent`, použijte místo toho `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)`.

### <a name="removed-preview-features"></a>Odebrané funkce verze Preview

Pokud upgradujete z verze 4,0-Preview na verzi 5, počítejte s tím, že podpora analýzy pole JSON a analýzy sdíleného svazku (CSV) pro indexery objektů BLOB byla odebrána, protože tyto funkce jsou stále ve verzi Preview. Byly odebrány konkrétně následující metody třídy `IndexingParametersExtensions`:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Pokud je vaše aplikace pevně závislá na těchto funkcích, nebudete moct upgradovat na verzi 5 sady Azure Search .NET SDK. Můžete dál používat verzi 4,0-Preview. Mějte ale na paměti, že **v produkčních aplikacích nedoporučujeme používat sady SDK pro verze Preview**. Funkce ve verzi Preview jsou jenom pro vyhodnocení a můžou se změnit.

## <a name="conclusion"></a>Závěr
Pokud potřebujete další podrobnosti o používání sady Azure Search .NET SDK, přečtěte si [článek postupy pro .NET](search-howto-dotnet-sdk.md).

Vaše názory na sadu SDK jsme uvítá. Pokud narazíte na problémy, řekněte nám, abychom vám pomohli [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Pokud narazíte na chybu, můžete založit problém v [úložišti GitHub Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Ujistěte se, že název problému bude předponou "[Azure Search]".

Děkujeme, že používáte Azure Search!
