---
title: Opětovné sestavení indexu Azure Search nebo aktualizujte prohledávatelného obsahu – Azure Search
description: Přidat nové prvky, aktualizovat stávající prvky nebo dokumenty nebo odstranit zastaralé dokumentů v úplné opětovné sestavení nebo částečné přírůstkové indexování na aktualizace indexu Azure Search.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 907ab5cd3272a3d3f64dcfd7c9628a609f4db2f4
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327642"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Postup opětovné sestavení indexu Azure Search

Tento článek vysvětluje, jak opětovné sestavení indexu Azure Search, podmínek, za kterých jsou potřeba znovu sestaví a doporučení pro jejich zmírnění dopadu znovu sestaví na požadavků na probíhající dotazy.

A *znovu sestavit* odkazuje na odstranění a opětovné vytvoření fyzické datové struktury přidružený index, včetně všech na základě pole obrácenou indexů. Ve službě Azure Search nelze vyřadit a znovu vytvořit jednotlivá pole. Opětovné sestavení indexu, musí se odstranit všechna pole úložiště, znovu vytvořit na základě schématu indexu existující nebo upravená a pak provést jeho plnění znovu s daty vloženo do indexu nebo získaných z externích zdrojů. Běžně se během vývoje provést nové sestavení indexů, ale možná budete také muset znovu sestavit index na provozní úrovni tak, aby vyhovovaly změny struktury, jako je například přidávání komplexních typů nebo přidávat pole na moduly pro návrhy.

