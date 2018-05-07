---
title: Upgrade na Azure Search .NET SDK verze 5 | Microsoft Docs
description: Upgrade na Azure Search .NET SDK verze 5
author: brjohnstmsft
manager: jlembicz
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: brjohnst
ms.openlocfilehash: 88949f4cf0e4408f5d1e4d9c9a5833b041b5a5ab
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-5"></a>Upgrade na Azure Search .NET SDK verze 5
Pokud používáte verzi 4.0 preview nebo starší z [Azure Search .NET SDK](https://aka.ms/search-sdk), tento článek vám pomůže při upgradu aplikace k používání verze 5.

Další obecné návod sady SDK, včetně příkladů, najdete v tématu [jak používat Azure Search z aplikace .NET](search-howto-dotnet-sdk.md).

Verze 5 .NET SDK služby Azure Search obsahuje některé změny z předchozích verzí. Tyto jsou většinou dílčí, takže měnili kód měli vyžadovat jen minimální úsilí. V tématu [kroky pro upgrade](#UpgradeSteps) pokyny o tom, jak upravit svůj kód k použití nové verze sady SDK.

> [!NOTE]
> Pokud používáte verzi 2.0 preview nebo starší, doporučujeme nejprve upgradovat na verzi 3 a pak upgradovat na verze 5. V tématu [upgrade .NET SDK služby Azure Search verze 3](search-dotnet-sdk-migration.md) pokyny.
>
> Instanci služby Azure Search podporuje několik verzí rozhraní REST API, včetně nejnovější. Můžete použít verzi, když je už nejnovějšího, ale doporučujeme migraci kód a použít nejnovější verzi. Při použití rozhraní REST API, musíte zadat verze rozhraní API v každé žádosti o prostřednictvím parametr api-version. Když pomocí sady .NET SDK, určuje verzi sady SDK používáte odpovídající verzi rozhraní REST API. Pokud používáte starší SDK, můžete spustit tento kód se žádné změny, i v případě, že je služba upgradovat pro podporu na novější verzi rozhraní API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Co je nového ve verzi 5
Verze 5 .NET SDK služby Azure Search cílí na nejnovější verzi obecně k dispozici REST API Azure Search, konkrétně 2017-11-11. Díky tomu je možné použít nové funkce služby Azure Search z aplikace .NET, včetně následujících:

* [Synonyma](search-synonyms.md).
* Nyní můžete prostřednictvím kódu programu k upozornění v historii spouštění indexer (najdete v článku `Warning` vlastnost `IndexerExecutionResult` v [.NET – referenční informace](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) podrobnosti).
* Podpora pro .NET Core 2.
* Nový balíček struktura podporuje používání pouze ty části sady SDK, která je nutné (viz [nejnovější změny ve verzi 5](#ListOfChanges) podrobnosti).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroky pro upgrade
Nejdřív aktualizovat vaše NuGet odkaz pro `Microsoft.Azure.Search` pomocí konzoly Správce balíčků NuGet nebo nástrojem pravým tlačítkem myši na vaše odkazy na projekt a výběrem "Správa NuGet balíčky..." v sadě Visual Studio.

Jakmile NuGet stáhl nové balíčky a jejich závislosti, znovu sestavte projekt. Pokud používáte funkce preview, která není v novou sadu SDK GA, třeba znovu sestavit úspěšně (Pokud ne, dejte nám vědět, na [Githubu](https://github.com/azure/azure-sdk-for-net/issues)). Pokud ano, jste připravení přejít!

Pamatujte, že z důvodu změn v balení .NET SDK služby Azure Search, je nutné znovu sestavit aplikaci, aby bylo možné používat verze 5. Tyto změny jsou podrobně popsané na [nejnovější změny ve verzi 5](#ListOfChanges).

Může se zobrazit další sestavení upozornění související s zastaralé metody nebo vlastnosti. Upozornění bude obsahovat pokyny o tom, jak používat místo zastaralé funkce. Například, pokud vaše aplikace používá `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` metoda, měli byste obdržet upozornění, že "Toto chování je teď povolené ve výchozím nastavení, takže voláním této metody není nutné."

Jakmile jste odstraněna všechna upozornění, sestavení, vám do vaší aplikace využívat výhod nových funkcí, nechcete-li provádět změny. Nové funkce v sadě SDK jsou podrobně popsané na [co je nového ve verzi 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Nejnovější změny ve verzi 5
Většina významné narušující změně ve verze 5 je, že `Microsoft.Azure.Search` sestavení a její obsah, rozdělena do čtyř samostatné sestavení, které jsou nyní distribuovaných jako čtyři samostatné balíčky NuGet:

 - `Microsoft.Azure.Search`: Toto je meta balíček, který obsahuje všechny ostatní balíčky Azure Search jako závislosti. Pokud provádíte upgrade ze starší verze sady SDK, jednoduše upgrade tohoto balíčku a nové sestavení by vám měly dostatečně k použití na novou verzi.
 - `Microsoft.Azure.Search.Data`: Tento balíček použijte, pokud vyvíjíte aplikaci .NET používání služby Azure Search a potřebujete jenom dotaz nebo aktualizovat dokumenty ve vašem indexy. Pokud také potřebujete vytvořit nebo aktualizovat indexy, synonymum mapy nebo jiným prostředkům úrovni služby, použijte `Microsoft.Azure.Search` balíček místo.
 - `Microsoft.Azure.Search.Service`: Tento balíček používejte, pokud vyvíjíte automatizace v rozhraní .NET pro správu indexů Azure Search, synonymum mapy, indexery, zdroje dat nebo jiným prostředkům na úrovni služby. Pokud potřebujete jenom dotazu nebo aktualizace dokumentů ve vašem indexy, použijte `Microsoft.Azure.Search.Data` balíček místo. Pokud budete potřebovat všechny funkce Azure Search, použijte `Microsoft.Azure.Search` balíček místo.
 - `Microsoft.Azure.Search.Common`: Vyžaduje na knihovny .NET vyhledávání Azure běžné typy. Neměli byste potřebovat pro tento balíček použít přímo v aplikaci; Smyslem je pouze má být použit jako závislost.
 
Tuto změnu je technicky nejnovější vzhledem k tomu, že mnoho typů byly přesunuty mezi sestavení. Z tohoto důvodu znovu sestavit aplikaci je nutné před upgradem na verzi 5 sady SDK.

Existuje malý počet jiné narušující změny ve verze 5, které mohou vyžadovat kód změní kromě nové sestavení aplikace.

### <a name="removed-obsolete-members"></a>Odebrat zastaralé členy

Může se zobrazit chyby související s metody nebo vlastnosti, které byly označeny jako zastaralé v dřívějších verzích a následně odstraněny v verze 5 sestavení. Pokud narazíte na takové chyby, zde je způsob jejich řešení:

- Pokud jste používali `IndexingParametersExtensions.IndexStorageMetadataOnly` metoda, použijte `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` místo.
- Pokud jste používali `IndexingParametersExtensions.SkipContent` metoda, použijte `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` místo.

### <a name="removed-preview-features"></a>Funkce odebrané preview

Pokud upgradujete z verze 4.0-preview verze 5, mějte na paměti, pole JSON a CSV analýza podporu pro objekt Blob indexery byl odebrán vzhledem k tomu, že tyto funkce jsou stále ve verzi preview. Konkrétně tyto metody `IndexingParametersExtensions` třídy byly odebrány:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Pokud vaše aplikace obsahuje pevné závislost na těchto funkcích, nebudete moci upgradovat na verzi 5 .NET SDK služby Azure Search. Můžete nadále používat verze 4.0-preview. Ale prosím mějte na paměti, **nedoporučujeme používání náhled sady SDK v produkční aplikace**. Funkce Preview jsou pouze zkušební verze a může změnit.

## <a name="conclusion"></a>Závěr
Pokud potřebujete další podrobnosti o použití .NET SDK služby Azure Search, přečtěte si [.NET postupy](search-howto-dotnet-sdk.md).

Uvítáme vaše názory týkající se sady SDK. Pokud narazíte na potíže, neváhejte nám požádat o pomoc na [fórum Azure Search MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Pokud narazíte na chyby, můžete soubor problém v [úložiště Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Ujistěte se, že předpony název vašeho problému s "[Azure Search]".

Děkujeme za používání služby Azure Search.
