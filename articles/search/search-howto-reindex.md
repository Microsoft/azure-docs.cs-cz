---
title: Opětovné sestavení indexu vyhledávání
titleSuffix: Azure Cognitive Search
description: Můžete přidat nové prvky, aktualizovat existující prvky nebo dokumenty nebo odstranit zastaralé dokumenty v rámci úplného opětovného sestavení nebo částečného indexování a aktualizovat index služby Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 47e9b80bb25b7ff14695cc67682265fe338ff76f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98119097"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Postup opětovného sestavení indexu v Azure Kognitivní hledání

Tento článek vysvětluje, jak znovu sestavit index služby Azure Kognitivní hledání, podmínky, za kterých je potřeba znovu sestavit, a doporučení pro zmírnění dopadu opětovného sestavení na probíhající požadavky na dotazy.

*Nové sestavení* odkazuje na vyřazení a opětovné vytvoření fyzických datových struktur přidružených k indexu, včetně všech obrácených indexů založených na polích. V Azure Kognitivní hledání nemůžete vyřadit a znovu vytvořit jednotlivá pole. Chcete-li znovu sestavit index, je nutné odstranit všechna úložiště pole, znovu vytvořit na základě existujícího nebo revidovaného schématu indexu a pak znovu naplnit data vložená do indexu nebo načíst z externích zdrojů. 

Je běžné sestavovat indexy během vývoje, když provádíte iteraci nad návrhem indexu, ale možná budete muset znovu sestavit index na úrovni výroby, abyste pokryli strukturální změny, jako je například přidávání složitých typů nebo přidávání polí do modulu pro návrhy.

## <a name="rebuild-versus-refresh"></a>"Znovu sestavit" oproti "obnovení"

Opětovné sestavení by nemělo být zaměněno s aktualizací obsahu indexu pomocí nových, upravených nebo odstraněných dokumentů. Aktualizace hledání corpus je skoro v každé vyhledávací aplikaci, a to s některými scénáři, které vyžadují aktualizace po minutách (například když corpus vyhledávání potřebuje, aby odrážely změny inventáře v online prodejní aplikaci).

Pokud neměníte strukturu indexu, můžete index aktualizovat pomocí stejných technik, jako jste použili k načtení indexu zpočátku:

* Pro indexování v režimu push volejte změny do indexu [zadáním přidat, aktualizovat nebo odstranit dokumenty](/rest/api/searchservice/addupdate-or-delete-documents) .

* U indexerů můžete [naplánovat provádění indexeru](search-howto-schedule-indexers.md) a použít sledování změn nebo časová razítka k identifikaci rozdílu. Pokud se aktualizace musí projevit rychleji, než může Plánovač spravovat, můžete místo toho použít indexování v režimu push.

## <a name="rebuild-conditions"></a>Podmínky opětovného sestavení

Pokud platí kterákoli z následujících podmínek, vyřaďte index a vytvořte ho znovu. 

| Podmínka | Description |
|-----------|-------------|
| Změna definice pole | Kontrola názvu pole, datového typu nebo konkrétních [atributů indexu](/rest/api/searchservice/create-index) (prohledávatelné, filtrovatelné, seřaditelné, plošky) vyžadují úplné opětovné sestavení. |
| Přiřazení analyzátoru k poli | [Analyzátory](search-analyzers.md) se definují v indexu a pak se přiřazují k polím. Novou definici analyzátoru můžete kdykoli přidat do indexu, ale když je pole Vytvořeno, můžete k němu *přiřadit* pouze analyzátor. To platí jak pro vlastnosti **analyzátoru** , tak pro **indexAnalyzer** . Vlastnost **searchAnalyzer** je výjimka (tuto vlastnost můžete přiřadit existujícímu poli). |
| Aktualizace nebo odstranění definice analyzátoru v indexu | Existující konfiguraci analyzátoru (Analyzer, provádějících tokenizaci, filtr tokenu nebo filtr znaků) v indexu nelze odstranit, pokud znovu nevytvoříte celý index. |
| Přidání pole do modulu pro návrhy | Pokud pole již existuje a chcete ho přidat [do konstrukce](index-add-suggesters.md) tvůrců, je nutné index znovu sestavit. |
| Odstranění pole | Aby bylo možné fyzicky odebrat všechna trasování v poli, je nutné index znovu sestavit. Pokud okamžité opětovné sestavení není praktické, můžete upravit kód aplikace, zakázat přístup k poli Deleted nebo použít [parametr $SELECT dotazu](search-query-odata-select.md) k výběru polí, která jsou v sadě výsledků zastoupena. Fyzicky, definice pole a obsah zůstanou v indexu až do dalšího sestavení, když použijete schéma, které vynechá příslušné pole. |
| Přepínací vrstvy | Pokud požadujete větší kapacitu, není Azure Portal žádný místní upgrade. Je nutné vytvořit novou službu a indexy musí být od začátku nové služby sestaveny. K automatizaci tohoto procesu můžete použít vzorový kód **index-Backup-Restore** v tomto [úložišti ukázek Azure kognitivní hledání .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). Tato aplikace zálohuje váš index na řadu souborů JSON a pak znovu vytvoří index ve vyhledávací službě, kterou zadáte.|

## <a name="update-conditions"></a>Aktualizovat podmínky

Mnoho dalších úprav lze provést bez vlivu na existující fyzické struktury. Konkrétně následující změny *nevyžadují opětovné* sestavení indexu. U těchto změn můžete [definici indexu aktualizovat](/rest/api/searchservice/update-index) pomocí vašich změn.

