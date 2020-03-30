---
title: Návrh tabulek Azure Cosmos DB pro škálování a výkon
description: 'Průvodce návrhem úložiště azure table: Škálovatelné a výkonné tabulky v Azure Cosmos DB a azure table storage'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: sakash279
ms.author: akshanka
ms.custom: seodec18
ms.openlocfilehash: 166076d366cbbf7bef24648772beaba9b3a88253
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246471"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Průvodce návrhem tabulky úložišť Azure Table: Škálovatelné a výkonné tabulky

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Při návrhu škálovatelných a výkonných tabulek je potřeba zvážit řadu faktorů, včetně nákladů. Pokud jste už někdy navrhovali schémata pro relační databáze, tyto aspekty vám budou povědomé. Přestože však mezi službou Azure Table Storage a relačními modely existují určité podobnosti, je mezi nimi i řada důležitých rozdílů. Tyto rozdíly obvykle vedou k různým návrhům, které pro někoho se zkušenostmi s relačními databázemi můžou působit nelogicky nebo špatně, ale které dávají smysl v případě návrhu pro úložiště NoSQL párů klíč-hodnota, jako je služba Table Storage.

Table storage je navržen tak, aby podporoval aplikace v cloudovém měřítku, které mohou obsahovat miliardy entit ("řádky" v terminologii relační databáze) dat nebo pro datové sady, které musí podporovat vysoké objemy transakcí. Proto je třeba přemýšlet jinak o tom, jak ukládat data a pochopit, jak funguje úložiště tabulky. Dobře navržené úložiště dat NoSQL může umožnit vašemu řešení škálovat mnohem dále (a za nižší cenu) než řešení, které používá relační databázi. Tato příručka vám pomůže s těmito tématy.  

## <a name="about-azure-table-storage"></a>O úložišti Azure Table
Tato část upozorňuje na některé klíčové funkce table storage, které jsou obzvláště důležité pro navrhování výkonu a škálovatelnosti. Pokud s Azure Storage a Table Storage teču, [přečtěte si článek Úvod do úložiště Microsoft Azure](../storage/common/storage-introduction.md) a [Začínáme s úložištěm Azure Table pomocí rozhraní .NET](table-storage-how-to-use-dotnet.md) před přečtením zbývající části tohoto článku. Přestože se tato příručka zaměřuje na úložiště table, zahrnuje určitou diskusi o úložišti Azure Queue a úložišti objektů blob Azure a o tom, jak je můžete používat spolu s table storage v řešení.  

Úložiště tabulky používá k ukládání dat tabulkový formát. Ve standardní terminologii představuje každý řádek tabulky entitu a sloupce ukládají různé vlastnosti této entity. Každá entita má dvojici klíčů, které ji jednoznačně identifikují, a sloupec časového razítka, který úložiště tabulky používá ke sledování, kdy byla entita naposledy aktualizována. Pole časového razítka se přidá automaticky a nelze ručně přepsat časové razítko libovolnou hodnotou. Úložiště tabulek používá toto poslední upravené časové razítko (LMT) ke správě optimistické souběžnosti.  

> [!NOTE]
> Operace rozhraní REST API `ETag` úložiště tabulky také vrátí hodnotu, která je odvozena z LMT. V tomto dokumentu se zaměnitelně používají termíny ETag a LMT, protože odkazují na stejná podkladová data.  
> 
> 