Rozdíl od znovu sestaví, které převést do režimu offline, index *aktualizace dat* běží jako úlohu na pozadí. Můžete přidat, odebrat a nahraďte dokumenty s minimálním dopadem na dotaz úlohy, i když dotazy obvykle trvá déle. Další informace o aktualizaci indexování obsahu, najdete v části [přidání, aktualizace nebo odstranění dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="rebuild-conditions"></a>Znovu sestavit podmínky

| Podmínka | Popis |
|-----------|-------------|
| Změna definice pole | Úprava pole název, datový typ nebo konkrétní [atributy indexu](https://docs.microsoft.com/rest/api/searchservice/create-index) (prohledávatelné, filtrovatelné, seřaditelné, kategorizovatelné) vyžaduje úplné opětovné sestavení. |
| Analyzátor přidání do pole | [Analyzátory](search-analyzers.md) jsou definovány v indexu a poté přiřazeny k polím. Nový analyzátor do indexu můžete přidat kdykoli, ale můžete jenom *přiřadit* analyzátor při vytváření pole. To platí pro obě **analyzátor** a **indexAnalyzer** vlastnosti. **SearchAnalyzer** vlastnost je výjimku. |
| Aktualizace nebo odstranění analyzátor konstrukce | Nejde odstranit ani změnit existující komponenty analýzy (Analyzátor, tokenizátor, token filtru nebo char filtr), není-li znovu sestavit celý index. |
| Přidání pole modulu pro návrhy | Pokud pole již existuje a chcete, a přidejte ji tak [moduly pro návrhy](index-add-suggesters.md) vytvořit, je nutné znovu sestavit index. |
| Odstranění pole | Fyzicky vyjměte všechna trasování pole, budete muset znovu sestavte index. Při okamžité opětovné sestavení není praktické, můžete upravit kód aplikace můžete zakázat přístup do pole "odstraněné". Fyzicky definici pole a obsah zůstane v indexu až do další sestavení pomocí schématu, která vynechává pole dotyčný. |
| Přepnutí úrovně | Pokud budete potřebovat větší kapacitu, není žádný místní upgrade. Vytvoření nové služby na nový bod kapacity a musí být sestaveny indexy od nuly v nové službě. |

Další změny můžete provést bez dopadu na existující fyzické struktury. Konkrétně, proveďte následující změny *není* vyžadují opětovné sestavení indexu:

+ Přidání nového pole
+ Nastavte **retrievable** atribut existujícího pole
+ Nastavte **searchAnalyzer** na existující pole
+ Přidat novou konstrukci analyzátor v indexu
+ Přidat, aktualizovat nebo odstranit bodovací profily
+ Přidání, aktualizace nebo odstranění nastavení CORS
+ Přidání, aktualizace nebo odstranění synonymMaps

Při přidání nového pole disponují existující indexovaných dokumentů pro nové pole hodnotu null. Při aktualizaci dat nahradit hodnoty z externího zdroje dat hodnoty Null přidal Azure Search. Další informace o aktualizaci indexování obsahu, najdete v části [přidání, aktualizace nebo odstranění dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="partial-or-incremental-indexing"></a>Částečné nebo přírůstkové indexování

Ve službě Azure Search nemůžete ovládat, indexování u jednotlivých polích, zvolíte-li odstranit nebo znovu vytvořte konkrétních polí. Podobně, neexistuje žádný předdefinovaný mechanismus pro [indexování dokumentů na základě kritérií](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents). Všechny požadavky, které máte pro řízené kritéria indexování musí být splněny prostřednictvím vlastního kódu.

Co můžete dělat, ale *aktualizovat dokumenty* v indexu. Pro mnoho řešení pro vyhledávání externího zdroje data jsou nestálá a synchronizace mezi zdrojem dat a index vyhledávání je běžnou praxí. V kódu, zavolejte [přidání, aktualizace nebo odstranění dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) operace nebo [ekvivalent .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) aktualizovat indexování obsahu, nebo přidejte hodnoty pro nové pole.

## <a name="partial-indexing-with-indexers"></a>Částečné indexování pomocí indexerů

[Indexery](search-indexer-overview.md) zjednodušit aktualizovat data. Indexer může indexovat pouze jednu tabulku nebo zobrazení ve zdroji dat. externí. Nejjednodušším přístupem k indexování více tabulek, je vytvořit zobrazení, které spojí tabulky a projekty sloupce, které chcete použít k indexování. 

Při použití indexerů, které procházejí externí zdroje dat, vyhledejte "vysoká označuje jako horní mez" sloupce ve zdrojových datech. Pokud takové existuje, můžete ho použít pro zjišťování přírůstkovou výběr pouze tyto řádky obsahující nový nebo upravený obsah. Pro [úložiště objektů Blob v Azure](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), `lastModified` pole se používá. Na [Azure Table storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` slouží stejnému účelu. Podobně i [indexer pro Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) a [indexeru služby Azure Cosmos DB](search-howto-index-cosmosdb.md#indexing-changed-documents) obsahovat pole pro nastavení příznaku aktualizace řádku. 

Další informace o indexerech najdete v tématu [přehled indexerů](search-indexer-overview.md) a [resetovat Indexer REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="how-to-rebuild-an-index"></a>Postup opětovné sestavení indexu

Plán na časté, úplné znovu sestaví při aktivním vývoji, když index schémata jsou ve stavu toku. Pro aplikace již v produkčním prostředí doporučujeme vytvořit nový index, na kterém běží souběžně existujícího indexu výpadky dotazu.

Pokud budete mít přísné požadavky smlouvy SLA, můžete zvážit nové služby speciálně pro tuto práci s vývojem pro zřizování a indexování, ke kterým dochází v úplnou izolaci od indexu produkčního prostředí. Samostatná služba běží na svůj vlastní hardware, což eliminuje možnost kolize prostředků. Jakmile je dokončen vývoj, buď ponecháte nový index na místě, přesměrování dotazy do nového koncového bodu a index nebo byste spustili Dokončený kód publikovat revidované indexu na původní služby Azure Search. Aktuálně neexistuje žádný mechanismus pro přesun do jiné služby připravené k použití indexu.

Oprávnění ke čtení a zápis na úrovni služby jsou požadovány pro aktualizace indexu. Prostřednictvím kódu programu, můžete volat [aktualizace indexu REST API](https://docs.microsoft.com/rest/api/searchservice/update-index) nebo rozhraní .NET API pro úplné opětovné sestavení. Žádost je stejný jako [vytvořit Index rozhraní REST API služby](https://docs.microsoft.com/rest/api/searchservice/create-index), ale má jiný kontext.

1. Pokud jsou opětovné použití názvu indexu [vyřaďte existujícího indexu](https://docs.microsoft.com/rest/api/searchservice/delete-index). Okamžitě se zahodí všechny dotazy, které cílí na daném indexu. Odstraňuje se index je nevratná operace, zničení fyzické úložiště kolekce polí a jiných objektů. Ujistěte se, že můžete vymazat o důsledcích odstranění indexu, než jste ji přemístili. 

2. Definice polí změněné nebo upravené poskytněte schématu indexu. Schéma požadavky jsou zdokumentované v [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

3. Poskytují [klíč správce](https://docs.microsoft.com/azure/search/search-security-api-keys) v požadavku.

4. Odeslání [aktualizaci indexu](https://docs.microsoft.com/rest/api/searchservice/update-index) příkaz k opětovnému sestavení fyzické výrazu indexu Azure Search. Text požadavku obsahuje schéma indexu, jakož i konstrukce, pro vyhodnocování profilů, analyzátory, moduly pro návrhy a možnosti CORS.

5. [Načtení indexu s dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) z externího zdroje. Můžete také pomocí tohoto rozhraní API, pokud aktualizujete existující, beze změny schématu indexu s aktualizovaných dokumentů.

Při vytváření indexu fyzické úložiště je alokováno pro každé pole v schéma indexu s obráceným index vytvořený pro každé prohledávatelná pole. Pole, která nejsou prohledávatelná lze použít v filtry nebo výrazy, ale mají obrácený není indexy a jsou není fulltextově nebo přibližné prohledávatelné. Na znovuvytvoření indexu jsou tyto indexy obrácenou odstranili a znovu vytvořili na základě schématu indexu je zadat.

Při načtení indexu obrácenou index každé pole naplněný všechna slova jedinečné, tokenizovaná z každého dokumentu pomocí mapy dokumentu odpovídající ID. Například názvy při indexování hotels datové sady, pravděpodobně obrácenou index vytvořený pro pole města podmínky pro Seattle, Portland a tak dále. Dokumenty, které zahrnují pole Město Seattle nebo Portland by měla ID dokumentu uvedené spolu s termín. Na žádném [přidávat, aktualizovat a odstraňovat](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) operace, podmínky a seznam ID dokumentu se aktualizují odpovídajícím způsobem.

## <a name="view-updates"></a>Zobrazit aktualizace

Můžete začít ihned poté, co je první dokument načten dotazování indexu. Pokud znáte ID dokumentu [rozhraní REST API pro vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/lookup-document) vrátí určitého dokumentu. Pro širší testování by měl Počkejte, dokud je index plně načten a pak použít dotazy k ověření kontextu, ve kterém byste měli vidět.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Přehled indexeru](search-indexer-overview.md)
+ [Index velkých datových sad ve velkém měřítku](search-howto-large-index.md)
+ [Indexování na portálu](search-import-data-portal.md)
+ [Indexer pro Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexer databáze Azure Cosmos](search-howto-index-cosmosdb.md)
+ [Indexer pro Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indexer pro Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpečení ve službě Azure Search](search-security-overview.md)