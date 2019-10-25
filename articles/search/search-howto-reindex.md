---
title: Opětovné sestavení indexu služby Azure Kognitivní hledání
titleSuffix: Azure Cognitive Search
description: Můžete přidat nové prvky, aktualizovat existující prvky nebo dokumenty nebo odstranit zastaralé dokumenty v rámci úplného opětovného sestavení nebo částečně přírůstkového indexování a aktualizovat index služby Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 26a751924985f94a7d7d12a382d4e6654f36ea48
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793709"
---
# <a name="how-to-rebuild-an-azure-cognitive-search-index"></a>Postup opětovného sestavení indexu služby Azure Kognitivní hledání

Tento článek vysvětluje, jak znovu sestavit index služby Azure Kognitivní hledání, podmínky, za kterých je potřeba znovu sestavit, a doporučení pro zmírnění dopadu opětovného sestavení na probíhající požadavky na dotazy.

*Nové sestavení* odkazuje na vyřazení a opětovné vytvoření fyzických datových struktur přidružených k indexu, včetně všech obrácených indexů založených na polích. V Azure Kognitivní hledání nemůžete vyřadit a znovu vytvořit jednotlivá pole. Chcete-li znovu sestavit index, je nutné odstranit všechna úložiště pole, znovu vytvořit na základě existujícího nebo revidovaného schématu indexu a pak znovu naplnit data vložená do indexu nebo načíst z externích zdrojů. Během vývoje je běžné znovu sestavit indexy, ale možná budete muset znovu sestavit index na úrovni výroby, aby bylo možné přizpůsobit strukturální změny, jako je například přidání komplexních typů nebo přidání polí do návrhů.

