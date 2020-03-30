---
title: Znovu sestavit index hledání
titleSuffix: Azure Cognitive Search
description: Přidejte nové prvky, aktualizujte existující prvky nebo dokumenty nebo odstraňte zastaralé dokumenty v úplném obnovení nebo částečném indexování, abyste aktualizovali index Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 58b60a0eee8ab407709f33911d3c6b13ffbf301a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498384"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Jak znovu sestavit index v Azure Cognitive Search

Tento článek vysvětluje, jak znovu sestavit index Azure Cognitive Search, okolnosti, za kterých jsou požadovány znovu sestavit a doporučení pro zmírnění dopadu znovusestavit na probíhající požadavky na dotazy.

*Opětovné sestavení* odkazuje na uvolnění mačká a znovu vytvoření fyzické datové struktury přidružené k indexu, včetně všech indexů na základě polí invertované. V Azure Cognitive Search nelze přetažení a znovu vytvořit jednotlivá pole. Chcete-li obnovit index, musí být všechna úložiště polí odstraněna, znovu vytvořena na základě existujícího nebo revidovaného schématu indexu a poté znovu naplněna daty, která jsou do indexu nabízena nebo zexterních zdrojů. 

Je běžné znovu sestavit indexy během vývoje, ale může být také nutné znovu sestavit index na úrovni výroby, aby se přizpůsobilstrukturálním změnám, jako je například přidání složitých typů nebo přidání polí do přimítačů.

## <a name="rebuild-conditions"></a>Podmínky opětovného sestavení

Přetažení a znovu vytvořit index, pokud jsou splněny některé z následujících podmínek. 