Následující příklad ukazuje jednoduchý návrh tabulky pro uložení entit zaměstnanců a oddělení. Mnoho příkladů uvedených dále v této příručce je založeno na tomto jednoduchém návrhu.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Časové razítko</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>E-mail</th>
</tr>
<tr>
<td>Don</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>E-mail</th>
</tr>
<tr>
<td>Jun</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Oddělení</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>ZaměstnanecCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>E-mail</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Zatím tento návrh vypadá podobně jako tabulka v relační databázi. Klíčovými rozdíly jsou povinné sloupce a možnost uložit více typů entit ve stejné tabulce. Kromě toho každá z uživatelem definovaných vlastností, například **FirstName** nebo **Age**, má datový typ, například celé číslo nebo řetězec, stejně jako sloupec v relační databázi. Na rozdíl od relační databáze však bez schématu povahy table storage znamená, že vlastnost nemusí mít stejný datový typ na každou entitu. Chcete-li uložit složité datové typy do jedné vlastnosti, musíte použít serializovaný formát, například JSON nebo XML. Další informace naleznete [v tématu Principy datového modelu úložiště tabulky](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Vaše volba `PartitionKey` `RowKey` a je zásadní pro dobrý design stolu. Každá entita uložená v tabulce `PartitionKey` `RowKey`musí mít jedinečnou kombinaci a . Stejně jako u klíčů v `PartitionKey` tabulce `RowKey` relační databáze jsou hodnoty a indexovány tak, aby vytvořily seskupený index, který umožňuje rychlé vyhledávání. Úložiště tabulky však nevytváří žádné sekundární indexy, takže se jedná pouze o dvě indexované vlastnosti (některé vzory popsané později ukazují, jak můžete obejít toto zjevné omezení).  

Tabulka se skládá z jednoho nebo více oddílů a mnoho rozhodnutí o návrhu, která učiníte, bude kolem výběru vhodného `PartitionKey` a `RowKey` optimalizovaného řešení. Řešení se může skládat pouze z jedné tabulky, která obsahuje všechny entity uspořádané do oddílů, ale obvykle řešení má více tabulek. Tabulky vám pomohou logicky uspořádat entity a spravovat přístup k datům pomocí seznamů řízení přístupu. Můžete přetáhnout celou tabulku pomocí jedné operace úložiště.  

### <a name="table-partitions"></a>Oddíly tabulky
Název účtu, název tabulky `PartitionKey` a společně identifikovat oddíl v rámci služby úložiště, kde table storage ukládá entity. Kromě toho, že jsou součástí adresovacího schématu pro entity, oddíly definují obor pro transakce (viz část dále v tomto článku [Transakce skupiny entit](#entity-group-transactions)) a tvoří základ pro škálování úložiště tabulky. Další informace o oddílech tabulky naleznete v [tématu Kontrola výkonu a škálovatelnosti pro úložiště tabulek](../storage/tables/storage-performance-checklist.md).  

V table storage jednotlivé uzly služby jeden nebo více úplných oddílů a služby škálování dynamicky vyrovnávání zatížení oddíly mezi uzly. Pokud je uzel pod zatížením, table storage můžete rozdělit rozsah oddílů obsluhovaných tímto uzlem na různé uzly. Když provoz odezní, table storage můžete sloučit oddíl rozsahy z tichých uzlů zpět na jeden uzel.  

Další informace o interních podrobnostech table storage a zejména o tom, jak spravuje oddíly, najdete v [tématu Microsoft Azure Storage: Vysoce dostupná služba cloudového úložiště se silnou konzistencí](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Transakce skupiny entit
V table storage jsou transakce skupiny entit (EGT) jediným integrovaným mechanismem pro provádění atomických aktualizací napříč více entitami. EgT jsou také označovány jako *dávkové transakce*. EGTs může pracovat pouze na entity uložené ve stejném oddílu (sdílení stejného klíče oddílu v konkrétní tabulce), takže kdykoli budete potřebovat atomické transakční chování napříč více entit, ujistěte se, že tyto entity jsou ve stejném oddílu. To je často důvodpro zachování více typů entit ve stejné tabulce (a oddíl) a nepoužíváte více tabulek pro různé typy entit. Jediné EGT může pracovat maximálně se 100 entitami.  Pokud odešlete více souběžných EGT pro zpracování, je důležité zajistit, že tyto EGTs nefungují na entity, které jsou společné napříč EGTs. V opačném případě riskujete zpoždění zpracování.

EGT s y také zavádějí potenciální kompromis, který můžete hodnotit ve svém návrhu. Použití další oddíly zvyšuje škálovatelnost vaší aplikace, protože Azure má více příležitostí pro vyrovnávání zatížení požadavky napříč uzly. To však může omezit schopnost vaší aplikace provádět atomické transakce a udržovat silnou konzistenci pro vaše data. Kromě toho existují konkrétní cíle škálovatelnosti na úrovni oddílu, který může omezit propustnost transakcí, které můžete očekávat pro jeden uzel.

Další informace o cílech škálovatelnosti pro účty úložiště Azure najdete v tématu [Škálovatelnost cíle pro účty standardního úložiště](../storage/common/scalability-targets-standard-account.md). Další informace o cílech škálovatelnosti pro úložiště tabulek naleznete v tématu [Škálovatelnost a výkonnostní cíle pro table storage](../storage/tables/scalability-targets.md). Pozdější části této příručky popisují různé strategie návrhu, které vám pomohou spravovat kompromisy, jako je tento, a popisují, jak nejlépe zvolit klíč oddílu na základě specifických požadavků klientské aplikace.  

### <a name="capacity-considerations"></a>Důležité informace o kapacitách
Následující tabulka obsahuje některé hodnoty klíče, které je třeba znát při navrhování řešení úložiště tabulky:  

| Celková kapacita účtu úložiště Azure | 500 TB |
| --- | --- |
| Počet tabulek v účtu úložiště Azure |Omezeno pouze kapacitou účtu úložiště. |
| Počet oddílů v tabulce |Omezeno pouze kapacitou účtu úložiště. |
| Počet entit v oddílu |Omezeno pouze kapacitou účtu úložiště. |
| Velikost jednotlivého subjektu |Až 1 MB s maximálně 255 vlastnostmi (včetně `PartitionKey`, `RowKey`a `Timestamp`). |
| Velikost`PartitionKey` |Řetězec až 1 KB ve velikosti. |
| Velikost`RowKey` |Řetězec až 1 KB ve velikosti. |
| Velikost transakce skupiny účetní jednotky |Transakce může zahrnovat maximálně 100 entit a datová část musí mít velikost menší než 4 MB. EGT může aktualizovat entitu pouze jednou. |

Další informace naleznete [v tématu Principy datového modelu služby Table](https://msdn.microsoft.com/library/azure/dd179338.aspx)Service .  

### <a name="cost-considerations"></a>Důležité informace o nákladech
Úložiště tabulky je relativně levné, ale měli byste zahrnout odhady nákladů pro využití kapacity a množství transakcí jako součást hodnocení libovolného řešení, které používá table storage. V mnoha scénářích je však ukládání nenormalizovaných nebo duplicitních dat za účelem zlepšení výkonu nebo škálovatelnosti vašeho řešení platným přístupem. Další informace o cenách najdete v tématu [Ceny azure storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Pokyny pro návrh tabulek
Tyto seznamy shrnují některé klíčové pokyny, které byste měli mít na paměti při navrhování tabulek. Tato příručka se zabývá je všechny podrobněji později. Tyto pokyny se liší od pokynů, které byste obvykle dodržovat pro návrh relační databáze.  

Návrh úložiště tabulky pro *efektivní čtení:*

* **Návrh pro dotazování v aplikacích s vysokým i pro čtení.** Při navrhování tabulek přemýšlejte o dotazech (zejména dotazech citlivých na latenci), které spustíte, než začnete přemýšlet o tom, jak budete aktualizovat entity. To obvykle vede k efektivní a výkonné řešení.  
* **Zadejte `PartitionKey` `RowKey` jak a ve svých dotazech.** *Bodové dotazy,* jako jsou tyto, jsou nejúčinnější dotazy úložiště tabulky.  
* **Zvažte uložení duplicitních kopií entit.** Úložiště tabulek je levné, proto zvažte uložení stejné entity vícekrát (s různými klíči), abyste povolili efektivnější dotazy.  
* **Zvažte denormalizaci dat.** Ukládání stolů je levné, proto zvažte denormalizaci dat. Například uložte souhrnné entity tak, aby dotazy na agregovaná data potřebovaly pouze pro přístup k jedné entitě.  
* **Použijte hodnoty složeného klíče.** Jediné klíče, které `PartitionKey` `RowKey`máte, jsou a . Pomocí hodnot složených klíčů můžete například povolit alternativní přístupové cesty s klíčem k entitám.  
* **Použijte projekci dotazu.** Množství dat, která přenášíte po síti, můžete snížit pomocí dotazů, které vyberou pouze pole, která potřebujete.  

Navrhování úložiště tabulky tak, aby bylo efektivní *zápis:*  

* **Nevytvářejte horké oddíly.** Vyberte klíče, které vám umožní rozložit požadavky na více oddílů v libovolném okamžiku.  
* **Vyhněte se špičkám v provozu.** Rozdělte provoz za přiměřené časové období a vyhněte se špičkám v provozu.
* **Nemusí nutně vytvořit samostatnou tabulku pro každý typ entity.** Pokud požadujete atomické transakce napříč typy entit, můžete uložit tyto typy více entit ve stejném oddílu ve stejné tabulce.
* **Zvažte maximální propustnost, které musíte dosáhnout.** Musíte si být vědomi cílů škálovatelnosti pro úložiště tabulky a ujistěte se, že váš návrh nezpůsobí jejich překročení.  

Dále v této příručce uvidíte příklady, které všechny tyto zásady uvedou do praxe.  

## <a name="design-for-querying"></a>Návrh pro dotazování
Úložiště tabulek lze číst náročné, psát intenzivní, nebo kombinaci obou. Tato část se zabývá návrhem pro efektivní podporu operací čtení. Návrh, který efektivně podporuje operace čtení, je obvykle také efektivní pro operace zápisu. Existují však další důležité informace při navrhování pro podporu operací zápisu. Ty jsou popsány v další části [Návrh pro úpravu dat](#design-for-data-modification).

Dobrým výchozím bodem, který vám umožní efektivně číst data, je zeptat se: "Jaké dotazy bude aplikace muset spustit, aby načetla data, která potřebuje?"  

> [!NOTE]
> S úložištěm table je důležité, aby byl návrh správný dopředu, protože později je obtížné a nákladné jej změnit. Například v relační databázi je často možné řešit problémy s výkonem jednoduše přidáním indexů do existující databáze. Toto není možnost s table storage.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Jak si `PartitionKey` vyberete a `RowKey` ovlivní výkon dotazu
Následující příklady předpokládají, že úložiště tabulky ukládá entity zaměstnanců s následující strukturou (většina příkladů vyneche `Timestamp` vlastnost pro přehlednost):  

| Název sloupce | Datový typ |
| --- | --- |
| `PartitionKey`(Název oddělení) |Řetězec |
| `RowKey`(ID zaměstnance) |Řetězec |
| `FirstName` |Řetězec |
| `LastName` |Řetězec |
| `Age` |Integer |
| `EmailAddress` |Řetězec |

Tady jsou některé obecné pokyny pro navrhování dotazů úložiště tabulky. Syntaxe filtru použitá v následujících příkladech pochází z rozhraní REST API úložiště tabulky. Další informace naleznete v tématu [Query entities](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* *Bodový dotaz* je nejúčinnější vyhledávání, které se používá, a doporučuje se pro vyhledávání ve vysokém objemu nebo vyhledávání vyžadující nejnižší latenci. Takový dotaz můžete použít indexy efektivně vyhledat jednotlivé entity `PartitionKey` zadáním `RowKey` a hodnoty. Například: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* Druhým nejlepším je *dotaz na rozsah*. Používá `PartitionKey`a filtry na rozsah `RowKey` hodnot vrátit více než jednu entitu. Hodnota `PartitionKey` identifikuje konkrétní oddíl a `RowKey` hodnoty identifikují podmnožinu entit v tomto oddílu. Například: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* Třetí nejlepší je *skenování oddílů*. Používá `PartitionKey`a filtry na jinou vlastnost bez klíče a může vrátit více než jednu entitu. Hodnota `PartitionKey` identifikuje konkrétní oddíl a hodnoty vlastností vybrat pro podmnožinu entit v tomto oddílu. Například: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* *Prohledávač tabulky* neobsahuje `PartitionKey`a je neefektivní, protože prohledává všechny oddíly, které tvoří tabulku pro všechny odpovídající entity. Provádí prohledávač tabulky bez ohledu na `RowKey`to, zda filtr používá . Například: `$filter=LastName eq 'Jones'`.  
* Azure Table storage dotazy, které vracejí `PartitionKey` více `RowKey` entit je seřadit a pořadí. Chcete-li se vyhnout uchýlení `RowKey` se k entitám v klientovi, zvolte a, která definuje nejběžnější pořadí řazení. Výsledky dotazu vrácené rozhraním Azure Table API v Azure Cosmos DB nejsou seřazeny podle klíče oddílu nebo klíče řádku. Podrobný seznam rozdílů funkcí najdete v tématu [rozdíly mezi rozhraním TABLE API v Azure Cosmos DB a Azure Table storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Použití "**nebo**" k určení `RowKey` filtru na základě hodnot vede k prohledání oddílu a není považováno za dotaz na oblast. Proto se vyhněte dotazům, `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`které používají filtry, například: .  

Příklady kódu na straně klienta, které ke spouštění efektivních dotazů používají klientskou knihovnu úložiště, naleznete v následujících tématech:  

* [Spuštění bodového dotazu pomocí klientské knihovny úložiště](#run-a-point-query-by-using-the-storage-client-library)
* [Načtení více entit pomocí LINQ](#retrieve-multiple-entities-by-using-linq)
* [Projekce na straně serveru](#server-side-projection)  

Příklady kódu na straně klienta, který dokáže zpracovat více typů entit uložených ve stejné tabulce, najdete v tématu:  

* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Zvolte vhodnou možnost`PartitionKey`
Vaše volba `PartitionKey` by měla vyvážit potřebu povolit použití EGTs (pro zajištění konzistence) proti požadavku na distribuci entit mezi více oddílů (pro zajištění škálovatelnéřešení).  

V jednom extrému můžete uložit všechny entity v jednom oddílu. To však může omezit škálovatelnost vašeho řešení a zabrání ukládání tabulek, aby bylo možné požadavky na vyrovnávání zatížení. Na druhé straně extrému můžete uložit jednu entitu na oddíl. To je vysoce škálovatelné a umožňuje table storage požadavky na vyrovnávání zatížení, ale zabraňuje použití transakcí skupiny entit.  

Ideální `PartitionKey` umožňuje používat efektivní dotazy a má dostatek oddílů k zajištění škálovatelnosti vašeho řešení. Obvykle zjistíte, že vaše entity budou mít vhodnou vlastnost, která distribuuje entity přes dostatečné oddíly.

> [!NOTE]
> Například v systému, který ukládá informace `UserID` o uživatelích nebo zaměstnancích, může být dobrý `PartitionKey`. Můžete mít několik entit, `UserID` které používají konkrétní jako klíč oddílu. Každá entita, která ukládá data o uživateli, je seskupena do jednoho oddílu. Tyto entity jsou přístupné prostřednictvím EGTs, zatímco stále jsou vysoce škálovatelné.
> 
> 

Existují další důležité informace ve `PartitionKey` vašem výběru, které se týkají způsobu vkládání, aktualizace a odstraňování entit. Další informace naleznete v [tématu Návrh pro úpravy dat](#design-for-data-modification) dále v tomto článku.  

### <a name="optimize-queries-for-table-storage"></a>Optimalizace dotazů pro úložiště tabulek
Úložiště tabulek automaticky indexuje entity `PartitionKey` `RowKey` pomocí hodnot a v jednom seskupeném indexu. To je důvod, proč bod dotazy jsou nejúčinnější použití. Neexistují však žádné indexy než v seskupeném `PartitionKey` `RowKey`indexu na a .

Mnoho návrhů musí splňovat požadavky, aby bylo možné vyhledat entity na základě více kritérií. Například vyhledání entit zaměstnanců na základě e-mailu, ID zaměstnance nebo příjmení. Následující vzory v části [Tabulka návrhových vzorů](#table-design-patterns) řešit tyto typy požadavků. Vzory také popisují způsoby práce o tom, že table storage neposkytuje sekundární indexy.  

* [Vzor sekundárního indexu uvnitř oddílu](#intra-partition-secondary-index-pattern): Uložte `RowKey` více kopií každé entity pomocí různých hodnot (ve stejném oddílu). To umožňuje rychlé a efektivní vyhledávání a alternativní `RowKey` řazení pomocí různých hodnot.  
* [Vzor sekundárního indexu mezi oddíly](#inter-partition-secondary-index-pattern): Uložte `RowKey` více kopií každé entity pomocí různých hodnot v samostatných oddílech nebo v samostatných tabulkách. To umožňuje rychlé a efektivní vyhledávání a alternativní `RowKey` řazení pomocí různých hodnot.  
* [Vzor entit indexu](#index-entities-pattern): Udržovat entity indexu, aby bylo možné efektivně vyhledávat, které vracejí seznamy entit.  

### <a name="sort-data-in-table-storage"></a>Řazení dat v úložišti tabulek

Úložiště tabulek vrací výsledky dotazu seřazené vzestupně podle `PartitionKey` písmene a) a potom podle `RowKey`.

> [!NOTE]
> Výsledky dotazu vrácené rozhraním Azure Table API v Azure Cosmos DB nejsou seřazeny podle klíče oddílu nebo klíče řádku. Podrobný seznam rozdílů funkcí najdete v tématu [rozdíly mezi rozhraním TABLE API v Azure Cosmos DB a Azure Table storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Klíče v úložišti tabulky jsou řetězcové hodnoty. Chcete-li zajistit správné řazení číselných hodnot, měli byste je převést na pevnou délku a vyřadit je nulami. Pokud `RowKey` je například hodnota ID zaměstnance, kterou používáte jako hodnotu celého čísla, měli byste převést ID zaměstnance **123** na **00000123**. 

Mnoho aplikací má požadavky na použití dat seřazených v různých objednávkách: například řazení zaměstnanců podle jména nebo podle data spojení. Následující vzory v části [Návrhové vzory tabulky](#table-design-patterns) se zabývají tím, jak střídat pořadí řazení pro entity:  

* [Vzor sekundárního indexu uvnitř oddílu](#intra-partition-secondary-index-pattern): Uložte `RowKey` více kopií každé entity pomocí různých hodnot (ve stejném oddílu). To umožňuje rychlé a efektivní vyhledávání a alternativní `RowKey` řazení pomocí různých hodnot.  
* [Vzor sekundárního indexu mezi oddíly](#inter-partition-secondary-index-pattern): Uložte `RowKey` více kopií každé entity pomocí různých hodnot v samostatných oddílech v samostatných tabulkách. To umožňuje rychlé a efektivní vyhledávání a alternativní `RowKey` řazení pomocí různých hodnot.
* [Vzor ocasu protokolu](#log-tail-pattern): Načíst *n* entity, které `RowKey` byly naposledy přidány do oddílu, pomocí hodnoty, která seřadí v obráceném pořadí data a času.  

## <a name="design-for-data-modification"></a>Návrh pro úpravu dat
Tato část se zaměřuje na aspekty návrhu pro optimalizaci vložení, aktualizace a odstranění. V některých případech budete muset vyhodnotit kompromis mezi návrhy, které optimalizují pro dotazování proti návrhům, které optimalizují pro úpravy dat. Toto hodnocení je podobné tomu, co děláte v návrzích pro relační databáze (i když techniky pro správu kompromisů návrhu se liší v relační databázi). V části [Návrhové vzory tabulky](#table-design-patterns) popisují některé podrobné návrhové vzory pro úložiště tabulek a zvýrazňují některé z těchto kompromisů. V praxi zjistíte, že mnoho návrhů optimalizovaných pro dotazování entit také funguje dobře pro úpravy entit.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optimalizace výkonu operací vložení, aktualizace a odstranění
Chcete-li entitu aktualizovat nebo odstranit, musíte `PartitionKey` být `RowKey` schopni ji identifikovat pomocí hodnoty a. V tomto ohledu by `PartitionKey` `RowKey` vaše volba a pro úpravu entit by měla dodržovat podobná kritéria jako vaše volba pro podporu dotazů bodů. Chcete identifikovat entity co nejefektivněji. Nechcete použít neefektivní oddíl nebo prohledávač tabulky k vyhledání entity za účelem zjištění hodnot `PartitionKey` a `RowKey` hodnot, které potřebujete k aktualizaci nebo odstranění.  

Následující vzory v části [Návrhové vzory tabulky](#table-design-patterns) označují optimalizaci výkonu operací vložení, aktualizace a odstranění:  

* [Vzor odstranění velkého objemu](#high-volume-delete-pattern): Povolte odstranění velkého objemu entit uložením všech entit pro současné odstranění ve vlastní samostatné tabulce. Entity odstraníte odstraněním tabulky.  
* [Vzor datových řad](#data-series-pattern): Uložte kompletní datové řady do jedné entity, abyste minimalizovali počet požadavků, které provedete.  
* [Vzor širokých entit](#wide-entities-pattern): K ukládání logických entit s více než 252 vlastnostmi použijte více fyzických entit.  
* [Vzor velkých entit:](#large-entities-pattern)K ukládání velkých hodnot vlastností použijte úložiště objektů blob.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Zajištění konzistence uložených entit
Dalším klíčovým faktorem, který ovlivňuje výběr klíčů pro optimalizaci změn dat, je, jak zajistit konzistenci pomocí atomických transakcí. EGT lze použít pouze k provozu s entitami uloženými ve stejném oddílu.  

Následující vzory v části [Návrhové vzory tabulky](#table-design-patterns) řeší správu konzistence:  

* [Vzor sekundárního indexu uvnitř oddílu](#intra-partition-secondary-index-pattern): Uložte `RowKey` více kopií každé entity pomocí různých hodnot (ve stejném oddílu). To umožňuje rychlé a efektivní vyhledávání a alternativní `RowKey` řazení pomocí různých hodnot.  
* [Vzor sekundárního indexu mezi oddíly](#inter-partition-secondary-index-pattern): Uložte `RowKey` více kopií každé entity pomocí různých hodnot v samostatných oddílech nebo v samostatných tabulkách. To umožňuje rychlé a efektivní vyhledávání a alternativní `RowKey` řazení pomocí různých hodnot.  
* [Nakonec konzistentní transakce vzor](#eventually-consistent-transactions-pattern): Povolit nakonec konzistentní chování přes hranice oddílu nebo hranice systému úložiště pomocí front Azure.
* [Vzor entit indexu](#index-entities-pattern): Udržovat entity indexu, aby bylo možné efektivně vyhledávat, které vracejí seznamy entit.  
* [Vzor denormalizace](#denormalization-pattern): Zkombinujte související data do jedné entity, abyste mohli načíst všechna data, která potřebujete, pomocí dotazu s jedním bodem.  
* [Vzor datových řad](#data-series-pattern): Uložte kompletní datové řady do jedné entity, abyste minimalizovali počet požadavků, které provedete.  

Další informace naleznete v [tématu transakce skupiny entit](#entity-group-transactions) dále v tomto článku.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Ujistěte se, že váš návrh pro efektivní úpravy usnadňuje efektivní dotazy
V mnoha případech návrh pro efektivní dotazování výsledky efektivní změny, ale vždy byste měli vyhodnotit, zda se jedná o tento případ pro konkrétní scénář. Některé vzory v části [Tabulka návrhových vzorů](#table-design-patterns) explicitně vyhodnocují kompromisy mezi dotazování mandatony a úpravy entit a vždy byste měli vzít v úvahu počet jednotlivých typů operací.  

Následující vzory v části [Návrhové vzory tabulky](#table-design-patterns) řeší kompromisy mezi návrhem efektivních dotazů a návrhem pro efektivní úpravy dat:  

* [Vzor složeného](#compound-key-pattern)klíče `RowKey` : Pomocí složených hodnot umožníte klientovi vyhledávat související data pomocí dotazu s jedním bodem.  
* [Vzor ocasu protokolu](#log-tail-pattern): Načíst *n* entity, které `RowKey` byly naposledy přidány do oddílu, pomocí hodnoty, která seřadí v obráceném pořadí data a času.  

## <a name="encrypt-table-data"></a>Šifrování dat tabulky
Klientská knihovna .NET Azure Storage podporuje šifrování vlastností entit řetězce pro operace vložení a nahrazení. Šifrované řetězce jsou uloženy ve službě jako binární vlastnosti a jsou převedeny zpět na řetězce po dešifrování.    

U tabulek musí uživatelé kromě zásad šifrování zadat vlastnosti, které mají být šifrovány. Buď zadejte `EncryptProperty` atribut (pro entity POCO, které jsou odvozeny z `TableEntity`) nebo zadejte překládání šifrování v možnostech požadavku. Překládání šifrování je delegát, který přebírá klíč oddílu, klíč řádku a název vlastnosti a vrátí logickou hodnotu, která označuje, zda má být tato vlastnost zašifrována. Během šifrování klientská knihovna používá tyto informace k rozhodnutí, zda má být vlastnost šifrována při zápisu do drátu. Delegát také poskytuje možnost logiky kolem jak jsou zašifrovány vlastnosti. (Například pokud X, pak šifrovat vlastnost A; jinak šifrovat vlastnosti A a B.) Není nutné poskytovat tyto informace při čtení nebo dotazování entit.

Sloučení není aktuálně podporováno. Vzhledem k tomu, že podmnožina vlastností mohla být dříve zašifrována pomocí jiného klíče, jednoduše slučování nových vlastností a aktualizace metadat bude mít za následek ztrátu dat. Sloučení buď vyžaduje provedení volání další služby ke čtení již existující entity ze služby nebo pomocí nového klíče na vlastnost. Ani jeden z nich není vhodný z důvodů výkonu.     

Informace o šifrování dat tabulky najdete v [tématu Šifrování na straně klienta a Azure Key Vault pro úložiště Microsoft Azure](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Vztahy modelu
Vytváření doménových modelů je klíčovým krokem při návrhu složitých systémů. Proces modelování se obvykle používá k identifikaci entit a vztahů mezi nimi jako způsob, jak porozumět obchodní doméně a informovat o návrhu systému. Tato část se zaměřuje na to, jak můžete přeložit některé běžné typy vztahů, které se nacházejí v modelech domény, do návrhů pro úložiště tabulek. Proces mapování z logického datového modelu na fyzický datový model založený na NoSQL se liší od procesu používaného při navrhování relační databáze. Návrh relačních databází obvykle předpokládá proces normalizace dat optimalizovaný pro minimalizaci redundance. Takový návrh také předpokládá deklarativní dotazování schopnost, která abstrahuje provádění fungování databáze.  

### <a name="one-to-many-relationships"></a>Vztahy 1:N
Vztahy 1:N mezi objekty obchodní domény se vyskytují často: například jedno oddělení má mnoho zaměstnanců. Existuje několik způsobů, jak implementovat relace 1:N v úložišti tabulky, každý s klady a zápory, které mohou být relevantní pro konkrétní scénář.  

Vezměme si příklad velké nadnárodní korporace s desítkami tisíc oddělení a zaměstnaneckých subjektů. Každé oddělení má mnoho zaměstnanců a každý zaměstnanec je spojen s jedním konkrétním oddělením. Jedním z přístupů je ukládání samostatných oddělení a zaměstnaneckých entit, například následující:  

![Obrázek znázorňující entitu oddělení a entitu zaměstnance][1]

Tento příklad ukazuje implicitní vztah 1:N mezi typy `PartitionKey` na základě hodnoty. Každé oddělení může mít mnoho zaměstnanců.  

Tento příklad také zobrazuje entitu oddělení a její související entity zaměstnanců ve stejném oddílu. Můžete použít různé oddíly, tabulky nebo dokonce účty úložiště pro různé typy entit.  

Alternativním přístupem je denormalizovat data a ukládat pouze entity zaměstnanců s nenormalizovanými daty oddělení, jak je znázorněno v následujícím příkladu. V tomto konkrétním scénáři tento nenormalizovaný přístup nemusí být nejlepší, pokud máte požadavek, aby bylo možné změnit podrobnosti vedoucího oddělení. Chcete-li to provést, budete muset aktualizovat každého zaměstnance v oddělení.  

![Grafika zaměstnaneckého subjektu][2]

Další informace naleznete v [tématu Denormalization vzor](#denormalization-pattern) dále v této příručce.  

Následující tabulka shrnuje výhody a nevýhody každého přístupu k ukládání entit zaměstnanců a oddělení, které mají vztah 1:N. Měli byste také zvážit, jak často očekáváte provádět různé operace. Může být přijatelné mít návrh, který zahrnuje nákladné operace, pokud tato operace probíhá pouze zřídka.  

<table>
<tr>
<th>Přístup</th>
<th>Výhody</th>
<th>Nevýhody</th>
</tr>
<tr>
<td>Samostatné typy entit, stejný oddíl, stejná tabulka</td>
<td>
<ul>
<li>Entitu oddělení můžete aktualizovat pomocí jedné operace.</li>
<li>EGT můžete použít k udržení konzistence, pokud máte požadavek na úpravu entity oddělení při každé aktualizaci/vložení/odstranění entity zaměstnance. Pokud například udržujete počet zaměstnanců oddělení pro každé oddělení.</li>
</ul>
</td>
<td>
<ul>
<li>Pro některé aktivity klientů může být nutné načíst zaměstnance i entitu oddělení.</li>
<li>Operace úložiště dojít ve stejném oddílu. Při vysokých objemech transakcí to může mít za následek hotspot.</li>
<li>Zaměstnance nelze přesunout do nového oddělení pomocí EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Samostatné typy entit, různé oddíly nebo tabulky nebo účty úložiště</td>
<td>
<ul>
<li>Entitu oddělení nebo entitu zaměstnance můžete aktualizovat pomocí jedné operace.</li>
<li>Při vysokých objemech transakcí to může pomoci rozložit zatížení na více oddílů.</li>
</ul>
</td>
<td>
<ul>
<li>Pro některé aktivity klientů může být nutné načíst zaměstnance i entitu oddělení.</li>
<li>Egts nelze použít k udržení konzistence při aktualizaci/ vložení/odstranění zaměstnance a aktualizaci oddělení. Například aktualizace počtu zaměstnanců v entitě oddělení.</li>
<li>Zaměstnance nelze přesunout do nového oddělení pomocí EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalizovat do typu jedné entity</td>
<td>
<ul>
<li>Pomocí jediného požadavku můžete načíst všechny potřebné informace.</li>
</ul>
</td>
<td>
<ul>
<li>Zachování konzistence může být nákladné, pokud potřebujete aktualizovat informace o oddělení (to by vyžadovalo aktualizaci všech zaměstnanců v oddělení).</li>
</ul>
</td>
</tr>
</table>

Způsob výběru mezi těmito možnostmi a které výhody a nevýhody jsou nejvýznamnější, závisí na konkrétních scénářích aplikace. Jak často například upravujete entity oddělení? Potřebují všechny dotazy zaměstnanců další informace o oddělení? Jak blízko jste k omezení škálovatelnosti na oddíly nebo váš účet úložiště?  

### <a name="one-to-one-relationships"></a>Vztahy 1:1
Modely domény mohou zahrnovat vztahy 1:1 mezi entitami. Pokud potřebujete implementovat relaci 1:1 v úložišti tabulky, musíte také zvolit, jak propojit dvě související entity, když je potřebujete načíst oba. Toto propojení může být implicitní, založené na konvenci v klíčových hodnotách, nebo explicitní uložením propojení ve formě `PartitionKey` a `RowKey` hodnot v každé entitě do související entity. Diskuse o tom, zda byste měli uložit související entity ve stejném oddílu, naleznete v části [Relace 1:N](#one-to-many-relationships).  

Existují také důležité informace o implementaci, které by mohly vést k implementaci relací 1:1 v úložišti tabulky:  

* Zpracování velkých entit (další informace naleznete v tématu [Vzor velkých entit).](#large-entities-pattern)  
* Implementace řízení přístupu (další informace naleznete v tématu [Řízení přístupu pomocí sdílených přístupových podpisů).](#control-access-with-shared-access-signatures)  

### <a name="join-in-the-client"></a>Připojte se ke klientovi
Přestože existují způsoby, jak modelovat vztahy v úložišti tabulky, nezapomeňte, že dva hlavní důvody pro použití úložiště tabulky jsou škálovatelnost a výkon. Pokud zjistíte, že modelujete mnoho relací, které ohrožují výkon a škálovatelnost vašeho řešení, měli byste se zeptat sami sebe, zda je nutné vytvořit všechny datové relace do návrhu tabulky. Je možné zjednodušit návrh a zlepšit škálovatelnost a výkon vašeho řešení, pokud umožníte klientské aplikaci provádět všechna potřebná spojení.  

Máte-li například malé tabulky obsahující data, která se často nemění, můžete tato data načíst jednou a uložit je do mezipaměti v klientovi. To může zabránit opakované zpáteční lety načíst stejná data. V příkladech, které jsme se podívali v této příručce, je pravděpodobné, že sada oddělení v malé organizaci bude malá a bude se měnit zřídka. Díky tomu je dobrým kandidátem pro data, která může klientská aplikace stáhnout jednou a ukládat do mezipaměti jako vyhledávací data.  

### <a name="inheritance-relationships"></a>Vztahy dědičnosti
Pokud vaše klientská aplikace používá sadu tříd, které jsou součástí vztahu dědičnosti k reprezentaci obchodních entit, můžete tyto entity snadno zachovat v úložišti table. Můžete mít například následující sadu tříd definovaných v `Person` klientské aplikaci, kde je abstraktní třída.

![Diagram vztahů dědičnosti][3]

Můžete zachovat instance dvou konkrétních tříd v table `Person` storage pomocí jedné tabulky. Použijte entity, které vypadají takto:  

![Obrázek znázorňující entitu a entitu zaměstnance odběratele][4]

Další informace o práci s více typy entit ve stejné tabulce v kódu klienta naleznete v [tématu Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types) dále v této příručce. To poskytuje příklady, jak rozpoznat typ entity v kódu klienta.  

## <a name="table-design-patterns"></a>Způsoby návrhu tabulek
V předchozích částech jste se dozvěděli o tom, jak optimalizovat návrh tabulky pro načítání dat entity pomocí dotazů a pro vkládání, aktualizaci a odstranění dat entity. Tato část popisuje některé vzory vhodné pro použití s table storage. Kromě toho uvidíte, jak můžete prakticky řešit některé problémy a kompromisy vznesené dříve v této příručce. Následující diagram shrnuje vztahy mezi různými vzory:  

![Diagram vzorů návrhu tabulky][5]

Mapa vzorů zvýrazní některé vztahy mezi vzory (modrá) a anti-patterns (oranžová), které jsou popsány v této příručce. Existuje samozřejmě mnoho dalších vzorů, které stojí za zvážení. Jedním z klíčových scénářů pro úložiště tabulky je například použití [vzoru materializovaného zobrazení](https://msdn.microsoft.com/library/azure/dn589782.aspx) ze vzoru [oddělení odpovědnosti příkazového dotazu.](https://msdn.microsoft.com/library/azure/jj554200.aspx)  

### <a name="intra-partition-secondary-index-pattern"></a>Vzor sekundárního indexu uvnitř oddílu
Uložte více kopií každé `RowKey` entity pomocí různých hodnot (ve stejném oddílu). To umožňuje rychlé a efektivní vyhledávání a alternativní `RowKey` řazení pomocí různých hodnot. Aktualizace mezi kopiemi mohou být konzistentní pomocí EGTs.  

#### <a name="context-and-problem"></a>Kontext a problém
Úložiště tabulek automaticky indexuje `PartitionKey` entity `RowKey` pomocí hodnoty a. To umožňuje klientské aplikaci efektivně načíst entitu pomocí těchto hodnot. Například pomocí následující struktury tabulky může klientská aplikace použít bodový dotaz k načtení jednotlivé entity zaměstnance `PartitionKey` pomocí `RowKey` názvu oddělení a ID zaměstnance (the a values). Klient může také načíst entity seřazené podle ID zaměstnance v rámci každého oddělení.

![Grafika zaměstnaneckého subjektu][6]

Pokud chcete také najít entitu zaměstnance na základě hodnoty jiné vlastnosti, jako je například e-mailová adresa, musíte k nalezení shody použít méně efektivní prohledávač oddílů. Důvodem je, že úložiště tabulky neposkytuje sekundární indexy. Kromě toho neexistuje možnost požádat o seznam zaměstnanců seřazených v jiném pořadí než `RowKey` pořadí.  

#### <a name="solution"></a>Řešení
Chcete-li obejít nedostatek sekundárních indexů, můžete uložit více kopií každé `RowKey` entity, přičemž každá kopie bude používat jinou hodnotu. Pokud ukládáte entitu s následujícími strukturami, můžete efektivně načíst entity zaměstnanců na základě e-mailové adresy nebo ID zaměstnance. Hodnoty předpony `RowKey`pro `empid_`aplikaci , a `email_` umožňují dotaz na jednoho zaměstnance nebo rozsah zaměstnanců pomocí řady e-mailových adres nebo ID zaměstnanců.  

![Obrázek znázorňující entitu zaměstnance s různými hodnotami RowKey][7]

Následující dvě kritéria filtru (jedno vyhledává podle ID zaměstnance a jedno podle e-mailové adresy) určují bodové dotazy:  

* $filter=(PartitionKey eq 'Prodej') a (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Prodej') a (RowKey eq 'email_jonesj@contoso.com')  

Pokud se dotazujete na řadu entit zaměstnanců, můžete zadat rozsah seřazený v objednávce ID zaměstnance nebo rozsah seřazený v pořadí e-mailových adres. Dotaz na entity s příslušnou předponou v . `RowKey`  

* Chcete-li najít všechny zaměstnance v oddělení prodeje s ID zaměstnance v rozsahu 000100 až 000199, použijte: $filter=(PartitionKey eq 'Sales') a (RowKey ge 'empid_000100') a (RowKey le 'empid_000199')  
* Chcete-li najít všechny zaměstnance v oddělení prodeje s e-mailovou adresou začínající písmenem "a", použijte: $filter=(PartitionKey eq 'Sales') a (RowKey ge 'email_a') a (RowKey lt 'email_b')  
  
Syntaxe filtru použitá v předchozích příkladech pochází z rozhraní REST API úložiště tabulky. Další informace naleznete v tématu [Query entities](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Úložiště tabulky je relativně levné, takže náklady na ukládání duplicitních dat by neměly být velkým problémem. Vždy byste však měli vyhodnotit náklady na návrh na základě očekávaných požadavků na úložiště a přidat pouze duplicitní entity pro podporu dotazů, které bude klientská aplikace spouštět.  
* Vzhledem k tomu, že sekundární index entity jsou uloženy ve stejném oddílu jako původní entity, ujistěte se, že nepřekročíte cíle škálovatelnosti pro jednotlivé oddíly.  
* Duplicitní entity můžete udržovat konzistentní mezi sebou pomocí EGTs aktualizovat dvě kopie entity atomicky. To znamená, že byste měli uložit všechny kopie entity ve stejném oddílu. Další informace naleznete v tématu [Use entity group transactions](#entity-group-transactions).  
* Hodnota použitá `RowKey` pro musí být jedinečná pro každou entitu. Zvažte použití hodnot složených klíčů.  
* Odsazení `RowKey` číselných hodnot v (například ID zaměstnance 000223) umožňuje správné řazení a filtrování na základě horních a dolních hranic.  
* Nemusíte nutně duplikovat všechny vlastnosti entity. Například pokud dotazy, které vyhledává entity pomocí e-mailové adresy v `RowKey` nikdy potřebovat věk zaměstnance, tyto entity mohou mít následující strukturu:

  ![Grafika zaměstnaneckého subjektu][8]

* Obvykle je lepší ukládat duplicitní data a ujistěte se, že můžete načíst všechna data, která potřebujete s jedním dotazem, než použít jeden dotaz k vyhledání entity a jiný vyhledat požadovaná data.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v těchto případech:

- Klientská aplikace potřebuje načíst entity pomocí různých klíčů.
- Váš klient potřebuje načíst entity v různých pořadí řazení.
- Každou entitu můžete identifikovat pomocí různých jedinečných hodnot.

Ujistěte se však, že nepřekročíte limity škálovatelnosti oddílu při vyhledávání `RowKey` entit pomocí různých hodnot.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor sekundárního indexu mezi oddíly](#inter-partition-secondary-index-pattern)
* [Vzor složeného klíče](#compound-key-pattern)
* [Transakce skupiny entit](#entity-group-transactions)
* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Vzor sekundárního indexu mezi oddíly
Uložte více kopií každé `RowKey` entity pomocí různých hodnot v samostatných oddílech nebo v samostatných tabulkách. To umožňuje rychlé a efektivní vyhledávání a alternativní `RowKey` řazení pomocí různých hodnot.  

#### <a name="context-and-problem"></a>Kontext a problém
Úložiště tabulek automaticky indexuje `PartitionKey` entity `RowKey` pomocí hodnoty a. To umožňuje klientské aplikaci efektivně načíst entitu pomocí těchto hodnot. Například pomocí následující struktury tabulky může klientská aplikace použít bodový dotaz k načtení jednotlivé entity zaměstnance `PartitionKey` pomocí `RowKey` názvu oddělení a ID zaměstnance (the a values). Klient může také načíst entity seřazené podle ID zaměstnance v rámci každého oddělení.  

![Grafika zaměstnaneckého subjektu][9]

Pokud také chcete mít možnost najít entitu zaměstnance na základě hodnoty jiné vlastnosti, jako je například e-mailová adresa, musíte použít méně efektivní prohledávač oddílů k nalezení shody. Důvodem je, že úložiště tabulky neposkytuje sekundární indexy. Kromě toho neexistuje možnost požádat o seznam zaměstnanců seřazených v jiném pořadí než `RowKey` pořadí.  

Očekáváte vysoký objem transakcí proti těmto entitám a chcete minimalizovat riziko rychlosti úložiště tabulka omezení vašeho klienta.  

#### <a name="solution"></a>Řešení
Chcete-li obejít nedostatek sekundárních indexů, můžete uložit více kopií `PartitionKey` každé `RowKey` entity, přičemž každá kopie bude používat různé a hodnoty. Pokud ukládáte entitu s následujícími strukturami, můžete efektivně načíst entity zaměstnanců na základě e-mailové adresy nebo ID zaměstnance. Hodnoty předpony `PartitionKey`pro `empid_`aplikaci , a `email_` umožňují určit, který index chcete pro dotaz použít.  

![Obrázek znázorňující entitu zaměstnance s primárním indexem a entitou zaměstnance se sekundárním indexem][10]

Následující dvě kritéria filtru (jedno vyhledává podle ID zaměstnance a jedno podle e-mailové adresy) určují bodové dotazy:  

* $filter=(PartitionKey eq 'empid_Sales') a (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') a (RowKey eq 'jonesj@contoso.com')  

Pokud se dotazujete na řadu entit zaměstnanců, můžete zadat rozsah seřazený v objednávce ID zaměstnance nebo rozsah seřazený v pořadí e-mailových adres. Dotaz na entity s příslušnou předponou v . `RowKey`  

* Chcete-li vyhledat všechny zaměstnance v obchodním oddělení s ID zaměstnance v rozsahu **000100** až **000199**, seřazeno v objednávce ID zaměstnance, použijte: $filter=(PartitionKey eq 'empid_Sales') a (RowKey ge '000100') a (RowKey le '000199')  
* Chcete-li najít všechny zaměstnance v oddělení prodeje s e-mailovou adresou začínající na "a", seřazenou v pořadí e-mailových adres, použijte: $filter=(PartitionKey eq 'email_Sales') a (RowKey ge 'a') a (RowKey lt 'b')  

Všimněte si, že syntaxe filtru použitá v předchozích příkladech pochází z rozhraní REST API úložiště tabulky. Další informace naleznete v tématu [Query entities](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Duplicitní entity můžete zachovat v konečném souladu s použitím [nakonec konzistentní transakce vzor](#eventually-consistent-transactions-pattern) udržovat primární a sekundární index entity.  
* Úložiště tabulky je relativně levné, takže náklady na ukládání duplicitních dat by neměly být velkým problémem. Vždy však vyhodnoťte náklady na návrh na základě očekávaných požadavků na úložiště a přidejte pouze duplicitní entity pro podporu dotazů, které bude klientská aplikace spouštět.  
* Hodnota použitá `RowKey` pro musí být jedinečná pro každou entitu. Zvažte použití hodnot složených klíčů.  
* Odsazení `RowKey` číselných hodnot v (například ID zaměstnance 000223) umožňuje správné řazení a filtrování na základě horních a dolních hranic.  
* Nemusíte nutně duplikovat všechny vlastnosti entity. Například pokud dotazy, které vyhledává entity pomocí e-mailové adresy v `RowKey` nikdy potřebovat věk zaměstnance, tyto entity mohou mít následující strukturu:
  
  ![Obrázek znázorňující entitu zaměstnance se sekundárním indexem][11]
* Obvykle je lepší ukládat duplicitní data a ujistěte se, že můžete načíst všechna data, která potřebujete s jedním dotazem, než použít jeden dotaz k vyhledání entity pomocí sekundárního indexu a další vyhledat požadovaná data v primárním indexu.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v těchto případech:

- Klientská aplikace potřebuje načíst entity pomocí různých klíčů.
- Váš klient potřebuje načíst entity v různých pořadí řazení.
- Každou entitu můžete identifikovat pomocí různých jedinečných hodnot.

Tento vzor použijte, pokud chcete vyhnout překročení limitů škálovatelnosti oddílu při `RowKey` provádění vyhledávání entit pomocí různých hodnot.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Nakonec konzistentní transakce vzor](#eventually-consistent-transactions-pattern)  
* [Vzor sekundárního indexu uvnitř oddílu](#intra-partition-secondary-index-pattern)  
* [Vzor složeného klíče](#compound-key-pattern)  
* [Transakce skupiny entit](#entity-group-transactions)  
* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Nakonec konzistentní transakce vzor
Povolte nakonec konzistentní chování přes hranice oddílu nebo hranice systému úložiště pomocí front Azure.  

#### <a name="context-and-problem"></a>Kontext a problém
EGTs povolit atomické transakce napříč více entit, které sdílejí stejný klíč oddílu. Z důvodů výkonu a škálovatelnosti se můžete rozhodnout ukládat entity, které mají požadavky na konzistenci v samostatných oddílech nebo v samostatném systému úložiště. V takovém scénáři nelze použít EGTs udržovat konzistenci. Můžete mít například požadavek zachovat konečnou konzistenci mezi:  

* Entity uložené ve dvou různých oddílech ve stejné tabulce, v různých tabulkách nebo v různých účtech úložiště.  
* Entita uložená v úložišti table a objekt blob uložený v úložišti objektů Blob.  
* Entita uložená v úložišti tabulky a soubor v systému souborů.  
* Entita uložená v úložišti table, ale indexovaná pomocí Azure Cognitive Search.  

#### <a name="solution"></a>Řešení
Pomocí front Azure můžete implementovat řešení, které poskytuje konečnou konzistenci mezi dvěma nebo více oddíly nebo systémy úložiště.

Pro ilustraci tohoto přístupu předpokládejme, že máte požadavek, abyste mohli archivovat dřívější entity zaměstnanců. Dřívější entity zaměstnanců jsou zřídka dotazovány a měly by být vyloučeny z aktivit, které se zabývají aktuálními zaměstnanci. Chcete-li tento požadavek implementovat, uložíte aktivní zaměstnance do tabulky **Aktuální** a bývalí zaměstnanci v tabulce **Archiv.** Archivace zaměstnance vyžaduje odstranění entity z **aktuální** tabulky a přidání entity do tabulky **Archiv.**

Ale nelze použít EGT k provedení těchto dvou operací. Aby se zabránilo riziku, že selhání způsobí, že se entita zobrazí v obou nebo ani v jedné tabulkách, musí být operace archivace nakonec konzistentní. Následující sekvenční diagram popisuje kroky v této operaci.  

![Diagram řešení pro konečnou konzistenci][12]

Klient zahájí operaci archivace umístěním zprávy do fronty Azure (v tomto příkladu archivovat zaměstnance #456). Role pracovního procesu dotazování fronty pro nové zprávy; když najde jeden, přečte zprávu a ponechá skrytou kopii ve frontě. Role pracovníka dále načte kopii entity z **aktuální** tabulky, vloží kopii do tabulky **Archiv** a potom odstraní originál z **aktuální** tabulky. Nakonec pokud nebyly žádné chyby z předchozích kroků, role pracovního procesu odstraní skrytou zprávu z fronty.  

V tomto příkladu krok 4 v diagramu vloží zaměstnance do tabulky **Archiv.** Můžete přidat zaměstnance do objektu blob v úložišti objektů Blob nebo souborv systému souborů.  

#### <a name="recover-from-failures"></a>Zotavit se z poruch
Je důležité, aby operace v krocích 4-5 v diagramu *idempotentní* v případě, že role pracovníka potřebuje restartovat operaci archivace. Pokud používáte table storage, pro krok 4 byste měli použít operaci "vložit nebo nahradit"; pro krok 5 byste měli použít operaci "odstranit, pokud existuje" v klientské knihovně, kterou používáte. Pokud používáte jiný úložný systém, musíte použít příslušnou idempotentní operaci.  

Pokud role pracovního procesu nikdy nedokončí krok 6 v diagramu, pak po časovém výpadku se zpráva znovu zobrazí ve frontě připravená pro roli pracovního procesu, aby se pokusila znovu zpracovat. Role pracovního procesu můžete zkontrolovat, kolikrát byla přečtena zpráva ve frontě a v případě potřeby označit jako "poškozená" zpráva pro šetření odesláním do samostatné fronty. Další informace o čtení zpráv fronty a kontrole počtu vyřazení z fronty naleznete v tématu [Získání zpráv](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Některé chyby z úložiště tabulek a fronty úložiště jsou přechodné chyby a klientská aplikace by měla obsahovat vhodné logiky opakování pro jejich zpracování.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení neposkytuje izolaci transakcí. Klient může například číst **aktuální** a **archivní** tabulky, když role pracovního procesu byla mezi kroky 4-5 v diagramu a zobrazit nekonzistentní zobrazení dat. Data budou nakonec konzistentní.  
* Musíte si být jisti, že kroky 4-5 jsou idempotentní, aby byla zajištěna konečná konzistence.  
* Řešení můžete škálovat pomocí více front a instancí role pracovního procesu.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud chcete zaručit konečnou konzistenci mezi entitami, které existují v různých oddílech nebo tabulkách. Tento vzor můžete rozšířit, abyste zajistili konečnou konzistenci pro operace mezi úložištěm table a blob a dalšími zdroji dat než Azure Storage, jako je databáze nebo systém souborů.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entit](#entity-group-transactions)  
* [Sloučit nebo nahradit](#merge-or-replace)  

> [!NOTE]
> Pokud je izolace transakcí pro vaše řešení důležitá, zvažte přepracovat tabulky tak, aby umožňovaly používat EGT.  
> 
> 

### <a name="index-entities-pattern"></a>Vzor entity indexu
Udržovat entity indexu povolit efektivní vyhledávání, které vracejí seznamy entit.  

#### <a name="context-and-problem"></a>Kontext a problém
Úložiště tabulek automaticky indexuje `PartitionKey` entity `RowKey` pomocí hodnoty a. To umožňuje klientské aplikaci efektivně načíst entitu pomocí bodového dotazu. Například pomocí následující struktury tabulky může klientská aplikace efektivně načíst jednotlivou entitu zaměstnance `PartitionKey` pomocí `RowKey`názvu oddělení a ID zaměstnance (a).  

![Grafika zaměstnaneckého subjektu][13]

Pokud také chcete mít možnost načíst seznam entit zaměstnanců na základě hodnoty jiné nejedinečné vlastnosti, například příjmení, je nutné použít méně efektivní prohledávač oddílů. Tato prohledávací je vyhledá shoda, nikoli pomocí indexu k jejich přímému vyhledávání. Důvodem je, že úložiště tabulky neposkytuje sekundární indexy.  

#### <a name="solution"></a>Řešení
Chcete-li povolit vyhledávání podle příjmení s předchozí strukturou entity, je nutné udržovat seznamy ID zaměstnanců. Pokud chcete načíst entity zaměstnance s určitým příjmením, například Jones, musíte nejprve vyhledat seznam ID zaměstnanců pro zaměstnance s Jonesem jako jejich příjmením a potom tyto entity zaměstnance načíst. Existují tři hlavní možnosti pro uložení seznamů ID zaměstnanců:  

* Použití úložiště objektů blob.  
* Vytvořte entity indexu ve stejném oddílu jako entity zaměstnanců.  
* Vytvořte entity indexu v samostatném oddílu nebo tabulce.  

Možnost 1: Použití úložiště objektů Blob  

Vytvořte objekt blob pro každé jedinečné příjmení a v `PartitionKey` každém úložišti `RowKey` objektů blob seznam hodnot (oddělení) a (ID zaměstnance) pro zaměstnance, kteří mají toto příjmení. Když přidáte nebo odstraníte zaměstnance, ujistěte se, že obsah příslušného objektu blob je nakonec konzistentní s entitami zaměstnance.  

Možnost 2: Vytvoření entit indexu ve stejném oddílu  

Použijte entity indexu, které ukládají následující data:  

![Obrázek znázorňující entitu zaměstnance s řetězcem obsahujícím seznam ID zaměstnanců se stejným příjmením][14]

Vlastnost `EmployeeIDs` obsahuje seznam ID zaměstnanců pro zaměstnance s příjmením `RowKey`uloženým v .  

Následující kroky popisují proces, který byste měli dodržovat při přidávání nového zaměstnance. V tomto příkladu přidáváme zaměstnance s ID 000152 a příjmením Jones v oddělení prodeje:  

1. Načíst entitu `PartitionKey` indexu s hodnotou `RowKey` "Prodej" a hodnotou "Jones". Uložte eTag této entity pro použití v kroku 2.  
2. Vytvořte transakci skupiny entit (tj. dávkovou operaci),`PartitionKey` která vloží novou `RowKey` entitu zaměstnance (hodnota "Prodej" a`PartitionKey` hodnota "000152") a aktualizuje entitu indexu ( hodnota "Prodej" a `RowKey` hodnota "Jones"). EGT to provádí přidáním nového ID zaměstnance do seznamu v poli ID zaměstnanců. Další informace o transakcích skupiny entit naleznete v [tématu Entity group transactions](#entity-group-transactions).  
3. Pokud egt selže z důvodu optimistické chyby souběžnosti (to znamená, že někdo jiný změnil entitu indexu), pak je třeba začít znovu v kroku 1.  

Můžete použít podobný přístup k odstranění zaměstnance, pokud používáte druhou možnost. Změna příjmení zaměstnance je o něco složitější, protože je třeba spustit EGT, které aktualizuje tři entity: entitu zaměstnance, entitu indexu pro staré příjmení a entitu indexu pro nové příjmení. Je nutné načíst každou entitu před provedením jakékoli změny, aby bylo možné načíst hodnoty ETag, které pak můžete použít k provedení aktualizací pomocí optimistické souběžnosti.  

Následující kroky popisují proces, který byste měli provést, když potřebujete vyhledat všechny zaměstnance s určitým příjmením v oddělení. V tomto příkladu hledáme všechny zaměstnance s příjmením Jones v oddělení prodeje:  

1. Načíst entitu `PartitionKey` indexu s hodnotou `RowKey` "Prodej" a hodnotou "Jones".  
2. Analyzovat seznam ID zaměstnanců v `EmployeeIDs` poli.  
3. Pokud potřebujete další informace o každém z těchto zaměstnanců (například jejich e-mailové `PartitionKey` adresy), načtěte každou entitu zaměstnance pomocí hodnoty "Prodej" a `RowKey` hodnot ze seznamu zaměstnanců, které jste získali v kroku 2.  

Možnost 3: Vytvoření entit indexu v samostatném oddílu nebo tabulce  

Pro tuto možnost použijte entity indexu, které ukládají následující data:  

![Obrázek znázorňující entitu zaměstnance s řetězcem obsahujícím seznam ID zaměstnanců se stejným příjmením][15]

Vlastnost `EmployeeIDs` obsahuje seznam ID zaměstnanců pro zaměstnance s příjmením `RowKey`uloženým v .  

Egts nelze použít k udržení konzistence, protože entity indexu jsou v samostatném oddílu od entit zaměstnanců. Ujistěte se, že entity indexu jsou nakonec konzistentní s entitami zaměstnanců.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení vyžaduje alespoň dva dotazy k načtení odpovídající entity: jeden dotaz `RowKey` entity indexu získat seznam hodnot a pak dotazy na načíst každou entitu v seznamu.  
* Vzhledem k tomu, že jednotlivá entita má maximální velikost 1 MB, možnost 2 a možnost 3 v řešení předpokládají, že seznam ID zaměstnanců pro konkrétní příjmení není nikdy větší než 1 MB. Pokud je pravděpodobné, že seznam ID zaměstnanců má velikost větší než 1 MB, použijte možnost 1 a uložte data indexu do úložiště objektů Blob.  
* Pokud použijete možnost 2 (pomocí EGTs pro zpracování přidání a odstranění zaměstnanců a změnu příjmení zaměstnance), musíte vyhodnotit, pokud objem transakcí se blíží omezení škálovatelnosti v určitém oddílu. Pokud se jedná o tento případ, měli byste zvážit nakonec konzistentní řešení (možnost 1 nebo možnost 3). Tyto používají fronty ke zpracování požadavků na aktualizaci a umožňují ukládat entity indexu v samostatném oddílu od entit zaměstnanců.  
* Možnost 2 v tomto řešení předpokládá, že chcete vyhledat podle příjmení v rámci oddělení. Chcete například načíst seznam zaměstnanců s příjmením Jones v oddělení prodeje. Pokud chcete mít možnost vyhledat všechny zaměstnance s příjmením Jones v celé organizaci, použijte možnost 1 nebo možnost 3.
* Můžete implementovat řešení založené na frontě, které poskytuje konečnou konzistenci. Další podrobnosti naleznete v nakonec [konzistentní transakce vzor](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud chcete vyhledat sadu entit, které všechny sdílejí hodnotu společné vlastnosti, například všechny zaměstnance s příjmením Jones.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor složeného klíče](#compound-key-pattern)  
* [Nakonec konzistentní transakce vzor](#eventually-consistent-transactions-pattern)  
* [Transakce skupiny entit](#entity-group-transactions)  
* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Vzor denormalizace
Zkombinujte související data do jedné entity a umožníte vám načíst všechna data, která potřebujete, pomocí dotazu s jedním bodem.  

#### <a name="context-and-problem"></a>Kontext a problém
V relační databázi obvykle normalizujete data, abyste odstranili duplikaci, ke které dochází při dotazech načíst data z více tabulek. Pokud normalizujete data v tabulkách Azure, musíte provést více zpátečních cest z klienta na server, abyste načetli související data. Například s následující strukturou tabulky potřebujete dvě zpáteční cesty k načtení podrobností pro oddělení. Jedna cesta načte entitu oddělení, která obsahuje ID manažera, a druhá cesta načte podrobnosti o manažerovi v entitě zaměstnance.  

![Grafika entity oddělení a zaměstnaneckého subjektu][16]

#### <a name="solution"></a>Řešení
Namísto ukládání dat ve dvou samostatných entitách denormalizovat data a uchovávat kopii podrobnosti vedoucího v entitě oddělení. Například:  

![Grafika nenormalizované entity a subjektu kombinovaného oddělení][17]

S entitami oddělení uloženými s těmito vlastnostmi můžete nyní načíst všechny podrobnosti, které potřebujete o oddělení, pomocí bodového dotazu.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* K dispozici je některé náklady režie spojené s ukládáním některých dat dvakrát. Výhoda výkonu vyplývající z menšího počtu požadavků na table storage obvykle převáží marginální nárůst nákladů na úložiště. Tyto náklady jsou navíc částečně kompenzovány snížením počtu transakcí, které potřebujete k načtení podrobností oddělení.  
* Je nutné zachovat konzistenci dvou entit, které ukládají informace o manažerech. Problém konzistence můžete zpracovat pomocí EGTs aktualizovat více entit v jedné atomické transakce. V tomto případě jsou entita oddělení a entita zaměstnance pro vedoucího oddělení uloženy ve stejném oddílu.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud často potřebujete vyhledat související informace. Tento vzor snižuje počet dotazů, které musí klient provést, aby načetl data, která vyžaduje.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor složeného klíče](#compound-key-pattern)  
* [Transakce skupiny entit](#entity-group-transactions)  
* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Vzor složeného klíče
Pomocí `RowKey` složených hodnot povolte klientovi vyhledávat související data pomocí dotazu s jedním bodem.  

#### <a name="context-and-problem"></a>Kontext a problém
V relační databázi je přirozené použít spojení v dotazech k vrácení souvisejících částí dat klientovi v jednom dotazu. Můžete například použít ID zaměstnance k vyhledání seznamu souvisejících entit, které obsahují údaje o výkonu a kontrole pro tohoto zaměstnance.  

Předpokládejme, že ukládáte entity zaměstnanců do úložiště tabulek pomocí následující struktury:  

![Grafika zaměstnaneckého subjektu][18]

Musíte také ukládat historická data týkající se recenzí a výkonu za každý rok, kdy zaměstnanec pracoval pro vaši organizaci, a musíte mít přístup k těmto informacím podle roku. Jednou z možností je vytvoření jiné tabulky, ve které jsou uloženy entity s následující strukturou:  

![Grafika subjektu přezkoumání zaměstnance][19]

Pomocí tohoto přístupu se můžete rozhodnout duplikovat některé informace (například křestní jméno a příjmení) v nové entitě, abyste mohli načíst data pomocí jediného požadavku. Však nelze zachovat silnou konzistenci, protože nelze použít EGT aktualizovat dvě entity atomicky.  

#### <a name="solution"></a>Řešení
Nový typ entity můžete uložit do původní tabulky pomocí entit s následující strukturou:  

![Grafika entity zaměstnance se složeným klíčem][20]

Všimněte `RowKey` si, jak je nyní složený klíč, který se skládá z ID zaměstnance a rok revize dat. To umožňuje načíst výkon zaměstnance a zkontrolovat data s jedním požadavkem pro jednu entitu.  

Následující příklad popisuje, jak můžete načíst všechna data kontroly pro konkrétního zaměstnance (například zaměstnanec 000123 v prodejním oddělení):  

$filter=(PartitionKey eq 'Sales') a (RowKey ge 'empid_000123') a (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Měli byste použít vhodný oddělovací znak, který `RowKey` usnadňuje analýzu hodnoty: například **000123_2012**.  
* Tuto entitu také ukládáte do stejného oddílu jako ostatní entity, které obsahují související data pro stejného zaměstnance. To znamená, že můžete použít EGTs k udržení silné konzistence.
* Měli byste zvážit, jak často budete dotaz ovat data k určení, zda je tento vzor vhodný. Pokud například přistupujete k datům recenze zřídka a k hlavním datům zaměstnance často, měli byste je uchovávat jako samostatné entity.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud potřebujete uložit jednu nebo více souvisejících entit, na které často zazníváte dotaz.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entit](#entity-group-transactions)  
* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)  
* [Nakonec konzistentní transakce vzor](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Vzorek ocasu protokolu
Načíst *n* entity naposledy přidané do oddílu pomocí `RowKey` hodnoty, která seřadí v obráceném pořadí data a času.  

> [!NOTE]
> Výsledky dotazu vrácené rozhraním Azure Table API v Azure Cosmos DB nejsou seřazeny podle klíče oddílu nebo klíče řádku. Proto zatímco tento vzor je vhodný pro table storage, není vhodný pro Azure Cosmos DB. Podrobný seznam rozdílů funkcí najdete v tématu [rozdíly mezi rozhraním TABLE API v Azure Cosmos DB a Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

#### <a name="context-and-problem"></a>Kontext a problém
Běžným požadavkem je možnost načíst naposledy vytvořené entity, například deset nejnovějších nároků na výdaje předložených zaměstnancem. Dotazy na tabulky `$top` podporují operaci dotazu vrátit první *n* entity ze sady. Neexistuje žádná ekvivalentní operace dotazu vrátit poslední *n* entity v sadě.  

#### <a name="solution"></a>Řešení
Uložte entity pomocí, `RowKey` která se přirozeně řadí v obráceném pořadí data a času, takže poslední položka je vždy první v tabulce.  

Chcete-li například načíst deset nejnovějších nároků na výdaje odeslaných zaměstnancem, můžete použít hodnotu storna značky odvozenou od aktuálního data a času. Následující ukázka kódu jazyka C# ukazuje jeden způsob, jak vytvořit `RowKey` vhodnou hodnotu "invertované značky" pro který seřadí od nejnovějších po nejstarší:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

K hodnotě data a času se můžete vrátit pomocí následujícího kódu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Dotaz na tabulku vypadá takto:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Musíte pad reverzní tick hodnotu s úvodní nuly, aby zajistily, že hodnota řetězce seřadí podle očekávání.  
* Musíte si být vědomi cíle škálovatelnosti na úrovni oddílu. Dávejte pozor, abyste nevytvářeli aktivní bod oddíly.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud potřebujete přístup k entitám v obráceném pořadí data a času nebo pokud potřebujete přístup k naposledy přidaným entitám.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Prepend / připojit anti-vzor](#prepend-append-anti-pattern)  
* [Načíst entity](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Vzorek odstranění s velkým objemem
Povolte odstranění velkého objemu entit uložením všech entit pro současné odstranění ve vlastní samostatné tabulce. Entity odstraníte odstraněním tabulky.  

#### <a name="context-and-problem"></a>Kontext a problém
Mnoho aplikací odstraní stará data, která již nemusí být k dispozici pro klientskou aplikaci nebo že aplikace byla archivována na jiné paměťové médium. Tato data obvykle identifikujete podle data. Máte například požadavek na odstranění záznamů všech žádostí o přihlášení, které jsou starší než 60 dní.  

Jedním z možných návrhů je použití data a času `RowKey`žádosti o přihlášení v :  

![Grafika entity pokusu o přihlášení][21]

Tento přístup zabraňuje oddílhotspoty, protože aplikace můžete vložit a odstranit přihlašovací entity pro každého uživatele v samostatném oddílu. Tento přístup však může být nákladné a časově náročné, pokud máte velký počet entit. Nejprve je třeba provést prohledávací tabulku, abyste identifikovali všechny entity, které chcete odstranit, a potom musíte odstranit každou starou entitu. Můžete snížit počet zpátečních cest na server potřebný k odstranění starých entit dávkováním více požadavků na odstranění do EGTs.  

#### <a name="solution"></a>Řešení
Pro každý den pokusů o přihlášení použijte samostatnou tabulku. Pomocí návrhu předchozí entity se můžete při vkládání entit vyhnout aktivním oblastem. Odstranění starých entit je nyní jednoduše otázkou odstranění jedné tabulky každý den (jedna operace úložiště), namísto hledání a mazání stovek a tisíců jednotlivých přihlašovacích entit každý den.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Podporuje váš návrh jiné způsoby, jak bude aplikace používat data, například vyhledání konkrétních entit, propojení s jinými daty nebo generování souhrnných informací?  
* Vyhýbá se váš návrh horkým místům při vkládání nových entit?  
* Očekávejte zpoždění, pokud chcete znovu použít stejný název tabulky po jeho odstranění. Je lepší vždy používat jedinečné názvy tabulek.  
* Očekávejte určité omezení rychlosti při prvním použití nové tabulky, zatímco table storage se učí vzory přístupu a distribuuje oddíly mezi uzly. Měli byste zvážit, jak často je třeba vytvořit nové tabulky.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud máte velký objem entit, které je nutné odstranit současně.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entit](#entity-group-transactions)
* [Úpravy entit](#modify-entities)  

### <a name="data-series-pattern"></a>Vzor datových řad
Uložte kompletní datové řady do jedné entity, abyste minimalizovali počet požadavků, které provedete.  

#### <a name="context-and-problem"></a>Kontext a problém
Běžným scénářem je pro aplikaci pro uložení řady dat, které obvykle potřebuje k načtení všechny najednou. Aplikace může například zaznamenat, kolik zpráv rychlé zprávy každý zaměstnanec odesílá každou hodinu a potom pomocí těchto informací vykreslit, kolik zpráv každý uživatel odeslal za předchozích 24 hodin. Jeden návrh může být pro uložení 24 entit pro každého zaměstnance:  

![Grafika entity statistiky zpráv][22]

Pomocí tohoto návrhu můžete snadno vyhledat a aktualizovat entitu, která se aktualizuje pro každého zaměstnance, kdykoli aplikace potřebuje aktualizovat hodnotu počtu zpráv. Chcete-li však načíst informace k vykreslení grafu aktivity za předchozích 24 hodin, je nutné načíst 24 entit.  

#### <a name="solution"></a>Řešení
Použijte následující návrh se samostatnou vlastností pro uložení počtu zpráv pro každou hodinu:  

![Obrázek znázorňující entitu statistik y zpráv s oddělenými vlastnostmi][23]

Pomocí tohoto návrhu můžete použít operaci sloučení k aktualizaci počtu zpráv pro zaměstnance pro určitou hodinu. Nyní můžete načíst všechny informace, které potřebujete k vykreslení grafu pomocí požadavku na jednu entitu.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Pokud se celá datová řada nevejde do jedné entity (entita může mít až 252 vlastností), použijte alternativní úložiště dat, jako je například objekt blob.  
* Pokud máte více klientů, kteří aktualizují entitu současně, použijte **eTag** k implementaci optimistické souběžnosti. Pokud máte mnoho klientů, může dojít k vysoké sváru.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud potřebujete aktualizovat a načíst datovou řadu přidruženou k jednotlivé entitě.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor velkých entit](#large-entities-pattern)  
* [Sloučit nebo nahradit](#merge-or-replace)  
* [Nakonec konzistentní transakce vzor](#eventually-consistent-transactions-pattern) (pokud ukládáte datové řady v objektu blob)  

### <a name="wide-entities-pattern"></a>Vzor širokých entit
K ukládání logických entit s více než 252 vlastnostmi použijte více fyzických entit.  

#### <a name="context-and-problem"></a>Kontext a problém
Jednotlivá entita nemůže mít více než 252 vlastností (s výjimkou povinných vlastností systému) a nemůže uložit celkem více než 1 MB dat. V relační databázi byste obvykle obejít všechna omezení velikosti řádku přidáním nové tabulky a vynucení mj.  

#### <a name="solution"></a>Řešení
Pomocí table storage můžete uložit více entit představujících jeden velký obchodní objekt s více než 252 vlastnostmi. Chcete-li například uložit počet zpráv rychlé ho schrátosti odeslaných jednotlivými zaměstnanci za posledních 365 dní, můžete použít následující návrh, který používá dvě entity s různými schématy:  

![Obrázek znázorňující entitu statistiky zpráv s řádekem 01 a entitu statistik zpráv pomocí řádku 02][24]

Pokud potřebujete provést změnu, která vyžaduje aktualizaci obou entit, aby byly synchronizovány mezi sebou, můžete použít EGT. V opačném případě můžete použít jednu operaci sloučení k aktualizaci počtu zpráv pro určitý den. Chcete-li načíst všechna data pro jednotlivézaměstnance, musíte načíst obě entity. Můžete to provést se dvěma efektivní `PartitionKey` požadavky, `RowKey` které používají jak a a hodnotu.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Při rozhodování o tom, jak implementovat tento vzor, zvažte následující bod:  

* Načtení úplné logické entity zahrnuje alespoň dvě transakce úložiště: jednu pro načtení každé fyzické entity.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud potřebujete uložit entity, jejichž velikost nebo počet vlastností překračuje limity pro jednotlivé entity v úložišti tabulky.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entit](#entity-group-transactions)
* [Sloučit nebo nahradit](#merge-or-replace)

### <a name="large-entities-pattern"></a>Vzor velkých entit
Úložiště objektů blob slouží k ukládání velkých hodnot vlastností.  

#### <a name="context-and-problem"></a>Kontext a problém
Jednotlivá entita nemůže uložit celkem více než 1 MB dat. Pokud jedna nebo několik vlastností ukládá hodnoty, které způsobí, že celková velikost entity překročí tuto hodnotu, nemůžete uložit celou entitu do úložiště tabulky.  

#### <a name="solution"></a>Řešení
Pokud vaše entita přesahuje velikost 1 MB, protože jedna nebo více vlastností obsahuje velké množství dat, můžete uložit data v úložišti objektů Blob a potom uložit adresu objektu blob do vlastnosti v entitě. Můžete například uložit fotografii zaměstnance do úložiště objektů Blob a uložit odkaz `Photo` na fotografii ve vlastnictví vaší entity zaměstnance:  

![Obrázek znázorňující entitu zaměstnance s řetězcem pro zobrazení obrázku fotografie na úložiště objektů Blob][25]

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Chcete-li zachovat konečnou konzistenci mezi entitou v úložišti table a daty v úložišti objektů Blob, použijte k údržbě entit [vzor Nakonec konzistentní transakce.](#eventually-consistent-transactions-pattern)
* Načtení úplné entity zahrnuje alespoň dvě transakce úložiště: jednu pro načtení entity a jednu pro načtení dat objektu blob.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud potřebujete uložit entity, jejichž velikost překračuje limity pro jednotlivé entity v úložišti tabulky.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Nakonec konzistentní transakce vzor](#eventually-consistent-transactions-pattern)  
* [Vzor širokých entit](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Předřadit/připojit anti-vzor
Pokud máte velký objem břitových destiček, zvyšte škálovatelnost rozprostřením břitových destiček mezi více oddílů.  

#### <a name="context-and-problem"></a>Kontext a problém
Přednastavené nebo připojené entity k uloženým entitám obvykle vede k tomu, že aplikace přidá nové entity do prvního nebo posledního oddílu posloupnosti oddílů. V tomto případě všechny vložky v určitém okamžiku probíhají ve stejném oddílu a vytvářejí hotspot. To zabrání table storage z vyrovnávání zatížení vloží přes více uzlů a může způsobit, že vaše aplikace zasáhnout cíle škálovatelnosti pro oddíl. Zvažte například případ aplikace, která protokoluje přístup zaměstnanců k síti a prostředkům. Struktura entity, jako je například následující, může vést k tomu, že se oddíl aktuální hodiny stane aktivním bodem, pokud objem transakcí dosáhne cíle škálovatelnosti pro jednotlivé oddíly:  

![Grafika zaměstnaneckého subjektu][26]

#### <a name="solution"></a>Řešení
Následující struktura alternativní entity se vyhýbá hotspot u libovolného oddílu, protože aplikace zaznamenává události:  

![Obrázek zobrazující entitu zaměstnance s řádkem slučující mno žací hmoty pro ID roku, měsíce, dne, hodiny a události][27]

Všimněte si v `PartitionKey` tomto `RowKey` příkladu, jak jsou složené klíče a jsou složené. Používá `PartitionKey` id oddělení a zaměstnance k distribuci protokolování mezi více oddílů.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Podporuje alternativní struktura klíče, která zabraňuje vytváření horkých oddílů v břitových destiček, efektivně dotazy, které provádí klientská aplikace?  
* Znamená očekávaný objem transakcí, že pravděpodobně dosáhnete cílů škálovatelnosti pro jednotlivé oddíly a budete omezeni úložištěm table?  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Vyhněte se předřadit/připojit anti-pattern, pokud je pravděpodobné, že objem transakcí bude mít za následek omezení rychlosti úložištěm tabulky při přístupu k aktivnímu oddílu.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor složeného klíče](#compound-key-pattern)  
* [Vzorek ocasu protokolu](#log-tail-pattern)  
* [Úpravy entit](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Protokolovat data anti-pattern
Obvykle byste měli k ukládání dat protokolu použít úložiště objektů blob místo table storage.  

#### <a name="context-and-problem"></a>Kontext a problém
Běžným případem použití dat protokolu je načtení výběru položek protokolu pro určitý rozsah data a času. Chcete například najít všechny chyby a kritické zprávy, které vaše aplikace zaznamenala mezi 15:04 a 15:06 k určitému datu. Nechcete použít datum a čas zprávy protokolu k určení oddílu, do kterého ukládáte entity protokolu. Výsledkem je aktivní oddíl, protože v každém konkrétním okamžiku budou `PartitionKey` všechny entity protokolu sdílet stejnou hodnotu (viz [End/append anti-pattern](#prepend-append-anti-pattern)). Například následující schéma entity pro zprávu protokolu má za následek aktivní oddíl, protože aplikace zapisuje všechny zprávy protokolu do oddílu pro aktuální datum a hodinu:  

![Grafika entity zprávy protokolu][28]

V tomto příkladu `RowKey` obsahuje datum a čas zprávy protokolu k zajištění, že zprávy protokolu jsou seřazeny v pořadí data a času. Obsahuje `RowKey` také ID zprávy v případě, že více zpráv protokolu sdílí stejné datum a čas.  

Dalším přístupem je `PartitionKey` použití, který zajišťuje, že aplikace zapisuje zprávy v celé řadě oddílů. Například pokud zdroj zprávy protokolu poskytuje způsob, jak distribuovat zprávy mezi mnoha oddíly, můžete použít následující schéma entity:  

![Grafika entity zprávy protokolu][29]

Problém s tímto schématem je však, že chcete-li načíst všechny zprávy protokolu pro určité časové rozpětí, je nutné prohledat každý oddíl v tabulce.

#### <a name="solution"></a>Řešení
Předchozí část upozornila na problém pokusu o použití úložiště tabulky k ukládání položek protokolu a navrhla dva neuspokojivé návrhy. Jedno řešení vedlo k horké oddíl s rizikem špatného výkonu zápisu zpráv protokolu. Jiné řešení mělo za následek nízký výkon dotazu z důvodu požadavku na prohledání každého oddílu v tabulce za účelem načtení zpráv protokolu pro určitý časový rozsah. Úložiště objektů blob nabízí lepší řešení pro tento typ scénáře, a to je způsob, jakým Azure Storage analytics ukládá data protokolu, které shromažďuje.  

Tato část popisuje, jak analýza úložiště ukládá data protokolu v úložišti objektů Blob, jako ilustraci tohoto přístupu k ukládání dat, které obvykle dotaz podle rozsahu.  

Analýza úložiště ukládá zprávy protokolu ve formátu oddělené ve více objektech BLOB. Formát s oddělovačem usnadňuje klientské aplikaci analyzovat data ve zprávě protokolu.  

Analýza úložiště používá konvenci pojmenování pro objekty BLOB, které umožňují vyhledat objekty blob (nebo objekty BLOB), které obsahují zprávy protokolu, pro které hledáte. Například objekt blob s názvem "queue/2014/07/31/1800/000001.log" obsahuje zprávy protokolu, které se vztahují ke službě fronty pro hodinu začínající v 18:00 na 31 července 2014. "000001" označuje, že se jedná o první soubor protokolu pro toto období. Analýza úložiště také zaznamenává časová razítka první a poslední zprávy protokolu uložené v souboru jako součást metadat objektu blob. Rozhraní API pro úložiště objektů BLOB umožňuje vyhledat objekty BLOB v kontejneru na základě předpony názvu. Chcete-li vyhledat všechny objekty BLOB, které obsahují data protokolu fronty pro hodinu začínající v 18:00, můžete použít předponu "queue/2014/07/31/1800".  

Analýza úložiště vyrovnávací paměti protokolovat zprávy interně a potom pravidelně aktualizuje příslušný objekt blob nebo vytvoří nový s nejnovější dávkou položek protokolu. To snižuje počet zápisů, které musí provádět do úložiště objektů Blob.  

Pokud implementujete podobné řešení ve vlastní aplikaci, zvažte, jak spravovat kompromis mezi spolehlivostí a náklady a škálovatelností. Jinými slovy, vyhodnotit účinek zápisu každé položky protokolu do úložiště objektů Blob, jak se to stane, ve srovnání s ukládání do vyrovnávací paměti aktualizace ve vaší aplikaci a jejich zápisu do úložiště objektů Blob v dávkách.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Při rozhodování o způsobu ukládání dat protokolu zvažte následující body:  

* Pokud vytvoříte návrh tabulky, který se vyhýbá potenciální horké oddíly, můžete zjistit, že nelze přistupovat k datům protokolu efektivně.  
* Chcete-li zpracovat data protokolu, klient často potřebuje načíst mnoho záznamů.  
* I když data protokolu je často strukturované, úložiště objektů blob může být lepší řešení.  

### <a name="implementation-considerations"></a>Důležité informace o implementaci
Tato část popisuje některé důležité informace, které je třeba mít na paměti při implementaci vzorů popsaných v předchozích částech. Většina této části používá příklady napsané v c#, které používají klientskou knihovnu úložiště (verze 4.3.0 v době psaní).  

### <a name="retrieve-entities"></a>Načíst entity
Jak je popsáno v části [Návrh pro dotazování](#design-for-querying), nejúčinnějšídotaz je bodový dotaz. V některých případech však může být nutné načíst více entit. Tato část popisuje některé běžné přístupy k načítání entit pomocí klientské knihovny úložiště.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Spuštění bodového dotazu pomocí klientské knihovny úložiště
Nejjednodušší způsob, jak spustit bodový dotaz, je použít operaci **Načíst** tabulku. Jak je znázorněno v následujícím fragmentu kódu jazyka C#, `PartitionKey` tato operace načte `RowKey` entitu s hodnotou "Prodej" a hodnotou "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Všimněte si, jak tento příklad očekává, `EmployeeEntity`že entita načte typ .  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Načtení více entit pomocí LINQ
Můžete načíst více entit pomocí LINQ s knihovnou klienta úložiště a zadání dotazu s **klauzulí where.** Chcete-li se vyhnout prohledávací tabulce, měli byste vždy zahrnout hodnotu `PartitionKey` do klauzule where a pokud možno hodnotu, `RowKey` abyste se vyhnuli prohledávačům tabulky a oddílů. Table storage podporuje omezenou sadu operátorů porovnání (větší než, větší než nebo rovno, menší než, menší než nebo rovno, rovné a není stejné) pro použití v where klauzule. Následující fragment kódu Jazyka C# vyhledá všechny zaměstnance, jejichž příjmení začíná písmenem "B" (za `RowKey` předpokladu, `PartitionKey` že ukládá příjmení) v prodejním oddělení (za předpokladu, že ukládá název oddělení):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Všimněte si, jak `RowKey` dotaz `PartitionKey` určuje a a zajistit lepší výkon.  

Následující ukázka kódu ukazuje ekvivalentní funkce pomocí rozhraní FLUENT API (další informace o plynulých rozhraních API obecně naleznete v [tématu Doporučené postupy pro navrhování plynulé rozhraní API](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Vzorek vnoří `CombineFilters` více metod, aby zahrnovaltři podmínky filtru.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Načtení velkého počtu entit z dotazu
Optimální dotaz vrátí jednotlivé entity `PartitionKey` na základě `RowKey` hodnoty a hodnoty. V některých případech však může mít požadavek vrátit mnoho entit ze stejného oddílu nebo dokonce z mnoha oddílů. Vždy byste měli plně otestovat výkon vaší aplikace v těchto scénářích.  

Dotaz proti table storage může vrátit maximálně 1 000 entit najednou a spustit maximálně pět sekund. Table storage vrátí token pokračování, který umožní klientské aplikaci požádat o další sadu entit, pokud platí některá z následujících skutečností:

- Sada výsledků obsahuje více než 1 000 entit.
- Dotaz nebyl dokončen během pěti sekund.
- Dotaz protíná hranice oddílu. 

Další informace o tom, jak tokeny pokračování fungují, naleznete v [tématu Časový limit dotazu a stránkování](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Pokud používáte klientskou knihovnu úložiště, může automaticky zpracovávat tokeny pokračování pro vás, protože vrací entity z úložiště tabulky. Například následující ukázka kódu jazyka C# automaticky zpracovává tokeny pokračování, pokud je úložiště tabulky vrátí v odpovědi:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

Následující kód jazyka C# zpracovává tokeny pokračování explicitně:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Pomocí tokenů pokračování explicitně můžete řídit, kdy vaše aplikace načte další segment dat. Pokud například klientská aplikace umožňuje uživatelům procházet entity uložené v tabulce, může se uživatel rozhodnout, že nebude procházet všechny entity načtené dotazem. Vaše aplikace by použít pouze token pokračování načíst další segment, když uživatel dokončil stránkování přes všechny entity v aktuálním segmentu. Tento přístup má několik výhod:  

* Můžete omezit množství dat, která chcete načíst z úložiště tabulky a které přesouváte po síti.  
* V rozhraní .NET můžete provádět asynchronní vstupně-va.  
* Token pokračování můžete serializovat do trvalého úložiště, takže můžete pokračovat v případě selhání aplikace.  

> [!NOTE]
> Token pokračování obvykle vrací segment obsahující 1 000 entit, i když může obsahovat méně. To platí také v případě, že omezíte počet položek, které dotaz vrátí **pomocí funkce Take,** a vrátíte tak první n entit, které odpovídají vašim vyhledávacím kritériím. Úložiště tabulky může vrátit segment obsahující méně než n entit, spolu s tokenpokračování, který vám umožní načíst zbývající entity.  
> 
> 

Následující kód jazyka C# ukazuje, jak změnit počet entit vrácených uvnitř segmentu:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projekce na straně serveru
Jedna entita může mít až 255 vlastností a mít velikost až 1 MB. Při dotazování na tabulku a načtení entit, nemusí potřebovat všechny vlastnosti a můžete se vyhnout přenosu dat zbytečně (ke snížení latence a nákladů). Projekce na straně serveru můžete použít k přenosu pouze vlastností, které potřebujete. Následující příklad načte `Email` pouze vlastnost `PartitionKey`(spolu `Timestamp`s `ETag`, `RowKey`, , a ) z entit vybraných dotazem.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Všimněte `RowKey` si, jak je hodnota k dispozici, i když není zahrnuta v seznamu vlastností, které chcete načíst.  

### <a name="modify-entities"></a>Úpravy entit
Klientská knihovna úložiště umožňuje upravit entity uložené v úložišti tabulek vložením, odstraněním a aktualizací entit. Egts můžete použít k dávkování více vložení, aktualizace a odstranění operace společně snížit počet zpátečních cest požadované a zlepšit výkon vašeho řešení.  

Výjimky vyvolány při knihovně klienta úložiště spustí EGT obvykle zahrnují index entity, která způsobila selhání dávky. To je užitečné při ladění kódu, který používá EGTs.  

Měli byste také zvážit, jak váš návrh ovlivňuje způsob, jakým klientská aplikace zpracovává souběžnost a operace aktualizace.  

#### <a name="managing-concurrency"></a>Správa souběžnosti
Ve výchozím nastavení table storage implementuje optimistické kontroly souběžnosti na úrovni jednotlivých entit pro operace vložení, sloučení a odstranění, i když je možné, aby klient vynutit table storage obejít tyto kontroly. Další informace najdete [v tématu Správa souběžnosti v Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Sloučit nebo nahradit
Metoda `Replace` třídy `TableOperation` vždy nahradí úplnou entitu v úložišti tabulky. Pokud do požadavku nezahrnete vlastnost, pokud tato vlastnost existuje v uložené entitě, požadavek tuto vlastnost odebere z uložené entity. Pokud nechcete explicitně odebrat vlastnost z uložené entity, musíte zahrnout všechny vlastnosti v požadavku.  

Metodu `Merge` `TableOperation` třídy můžete použít ke snížení množství dat, která odešlete do úložiště tabulky, když chcete aktualizovat entitu. Metoda `Merge` nahradí všechny vlastnosti v uložené entitě hodnotami vlastností z entity obsažené v požadavku. Tato metoda ponechá beze změny všechny vlastnosti v uložené entity, které nejsou zahrnuty v požadavku. To je užitečné, pokud máte velké entity a stačí aktualizovat malý počet vlastností v požadavku.  

> [!NOTE]
> Metody `*Replace` `Merge` a se nezdaří, pokud entita neexistuje. Jako alternativu můžete použít `InsertOrReplace` `InsertOrMerge` metody a, které vytvářejí novou entitu, pokud neexistuje.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Práce s heterogenními typy entit
Úložiště tabulek je úložiště tabulek *bez schématu.* To znamená, že jedna tabulka může ukládat entity více typů, což poskytuje velkou flexibilitu v návrhu. Následující příklad ilustruje tabulku s entitami zaměstnanců i oddělení:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Časové razítko</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>ZaměstnanecCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Každá entita `PartitionKey` `RowKey`musí `Timestamp` mít stále , a hodnoty, ale může mít libovolnou sadu vlastností. Kromě toho není nic, co by naznačovalo typ entity, pokud se rozhodnete tyto informace někam uložit. Existují dvě možnosti pro identifikaci typu entity:  

* Předřazovat typ `RowKey` entity na `PartitionKey`(nebo případně). Například, `EMPLOYEE_000123` `DEPARTMENT_SALES` nebo `RowKey` jako hodnoty.  
* K zaznamenání typu entity použijte samostatnou vlastnost, jak je znázorněno v následující tabulce.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Časové razítko</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>E-mail</th>
</tr>
<tr>
<td>Zaměstnanec</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>E-mail</th>
</tr>
<tr>
<td>Zaměstnanec</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>ZaměstnanecCount</th>
</tr>
<tr>
<td>Oddělení</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>E-mail</th>
</tr>
<tr>
<td>Zaměstnanec</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

První možnost, před přijetím typu `RowKey`entity na , je užitečná, pokud existuje možnost, že dvě entity různých typů mohou mít stejnou hodnotu klíče. Také seskupí entity stejného typu společně v oddílu.  

Techniky popsané v této části jsou důležité zejména pro diskusi o[vztahy dědičnosti](#inheritance-relationships).  

> [!NOTE]
> Zvažte zahrnutí čísla verze do hodnoty typu entity, aby klientské aplikace mohly vyvíjet objekty POCO a pracovat s různými verzemi.  
> 
> 

Zbývající část této části popisuje některé funkce v klientské knihovně úložiště, které usnadňují práci s více typy entit ve stejné tabulce.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Načíst heterogenní typy entit
Pokud používáte klientskou knihovnu úložiště, máte tři možnosti pro práci s více typy entit.  

Pokud znáte typ entity uložené s `RowKey` `PartitionKey` konkrétními a hodnotami, můžete při načtení entity zadat typ entity. Viděli jste to v předchozích dvou příkladech, které načítají entity typu `EmployeeEntity`: [Spusťte bodový dotaz pomocí klientské knihovny úložiště](#run-a-point-query-by-using-the-storage-client-library) a [načtěte více entit pomocí LINQ](#retrieve-multiple-entities-by-using-linq).  

Druhou možností je použít `DynamicTableEntity` typ (vlastnost taška), namísto konkrétní ho typu entity POCO. Tato možnost může také zlepšit výkon, protože není nutné serializovat a rekonstruovat entitu do typů .NET. Následující kód jazyka C# potenciálně načte více entit různých typů z `DynamicTableEntity` tabulky, ale vrátí všechny entity jako instance. Potom používá `EntityType` vlastnost k určení typu každé entity:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Chcete-li načíst další `TryGetValue` vlastnosti, `Properties` musíte `DynamicTableEntity` použít metodu na vlastnost třídy.  

Třetí možností je kombinovat `DynamicTableEntity` pomocí typu `EntityResolver` a instance. To umožňuje vyřešit více typů POCO ve stejném dotazu. V tomto příkladu `EntityResolver` delegát `EntityType` používá vlastnost k rozlišení mezi dvěma typy entit, které dotaz vrátí. Metoda `Resolve` používá `resolver` delegáta `DynamicTableEntity` k překladu instancí na `TableEntity` instance.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modify-heterogeneous-entity-types"></a>Úprava heterogenních typů entit
K odstranění entity nemusíte znát typ entity a vždy znáte typ entity při vložení. `DynamicTableEntity` Typ však můžete použít k aktualizaci entity bez znalosti jejího typu a bez použití třídy entity POCO. Následující ukázka kódu načte jednu entitu a zkontroluje, zda `EmployeeCount` vlastnost existuje před aktualizací.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="control-access-with-shared-access-signatures"></a>Řízení přístupu pomocí sdílených přístupových podpisů
Tokeny sdíleného přístupového podpisu (SAS) můžete klientům umožnit přímo upravovat (a dotazovat) entity tabulky bez nutnosti ověřovat přímo pomocí table storage. Obvykle existují tři hlavní výhody používání SAS ve vaší aplikaci:  

* Není nutné distribuovat klíč účtu úložiště na nezabezpečenou platformu (například mobilní zařízení), aby toto zařízení mohlo přistupovat k entitám v úložišti table.  
* Můžete přetížit některé práce, které webové a pracovní role provádět při správě entit. Můžete převést zátěž na klientská zařízení, jako jsou počítače koncových uživatelů a mobilní zařízení.  
* Klientovi můžete přiřadit omezenou a časově omezenou sadu oprávnění (například povolit přístup jen pro čtení k určitým prostředkům).  

Další informace o používání tokenů SAS s table storage naleznete [v tématu Použití sdílených přístupových podpisů (SAS).](../storage/common/storage-dotnet-shared-access-signature-part-1.md)  

Je však nutné stále generovat tokeny SAS, které udělují klientskou aplikaci entitám v úložišti table. Udělejte to v prostředí, které má zabezpečený přístup ke klíčům účtu úložiště. Obvykle používáte webovou nebo pracovní roli ke generování tokenů SAS a jejich doručení do klientských aplikací, které potřebují přístup k vašim entitám. Vzhledem k tomu, že stále existuje režie, která se podílí na generování a doručování tokenů SAS klientům, měli byste zvážit, jak nejlépe snížit tuto režii, zejména ve scénářích s velkým objemem.  

Je možné generovat token SAS, který uděluje přístup k podmnožině entit v tabulce. Ve výchozím nastavení vytvoříte token SAS pro celou tabulku. Ale je také možné určit, že token SAS udělit `PartitionKey` přístup k rozsahu `PartitionKey` `RowKey` hodnot nebo rozsah a hodnoty. Můžete se rozhodnout generovat tokeny SAS pro jednotlivé uživatele vašeho systému, takže token SAS každého uživatele jim umožňuje pouze přístup k vlastním entitám v úložišti table.  

### <a name="asynchronous-and-parallel-operations"></a>Asynchronní a paralelní operace
Za předpokladu, že rozšiřujete požadavky na více oddílů, můžete zlepšit propustnost a odezvu klienta pomocí asynchronních nebo paralelních dotazů.
Můžete mít například dvě nebo více instancí role pracovní ho distančního procesu, které přistupují k tabulkám paralelně. Můžete mít jednotlivé role pracovního procesu odpovědné za určité sady oddílů nebo jednoduše mít více instancí rolí pracovního procesu, z nichž každá má přístup ke všem oddílům v tabulce.  

V rámci instance klienta můžete zlepšit propustnost spuštěním operací úložiště asynchronně. Klientská knihovna úložiště usnadňuje psaní asynchronních dotazů a úprav. Můžete například začít synchronní metodou, která načte všechny entity v oddílu, jak je znázorněno v následujícím kódu jazyka C#:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

Tento kód můžete snadno upravit tak, aby byl dotaz spuštěn asynchronně, a to následovně:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

V tomto asynchronním příkladu se zobrazí následující změny ze synchronní verze:  

* Podpis metody nyní `async` obsahuje modifikátor `Task` a vrátí instanci.  
* Místo volání `ExecuteSegmented` metody k načtení výsledků metoda `ExecuteSegmentedAsync` nyní volá metodu. Metoda používá `await` modifikátor k načtení výsledků asynchronně.  

Klientská aplikace může volat tuto metodu vícekrát, s různými hodnotami pro `department` parametr. Každý dotaz běží v samostatném vlákně.  

Neexistuje žádná asynchronní verze `Execute` metody ve `TableQuery` třídě, `IEnumerable` protože rozhraní nepodporuje asynchronní výčet.  

Entity můžete také asynchronně vkládat, aktualizovat a odstraňovat. Následující příklad jazyka C# ukazuje jednoduchou synchronní metodu vložení nebo nahrazení entity zaměstnance:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Tento kód můžete snadno upravit tak, aby aktualizace byla spuštěna asynchronně, a to následovně:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

V tomto asynchronním příkladu se zobrazí následující změny ze synchronní verze:  

* Podpis metody nyní `async` obsahuje modifikátor `Task` a vrátí instanci.  
* Místo volání `Execute` metody k aktualizaci entity metoda `ExecuteAsync` nyní volá metodu. Metoda používá `await` modifikátor k načtení výsledků asynchronně.  

Klientská aplikace může volat více asynchronních metod, jako je tento, a každá vyvolání metody běží v samostatném vlákně.  


[1]: ./media/storage-table-design-guide/storage-table-design-IMAGE01.png
[2]: ./media/storage-table-design-guide/storage-table-design-IMAGE02.png
[3]: ./media/storage-table-design-guide/storage-table-design-IMAGE03.png
[4]: ./media/storage-table-design-guide/storage-table-design-IMAGE04.png
[5]: ./media/storage-table-design-guide/storage-table-design-IMAGE05.png
[6]: ./media/storage-table-design-guide/storage-table-design-IMAGE06.png
[7]: ./media/storage-table-design-guide/storage-table-design-IMAGE07.png
[8]: ./media/storage-table-design-guide/storage-table-design-IMAGE08.png
[9]: ./media/storage-table-design-guide/storage-table-design-IMAGE09.png
[10]: ./media/storage-table-design-guide/storage-table-design-IMAGE10.png
[11]: ./media/storage-table-design-guide/storage-table-design-IMAGE11.png
[12]: ./media/storage-table-design-guide/storage-table-design-IMAGE12.png
[13]: ./media/storage-table-design-guide/storage-table-design-IMAGE13.png
[14]: ./media/storage-table-design-guide/storage-table-design-IMAGE14.png
[15]: ./media/storage-table-design-guide/storage-table-design-IMAGE15.png
[16]: ./media/storage-table-design-guide/storage-table-design-IMAGE16.png
[17]: ./media/storage-table-design-guide/storage-table-design-IMAGE17.png
[18]: ./media/storage-table-design-guide/storage-table-design-IMAGE18.png
[19]: ./media/storage-table-design-guide/storage-table-design-IMAGE19.png
[20]: ./media/storage-table-design-guide/storage-table-design-IMAGE20.png
[21]: ./media/storage-table-design-guide/storage-table-design-IMAGE21.png
[22]: ./media/storage-table-design-guide/storage-table-design-IMAGE22.png
[23]: ./media/storage-table-design-guide/storage-table-design-IMAGE23.png
[24]: ./media/storage-table-design-guide/storage-table-design-IMAGE24.png
[25]: ./media/storage-table-design-guide/storage-table-design-IMAGE25.png
[26]: ./media/storage-table-design-guide/storage-table-design-IMAGE26.png
[27]: ./media/storage-table-design-guide/storage-table-design-IMAGE27.png
[28]: ./media/storage-table-design-guide/storage-table-design-IMAGE28.png
[29]: ./media/storage-table-design-guide/storage-table-design-IMAGE29.png