+ Přidání nového pole
+ Nastavení navýšení **atributu v** existujícím poli
+ Nastavení **searchAnalyzer** pro existující pole
+ Přidání nové definice analyzátoru v indexu
+ Přidávání, aktualizace a odstraňování profilů vyhodnocování
+ Přidání, aktualizace nebo odstranění nastavení CORS
+ Přidat, aktualizovat nebo odstranit synonymMaps

Když přidáte nové pole, u existujících indexovaných dokumentů se pro nové pole předává hodnota null. Při budoucí aktualizaci dat hodnoty z externích zdrojových dat nahradí hodnoty null přidané službou Azure Kognitivní hledání. Další informace o aktualizaci obsahu indexu najdete v tématu [Přidání, aktualizace nebo odstranění dokumentů](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="how-to-rebuild-an-index"></a>Postup opětovného sestavení indexu

Během vývoje se schéma indexu často mění. Můžete ji naplánovat vytvořením indexů, které je možné odstranit, znovu vytvořit a znovu načíst pomocí malého zástupce datové sady.

Pro aplikace, které už jsou v produkčním prostředí, doporučujeme vytvořit nový index, který se spouští souběžně se stávajícím indexem, aby se předešlo výpadkům dotazů. Váš kód aplikace poskytuje přesměrování na nový index.

Indexování se nespouští na pozadí a služba vyrovnává další indexování u probíhajících dotazů. Během indexování můžete na portálu [monitorovat požadavky](search-monitor-queries.md) na dotazy, aby se zajistilo, že dotazy budou dokončeny včas.

1. Určete, zda je nutné znovu sestavit sestavení. Pokud přidáváte pouze pole nebo měníte část indexu, která nesouvisí s poli, je možné jednoduše [aktualizovat definici](/rest/api/searchservice/update-index) bez odstranění, opětovného vytvoření a úplného opětovného načtení.

1. [Získejte definici indexu](/rest/api/searchservice/get-index) pro případ, že ho budete potřebovat pro budoucí referenci.

1. [Vyřaďte existující index](/rest/api/searchservice/delete-index)za předpokladu, že nespouštíte nové a staré indexy vedle sebe. 

   Všechny dotazy, které cílí na tento index, jsou okamžitě vyřazeny. Pamatujte, že odstranění indexu je nevratné a zničení fyzického úložiště pro kolekce polí a další konstrukce. Než se pustíte do úvahy o důsledcích, pozastavíte je. 

1. [Vytvoří revidovaný index](/rest/api/searchservice/create-index), kde tělo požadavku obsahuje změněné nebo upravené definice polí.

1. [Načtěte index s dokumenty](/rest/api/searchservice/addupdate-or-delete-documents) z externího zdroje.

Když vytvoříte index, fyzické úložiště se přidělí pro každé pole ve schématu indexu a pro každé prohledávatelné pole se vytvořil obrácený index. Pole, která nelze prohledávat, lze použít ve filtrech nebo výrazech, ale nemají obrácené indexy a nejsou fulltextové nebo přibližné prohledávatelné. Při opětovném sestavení indexu se tyto opačné indexy odstraní a znovu vytvoří na základě schématu indexu, který zadáte.

Při načtení indexu se převedený index každého pole vyplní všemi jedinečnými slovy, která jsou v jednotlivých dokumentech, a s mapováním na odpovídající ID dokumentů. Například při indexování sady dat hotelů může obrácený index vytvořený pro pole City obsahovat výrazy pro Seattle, Portland a tak dále. K dokumentům, které obsahují Seattle nebo Portland v poli City, by mělo být uvedené ID dokumentu vedle termínu. U všech operací [Přidat, aktualizovat nebo odstranit](/rest/api/searchservice/addupdate-or-delete-documents) jsou výrazy a seznam ID dokumentů odpovídajícím způsobem aktualizovány.

> [!NOTE]
> Pokud máte přísné požadavky na smlouvu SLA, můžete zvážit zřízení nové služby speciálně pro tuto práci s vývojem a indexováním, ke kterým dochází v plné izolaci z produkčního indexu. Samostatná služba se spouští na svém vlastním hardwaru a odstraňuje případné případné spory prostředků. Po dokončení vývoje můžete buď ponechat nový index na místě, přesměrovat dotazy do nového koncového bodu a indexu, nebo spustit dokončený kód pro publikování revidovaného indexu v původní službě Azure Kognitivní hledání. V současnosti není k dispozici žádný mechanismus pro přesun připraveného indexu do jiné služby.

## <a name="check-for-updates"></a>Vyhledat aktualizace

Můžete zahájit dotazování indexu, jakmile se načte první dokument. Pokud znáte ID dokumentu, [vyhledávací dokument REST API](/rest/api/searchservice/lookup-document) vrátí konkrétní dokument. Pro širší testování byste měli počkat, až se index zcela načte, a pak použít dotazy k ověření kontextu, který očekáváte, abyste viděli.

K vyhledání aktualizovaného obsahu můžete použít [Průzkumníka služby Search](search-explorer.md) nebo nástroj pro testování webu, jako je například [post](search-get-started-rest.md) nebo [Visual Studio Code](search-get-started-vs-code.md) .

Pokud jste přidali nebo přejmenovali pole, použijte [$Select](search-query-odata-select.md) k vrácení tohoto pole: `search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Viz také

+ [Přehled indexeru](search-indexer-overview.md)
+ [Indexování velkých datových sad ve velkém měřítku](search-howto-large-index.md)
+ [Indexování na portálu](search-import-data-portal.md)
+ [Azure SQL Database indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexer pro Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indexer pro Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indexer pro Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpečení v Azure Kognitivní hledání](search-security-overview.md)