| Podmínka | Popis |
|-----------|-------------|
| Změna definice pole | Změna názvu pole, datového typu nebo [konkrétních atributů indexu](https://docs.microsoft.com/rest/api/searchservice/create-index) (prohledávatelné, filtrovatelné, seřaditelné, s tůlnou tváří v tvář) vyžaduje úplné opětovné sestavení. |
| Přiřazení analyzátoru k poli | [Analyzátory](search-analyzers.md) jsou definovány v indexu a poté přiřazeny polím. Novou definici analyzátoru můžete do indexu kdykoli přidat, ale analyzátor můžete *přiřadit* pouze při vytvoření pole. To platí pro **vlastnosti analyzátoru** a **indexAnalyzer.** Vlastnost **searchAnalyzer** je výjimkou (tuto vlastnost můžete přiřadit k existujícímu poli). |
| Aktualizace nebo odstranění definice analyzátoru v indexu | Existující konfiguraci analyzátoru (analyzátor, tokenizátor, filtr tokenů nebo filtr znaků) nelze v indexu odstranit ani změnit, pokud neobnovíte celý index. |
| Přidání pole k návrhu | Pokud pole již existuje a chcete jej přidat do konstrukce [návrhy,](index-add-suggesters.md) je nutné znovu vytvořit index. |
| Odstranění pole | Chcete-li fyzicky odebrat všechna trasování pole, je třeba znovu vytvořit index. Pokud okamžité opětovné sestavení není praktické, můžete upravit kód aplikace a zakázat tak přístup k poli "odstraněno" nebo pomocí [parametru $select dotazu](search-query-odata-select.md) zvolit, která pole jsou reprezentována v sadě výsledků. Fyzicky definice pole a obsah zůstávají v indexu až do dalšího znovusestavit, když použijete schéma, které vyneche dané pole. |
| Přepnout úrovně | Pokud požadujete větší kapacitu, neexistuje žádný upgrade na místě na webu Azure Portal. Je nutné vytvořit novou službu a indexy musí být sestaveny od začátku na nové služby. Chcete-li tento proces automatizovat, můžete použít ukázkový kód **obnovení indexu** v tomto [ukázkovém repo nástroje Azure Cognitive Search .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). Tato aplikace bude zálohovat index na řadu souborů JSON a pak znovu vytvořit index ve vyhledávací službě, kterou zadáte.|

## <a name="update-conditions"></a>Aktualizovat podmínky

Mnoho dalších úprav lze provést bez dopadu na stávající fyzické struktury. Konkrétně následující změny *nevyžadují* znovusestavit index. Pro tyto změny můžete [aktualizovat definici indexu](https://docs.microsoft.com/rest/api/searchservice/update-index) se změnami.

+ Přidání nového pole
+ Nastavení **načítatelného** atributu u existujícího pole
+ Nastavení **vyhledávacího analyzátoru** v existujícím poli
+ Přidání nové definice analyzátoru do indexu
+ Přidání, aktualizace nebo odstranění profilů hodnocení
+ Přidání, aktualizace nebo odstranění nastavení CORS
+ Přidání, aktualizace nebo odstranění synonymMapy

Když přidáte nové pole, existující indexované dokumenty dostanou nulovou hodnotu pro nové pole. Při budoucí aktualizaci dat nahradí hodnoty z externích zdrojových dat hodnoty null přidané azure cognitive search. Další informace o aktualizaci obsahu indexu naleznete v tématu [Přidání, Aktualizace nebo Odstranění dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="how-to-rebuild-an-index"></a>Jak znovu sestavit index

Během vývoje schéma indexu se často mění. Můžete naplánovat pro vytvoření indexy, které lze odstranit, znovu a znovu rychle s malou reprezentativní sadu dat.

Pro aplikace, které jsou již ve výrobě, doporučujeme vytvořit nový index, který běží vedle sebe existující index, aby se zabránilo prostojům dotazu. Kód aplikace poskytuje přesměrování na nový index.

Indexování neběží na pozadí a služba vyrovná další indexování proti probíhající dotazy. Během indexování můžete [sledovat požadavky na dotazy](search-monitor-queries.md) na portálu a zajistit tak, že dotazy se dokončují včas.

1. Určete, zda je požadováno opětovné sestavení. Pokud právě přidáváte pole nebo měníte část indexu, která nesouvisí s poli, můžete jednoduše [aktualizovat definici](https://docs.microsoft.com/rest/api/searchservice/update-index) bez odstranění, opětovného načtení a úplného opětovného načtení.

1. [Získejte definici indexu](https://docs.microsoft.com/rest/api/searchservice/get-index) v případě, že ji potřebujete pro budoucí použití.

1. [Přetažení existující index](https://docs.microsoft.com/rest/api/searchservice/delete-index), za předpokladu, že nejste spuštěny nové a staré indexy vedle sebe. 

   Všechny dotazy zaměřené na tento index jsou okamžitě vynechány. Nezapomeňte, že odstranění indexu je nevratné, ničí fyzické úložiště pro kolekce polí a další konstrukce. Pauza přemýšlet o důsledcích před pádem to. 

1. [Vytvořte revidovaný index](https://docs.microsoft.com/rest/api/searchservice/create-index), kde tělo požadavku obsahuje změněné nebo změněné definice polí.

1. [Načtěte index s dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) z externího zdroje.

Při vytváření indexu je fyzické úložiště přiděleno pro každé pole ve schématu indexu s obráceným indexem vytvořeným pro každé prohledávatelné pole. Pole, která nelze prohledávat, lze použít ve filtrech nebo výrazech, ale nemají invertované indexy a nelze je prohledávat jako fulltextové nebo neprohledávatelné. Při opětovném sestavení indexu jsou tyto invertované indexy odstraněny a znovu vytvořeny na základě zadaných schémat u indexu.

Při načtení indexu je invertovaný index každého pole naplněn všemi jedinečnými tokenizovanými slovy z každého dokumentu s mapou na odpovídající ID dokumentu. Například při indexování datové sady hotelů může obrácený index vytvořený pro pole Město obsahovat termíny pro Seattle, Portland atd. Dokumenty, které obsahují Seattle nebo Portland v poli Město, budou mít vedle termínu uvedeno id dokumentu. Při jakékoli operaci [Přidat, Aktualizovat nebo Odstranit](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) jsou odpovídajícím způsobem aktualizovány podmínky a seznam ID dokumentu.

> [!NOTE]
> Pokud máte přísné požadavky s la, můžete zvážit zřizování nové služby speciálně pro tuto práci, s vývojem a indexování dochází v úplné izolaci od indexu výroby. Samostatná služba běží na vlastním hardwaru, což eliminuje možnost iniciála prostředků. Po dokončení vývoje byste buď ponechat nový index na místě, přesměrování dotazů na nový koncový bod a index, nebo byste spustit hotový kód publikovat revidovaný index na původní služby Azure Cognitive Search. V současné době neexistuje žádný mechanismus pro přesunutí indexu připraveného k použití do jiné služby.

## <a name="check-for-updates"></a>Vyhledat aktualizace

Můžete začít dotazování indexu, jakmile je načten první dokument. Pokud znáte ID dokumentu, [vrátí rozhraní REST API vyhledávacího dokumentu](https://docs.microsoft.com/rest/api/searchservice/lookup-document) konkrétní dokument. Pro širší testování byste měli počkat, dokud index je plně načten a potom pomocí dotazů k ověření kontextu, který očekáváte zobrazit.

[Pomocí Průzkumníka vyhledávání](search-explorer.md) nebo nástroje pro testování webu, jako je [Postman,](search-get-started-postman.md) můžete vyhledat aktualizovaný obsah.

Pokud jste pole přidali nebo přejmenovali, použijte [$select](search-query-odata-select.md) k vrácení tohoto pole:`search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Viz také

+ [Přehled indexeru](search-indexer-overview.md)
+ [Indexování velkých datových sad ve velkém měřítku](search-howto-large-index.md)
+ [Indexování na portálu](search-import-data-portal.md)
+ [Indexer databáze Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexer pro Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indexer pro Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indexer pro Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpečení v Azure Cognitive Search](search-security-overview.md)