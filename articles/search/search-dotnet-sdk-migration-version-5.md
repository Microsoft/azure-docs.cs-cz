---
title: Upgrade na Azure Search .NET SDK verze 5 – Azure Search
description: Migrace ze starší verze kódu do Azure Search .NET SDK verze 5. Zjistěte, co je nového a jaké změny kódu jsou povinné.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 743ac433418386281acc58ad1deef06ee75e38d9
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316862"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-5"></a>Upgrade na Azure Search .NET SDK verze 5
Pokud používáte verzi 4.0 ve verzi preview nebo starší aplikace [Azure Search .NET SDK](https://aka.ms/search-sdk), tento článek vám pomůže při upgradu aplikace pro používání verze 5.

Další obecné návod sady SDK, včetně příkladů najdete v tématu [použití Azure Search z aplikace .NET](search-howto-dotnet-sdk.md).

Azure Search .NET SDK verze 5 obsahuje některé změny z předchozích verzí. Toto jsou většinou dílčí, takže změna kódu by měla trvat pouze minimálním úsilím. Zobrazit [kroky pro upgrade](#UpgradeSteps) pokyny o tom, jak změnit váš kód k použití nové verze sady SDK.

> [!NOTE]
> Pokud používáte verzi 2.0 preview nebo starší, byste nejprve upgradovat na verzi 3 a pak provedete upgrade na verzi 5. Zobrazit [upgrade na Azure Search .NET SDK verze 3](search-dotnet-sdk-migration.md) pokyny.
>
> Instance služby Azure Search podporuje několik verzí rozhraní REST API, včetně nejnovější. Můžete nadále používat verzi, pokud již není nejnovější, ale doporučujeme migrovat kód Refaktorovat pro použití na nejnovější verzi. Při použití rozhraní REST API, je nutné zadat verzi rozhraní API v každé žádosti prostřednictvím parametru verze api-version. Při použití sady .NET SDK, určuje verzi sady SDK, které používáte odpovídající verzi rozhraní REST API. Pokud používáte starší sada SDK, můžete nadále spouštět tento kód beze změny i v případě, že služba se upgraduje na novější verzi rozhraní API podporují.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Co je nového ve verzi 5
Azure Search .NET SDK verze 5, zaměřuje na nejnovější verzi obecně dostupnou verzi REST API služby Azure Search, konkrétně 2017-11-11. To umožňuje používat nové funkce služby Azure Search z aplikace .NET, včetně následujících:

* [Synonyma](search-synonyms.md).
* Nyní můžete prostřednictvím kódu programu k upozornění v historii spuštění indexeru (najdete v článku `Warning` vlastnost `IndexerExecutionResult` v [.NET – referenční informace](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) další podrobnosti).
* Podpora pro .NET Core 2.
* Nová struktura balíčku podporuje používání jen ty části sady SDK, které potřebujete (naleznete v tématu [nejnovější změny ve verzi 5](#ListOfChanges) podrobnosti).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroky pro upgrade
Nejprve aktualizujte referenci NuGet pro `Microsoft.Azure.Search` pomocí konzoly Správce balíčků NuGet nebo tím, že pravým tlačítkem myši na odkazy projektu a vyberte "Spravovat NuGet balíčky..." v sadě Visual Studio.

Po stažení nové balíčky a jejich závislostí NuGet znovu sestavte projekt. Pokud používáte funkci ve verzi preview, které nejsou v sadu SDK pro verzi GA, třeba znovu sestavit úspěšně (Pokud ne, dejte nám vědět o [Githubu](https://github.com/azure/azure-sdk-for-net/issues)). Pokud ano, jste připraveni začít!

Upozorňujeme, že z důvodu změn v balíčku Azure Search .NET SDK, je nutné znovu sestavit aplikaci, aby bylo možné používat verze 5. Tyto změny jsou podrobně popsány v [nejnovější změny ve verzi 5](#ListOfChanges).

Může se zobrazit další upozornění související s zastaralé metody nebo vlastnosti. Upozornění bude obsahovat pokyny, jak použít namísto zastaralé funkce. Například, pokud vaše aplikace používá `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` metoda, měli byste obdržet upozornění, že "Toto chování je teď ve výchozím nastavení povoleno, takže volání této metody již není nezbytné."

Po vyřešili jsme upozornění sestavení, můžete aplikaci výhod nových funkcí, pokud chcete měnit. Nové funkce v sadě Windows SDK jsou podrobně popsány v [co je nového ve verzi 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Rozbíjející změny v verze 5
Většina podstatné rozbíjející změnu do verze 5 je, že `Microsoft.Azure.Search` sestavení a jeho obsah rozdělena do čtyř samostatné sestavení, které se nyní distribuují jako čtyři samostatné balíčky NuGet:

 - `Microsoft.Azure.Search`: Jde o meta balíček, který obsahuje všechny ostatní balíčky Azure Search jako závislosti. Pokud upgradujete ze starší verze sady SDK, jednoduše upgradu tohoto balíčku a nové sestavení by vám měly dostatečně chcete začít používat novou verzi.
 - `Microsoft.Azure.Search.Data`: Tento balíček použijte, pokud vyvíjíte aplikace .NET pomocí Azure Search a potřebujete jenom dotazování a aktualizaci dokumentů v indexů. Pokud je také potřeba vytvořit nebo aktualizovat indexů, map synonym, nebo další prostředky na úrovni služby, použijte `Microsoft.Azure.Search` balíček místo.
 - `Microsoft.Azure.Search.Service`: Tento balíček použijte, pokud vyvíjíte v rozhraní .NET pro správu indexů Azure Search, map synonym, indexery, zdroje dat nebo další prostředky na úrovni služby automation. Pokud potřebujete jenom k dokumentům dotazu nebo aktualizace v indexů, použijte `Microsoft.Azure.Search.Data` balíček místo. Pokud budete potřebovat všechny funkce služby Azure Search, použijte `Microsoft.Azure.Search` balíček místo.
 - `Microsoft.Azure.Search.Common`: Běžné typy vyžadované knihoven Azure Search pro .NET. By neměl muset použít tento balíček přímo v aplikaci; Je určená jenom pro použití jako závislost.
 
Tato změna je technicky zásadní, protože mnoho typů se přesunuly mezi sestaveními. To je důvod, proč znovu sestavit aplikaci je nutné před upgradem na verzi 5 sady SDK.

Existuje malý počet dalších rozbíjející změny v verze 5, které mohou vyžadovat kód změní kromě nové sestavení aplikace.

### <a name="removed-obsolete-members"></a>Odebrat zastaralé členy

Může se zobrazit chyby související s metodami nebo vlastnostmi, které byly označeny jako zastaralé ve starších verzích a následně odebrat ve verzi 5 sestavení. Pokud narazíte na takové chyby, tady je způsob jejich řešení:

- Pokud jste používali `IndexingParametersExtensions.IndexStorageMetadataOnly` metody, použijte `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` místo.
- Pokud jste používali `IndexingParametersExtensions.SkipContent` metody, použijte `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` místo.

### <a name="removed-preview-features"></a>Funkce odebrané ve verzi preview

Pokud provádíte upgrade z verze 4.0-preview do verze 5, mějte na paměti, že pole JSON a parsování podporu pro indexování objektů Blob CSV má se odebrat, protože tyto funkce jsou stále ve verzi preview. Konkrétně tyto metody `IndexingParametersExtensions` jsme odebrali třídu:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Pokud vaše aplikace má pevný závislost na těchto funkcích, nebude možné upgradovat na verzi 5 Azure Search .NET SDK. Můžete nadále používat verze 4.0-preview. Však prosím mějte na paměti, která **nedoporučujeme používání verze preview sady SDK v aplikacích v produkčním prostředí**. Funkce ve verzi Preview jsou určené pro zkušební pouze a může změnit.

## <a name="conclusion"></a>Závěr
Pokud potřebujete další podrobnosti o použití Azure Search .NET SDK, přečtěte si [.NET postupy](search-howto-dotnet-sdk.md).

Vítáme váš názor na sadě SDK. Pokud narazíte na problémy, neváhejte nás požádat o pomoc na [fórum Azure Search na webu MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Pokud zjistíte chybu, můžete založit problém v [úložiště Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Ujistěte se, že jako předpona váš problém název s "[Azure Search]".

Děkujeme vám za použití Azure Search.