Na rozdíl od opětovného sestavení, které přebírají index v režimu offline, se *aktualizace dat* spouští jako úloha na pozadí. K dotazování na úlohy můžete přidávat, odebírat a nahrazovat dokumenty s minimálním přerušením, i když dotazy obvykle trvá déle. Další informace o aktualizaci obsahu indexu najdete v tématu [Přidání, aktualizace nebo odstranění dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="rebuild-conditions"></a>Podmínky opětovného sestavení

| Podmínka | Popis |
|-----------|-------------|
| Změna definice pole | Kontrola názvu pole, datového typu nebo konkrétních [atributů indexu](https://docs.microsoft.com/rest/api/searchservice/create-index) (prohledávatelné, filtrovatelné, seřaditelné, plošky) vyžadují úplné opětovné sestavení. |
| Přiřazení analyzátoru k poli | [Analyzátory](search-analyzers.md) se definují v indexu a pak se přiřazují k polím. Novou definici analyzátoru můžete kdykoli přidat do indexu, ale když je pole Vytvořeno, můžete k němu *přiřadit* pouze analyzátor. To platí jak pro vlastnosti **analyzátoru** , tak pro **indexAnalyzer** . Vlastnost **searchAnalyzer** je výjimka (tuto vlastnost můžete přiřadit existujícímu poli). |
| Aktualizace nebo odstranění definice analyzátoru v indexu | Existující konfiguraci analyzátoru (Analyzer, provádějících tokenizaci, filtr tokenu nebo filtr znaků) v indexu nelze odstranit, pokud znovu nevytvoříte celý index. |
| Přidání pole do modulu pro návrhy | Pokud pole již existuje a chcete ho přidat [do konstrukce](index-add-suggesters.md) tvůrců, je nutné index znovu sestavit. |
| Odstranění pole | Aby bylo možné fyzicky odebrat všechna trasování v poli, je nutné index znovu sestavit. Pokud okamžité opětovné sestavení není praktické, můžete upravit kód aplikace a zakázat tak přístup k poli Deleted (odstraněno). Fyzicky, definice pole a obsah zůstanou v indexu až do dalšího sestavení, když použijete schéma, které vynechá příslušné pole. |
| Přepínací vrstvy | Pokud požadujete větší kapacitu, není Azure Portal žádný místní upgrade. Je nutné vytvořit novou službu a indexy musí být od začátku nové služby sestaveny. K automatizaci tohoto procesu můžete použít vzorový kód **index-Backup-Restore** v tomto [úložišti ukázek Azure kognitivní hledání .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). Tato aplikace bude index zálohovat na řadu souborů JSON a pak znovu vytvořit index ve vyhledávací službě, kterou zadáte.|

Jakékoli další úpravy lze provádět bez vlivu na existující fyzické struktury. Konkrétně následující změny *nevyžadují opětovné* sestavení indexu:

+ Přidat nové pole
+ Nastavení navýšení **atributu v** existujícím poli
+ Nastavení **searchAnalyzer** pro existující pole
+ Přidání nové definice analyzátoru v indexu
+ Přidávání, aktualizace a odstraňování profilů vyhodnocování
+ Přidání, aktualizace nebo odstranění nastavení CORS
+ Přidat, aktualizovat nebo odstranit synonymMaps

Když přidáte nové pole, u existujících indexovaných dokumentů se pro nové pole předává hodnota null. Při budoucí aktualizaci dat hodnoty z externích zdrojových dat nahradí hodnoty null přidané službou Azure Kognitivní hledání. Další informace o aktualizaci obsahu indexu najdete v tématu [Přidání, aktualizace nebo odstranění dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="partial-or-incremental-indexing"></a>Částečné nebo přírůstkové indexování

V Azure Kognitivní hledání nemůžete řídit indexování pro jednotlivá pole, a to volbou pro odstranění nebo opětovné vytvoření určitých polí. Podobně není k dispozici žádný vestavěný mechanismus pro [indexování dokumentů na základě kritérií](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents). Všechny požadavky, které máte pro indexování řízené pomocí kritérií, musí být splněné prostřednictvím vlastního kódu.

To, co můžete dělat snadno, ale *aktualizuje dokumenty* v indexu. Pro mnoho řešení hledání je externí zdrojová data nestálá a synchronizace mezi zdrojovými daty a indexem vyhledávání je běžný postup. V kódu zavolejte operaci [Přidat, aktualizovat nebo odstranit dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) nebo [ekvivalent .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) pro aktualizaci obsahu indexu nebo přidejte hodnoty pro nové pole.

## <a name="partial-indexing-with-indexers"></a>Částečné indexování s indexery

[Indexery](search-indexer-overview.md) zjednodušují úlohu aktualizace dat. Indexer může indexovat pouze jednu tabulku nebo zobrazení v externím zdroji dat. Chcete-li indexovat více tabulek, nejjednodušší způsob je vytvořit zobrazení, které spojuje tabulky a projekty sloupce, které chcete indexovat. 

Pokud používáte indexery, které procházejí externími zdroji dat, vyhledejte ve zdrojových datech sloupec horní mez. Pokud existuje, můžete ho použít pro detekci přírůstkových změn, a to tak, že vybíráte pouze ty řádky, které obsahují nový nebo revidovaný obsah. Pro [úložiště objektů BLOB v Azure](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection)se používá pole `lastModified`. V [úložišti tabulek v Azure](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection)`timestamp` slouží ke stejnému účelu. Podobně obě [Azure SQL Database indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) i [Azure Cosmos DB indexer](search-howto-index-cosmosdb.md#indexing-changed-documents) mají pole pro označení aktualizací řádků. 

Další informace o indexerech najdete v tématu [Přehled indexeru](search-indexer-overview.md) a [resetování indexeru REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="how-to-rebuild-an-index"></a>Postup opětovného sestavení indexu

Naplánujte časté a úplné opětovné sestavení během aktivního vývoje, když jsou schémata indexů ve stavu toku. Pro aplikace, které už jsou v produkčním prostředí, doporučujeme vytvořit nový index, který se spouští souběžně se stávajícím indexem, aby se předešlo výpadkům dotazů.

Pro aktualizace indexu se vyžadují oprávnění ke čtení i zápisu na úrovni služby. 

Index na portálu nemůžete znovu sestavit. Prostřednictvím kódu programu můžete volat [index aktualizace REST API](https://docs.microsoft.com/rest/api/searchservice/update-index) nebo [ekvivalentní rozhraní API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) pro úplné opětovné sestavení. Požadavek na aktualizaci indexu je stejný jako při [vytváření REST API indexu](https://docs.microsoft.com/rest/api/searchservice/create-index), ale má jiný kontext.

Následující pracovní postup se posune k REST API, ale platí stejně pro sadu .NET SDK.

1. Pokud znovu použijete název indexu, [vyřaďte stávající index](https://docs.microsoft.com/rest/api/searchservice/delete-index). 

   Všechny dotazy, které cílí na tento index, jsou okamžitě vyřazeny. Odstranění indexu je nevratné a zničení fyzického úložiště pro kolekci polí a další konstrukce. Ujistěte se, že jste v souvislosti s odstraněním indexu ještě před jeho přetažením nejasní. 

2. Formulujte požadavek na [index aktualizace](https://docs.microsoft.com/rest/api/searchservice/update-index) s vaším koncovým bodem služby, klíčem rozhraní API a [klíčem správce](https://docs.microsoft.com/azure/search/search-security-api-keys). Pro operace zápisu je vyžadován klíč správce.

3. V těle žádosti zadejte schéma indexu se změněnými nebo upravenými definicemi polí. Tělo žádosti obsahuje schéma indexu a také konstrukce pro profily vyhodnocování, analyzátory, moduly pro návrhy a možnosti CORS. Požadavky na schéma jsou zdokumentovány v [vytvoření indexu](https://docs.microsoft.com/rest/api/searchservice/create-index).

4. Odešlete žádost o [aktualizaci indexu](https://docs.microsoft.com/rest/api/searchservice/update-index) pro opětovné sestavení fyzického výrazu indexu v Azure kognitivní hledání. 

5. [Načtěte index s dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) z externího zdroje.

Když vytvoříte index, fyzické úložiště se přidělí pro každé pole ve schématu indexu a pro každé prohledávatelné pole se vytvořil obrácený index. Pole, která nelze prohledávat, lze použít ve filtrech nebo výrazech, ale nemají obrácené indexy a nejsou fulltextové nebo přibližné prohledávatelné. Při opětovném sestavení indexu se tyto opačné indexy odstraní a znovu vytvoří na základě schématu indexu, který zadáte.

Při načtení indexu se převedený index každého pole vyplní všemi jedinečnými slovy, která jsou v jednotlivých dokumentech, a s mapováním na odpovídající ID dokumentů. Například při indexování sady dat hotelů může obrácený index vytvořený pro pole City obsahovat výrazy pro Seattle, Portland a tak dále. K dokumentům, které obsahují Seattle nebo Portland v poli City, by mělo být uvedené ID dokumentu vedle termínu. U všech operací [Přidat, aktualizovat nebo odstranit](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) jsou výrazy a seznam ID dokumentů odpovídajícím způsobem aktualizovány.

> [!NOTE]
> Pokud máte přísné požadavky na smlouvu SLA, můžete zvážit zřízení nové služby speciálně pro tuto práci s vývojem a indexováním, ke kterým dochází v plné izolaci z produkčního indexu. Samostatná služba se spouští na svém vlastním hardwaru a odstraňuje případné případné spory prostředků. Po dokončení vývoje můžete buď ponechat nový index na místě, přesměrovat dotazy do nového koncového bodu a indexu, nebo spustit dokončený kód pro publikování revidovaného indexu v původní službě Azure Kognitivní hledání. V současnosti není k dispozici žádný mechanismus pro přesun připraveného indexu do jiné služby.

## <a name="view-updates"></a>Zobrazit aktualizace

Můžete zahájit dotazování indexu, jakmile se načte první dokument. Pokud znáte ID dokumentu, [vyhledávací dokument REST API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) vrátí konkrétní dokument. Pro širší testování byste měli počkat, až se index zcela načte, a pak použít dotazy k ověření kontextu, který očekáváte, abyste viděli.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Přehled indexeru](search-indexer-overview.md)
+ [Indexování velkých datových sad ve velkém měřítku](search-howto-large-index.md)
+ [Indexování na portálu](search-import-data-portal.md)
+ [Azure SQL Database indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexer databáze Azure Cosmos](search-howto-index-cosmosdb.md)
+ [Indexer pro Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indexer pro Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpečení v Azure Kognitivní hledání](search-security-overview.md)