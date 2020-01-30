---
title: Návrh Azure Cosmos DBch tabulek pro škálování a výkon
description: 'Průvodce návrhem služby Azure Table Storage: škálovatelné a výkonné tabulky v Azure Cosmos DB a v úložišti tabulek v Azure'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: sakash279
ms.author: akshanka
ms.custom: seodec18
ms.openlocfilehash: 166076d366cbbf7bef24648772beaba9b3a88253
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771522"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Průvodce návrhem tabulky Azure Table Storage: škálovatelné a výkonné tabulky

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Při návrhu škálovatelných a výkonných tabulek je potřeba zvážit řadu faktorů, včetně nákladů. Pokud jste už někdy navrhovali schémata pro relační databáze, tyto aspekty vám budou povědomé. Přestože však mezi službou Azure Table Storage a relačními modely existují určité podobnosti, je mezi nimi i řada důležitých rozdílů. Tyto rozdíly obvykle vedou k různým návrhům, které pro někoho se zkušenostmi s relačními databázemi můžou působit nelogicky nebo špatně, ale které dávají smysl v případě návrhu pro úložiště NoSQL párů klíč-hodnota, jako je služba Table Storage.

Table Storage je navržený tak, aby podporoval aplikace v cloudovém měřítku, které mohou obsahovat miliardy entit ("řádky" v terminologii relačních databází) dat nebo datové sady, které musí podporovat vysoké objemy transakcí. Proto je potřeba se rozmyslet, jak se data ukládají, a pochopit, jak funguje úložiště tabulek. Dobře navržené úložiště dat NoSQL může umožňovat, aby vaše řešení bylo mnohem dalšího škálovatelné (a za nižší náklady) než řešení, které používá relační databázi. Tento průvodce vám pomůže s těmito tématy.  

## <a name="about-azure-table-storage"></a>Informace o službě Azure Table Storage
Tato část popisuje některé klíčové funkce úložiště tabulek, které jsou obzvláště důležité pro navrhování výkonu a škálovatelnosti. Pokud Azure Storage a úložiště tabulek teprve začínáte, přečtěte si téma [Úvod do Microsoft Azure Storage](../storage/common/storage-introduction.md) a [Začínáme s Azure Table Storage pomocí rozhraní .NET](table-storage-how-to-use-dotnet.md) ještě před přečtením zbývající části tohoto článku. I když se tato příručka zaměřuje na úložiště tabulek, obsahuje několik diskuzí o službě Azure Queue Storage a Azure Blob Storage a o tom, jak je můžete používat spolu s tabulkovým úložištěm v řešení.  

Table Storage používá k ukládání dat tabulkový formát. Ve standardní terminologie každý řádek v tabulce představuje entitu a sloupce, které ukládat různé vlastnosti dané entity. Každá entita má dvojici klíčů, které ji jednoznačně identifikují, a sloupec časového razítka, které služba Table Storage používá ke sledování, kdy se entita naposledy aktualizovala. Pole časového razítka se přidá automaticky a časové razítko nemůžete ručně přepsat libovolnou hodnotou. Table Storage používá toto poslední upravené časové razítko (LMT) ke správě optimistické souběžnosti.  

> [!NOTE]
> Operace REST API Table Storage také vrací `ETag` hodnotu, která je odvozena z LMT. V tomto dokumentu se výrazy ETag a LMT používají zaměnitelné, protože odkazují na stejná základní data.  
> 
> 

Následující příklad ukazuje, jednoduché tabulky návrhu k uložení entity zaměstnanci a oddělení. Mnoho příkladů uvedených dál v této příručce jsou založeny na toto jednoduché konstrukce.  

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
<th>Jméno</th>
<th>Příjmení</th>
<th>Věk</th>
<th>E-mail</th>
</tr>
<tr>
<td>Nepřipojovat</td>
<td>Radnice</td>
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
<th>Jméno</th>
<th>Příjmení</th>
<th>Věk</th>
<th>E-mail</th>
</tr>
<tr>
<td>Čer</td>
<td>CaO</td>
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
<th>EmployeeCount</th>
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
<th>Jméno</th>
<th>Příjmení</th>
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


Tento návrh se zatím bude podobat tabulce v relační databázi. Hlavní rozdíly jsou povinné sloupce a možnost ukládat více typů entit do stejné tabulky. Kromě toho má každý z uživatelem definovaných vlastností, jako je například **FirstName** nebo **věk**, datový typ, jako je například celé číslo nebo řetězec, stejně jako sloupec v relační databázi. Na rozdíl od relační databáze ale bez schématu pro úložiště tabulek je to, že vlastnost nemusí mít stejný datový typ pro každou entitu. Pokud chcete uložit komplexních datových typů v jedné vlastnosti, musíte použít serializovaný formát jako je JSON nebo XML. Další informace najdete v tématu [Principy datového modelu Table Storage](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Vaše volba `PartitionKey` a `RowKey` jsou zásadní pro dobrý návrh tabulek. Každá entita uložená v tabulce musí mít jedinečnou kombinaci `PartitionKey` a `RowKey`. Stejně jako u klíčů v tabulce relační databáze jsou hodnoty `PartitionKey` a `RowKey` indexovány, aby se vytvořil clusterovaný index, který umožňuje rychlé vyhledávání. Table Storage ale nevytváří žádné sekundární indexy, takže se jedná o jediné dvě indexované vlastnosti (některé ze vzorů popsaných později ukazují, jak můžete toto zjevné omezení obejít).  

Tabulka se skládá z jednoho nebo více oddílů a mnohé z rozhodnutí o návrhu, které uděláte, budou kolem výběru vhodné `PartitionKey` a `RowKey` k optimalizaci vašeho řešení. Řešení se může skládat pouze z jedné tabulky, která obsahuje všechny vaše entity uspořádané do oddílů, ale obvykle má řešení více tabulek. Tabulky vám pomůžou logicky organizovat vaše entity a pomáhat vám spravovat přístup k datům pomocí seznamů řízení přístupu. Celou tabulku můžete vyřadit pomocí jediné operace úložiště.  

### <a name="table-partitions"></a>Oddíly tabulky
Název účtu, název tabulky a `PartitionKey` společně identifikují oddíl v rámci služby úložiště, ve které je tato entita uložená v úložišti tabulek. Stejně jako součást schématu adresování pro entity, oddíly definují obor pro transakce (Další informace najdete v části dále v tomto článku, [transakce skupiny entit](#entity-group-transactions)) a tvoří základ způsobu, jakým se tabulkové úložiště škálují. Další informace o oddílech tabulky najdete v tématu [Kontrolní seznam pro výkon a škálovatelnost pro úložiště tabulek](../storage/tables/storage-performance-checklist.md).  

V tabulkovém úložišti má jednotlivé uzly služby jednoho nebo více úplných oddílů a služba se škáluje podle dynamicky vyrovnávání zatížení oddílů v uzlech. Pokud je uzel v zatížení, může tabulka úložiště rozdělit rozsah oddílů, které služba obsluhuje v tomto uzlu, na jiné uzly. V případě provozu může tabulka úložiště sloučit rozsahy oddílů z tichých uzlů zpátky do jednoho uzlu.  

Další informace o interních podrobnostech úložiště tabulek a zejména o tom, jak spravuje oddíly, najdete v tématu [Microsoft Azure Storage: vysoce dostupnou cloudovou službu cloudového úložiště se silnými konzistencí](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Transakce skupiny entit
V úložišti tabulek jsou transakce skupin entit (EGTs) jediným integrovaným mechanismem pro provádění atomických aktualizací v několika entitách. EGTs se také označují jako *transakce Batch*. EGTs může pracovat pouze s entitami uloženými ve stejném oddílu (sdílení stejného klíče oddílu v konkrétní tabulce), takže kdykoli budete potřebovat atomické transakční chování napříč více entitami, ujistěte se, že jsou tyto entity ve stejném oddílu. To je často důvod pro udržení více typů entit ve stejné tabulce (a oddílu) a nepoužívá více tabulek pro různé typy entit. Jeden EGT může pracovat na maximálně 100 entit.  Pokud odešlete více souběžných EGTs ke zpracování, je důležité zajistit, aby tyto EGTs nespolupracovaly s entitami, které jsou společné napříč EGTs. V opačném případě riskujete zpracování zpoždění.

EGTs také zavede potenciální kompromis k vyhodnocení v návrhu. Použití více oddílů zvyšuje škálovatelnost vaší aplikace, protože Azure nabízí více příležitostí pro žádosti o vyrovnávání zatížení napříč uzly. To ale může omezit schopnost vaší aplikace provádět atomické transakce a udržovat velkou konzistenci pro vaše data. Kromě toho existují konkrétní cíle škálovatelnosti na úrovni oddílu, která může omezit propustnost transakcí, které můžete očekávat pro jeden uzel.

Další informace o cílech škálovatelnosti pro účty Azure Storage najdete v tématu [cíle škálovatelnosti pro účty úložiště úrovně Standard](../storage/common/scalability-targets-standard-account.md). Další informace o cílech škálovatelnosti pro úložiště tabulek najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště tabulek](../storage/tables/scalability-targets.md). Novější části této příručky popisují různé návrhu strategie, které vám pomůžou spravovat kompromisy, jako je například tento a diskutovat o tom, jak nejlépe zvolit klíč oddílu na základě konkrétních požadavků klientské aplikace.  

### <a name="capacity-considerations"></a>Důležité informace o kapacity
Následující tabulka obsahuje některé klíčové hodnoty, které je třeba znát při navrhování řešení úložiště tabulek:  

| Celková kapacita účtu služby Azure storage | 500 TB |
| --- | --- |
| Počet tabulek v účtu služby Azure storage |Omezeno pouze na kapacitu účtu úložiště. |
| Počet oddílů v tabulce |Omezeno pouze na kapacitu účtu úložiště. |
| Počet entit v oddílu |Omezeno pouze na kapacitu účtu úložiště. |
| Velikost jednotlivých entit |Až 1 MB s maximálním počtem 255 vlastností (včetně `PartitionKey`, `RowKey`a `Timestamp`). |
| Velikost `PartitionKey` |Velikost řetězce až 1 KB. |
| Velikost `RowKey` |Velikost řetězce až 1 KB. |
| Velikost transakce skupiny entit |Transakce může zahrnovat maximálně 100 entit a velikost datové části musí být menší než 4 MB. EGT lze aktualizovat pouze entity jednou. |

Další informace najdete v tématu [Principy datového modelu Table Service](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Důležité informace o nákladech
Úložiště tabulek je poměrně levné, ale měli byste zahrnout odhadované náklady na využití kapacity a množství transakcí v rámci vyhodnocení jakéhokoli řešení, které využívá úložiště tabulek. V mnoha scénářích ale ukládání denormalizovaných nebo duplicitních dat, aby se zlepšil výkon nebo škálovatelnost vašeho řešení, je platný přístup, který je možné provést. Další informace o cenách najdete v tématu [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Pokyny pro návrh tabulek
Tyto seznamy shrnují některé klíčové pokyny, které byste měli mít na paměti při navrhování tabulek. Tato příručka je podrobněji popsána dále v. Tyto pokyny se liší od pokynů, které byste obvykle dodržovali pro návrh relačních databází.  

Návrh tabulkového úložiště, aby bylo možné efektivně *číst* :

* **Návrh pro dotazování aplikace náročné na čtení.** Pokud navrhujete své tabulky, zamyslete se nad dotazy (zejména s těmi, které jsou u nich závislé), a teprve potom si myslíte, jak budete entity aktualizovat. Obvykle v důsledku efektivní a výkonné řešení.  
* **V dotazech zadejte jak `PartitionKey`, tak `RowKey`.** *Dotazy na body* , jako jsou ty, nejúčinnější dotazy na úložiště tabulek.  
* **Zvažte uložení duplicitní kopie entity.** Úložiště tabulek je levné, proto zvažte uložení stejné entity vícekrát (s různými klíči), čímž umožníte efektivnější dotazy.  
* **Vezměte v úvahu denormalizing vaše data.** Úložiště tabulek je levné, proto zvažte denormalizaci vašich dat. Například ukládat souhrn entit, aby se dotazy pro agregovaná data, která stačí pro přístup k jedné entity.  
* **Použijte složené klíčové hodnoty.** K dispozici jsou tyto jediné klíče `PartitionKey` a `RowKey`. Například použijte složené klíčové hodnoty a povolit alternativní s klíči přístup cesty k entitám.  
* **Použijte dotaz projekce.** Můžete snížit množství dat, která přenos přes síť pomocí dotazů, které vybírají pouze pole, které potřebujete.  

Návrh tabulkového úložiště pro efektivní *zápis* :  

* **Nevytvářejte aktivní oddíly.** Zvolte klíče, které vám umožní rozdělit mezi několik oddílů v některém okamžiku vašich požadavků.  
* **Vyhněte se špičkám provozu.** Distribuujte provoz v rozumné době a zabraňte špičkám v provozu.
* **Není nutně vytvořit samostatnou tabulku pro každý typ entity.** Pokud požadujete atomické transakce mezi typy entit, můžete uložit tyto několik typů entit ve stejném oddílu ve stejné tabulce.
* **Vezměte v úvahu maximální propustnost, kterou musí dosáhnout.** Musíte znát cíle škálovatelnosti pro úložiště tabulek a zajistit, aby vám návrh nezpůsobí jejich překročení.  

Později v tomto průvodci uvidíte příklady, které obsahují všechny tyto zásady v praxi.  

## <a name="design-for-querying"></a>Návrh pro dotazování
Úložiště tabulek může být náročné na čtení, náročné na zápis nebo kombinace těchto dvou. V této části se předpokládá návrh na efektivní podporu operací čtení. Návrh, že podporuje operace čtení efektivně je obvykle také efektivní pro operace zápisu. Při návrhu na podporu operací zápisu ale existují další okolnosti. Ty jsou popsány v následující části, [Návrh pro úpravu dat](#design-for-data-modification).

Dobrým výchozím bodem, který vám umožní efektivně číst data, je požádat o to, jaké dotazy bude aplikace muset spustit, aby se načetla data, která potřebuje?  

> [!NOTE]
> S tabulkovým úložištěm je důležité, abyste si před návrhem nastavili správné informace, protože je obtížné ji později změnit. Například v relační databázi je často možné vyřešit problémy s výkonem pouhým přidáním indexů do existující databáze. Nejedná se o možnost s tabulkovým úložištěm.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Způsob, jakým volba `PartitionKey` a `RowKey` ovlivní výkon dotazů
Následující příklady předpokládají, že Table Storage ukládá entity zaměstnanců s následující strukturou (většina příkladů vynechává vlastnost `Timestamp` pro přehlednost):  

| název sloupce | Data type |
| --- | --- |
| `PartitionKey` (název oddělení) |Řetězec |
| `RowKey` (ID zaměstnance) |Řetězec |
| `FirstName` |Řetězec |
| `LastName` |Řetězec |
| `Age` |Integer |
| `EmailAddress` |Řetězec |

Tady jsou některé obecné pokyny pro navrhování dotazů na úložiště tabulek. Syntaxe filtru použitá v následujících příkladech je z tabulkového úložiště REST API. Další informace najdete v tématu věnovaném [dotazování entit](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* *Dotaz na bod* je nejúčinnější vyhledávání, které se má použít, a doporučuje se pro vyhledávání ve velkém objemu nebo vyhledávání vyžadující nejnižší latenci. Takový dotaz může pomocí indexů snadno vyhledat jednotlivou entitu tím, že zadáte hodnoty `PartitionKey` i `RowKey`. Například: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* Druhý nejlepší je *dotaz na rozsah*. Používá `PartitionKey`a filtruje rozsah `RowKey` hodnot pro vrácení více než jedné entity. Hodnota `PartitionKey` identifikuje konkrétní oddíl a `RowKey` hodnoty identifikují podmnožinu entit v tomto oddílu. Například: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* Třetí nejlepší je *Kontrola oddílu*. Používá `PartitionKey`a filtruje jinou neklíčovou vlastnost a může vracet více než jednu entitu. Hodnota `PartitionKey` identifikuje konkrétní oddíl a hodnoty vlastností se vyberou pro podmnožinu entit v tomto oddílu. Například: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* *Prohledávání tabulky* neobsahuje `PartitionKey`a je neefektivní, protože hledá všechny oddíly, které tvoří tabulku pro všechny odpovídající entity. Provede prohledávání tabulky bez ohledu na to, zda filtr používá `RowKey`. Například: `$filter=LastName eq 'Jones'`.  
* Dotazy služby Azure Table Storage, které vracejí více entit, je seřadí v `PartitionKey` a `RowKey` pořadí. Chcete-li se vyhnout vyřazování entit v klientovi, vyberte `RowKey`, který definuje nejběžnější pořadí řazení. Výsledky dotazu vrácené službou Azure rozhraní API pro tabulky v Azure Cosmos DB nejsou seřazené podle klíče oddílu nebo klíče řádku. Podrobný seznam rozdílů funkcí najdete v tématu [rozdíly mezi rozhraní API pro tabulky v Azure Cosmos DB a v úložišti tabulek Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Použití "**nebo**" k určení filtru na základě `RowKey` hodnoty má za následek kontrolu oddílu a není považována za dotaz na rozsah. Proto se vyhněte dotazům, které používají filtry jako: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`.  

Příklady kódu na straně klienta, který používá klientskou knihovnu pro úložiště ke spouštění efektivních dotazů, najdete v těchto tématech:  

* [Spustit dotaz typu Point pomocí klientské knihovny pro úložiště](#run-a-point-query-by-using-the-storage-client-library)
* [Načtení více entit pomocí LINQ](#retrieve-multiple-entities-by-using-linq)
* [Projekce na straně serveru](#server-side-projection)  

Příklady kódu na straně klienta, který dokáže zpracovat víc typů entit, které jsou uloženy ve stejné tabulce naleznete v tématu:  

* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Zvolit vhodný `PartitionKey`
Vaše volba `PartitionKey` by měla vyrovnávat nutnost použití EGTs (pro zajištění konzistence) s požadavkem na distribuci entit napříč více oddíly (aby se zajistilo škálovatelné řešení).  

V jednom krajním případě můžete všechny entity ukládat do jednoho oddílu. To ale může omezit škálovatelnost vašeho řešení a zabránit tomu, aby úložiště tabulek mohlo umožňovat vyrovnávání zatížení požadavků. V ostatních extrémních případech můžete ukládat jednu entitu na oddíl. To je vysoce škálovatelné a umožňuje úložišti tabulek vyrovnávat zatížení požadavků, ale zabraňuje vám v použití transakcí skupin entit.  

Ideální `PartitionKey` umožňuje používat efektivní dotazy a má dostatečné oddíly pro zajištění škálovatelnosti vašeho řešení. Obvykle zjistíte, že vaše entity budou mít vhodnou vlastnost, která distribuuje vaše entity mezi dostatečné oddíly.

> [!NOTE]
> Například v systému, který ukládá informace o uživatelích nebo zaměstnaních, může být `UserID` dobrým `PartitionKey`. Je možné, že máte několik entit, které jako klíč oddílu používají konkrétní `UserID`. Každá entita, která ukládá data o uživateli, je seskupena do jednoho oddílu. Tyto entity jsou přístupné prostřednictvím EGTs, zatímco jsou pořád vysoce škálovatelné.
> 
> 

Existují další okolnosti, které si vyberete `PartitionKey`, které se týkají způsobu vkládání, aktualizace a odstraňování entit. Další informace najdete v tématu [Návrh pro úpravu dat](#design-for-data-modification) dále v tomto článku.  

### <a name="optimize-queries-for-table-storage"></a>Optimalizace dotazů pro úložiště tabulek
Table Storage automaticky indexuje vaše entity pomocí hodnot `PartitionKey` a `RowKey` v jednom seskupeném indexu. Důvodem je to, že dotazy na daný bod jsou nejúčinnější pro použití. Neexistují však žádné indexy jiné než v clusterovaných indexech `PartitionKey` a `RowKey`.

Řada návrhů musí splňovat požadavky na povolení vyhledávání entit na základě několika kritérií. Například vyhledání entit zaměstnanců na základě e-mailu, ID zaměstnance nebo příjmení. Následující vzory [vzorů návrhu tabulek](#table-design-patterns) řeší tyto typy požadavků. Vzory také popisují způsob práce se skutečností, že služba Table Storage neposkytuje sekundární indexy.  

* [Vzor sekundárního indexu v rámci oddílu](#intra-partition-secondary-index-pattern): uložte více kopií každé entity pomocí různých hodnot `RowKey` (ve stejném oddílu). Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých hodnot `RowKey`.  
* [Vzor sekundárního indexu mezi oddíly](#inter-partition-secondary-index-pattern): uložte více kopií každé entity pomocí různých `RowKey`ch hodnot v samostatných oddílech nebo v samostatných tabulkách. Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých hodnot `RowKey`.  
* [Index entity model](#index-entities-pattern): Udržujte entity indexu, aby bylo možné efektivně vyhledávat, které vracejí seznam entit.  

### <a name="sort-data-in-table-storage"></a>Řazení dat v úložišti tabulek

Table Storage vrátí výsledky dotazu seřazené vzestupně podle `PartitionKey` a potom podle `RowKey`.

> [!NOTE]
> Výsledky dotazu vrácené službou Azure rozhraní API pro tabulky v Azure Cosmos DB nejsou seřazené podle klíče oddílu nebo klíče řádku. Podrobný seznam rozdílů funkcí najdete v tématu [rozdíly mezi rozhraní API pro tabulky v Azure Cosmos DB a v úložišti tabulek Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Klíče v úložišti tabulek jsou řetězcové hodnoty. Chcete-li zajistit, aby číselné hodnoty byly správně řazeny, je nutné je převést na pevnou délku a doplnit je nulami. Pokud například hodnota ID zaměstnance, kterou použijete jako `RowKey`, je celočíselná hodnota, měli byste převést ID zaměstnance **123** na **00000123**. 

Mnoho aplikací mají požadavky na používání dat v různém pořadí řazení: podle názvu nebo díky připojení ke službě data, například řazení zaměstnanci. Následující vzory [vzorů návrhu tabulek](#table-design-patterns) popisují, jak alternativní objednávky řazení pro vaše entity:  

* [Vzor sekundárního indexu v rámci oddílu](#intra-partition-secondary-index-pattern): uložte více kopií každé entity pomocí různých hodnot `RowKey` (ve stejném oddílu). Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých hodnot `RowKey`.  
* [Vzor sekundárního indexu mezi oddíly](#inter-partition-secondary-index-pattern): uložte více kopií každé entity pomocí různých `RowKey`ch hodnot v samostatných oddílech v samostatných tabulkách. Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých hodnot `RowKey`.
* [Vzor](#log-tail-pattern)odchodu protokolu: načte entity *n* naposledy přidané do oddílu pomocí `RowKey` hodnoty, která se seřadí v pořadí podle data a času.  

## <a name="design-for-data-modification"></a>Návrh pro úpravu dat
Tato část se zaměřuje na aspekty návrhu pro optimalizaci vložení, aktualizace a odstranění. V některých případech budete muset vyhodnotit kompromis mezi návrhy, které se optimalizují pro dotazování na návrhy, které optimalizují úpravu dat. Toto hodnocení je podobné jako v návrzích pro relační databáze (i když postupy pro správu kompromisů v návrhu se liší v relační databázi). [Vzory návrhu tabulek](#table-design-patterns) oddílu popisují některé podrobné vzory návrhu pro úložiště tabulek a zvýrazňují některé z těchto kompromisů. V praxi zjistíte, že mnoho návrhů, které jsou optimalizované pro dotazování entit, také dobře fungují pro úpravy entit.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optimalizace výkonu operací vložení, aktualizace a odstranění
Aby bylo možné entitu aktualizovat nebo odstranit, je nutné ji identifikovat pomocí hodnot `PartitionKey` a `RowKey`. V tomto ohledu si můžete zvolit `PartitionKey` a `RowKey` pro úpravu entit by se měla řídit podobnými kritérii pro dotazy na bod podpory. Chcete co nejefektivnější identifikaci entit. Nechcete vyhledat entitu, aby bylo možné zjistit `PartitionKey` a `RowKey` hodnoty, které potřebujete k aktualizaci nebo odstranění.  

Následující vzory [vzorů návrhu tabulek](#table-design-patterns) řeší optimalizaci výkonu operací vložení, aktualizace a odstranění:  

* [Vzor vysokého odstranění svazků](#high-volume-delete-pattern): umožňuje odstranit velké objemy entit uložením všech entit pro současné odstranění ve vlastní samostatné tabulce. Odstraňte entity odstraněním tabulky.  
* [Vzor datových řad](#data-series-pattern): uložte kompletní datové řady do jedné entity, abyste minimalizovali počet požadavků, které provedete.  
* [Model nejrůznějších entit](#wide-entities-pattern): k ukládání logických entit s více než 252 vlastnostmi použijte více fyzických entit.  
* [Vzor velkých entit](#large-entities-pattern): k ukládání velkých vlastností použijte úložiště objektů BLOB.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Zajištění konzistence uložených entit
Klíčovým faktorem, který ovlivňuje podle vašeho výběru klíče pro optimalizaci změny dat je jak zajistit soulad s použitím atomické transakce. Můžete použít pouze EGT pracovat u entit se ukládají do stejného oddílu.  

Následující modely v sekcích [vzory návrhu tabulek](#table-design-patterns) řeší konzistenci správy:  

* [Vzor sekundárního indexu v rámci oddílu](#intra-partition-secondary-index-pattern): uložte více kopií každé entity pomocí různých hodnot `RowKey` (ve stejném oddílu). Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých hodnot `RowKey`.  
* [Vzor sekundárního indexu mezi oddíly](#inter-partition-secondary-index-pattern): uložte více kopií každé entity pomocí různých `RowKey`ch hodnot v samostatných oddílech nebo v samostatných tabulkách. Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých hodnot `RowKey`.  
* [Vzor nakonec konzistentních transakcí](#eventually-consistent-transactions-pattern): umožňuje zajistit trvalé konzistentní chování napříč hranicemi oddílů nebo systémem úložiště pomocí front Azure.
* [Index entity model](#index-entities-pattern): Udržujte entity indexu, aby bylo možné efektivně vyhledávat, které vracejí seznam entit.  
* [Vzorek denormalizace](#denormalization-pattern): kombinovat související data společně v jedné entitě, abyste mohli načíst všechna potřebná data pomocí dotazu s jedním bodem.  
* [Vzor datových řad](#data-series-pattern): uložte kompletní datové řady do jedné entity, abyste minimalizovali počet požadavků, které provedete.  

Další informace najdete v tématu [transakce skupin entit](#entity-group-transactions) dále v tomto článku.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Ujistěte se, že návrh efektivních úprav usnadňuje efektivní dotazy.
V mnoha případech by měl návrh pro efektivní dotazování výsledků v efektivní úpravy, ale vždy vyhodnoťte, jestli to platí pro váš konkrétní scénář. Některé ze vzorů v [vzorcích pro navrhování tabulek](#table-design-patterns) jsou explicitně vyhodnoceny kompromisy mezi dotazování a úpravou entit a měli byste vždycky vzít v úvahu počet jednotlivých typů operací.  

Následující vzory [vzorů návrhu tabulek](#table-design-patterns) řeší kompromisy mezi návrhem pro efektivní dotazy a návrhem pro efektivní úpravu dat:  

* [Vzor složeného klíče](#compound-key-pattern): pomocí složených hodnot `RowKey` můžete klientovi povolit, aby vyhledal související data pomocí dotazu s jedním bodem.  
* [Vzor](#log-tail-pattern)odchodu protokolu: načte entity *n* naposledy přidané do oddílu pomocí `RowKey` hodnoty, která se seřadí v pořadí podle data a času.  

## <a name="encrypt-table-data"></a>Šifrovat data tabulky
Klientská knihovna Azure Storage .NET podporuje šifrování vlastností entity řetězce pro operace INSERT a nahrazování. Šifrované řetězce jsou uloženy ve službě jako binární vlastnosti a jsou převedeny zpět na řetězce po dešifrování.    

Pro tabulky, vedle zásady šifrování musí uživatelé zadat vlastnosti, které mají být šifrována. Buď zadejte atribut `EncryptProperty` (pro entity POCO odvozené od `TableEntity`), nebo v možnostech žádosti zadejte překladač šifrování. Překladač šifrování je delegát, který převezme klíč oddílu, klíč řádku a název vlastnosti a vrátí logickou hodnotu, která označuje, jestli má být tato vlastnost zašifrovaná. Při šifrování používá Klientská knihovna tyto informace k rozhodnutí, jestli má být při zápisu do tohoto drátu zašifrovaná vlastnost. Delegát také poskytuje možnost logiky po tom, jak jsou zašifrované vlastnosti. (Například pokud X, pak Šifrujte vlastnost A; jinak Zašifrujte vlastnosti a a B.) Při čtení nebo dotazování entit není nutné tyto informace zadávat.

Sloučení není aktuálně podporováno. Vzhledem k tomu, že podmnožina vlastností mohla být předtím zašifrovaná pomocí jiného klíče, stačí sloučit nové vlastnosti a aktualizace metadat bude mít za následek ztrátu dat. Slučování vyžaduje, aby dodatečná volání služby přečetla existující entita ze služby nebo používala nový klíč na vlastnost. Ani jedna z těchto možností není vhodná z důvodů výkonu.     

Informace o šifrování dat tabulky najdete v tématu [šifrování na straně klienta a Azure Key Vault pro Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Vztahy modelů
Vytváření modelů domény je klíče krokem návrhu komplexních systémů. Obvykle se používá proces modelování k identifikaci entit a vztahů mezi nimi jako způsob pochopení obchodní domény a informování o návrhu systému. Tato část se zaměřuje na to, jak můžete přeložit některé běžné typy vztahů nalezené v doménových modelech na návrhy pro úložiště tabulek. Proces mapování z logického datového modelu na fyzický datový model založený na NoSQL se liší od toho, který se používá při navrhování relační databáze. Návrh relačních databází obvykle předpokládá proces normalizace dat optimalizovaný pro minimalizaci redundance. Tento návrh také předpokládá deklarativní schopnost dotazování, která abstrakce implementaci způsobu fungování databáze.  

### <a name="one-to-many-relationships"></a>Vztah jeden mnoho
1 n vztahy mezi objekty obchodní domény docházet často: například jeden oddělení má mnoho zaměstnanců. Existuje několik způsobů implementace vztahů 1: n v úložišti tabulek, z nichž každá má výhody a nevýhody, které mohou být relevantní pro konkrétní scénář.  

Vezměte v úvahu příklad velké nadnárodní společnosti s desítkami tisíců oddělení a entit zaměstnanců. Každé oddělení má mnoho zaměstnanců a každý zaměstnanec je přidružený k jednomu konkrétnímu oddělení. Jedním z možností je ukládat samostatné entity oddělení a zaměstnanců, například následující:  

![Obrázek znázorňující entitu oddělení a entitu zaměstnance][1]

Tento příklad ukazuje implicitní vztah 1:1 mezi typy na základě hodnoty `PartitionKey`. Každé oddělení může mít mnoho zaměstnanců.  

Tento příklad také uvádí oddělení entity a její související zaměstnance entity do stejného oddílu. Pro různé typy entit se můžete rozhodnout pro použití různých oddílů, tabulek nebo dokonce účtů úložiště.  

Alternativním řešením je denormalizovat data a ukládat pouze entity zaměstnanců s denormalizovanými daty oddělení, jak je znázorněno v následujícím příkladu. V tomto konkrétním scénáři nemusí být tento denormalizovaný přístup nejlepší, pokud máte požadavek, abyste mohli změnit podrobnosti o manažerovi oddělení. K tomu je potřeba aktualizovat každého zaměstnance v oddělení.  

![Obrázek entity zaměstnance][2]

Další informace najdete v tématu [Denormalizace vzor](#denormalization-pattern) dále v tomto průvodci.  

Následující tabulka shrnuje odborníky a nevýhody jednotlivých přístupů k ukládání entit zaměstnanců a oddělení, které mají vztah 1: n. Měli byste taky zvážit, jak často očekáváte provádění různých operací. Může být přijatelné, aby měl návrh, který zahrnuje náročnou operaci, pokud tato operace proběhne jenom zřídka.  

<table>
<tr>
<th>Přístup</th>
<th>V oblasti IT</th>
<th>Nevýhody</th>
</tr>
<tr>
<td>Typy entit, stejného oddílu, stejná tabulka</td>
<td>
<ul>
<li>Oddělení entity můžete aktualizovat pomocí jedné operace.</li>
<li>Můžete použít EGT můžete zachovat konzistenci, pokud máte požadavek na Upravit entity oddělení pokaždé, když je aktualizace, vložení nebo odstranění entity zaměstnance. Například, pokud chcete zachovat počet zaměstnanců oddělení pro každé oddělení.</li>
</ul>
</td>
<td>
<ul>
<li>Možná budete muset pro některé aktivity klienta načíst jak zaměstnance, tak i entitu oddělení.</li>
<li>Operace úložiště stát do stejného oddílu. Ve velkém objemu transakcí může to mít za následek aktivní hotspot.</li>
<li>Zaměstnance nemůžete přesunout do nového oddělení pomocí EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Typy entit, různých oddílů, nebo tabulky nebo účty úložiště</td>
<td>
<ul>
<li>Oddělení entity nebo zaměstnance entity můžete aktualizovat pomocí jedné operace.</li>
<li>Na vysoce transakčních svazcích to může přispět k rozložení zatížení napříč více oddíly.</li>
</ul>
</td>
<td>
<ul>
<li>Možná budete muset pro některé aktivity klienta načíst jak zaměstnance, tak i entitu oddělení.</li>
<li>EGTs se nedá použít k zachování konzistence při aktualizaci, vložení nebo odstranění zaměstnance a aktualizaci oddělení. Aktualizuje se například počet zaměstnanců v entitě oddělení.</li>
<li>Zaměstnance nemůžete přesunout do nového oddělení pomocí EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalizovat do jedné entity typu</td>
<td>
<ul>
<li>Můžete načíst všechny informace, které potřebujete, prostřednictvím jednoho požadavku.</li>
</ul>
</td>
<td>
<ul>
<li>Pokud potřebujete aktualizovat informace o odděleních, může být finančně zaručena konzistence. (to by vyžadovalo, abyste aktualizovali všechny zaměstnance v oddělení).</li>
</ul>
</td>
</tr>
</table>

Jak si zvolíte mezi těmito možnostmi, které jsou nejdůležitější pro odborníky a nevýhody, závisí na konkrétních scénářích aplikací. Například jak často měníte entity oddělení? Budou všechny dotazy zaměstnanců potřebovat další informace oddělení? Jak blízko máte omezení škálovatelnosti pro vaše oddíly nebo účet úložiště?  

### <a name="one-to-one-relationships"></a>Relace 1: 1
Doménové modely můžou mezi entitami zahrnovat relace 1:1. Pokud potřebujete v úložišti tabulek implementovat relaci 1:1, musíte také zvolit, jak propojit tyto dvě související entity, když je potřebujete načítat. Tento odkaz může být buď implicitní, na základě konvence v hodnotách klíče, nebo explicitní, uložením odkazu ve formě `PartitionKey` a `RowKey` hodnot v každé entitě na související entitu. Informace o tom, jestli byste měli uložit související entity do stejného oddílu, naleznete v části [jeden mnoho relací](#one-to-many-relationships).  

K dispozici jsou také pokyny k implementaci, které by vám mohly vést k implementaci relací 1:1 v tabulce úložiště:  

* Zpracování velkých entit (Další informace najdete v tématu [vzor velkých entit](#large-entities-pattern)).  
* Implementace ovládacích prvků přístupu (Další informace najdete v tématu [řízení přístupu pomocí sdílených přístupových podpisů](#control-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Připojte se k v klientovi
I když existují způsoby modelování vztahů v úložišti tabulek, nezapomeňte, že dva hlavní důvody pro použití služby Table Storage jsou škálovatelnost a výkon. Pokud zjistíte, že pracujete s modelem mnoha vztahů, které ohrožují výkon a škálovatelnost vašeho řešení, měli byste se dotazovat, pokud je nutné sestavit všechny relace dat do návrhu tabulky. Je možné, že budete moci zjednodušit návrh a zlepšit škálovatelnost a výkon vašeho řešení, pokud povolíte, aby klientská aplikace prováděla potřebná spojení.  

Například pokud máte malé tabulky obsahující data, která se nemění často, můžete tato data načíst jednou a uložit je do mezipaměti v klientovi. To se můžete vyhnout opakované výměn dat k načtení stejná data. V příkladech, které jsme prohlédli v tomto průvodci, se sada oddělení v malé organizaci bude pravděpodobně malým a často mění. Díky tomu je vhodným kandidátem na data, která může klientská aplikace Stáhnout, a uložit je jako data vyhledávání.  

### <a name="inheritance-relationships"></a>Vztahy dědičnosti
Pokud vaše klientská aplikace používá sadu tříd, které tvoří část vztahu dědičnosti k reprezentaci obchodních entit, můžete tyto entity snadno uchovat v úložišti tabulek. Například můžete mít následující sadu tříd definované v klientské aplikaci, kde `Person` je abstraktní třída.

![Diagram vztahů dědičnosti][3]

Instance dvou konkrétních tříd v úložišti tabulek můžete zachovat pomocí jedné `Person` tabulky. Použijte entity, které vypadají jako následující:  

![Obrázek znázorňující entitu zákazníka a zaměstnance][4]

Další informace o práci s více typy entit ve stejné tabulce v kódu klienta najdete v části [práce s heterogenními typy entit](#work-with-heterogeneous-entity-types) dále v této příručce. To poskytuje příklady toho, jak rozpoznat typ entity v klientském kódu.  

## <a name="table-design-patterns"></a>Způsoby návrhu tabulek
V předchozích částech jste se dozvěděli o tom, jak optimalizovat návrh tabulky pro načítání dat entity pomocí dotazů, a pro vkládání, aktualizaci a odstraňování dat entit. Tato část popisuje některé vzory vhodné pro použití s tabulkovým úložištěm. Kromě toho se dozvíte, jak můžete prakticky vyřešit některé problémy a kompromisy, které byly vyvolány dříve v této příručce. Následující diagram shrnuje vztahy mezi různými vzory:  

![Diagram vzorů návrhu tabulky][5]

Mapa vzorů zvýrazňuje některé vztahy mezi vzory (modré) a antipatterns (oranžová), které jsou popsány v tomto průvodci. Jsou samozřejmě mnoha způsoby, které stojí za zvážení. Jedním z klíčových scénářů pro úložiště tabulek je například použití [schématu materializované zobrazení](https://msdn.microsoft.com/library/azure/dn589782.aspx) ze vzoru [oddělení zodpovědnosti dotazu příkazu](https://msdn.microsoft.com/library/azure/jj554200.aspx) .  

### <a name="intra-partition-secondary-index-pattern"></a>Model sekundárních indexů uvnitř oddílu
Ukládat více kopií každé entity pomocí různých hodnot `RowKey` (ve stejném oddílu). Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých hodnot `RowKey`. Aktualizace mezi kopiemi se můžou uchovávat konzistentně pomocí EGTs.  

#### <a name="context-and-problem"></a>Kontext a problém
Table Storage automaticky indexuje entity pomocí `PartitionKey` a `RowKey`ch hodnot. To umožňuje klientské aplikaci efektivně načíst entitu pomocí těchto hodnot. Například pomocí následující struktury tabulek může klientská aplikace použít dotaz na bod k načtení konkrétní entity zaměstnance pomocí názvu oddělení a ID zaměstnance (`PartitionKey` a `RowKey` hodnoty). Klient může také načíst entity seřazené podle ID zaměstnance v rámci každého oddělení.

![Obrázek entity zaměstnance][6]

Pokud chcete také vyhledat entitu zaměstnance na základě hodnoty jiné vlastnosti, jako je například e-mailová adresa, je třeba použít méně efektivní kontrolu oddílů a vyhledat shodu. Důvodem je to, že služba Table Storage neposkytuje sekundární indexy. Kromě toho není k dispozici možnost požádat o seznam zaměstnanců seřazených v jiném pořadí, než `RowKey` objednávka.  

#### <a name="solution"></a>Řešení
Chcete-li obejít nedostatek sekundárních indexů, můžete uložit více kopií každé entity s každou kopií s použitím jiné hodnoty `RowKey`. Pokud uložíte entitu s následujícími strukturami, můžete efektivně načíst entity zaměstnanců na základě e-mailové adresy nebo ID zaměstnance. Hodnoty předpony pro `RowKey`, `empid_`a `email_` umožňují dotazovat se na jednoho zaměstnance nebo na určitou škálu zaměstnanců pomocí rozsahu e-mailových adres nebo ID zaměstnanců.  

![Obrázek znázorňující entitu zaměstnance s různými RowKey hodnotami][7]

Následující dvě kritéria filtru (jedno vyhledávání podle ID zaměstnance a jedna z nich hledají e-mailovou adresu) určují obě dotazy na bod:  

* $filter = (PartitionKey eq "Prodeje") a (RowKey eq "empid_000223")  
* $filter = (PartitionKey eq "Prodeje") a (RowKey eq 'email_jonesj@contoso.com")  

Pokud se dotazuje na rozsah entit zaměstnanců, můžete určit rozsah seřazený v pořadí podle ID zaměstnance nebo rozsah seřazený v e-mailové adrese. Dotaz na entity s příslušnou předponou v `RowKey`.  

* Chcete-li najít všechny zaměstnance v rámci prodejního oddělení s ID zaměstnance v rozsahu 000100 až 000199, použijte: $filter = (PartitionKey EQ ' Sales ') a (RowKey GE ' empid_000100 ') a (RowKey Le ' empid_000199 ')  
* Chcete-li najít všechny zaměstnance v oddělení Sales s e-mailovou adresou začínající písmenem "a", použijte: $filter = (PartitionKey EQ ' Sales ') a (RowKey GE ' email_a ') a (RowKey lt ' email_b ')  
  
Syntaxe filtru použitá v předchozích příkladech je z tabulkového úložiště REST API. Další informace najdete v tématu věnovaném [dotazování entit](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Úložiště tabulek je poměrně levné na použití, takže náklady na ukládání duplicitních dat by neměly být zásadním problémem. Měli byste ale vždycky vyhodnotit náklady na váš návrh na základě předpokládaných požadavků na úložiště a přidávat duplicitní entity jenom na podporu dotazů, které vaše klientská aplikace spustí.  
* Vzhledem k tomu, že se entity sekundárního indexu ukládají do stejného oddílu jako původní entity, ujistěte se, že nepřekračujete cíle škálovatelnosti pro jednotlivé oddíly.  
* Abyste mohli duplicitní položky konzistentní mezi sebou pomocí EGTs atomicky aktualizovat dvě kopie entity. Z toho vyplývá, že byste měli uložit všechny kopie entity do stejného oddílu. Další informace najdete v tématu [použití transakcí skupin entit](#entity-group-transactions).  
* Hodnota použitá pro `RowKey` musí být jedinečná pro každou entitu. Zvažte použití složené klíčové hodnoty.  
* Vyplňování číselných hodnot v `RowKey` (například zaměstnanci s ID 000223) umožňuje správné řazení a filtrování na základě horních a dolních mezí.  
* Nemusíte nutně Duplikovat všechny vlastnosti vaší entity. Například pokud dotazy, které hledají entity pomocí e-mailové adresy v `RowKey` nikdy nepotřebují věk zaměstnance, můžou mít následující strukturu:

  ![Obrázek entity zaměstnance][8]

* Obvykle je lepší ukládat duplicitní data a zajistit, že všechna data, která potřebujete, můžete načíst pomocí jediného dotazu, než můžete použít jeden dotaz k vyhledání entity a jiné pro vyhledání požadovaných dat.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v těchto případech:

- Klientská aplikace musí načítat entity pomocí různých klíčů.
- Váš klient potřebuje načíst entity v různých objednávkách řazení.
- Každou entitu můžete identifikovat pomocí nejrůznějších jedinečných hodnot.

Ujistěte se však, že při provádění vyhledávání entit nepřekračujete omezení škálovatelnosti oddílu pomocí různých hodnot `RowKey`.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Model mezi oddílu sekundárních indexů](#inter-partition-secondary-index-pattern)
* [Složené sekvence klíče](#compound-key-pattern)
* [Transakce skupiny entit](#entity-group-transactions)
* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Model mezi oddílu sekundárních indexů
Pomocí různých hodnot `RowKey` v samostatných oddílech nebo v samostatných tabulkách uložte několik kopií každé entity. Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých hodnot `RowKey`.  

#### <a name="context-and-problem"></a>Kontext a problém
Table Storage automaticky indexuje entity pomocí `PartitionKey` a `RowKey`ch hodnot. To umožňuje klientské aplikaci efektivně načíst entitu pomocí těchto hodnot. Například pomocí následující struktury tabulek může klientská aplikace použít dotaz na bod k načtení konkrétní entity zaměstnance pomocí názvu oddělení a ID zaměstnance (`PartitionKey` a `RowKey` hodnoty). Klient může také načíst entity seřazené podle ID zaměstnance v rámci každého oddělení.  

![Obrázek entity zaměstnance][9]

Pokud chcete také moct vyhledat entitu zaměstnanci založena na hodnotě jiné vlastnosti, jako jsou e-mailovou adresu, musíte použít méně efektivní prohledávání oddílu pro vyhledání shody. Důvodem je to, že služba Table Storage neposkytuje sekundární indexy. Kromě toho není k dispozici možnost požádat o seznam zaměstnanců seřazených v jiném pořadí, než `RowKey` objednávka.  

Očekáváte velký objem transakcí na těchto entitách a chcete minimalizovat riziko, že míra úložiště tabulek omezuje vaše klienta.  

#### <a name="solution"></a>Řešení
Pokud chcete obejít nedostatku sekundárních indexů, můžete ukládat víc kopií každé entity s každým kopírováním pomocí různých `PartitionKey` a `RowKey`ch hodnot. Pokud uložíte entitu s následujícími strukturami, můžete efektivně načíst entity zaměstnanců na základě e-mailové adresy nebo ID zaměstnance. Hodnoty předpony pro `PartitionKey`, `empid_`a `email_` umožňují určit, který index chcete použít pro dotaz.  

![Obrázek znázorňující entitu zaměstnance s primárním indexem a entitou zaměstnance se sekundárním indexem][10]

Následující dvě kritéria filtru (jedno vyhledávání podle ID zaměstnance a jedna z nich hledají e-mailovou adresu) určují obě dotazy na bod:  

* $filter = (PartitionKey eq ' empid_Sales") a (RowKey eq"000223")
* $filter = (PartitionKey eq ' email_Sales") a (RowKey eq 'jonesj@contoso.com")  

Pokud se dotazuje na rozsah entit zaměstnanců, můžete určit rozsah seřazený v pořadí podle ID zaměstnance nebo rozsah seřazený v e-mailové adrese. Dotaz na entity s příslušnou předponou v `RowKey`.  

* Chcete-li najít všechny zaměstnance v rámci prodejního oddělení s ID zaměstnance v rozsahu **000100** až **000199**, seřazené v pořadí ID zaměstnanců, použijte: $Filter = (PartitionKey EQ ' empid_Sales ') a (RowKey GE ' 000100 ') a (RowKey Le ' 000199 ')  
* Chcete-li najít všechny zaměstnance v prodejním oddělení pomocí e-mailové adresy, která začíná na "a" seřazené v e-mailové adrese, použijte: $filter = (PartitionKey EQ ' email_Sales ') a (RowKey GE ' a ') a (RowKey lt ' b ')  

Všimněte si, že syntaxe filtru použitá v předchozích příkladech je z tabulkového úložiště REST API. Další informace najdete v tématu věnovaném [dotazování entit](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Můžete ponechat duplicitní položky konzistentní mezi sebou pomocí [konečnou konzistenci transakcí vzor](#eventually-consistent-transactions-pattern) udržovat entity primárních a sekundárních indexů.  
* Úložiště tabulek je poměrně levné na použití, takže náklady spojené s ukládáním duplicitních dat by neměly být zásadním problémem. Na základě předpokládaných požadavků na úložiště ale vždy vyhodnoťte náklady na váš návrh a přidejte duplicitní entity, které budou podporovat dotazy, které klientská aplikace spustí.  
* Hodnota použitá pro `RowKey` musí být jedinečná pro každou entitu. Zvažte použití složené klíčové hodnoty.  
* Vyplňování číselných hodnot v `RowKey` (například zaměstnanci s ID 000223) umožňuje správné řazení a filtrování na základě horních a dolních mezí.  
* Nemusíte nutně Duplikovat všechny vlastnosti vaší entity. Například pokud dotazy, které hledají entity pomocí e-mailové adresy v `RowKey` nikdy nepotřebují věk zaměstnance, můžou mít následující strukturu:
  
  ![Obrázek znázorňující entitu zaměstnance se sekundárním indexem][11]
* Obvykle je lepší ukládat duplicitní data a zajistit, abyste mohli načíst všechna potřebná data s jediným dotazem, než použijete jeden dotaz k vyhledání entity pomocí sekundárního indexu a další pro vyhledání požadovaných dat v primárním indexu.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v těchto případech:

- Klientská aplikace musí načítat entity pomocí různých klíčů.
- Váš klient potřebuje načíst entity v různých objednávkách řazení.
- Každou entitu můžete identifikovat pomocí nejrůznějších jedinečných hodnot.

Tento model použijte, pokud chcete vyhnout překročení omezení škálovatelnosti oddílu při provádění vyhledávání entit pomocí různých hodnot `RowKey`.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern)  
* [Model sekundárních indexů uvnitř oddílu](#intra-partition-secondary-index-pattern)  
* [Složené sekvence klíče](#compound-key-pattern)  
* [Transakce skupiny entit](#entity-group-transactions)  
* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Vzor konečnou konzistenci transakcí
Pomocí front Azure umožňuje konzistentní chování napříč hranice oddílů nebo systému hranice úložiště.  

#### <a name="context-and-problem"></a>Kontext a problém
EGTs umožňuje atomické transakce napříč více entit, které sdílejí stejný klíč oddílu. V zájmu výkonu a škálovatelnosti se můžete rozhodnout ukládat entity, které mají požadavky na konzistenci v samostatných oddílech nebo v samostatném systému úložiště. V takovém případě nemůžete použít EGTs k zachování konzistence. Například můžete mít povinnost udržovat konečnou konzistenci mezi:  

* Entity uložená ve dvou různých oddílů ve stejné tabulce, různých tabulek nebo jiný účet úložiště.  
* Entita uložená v úložišti tabulek a objekt BLOB uložený v úložišti objektů BLOB  
* Entita uložená v úložišti tabulek a v souboru v systému souborů.  
* Entita uložená v úložišti tabulek, která je ještě indexována pomocí Azure Kognitivní hledání.  

#### <a name="solution"></a>Řešení
Pomocí front Azure můžete implementovat řešení, které zajišťuje konečnou konzistenci napříč dvěma nebo více oddílů nebo úložných systémů.

Pro ilustraci tohoto přístupu Předpokládejme, že máte požadavek na to, abyste mohli archivovat bývalé entity zaměstnanců. Bývalé entity zaměstnanců se dotazují zřídka a měly by být vyloučeny ze všech aktivit, které se týkají současných zaměstnanců. K implementaci tohoto požadavku ukládáte aktivní zaměstnance do **aktuální** tabulky a bývalé zaměstnance v **archivní** tabulce. Archivace zaměstnance vyžaduje, abyste odstranili entitu z **aktuální** tabulky a přidali entitu do **archivní** tabulky.

K provádění těchto dvou operací ale nemůžete použít EGT. Aby nevznikalo riziko, způsobující selhání entity se zobrazí v obou nebo ani jedno z těchto tabulek, musí být operace archivování konečnou konzistenci. Následující sekvence diagramu popisuje kroky v této operaci.  

![Diagram řešení pro konečné konzistence][12]

Klient inicializuje operaci archivu tím, že umístí zprávu do fronty Azure (v tomto příkladu pro archivaci #456 zaměstnanců). Role pracovního procesu dotazuje fronty na nové zprávy. Po nalezení znaku jednu, přečte zprávu a ponechá skrytá kopie ve frontě. Role pracovního procesu načítá další kopie entity z **aktuální** tabulky, vloží kopii **archivu** tabulku a pak odstraní původní z **aktuální** tabulky. Nakonec pokud nebyly zjištěny žádné chyby v předchozích krocích, role pracovního procesu odstraní skrytou zprávu z fronty.  

V tomto příkladu krok 4 v diagramu vloží zaměstnance do **archivní** tabulky. Může přidat zaměstnance do objektu BLOB v úložišti objektů BLOB nebo v souboru v systému souborů.  

#### <a name="recover-from-failures"></a>Obnovit po selhání
Je důležité, aby operace v krocích 4-5 v diagramu byly *idempotentní* v případě, že role pracovního procesu potřebuje restartovat operaci archivace. Pokud používáte úložiště Table, měli byste pro krok 4 použít operaci vložení nebo nahrazení; v kroku 5 byste měli použít operaci odstranit, pokud existuje, v klientské knihovně, kterou používáte. Pokud používáte jiný systém úložiště, je nutné použít příslušnou operaci idempotentní.  

Pokud role pracovního procesu nikdy nedokončí krok 6 v diagramu, potom po vypršení časového limitu se tato zpráva zobrazí ve frontě připravené pro roli pracovního procesu a pokusí se ji znovu zpracovat. Role pracovního procesu může kontrolovat počet čtení zprávy ve frontě a v případě potřeby ji označit jako "nezpracovatelnou" zprávu pro účely šetření odesláním do samostatné fronty. Další informace o čtení zpráv fronty a o kontrole počtu vyřazování z fronty najdete v tématu [Get Messages](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Některé chyby z tabulkového úložiště a úložiště front jsou přechodné chyby a vaše klientská aplikace by měla k jejich zpracování použít vhodnou logiku opakování.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení neposkytuje izolaci transakcí. Klient může například číst **aktuální** a **archivní** tabulky, pokud byla role pracovního procesu mezi kroky 4-5 v diagramu, a zobrazit nekonzistentní zobrazení dat. Nakonec bude konzistentní data.  
* Musíte mít jistotu, že kroky 4-5 jsou idempotentní, aby se zajistila konečná konzistence.  
* Řešení můžete škálovat s použitím více front a instancí rolí pracovního procesu.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, pokud chcete zajistit konečnou konzistenci mezi entitami, které existují v různých oddílů nebo tabulky. Tento model můžete roztáhnout, aby se zajistila konečná konzistence operací napříč tabulkovým úložištěm a úložištěm objektů BLOB a dalšími neAzure Storagemi zdroji dat, jako je databáze nebo systém souborů.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entit](#entity-group-transactions)  
* [Sloučení nebo nahradit](#merge-or-replace)  

> [!NOTE]
> Pokud je pro vaše řešení důležité oddělení transakcí, zvažte možnost změnit návrh tabulek, abyste mohli používat EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Vzor entit indexu
Udržujte index entity, které umožňují efektivní prohledávání, které vrací seznam entit.  

#### <a name="context-and-problem"></a>Kontext a problém
Table Storage automaticky indexuje entity pomocí `PartitionKey` a `RowKey`ch hodnot. To umožňuje klientské aplikaci efektivně načíst entitu pomocí dotazu na bod. Například pomocí následující struktury tabulek může klientská aplikace efektivně načíst jednotlivou entitu zaměstnance pomocí názvu oddělení a ID zaměstnance (`PartitionKey` a `RowKey`).  

![Obrázek entity zaměstnance][13]

Pokud chcete mít také přístup k seznamu entit zaměstnanců v závislosti na hodnotě jiné nejedinečné vlastnosti, jako je například příjmení, je nutné použít méně efektivní kontrolu oddílů. Tato kontrola vyhledá shodu a místo toho použije index k jejich přímému vyhledání. Důvodem je to, že služba Table Storage neposkytuje sekundární indexy.  

#### <a name="solution"></a>Řešení
Chcete-li povolit vyhledávání podle příjmení s předchozí strukturou entit, je nutné udržovat seznamy ID zaměstnanců. Pokud chcete načíst entity zaměstnanců s konkrétním názvem, jako je například Novotný, musíte nejprve vyhledat seznam ID zaměstnanců pro zaměstnance s názvem Novotný jako své příjmení a pak tyto entity zaměstnanců načíst. Existují tři hlavní možnosti ukládání seznamů ID zaměstnanců:  

* Použijte úložiště objektů BLOB.  
* Vytvořte index entity do stejného oddílu jako entity zaměstnance.  
* Vytvoření indexu entit v samostatném oddílu nebo tabulky.  

Možnost 1: použití úložiště objektů BLOB  

Vytvořte objekt BLOB pro každé jedinečné příjmení a v každém objektu BLOB uložte seznam `PartitionKey` (oddělení) a `RowKey` (ID zaměstnance) pro zaměstnance, kteří mají toto příjmení. Když zaměstnance přidáte nebo odstraníte, zajistěte, aby obsah relevantního objektu BLOB byl nakonec konzistentní s entitami zaměstnanců.  

Možnost 2: vytvoření entit indexu ve stejném oddílu  

Použijte entity indexů, které ukládají následující data:  

![Obrázek znázorňující entitu zaměstnance s řetězcem, který obsahuje seznam ID zaměstnanců se stejným posledním jménem][14]

Vlastnost `EmployeeIDs` obsahuje seznam ID zaměstnanců pro zaměstnance s posledním jménem uloženým v `RowKey`.  

Následující kroky popisují postup, který byste měli provést při přidávání nového zaměstnance. V tomto příkladu přidáme zaměstnance s ID 000152 a názvem Novotný do oddělení Sales:  

1. Načtěte indexovou entitu s hodnotou `PartitionKey` Sales a hodnotou `RowKey` "Novotný". Uložte ETag tuto entitu pro použití v kroku 2.  
2. Vytvoření transakce skupiny entit (tj. operace dávky), která vloží entitu New Employee (`PartitionKey` value "Sales" (prodej) a `RowKey` value "000152") a aktualizuje entitu indexu (hodnota`PartitionKey` prodej) a `RowKey` hodnota "Novák"). EGT to dělá přidáním nového ID zaměstnance do seznamu v poli ČísloZaměstnance. Další informace o EGTs najdete v tématu [transakce skupiny entit](#entity-group-transactions).  
3. Pokud se EGT nepovede kvůli optimistické chybě souběžnosti (to znamená, že entita indexu změnila někdo jiný), pak je potřeba začít znovu v kroku 1.  

Pokud používáte druhou možnost, můžete použít podobný přístup k odstranění zaměstnance. Změna příjmení zaměstnance je mírně složitější, protože potřebujete spustit EGT, který aktualizuje tři entity: entita zaměstnance, indexová entita pro staré příjmení a entitu indexu pro nové příjmení. Musíte načíst každou entitu před provedením jakýchkoli změn, aby bylo možné načíst hodnoty ETag, které pak můžete použít k provedení aktualizací pomocí optimistické souběžnosti.  

Následující kroky popisují postup, který byste měli provést, pokud potřebujete vyhledat všechny zaměstnance s určitým jménem v oddělení. V tomto příkladu prohledáváme všechny zaměstnance s názvem Novotný v oddělení Sales (prodej):  

1. Načtěte indexovou entitu s hodnotou `PartitionKey` Sales a hodnotou `RowKey` "Novotný".  
2. Analyzovat seznam ID zaměstnanců v poli `EmployeeIDs`.  
3. Pokud potřebujete další informace o každém z těchto zaměstnanců (například jejich e-mailové adresy), načtěte každou entitu zaměstnanců pomocí `PartitionKey` hodnoty "Sales" a `RowKey` hodnoty ze seznamu zaměstnanců, který jste získali v kroku 2.  

Možnost 3: vytvoření entit indexu v samostatném oddílu nebo tabulce  

Pro tuto možnost použijte entity indexů, které ukládají následující data:  

![Obrázek znázorňující entitu zaměstnance s řetězcem, který obsahuje seznam ID zaměstnanců se stejným posledním jménem][15]

Vlastnost `EmployeeIDs` obsahuje seznam ID zaměstnanců pro zaměstnance s posledním jménem uloženým v `RowKey`.  

EGTs se nedá použít k udržení konzistence, protože entity indexu jsou v samostatném oddílu od entit zaměstnanců. Zajistěte, aby entity indexu byly nakonec konzistentní s entitami zaměstnanců.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení vyžaduje aspoň dva dotazy, aby se načetly vyhovující entity: jeden pro dotazování entit indexu, aby získal seznam hodnot `RowKey`, a pak dotazy pro načtení všech entit v seznamu.  
* Vzhledem k tomu, že každá entita má maximální velikost 1 MB, možnost 2 a možnost 3 v řešení, předpokládá, že seznam ID zaměstnanců pro jakékoli konkrétní příjmení není nikdy větší než 1 MB. Pokud je seznam ID zaměstnanců pravděpodobně větší než 1 MB, použijte možnost 1 a uložte data indexu do úložiště objektů BLOB.  
* Pokud použijete možnost 2 (pomocí EGTs k obsluze přidávání a odstraňování zaměstnanců a změníte příjmení zaměstnance), musíte vyhodnotit, jestli se objem transakcí bude přicházet k omezením škálovatelnosti v konkrétním oddílu. Pokud se jedná o tento případ, měli byste zvážit vhodné řešení (možnost 1 nebo možnost 3). Tyto fronty slouží ke zpracování požadavků na aktualizace a umožňují ukládat entity indexu do samostatného oddílu od entit zaměstnanců.  
* Možnost 2 v tomto řešení předpokládá, že chcete hledat podle příjmení v rámci oddělení. Například chcete načíst seznam zaměstnanců s posledním názvem Novotný v oddělení Sales (prodej). Pokud chcete mít možnost Hledat všechny zaměstnance s posledním názvem Novotný v celé organizaci, použijte jednu z možností 1 nebo 3.
* Můžete implementovat řešení založené na frontách, které poskytuje konečnou konzistenci. Další podrobnosti najdete ve [vzoru nakonec konzistentní transakce](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že chcete vyhledat sadu entit, které budou sdílet všechny společné hodnoty vlastností, jako jsou všichni zaměstnanci s posledním názvem Novotný.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Složené sekvence klíče](#compound-key-pattern)  
* [Vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern)  
* [Transakce skupiny entit](#entity-group-transactions)  
* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Vzor denormalizace
Kombinování souvisejících dat společně v jedné entitě umožňuje načíst všechna data, které potřebujete, s jediným bodem dotazu.  

#### <a name="context-and-problem"></a>Kontext a problém
V relační databázi obvykle Normalizujte data pro odstranění duplicit, ke kterým dochází, když dotazy načítají data z více tabulek. Pokud jste normalizovat data do tabulek Azure, musíte udělat více výměn od klienta k serveru a související data načíst. Například s následující strukturou tabulky potřebujete dvě zpáteční cesty pro získání podrobností o oddělení. Jedna cesta načte entitu oddělení, která zahrnuje ID manažera, a druhá cesta načte podrobnosti správce v entitě zaměstnanec.  

![Grafika entity oddělení a entit zaměstnanců][16]

#### <a name="solution"></a>Řešení
Místo ukládání dat v rámci dvě samostatné entity, denormalizovat data a ponechat si kopii manažera podrobnosti v entitě oddělení. Příklad:  

![Obrázek denormalizované a kombinované entity oddělení][17]

S entitami oddělení uloženými s těmito vlastnostmi teď můžete načíst všechny podrobnosti, které potřebujete o oddělení, pomocí dotazu na bod.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Existuje nějaké náklady režie spojené s ukládáním některá data dvakrát. Výhoda výkonu vyplývající z menšího počtu požadavků na úložiště tabulek obvykle převažuje nad nárůstem nákladů na úložiště. Dále jsou tyto náklady částečně posunuty snížením počtu transakcí, které potřebujete k načtení podrobností o oddělení.  
* Konzistence dvě entity, které ukládají informace o správcích, musíte mít. Problém konzistence můžete zpracovat pomocí EGTs k aktualizaci více entit v jedné atomické transakci. V tomto případě se entita oddělení a entita zaměstnanci pro správce oddělení ukládají do stejného oddílu.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když je často potřeba vyhledat související informace. Tento model snižuje počet dotazů, které váš klient musí Ujistěte se, aby se načetla data, které vyžaduje.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Složené sekvence klíče](#compound-key-pattern)  
* [Transakce skupiny entit](#entity-group-transactions)  
* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Složené sekvence klíče
Pomocí složených `RowKey`ch hodnot můžete klientovi Povolit hledání souvisejících dat pomocí dotazu s jedním bodem.  

#### <a name="context-and-problem"></a>Kontext a problém
V relační databázi je použití spojení v dotazech k vrácení souvisejících částí dat klientovi v jednom dotazu. ID zaměstnance můžete například použít k vyhledání seznamu souvisejících entit, které obsahují údaje o výkonu a kontrole daného zaměstnance.  

Předpokládejme, že ukládáte entity zaměstnanců do úložiště tabulek pomocí následující struktury:  

![Obrázek entity zaměstnance][18]

Je také potřeba ukládat historická data týkající se kontrol a výkonu každého roku, který zaměstnanec pracoval pro vaši organizaci, a vy budete mít přístup k těmto informacím po rocích. Jednou z možností je vytvoření další tabulky, která ukládá entity s následující strukturou:  

![Obrázek entity kontroly zaměstnance][19]

S tímto přístupem se můžete rozhodnout duplikovat některé informace (například křestní jméno a příjmení) v nové entitě, abyste mohli data načíst pomocí jediného požadavku. Nemůžete ale zachovat silnou konzistenci, protože nemůžete použít EGT k tomu, aby se tyto dvě entity používaly atomicky.  

#### <a name="solution"></a>Řešení
Uložte do původní tabulky nový typ entity pomocí entit s následující strukturou:  

![Obrázek entity zaměstnance se složeným klíčem][20]

Všimněte si, jak `RowKey` je nyní složený klíč, který se skládá z ID zaměstnance a roku revizních dat. Díky tomu můžete načíst výkon a zkontrolovat data pomocí jediné žádosti pro jednu entitu.  

Následující příklad popisuje, jak můžete načíst všechna data kontroly pro zaměstnance (třeba 000123 zaměstnanci z oddělení prodeje):  

$filter = (PartitionKey eq "Prodeje") a (RowKey ge "empid_000123") a (RowKey lt 'empid_000124') & $select = RowKey, správce hodnocení, Peer hodnocení, komentáře  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Měli byste použít vhodný oddělovací znak, který usnadňuje analýzu `RowKey` hodnoty: například **000123_2012**.  
* Tuto entitu ukládáte i do stejného oddílu jako jiné entity, které obsahují související data pro stejného zaměstnance. To znamená, že můžete pomocí EGTs zachovat silnou konzistenci.
* Měli byste zvážit, jak často budete zadávat dotazy na data, abyste zjistili, jestli je tento model vhodný. Například pokud ke kontrole dat přistupujete nečasto a hlavním datům zaměstnanců, měli byste je uchovávat jako samostatné entity.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když potřebujete ukládat jeden nebo více souvisejících entit dotazu často.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entit](#entity-group-transactions)  
* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)  
* [Vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Vzor log tail
Načtěte entity *n* naposledy přidané do oddílu pomocí `RowKey` hodnoty, která se seřadí v pořadí podle data a času.  

> [!NOTE]
> Výsledky dotazu vrácené službou Azure rozhraní API pro tabulky v Azure Cosmos DB nejsou seřazené podle klíče oddílu nebo klíče řádku. Takže i když je tento model vhodný pro úložiště tabulek, není vhodný pro Azure Cosmos DB. Podrobný seznam rozdílů funkcí najdete v tématu [rozdíly mezi rozhraní API pro tabulky v Azure Cosmos DB a v Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

#### <a name="context-and-problem"></a>Kontext a problém
Běžné požadavky, je moct načíst nedávno vytvořené entity, třeba posledních deset výdaje odeslal zaměstnanec deklarací identity. Dotazy tabulky podporují operaci `$top` dotazu, která vrací první *n* entit ze sady. Neexistuje žádná ekvivalentní operace dotazu, která by vrátila poslední *n* entit v sadě.  

#### <a name="solution"></a>Řešení
Uložte entity pomocí `RowKey`, které přirozeně seřadí v pořadí podle data a času, takže poslední položka je vždy první v tabulce.  

Například aby bylo možné načíst deset nejnovější deklarací výdajů odeslal zaměstnanec, můžete použít reverzní značek hodnotou odvozenou od aktuálního data a času. Následující C# ukázka kódu ukazuje jeden ze způsobů, jak vytvořit vhodnou hodnotu "obrácené" značky "pro `RowKey`, která seřadí z nejnovějších k nejstarší:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

K hodnotě data a času se můžete vrátit pomocí následujícího kódu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Tabulka dotaz vypadá takto:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Chcete-li zajistit, aby se řetězcová hodnota seřadí podle očekávání, je nutné obklopit hodnotu reverzní Tick počátečními nulami.  
* Musíte být vědomi cíle škálovatelnosti na úrovni oddílu. Buďte opatrní, abyste nevytvořili oddíly s aktivním bodem.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že potřebujete získat přístup k entitám v pořadí zpětného data a času, nebo když potřebujete přístup k naposledy přidaným entitám.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Předřaďte / připojovat proti vzor](#prepend-append-anti-pattern)  
* [Načtení entit](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Vzor vysoké objemy delete
Umožněte odstranění velkého objemu entit tím, že uložíte všechny entity pro současné odstranění ve vlastní samostatné tabulce. Odstraňte entity odstraněním tabulky.  

#### <a name="context-and-problem"></a>Kontext a problém
Mnoho aplikací odstranit stará data, která již nemusí být k dispozici pro klientské aplikace, nebo aplikace má archivovat do jiné úložné médium. Tato data obvykle Identifikujte podle data. Například máte požadavek na odstranění záznamů všech žádostí o přihlášení, které jsou starší než 60 dní.  

Jedním z možných návrhů je použití data a času žádosti o přihlášení v `RowKey`:  

![Obrázek entity pokus o přihlášení][21]

Tento přístup zabraňuje dělení hotspotů, protože aplikace může vkládat a odstraňovat přihlašovací entity pro každého uživatele v samostatném oddílu. Tento přístup ale může být nákladný a časově náročný, pokud máte velký počet entit. Nejprve je třeba provést prohledávání tabulky, aby bylo možné identifikovat všechny entity, které chcete odstranit, a pak musíte odstranit každou starou entitu. Můžete snížit počet zpátečních cest k serveru muset odstranit staré entity do EGTs dávkování víc požadavků delete.  

#### <a name="solution"></a>Řešení
Do samostatné tabulky použijte pro každý den pokusů o přihlášení. Můžete použít předchozí návrh entity a vyhnout se tak hotspotům při vkládání entit. Odstranění starých entit je teď pouhou otázkou odstranění jedné tabulky každý den (operace jednoho úložiště) místo hledání a odstraňování stovek a tisíců jednotlivých entit přihlašování každý den.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Podporuje vaše návrh i další způsoby, kterými vaše aplikace bude data používat, jako je například vyhledávání konkrétních entit, propojení s ostatními daty nebo generování agregačních informací?  
* Váš návrh vyhnout aktivní body při vkládání nové entity?  
* Pokud chcete znovu použít stejný název tabulky po jejím odstranění očekávat, že ke zpoždění. Je lepší, vždy používali unikátní tabulku názvů.  
* Očekává se, že při prvním použití nové tabulky dojde k omezení četnosti, zatímco služba Table Storage se učí vzory přístupu a distribuuje oddíly mezi uzly. Měli byste zvážit, jak často je potřeba vytvořit nové tabulky.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že máte velký počet entit, které je nutné odstranit ve stejnou dobu.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entit](#entity-group-transactions)
* [Úprava entit](#modify-entities)  

### <a name="data-series-pattern"></a>Vzor data řady
Řada kompletní data Store v jedné entity, chcete-li minimalizovat počet požadavků, které provedete.  

#### <a name="context-and-problem"></a>Kontext a problém
Běžný scénář, kdy je aplikace k ukládání řadu data, která je obvykle potřeba načíst všechny najednou. Vaše aplikace například může zaznamenat kolik zasílání Rychlých zpráv každý zaměstnanec odešle každou hodinu a pak tyto informace slouží k vykreslení počet zpráv každý uživatel odesílá přes předchozími 24 hodinami. Jeden návrhu může být k ukládání 24 entity pro každý zaměstnanec:  

![Obrázek entity statistiky zprávy][22]

V tomto návrhu může snadno najít a aktualizovat se pro každý zaměstnanec pokaždé, když aplikace potřebuje aktualizovat hodnota počtu zpráv. K načtení informací k vykreslení grafu aktivity za předchozích 24 hodin, ale musíte načíst 24 entity.  

#### <a name="solution"></a>Řešení
Použijte následující návrh s samostatnou vlastností pro uložení počtu zpráv pro každou hodinu:  

![Obrázek znázorňující entitu statistiky zprávy s oddělenými vlastnostmi][23]

Operace sloučení s tímto návrhem slouží k aktualizaci počet zpráv pro zaměstnance jenom konkrétní hodiny. Nyní můžete načíst všechny informace potřebné k vykreslení grafu pomocí žádosti o jednu entitu.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Pokud se vaše celá datová řada nevejde do jedné entity (entita může mít až 252 vlastností), použijte alternativní úložiště dat, jako je například objekt BLOB.  
* Pokud máte více klientů s aktualizací entity současně, použijte **značku ETag** k implementaci optimistického řízení souběžnosti. Pokud máte mnoho klientů, může docházet ke vysokému obsahu.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když potřebujete aktualizovat a načíst datové řady přidružené jednotlivých entit.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor velkých entit](#large-entities-pattern)  
* [Sloučení nebo nahradit](#merge-or-replace)  
* [Vzorec pro nakonec konzistentní transakce](#eventually-consistent-transactions-pattern) (Pokud ukládáte datovou řadu do objektu BLOB)  

### <a name="wide-entities-pattern"></a>Vzor široké entity
Slouží k ukládání logické entity s více než 252 vlastností více fyzických entit.  

#### <a name="context-and-problem"></a>Kontext a problém
Jednotlivá entita nemůže mít více než 252 vlastností (kromě povinných vlastností systému) a nemůže uchovávat více než 1 MB dat. V relační databázi byste obvykle obejít omezení velikosti řádku přidáním nové tabulky a vynucením vztahu 1:1 mezi nimi.  

#### <a name="solution"></a>Řešení
Pomocí služby Table Storage můžete ukládat více entit, které reprezentují jeden velký obchodní objekt s více než 252 vlastnostmi. Pokud například chcete uložit počet zpráv IM odesílaných jednotlivými zaměstnanci po dobu posledních 365 dní, můžete použít následující návrh, který používá dvě entity s různými schématy:  

![Obrázek znázorňující entitu statistiky zprávy s Rowkey 01 a entitou Statistika zprávy s Rowkey 02][24]

Pokud potřebujete provést změnu, která vyžaduje aktualizaci obě entity k zachování pro synchronizaci mezi sebou, můžete použít EGT. V opačném případě můžete použít jeden sloučených aktualizovat počet zpráv pro určitý den. Chcete-li načíst všechna data pro jednotlivé zaměstnance, je nutné načíst obě entity. To lze provést pomocí dvou efektivních požadavků, které používají `PartitionKey` a hodnotu `RowKey`.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Při rozhodování, jak tento model implementovat, vezměte v úvahu následující body:  

* Načítání kompletní logická entita vyžaduje alespoň dva transakce služby storage: jednu k načtení každá fyzická entita.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že potřebujete uložit entity, jejichž velikost nebo počet vlastností překračuje limity pro jednotlivé entity v úložišti tabulek.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entit](#entity-group-transactions)
* [Sloučení nebo nahradit](#merge-or-replace)

### <a name="large-entities-pattern"></a>Vzor velkých entit
Úložiště objektů blob můžete použít k ukládání velkých hodnot vlastností.  

#### <a name="context-and-problem"></a>Kontext a problém
Jednotlivé entity nemůžou ukládat celkem víc než 1 MB dat. Pokud jedna nebo několik vlastností ukládá hodnoty, které způsobí, že celková velikost vaší entity překročí tuto hodnotu, nemůžete uložit celou entitu v úložišti tabulek.  

#### <a name="solution"></a>Řešení
Pokud má vaše entita velikost větší než 1 MB, protože jedna nebo více vlastností obsahuje velké množství dat, můžete ukládat data v úložišti objektů BLOB a potom uložit adresu objektu blob do vlastnosti v entitě. Můžete například uložit fotografii zaměstnance do úložiště objektů BLOB a Uložit odkaz na fotografii ve vlastnosti `Photo` vaší entity zaměstnance:  

![Obrázek znázorňující entitu zaměstnance s řetězcem pro fotografii ukazující na úložiště objektů BLOB][25]

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Aby se zachovala konečná konzistence mezi entitou v úložišti tabulek a daty v úložišti objektů blob, použijte ke správě svých entit [model nakonec konzistentní transakce](#eventually-consistent-transactions-pattern) .
* Načítání úplnou entitu zahrnuje nejméně dva transakce služby storage: jednu k načtení entity a z nich se má načíst data objektů blob.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že potřebujete ukládat entity, jejichž velikost překračuje limity pro jednotlivé entity v úložišti tabulek.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern)  
* [Vzor široké entity](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Předřaďte/připojovat proti vzor
Když máte velký objem vložení, zvyšte škálovatelnost tím, že rozšíříte vložení mezi více oddílů.  

#### <a name="context-and-problem"></a>Kontext a problém
Předřazení nebo připojením entit k uložené entity obvykle za následek přidání nové entity na první nebo poslední oddíl posloupnost oddíly aplikace. V tomto případě jsou všechna vložení v jakémkoli čase prováděna ve stejném oddílu a vytváří hotspot. Tím se zabrání tomu, aby úložiště tabulek z vyrovnávání zátěže vytvářely mezi více uzly, a případně způsobí, že aplikace dosáhne cílů škálovatelnosti pro oddíl. Zvažte například případ aplikace, která protokoluje přístup zaměstnanců k síti a prostředkům. Struktura entity, jako je například následující, může způsobit, že se oddíl aktuální hodiny stane aktivním rozsahem, pokud objem transakcí dosáhne cíle škálovatelnosti pro jednotlivé oddíly:  

![Obrázek entity zaměstnance][26]

#### <a name="solution"></a>Řešení
Následující alternativní struktura entity zabraňuje hotspotu v jakémkoli konkrétním oddílu, protože aplikace protokoluje události:  

![Obrázek znázorňující entitu zaměstnance s RowKeyem složeným z kódu pro rok, měsíc, den, hodinu a ID události][27]

Všimněte si, jak se v tomto příkladu používají složené klíče `PartitionKey` i `RowKey`. `PartitionKey` používá ID oddělení i zaměstnance k distribuci protokolování napříč více oddíly.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Alternativní klíče struktura, která zabraňuje vytváření aktivních oddílů na vloží efektivně podporuje dotazy, které klientská aplikace odešle?  
* Znamená to, že se vám pravděpodobně dosáhnete cíle škálovatelnosti pro jednotlivý oddíl a omezit ho na úložiště tabulek?  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Nepoužívejte antipattern předplatného/připojovat, pokud je pravděpodobný objem transakcí v případě přístupu k aktivnímu oddílu omezením omezení podle úložiště tabulek.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Složené sekvence klíče](#compound-key-pattern)  
* [Vzor log tail](#log-tail-pattern)  
* [Úprava entit](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Ochrana proti vzorek dat protokolu
K ukládání dat protokolu obvykle byste měli místo tabulkového úložiště používat úložiště objektů BLOB.  

#### <a name="context-and-problem"></a>Kontext a problém
Běžný případ použití pro data protokolu je načtení výběru položek protokolu pro určitý rozsah data a času. Například chcete najít všechny chybové a kritické zprávy, které aplikace zaznamenala mezi 15:04 a 15:06 v konkrétní datum. Nechcete pomocí data a času zprávy protokolu určit oddíl, do kterého ukládáte entity protokolu. Vzhledem k tomu, že v jakémkoli čase bude aktivní oddíl, všechny entity protokolu budou sdílet stejnou `PartitionKey` hodnotu (viz [předplatná/Append anti-Pattern](#prepend-append-anti-pattern)). Například následující schéma entity pro zprávu protokolu má za následek aktivní oddíl, protože aplikace zapisuje všechny zprávy protokolu do oddílu aktuálního data a hodiny:  

![Obrázek entity zprávy protokolu][28]

V tomto příkladu `RowKey` obsahuje datum a čas zprávy protokolu, aby se zajistilo, že se zprávy protokolu v pořadí podle data a času řadí. `RowKey` také obsahuje ID zprávy, v případě, že více zpráv protokolu sdílí stejné datum a čas.  

Další možností je použít `PartitionKey`, která zajišťuje, že aplikace zapisuje zprávy do celé řady oddílů. Pokud například zdroj zprávy protokolu poskytuje způsob, jak distribuovat zprávy v mnoha oddílech, můžete použít následující schéma entity:  

![Obrázek entity zprávy protokolu][29]

Problém s tímto schématem je však, že k načtení všech zpráv protokolu pro určité časové období je nutné v tabulce vyhledat všechny oddíly.

#### <a name="solution"></a>Řešení
Předchozí část zdůraznila problém při pokusu o použití úložiště tabulek k ukládání položek protokolu a navržených dvou neuspokojivých návrhů. Jedno řešení vedlo k Hot oddílu s rizikem špatného výkonu při zápisu zpráv protokolu. Druhé řešení vedlo k nekvalitnímu výkonu dotazů, protože z důvodu požadavku na kontrolu každého oddílu v tabulce načítá zprávy protokolu pro určité časové období. Úložiště objektů BLOB nabízí pro tento typ scénáře lepší řešení a toto je způsob, jakým Azure Storage Analytics ukládá data protokolu, která shromažďuje.  

Tato část popisuje, jak Analytics Storage ukládá data protokolu do úložiště objektů blob, jako ilustraci tohoto přístupu k ukládání dat, která se obvykle dotazují podle rozsahu.  

Služba Storage Analytics ukládá zprávy protokolu ve formátu s oddělovači ve více objektech blob. Formát odděleného usnadňuje klientské aplikaci parsovat data ve zprávě protokolu.  

Služba Storage Analytics používá zásady vytváření názvů objektů blob, které umožňují vyhledat objekty BLOB (nebo objekty BLOB), které obsahují zprávy protokolu, pro které hledáte. Například objekt BLOB s názvem Queue/2014/07/31/1800/000001. log obsahuje zprávy protokolu, které se vztahují ke službě Queue za hodinu 18:00 od 31. července 2014. "000001" označuje, že toto je první soubor protokolu pro toto období. Analýza úložiště také zaznamenává časová razítka první a poslední zprávy protokolu uložené v souboru jako součást metadat objektu BLOB. Rozhraní API pro úložiště objektů BLOB umožňuje vyhledat objekty BLOB v kontejneru na základě předpony názvu. K vyhledání všech objektů blob, které obsahují data protokolu fronty po dobu od 18:00, můžete použít předponu Queue/2014/07/31/1800.  

Služba Storage Analytics ukládá zprávy protokolu interně a následně pravidelně aktualizuje příslušný objekt BLOB nebo vytvoří nový s nejnovější dávkou položek protokolu. Tím se sníží počet zápisů, které musí provádět v úložišti objektů BLOB.  

Pokud implementujete podobné řešení ve své vlastní aplikaci, zvažte, jak spravovat kompromis mezi spolehlivostí a náklady a škálovatelností. Jinými slovy, můžete vyhodnotit účinek zápisu všech záznamů do úložiště objektů blob, a to v porovnání s aktualizacemi ukládání do vyrovnávací paměti v aplikaci a jejich zápisem do úložiště objektů BLOB v dávkách.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Při rozhodování o tom, jak ukládat data protokolu, zvažte následující body:  

* Pokud vytvoříte návrh tabulky, který se vyhne potenciálním neaktivním oddílům, můžete zjistit, že k datům protokolu nemůžete efektivně přistupovat.  
* Ke zpracování dat protokolu, klient často potřebuje načíst mnoho záznamů.  
* I když jsou data protokolu často strukturovaná, může být lepším řešením úložiště objektů BLOB.  

### <a name="implementation-considerations"></a>Důležité informace o implementaci
Tato část popisuje některé důležité informace k berte v úvahu při implementaci vzorce popsané v předchozích částech. Většinu této části se používají příklady napsané v jazyce C#, které použijte klientskou knihovnu pro úložiště (verze 4.3.0 v době psaní).  

### <a name="retrieve-entities"></a>Načtení entit
Jak je popsáno v části [návrhu pro dotazování](#design-for-querying), efektivní dotaz je dotaz bodu. V některých scénářích ale možná budete potřebovat načíst více entit. Tato část popisuje některé běžné přístupy k načítání entit pomocí klientské knihovny pro úložiště.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Spustit dotaz typu Point pomocí klientské knihovny pro úložiště
Nejjednodušší způsob, jak spustit dotaz na bod, je použít operaci **načíst** tabulku. Jak je znázorněno v C# následujícím fragmentu kódu, tato operace načte entitu s `PartitionKey` hodnotou Sales a `RowKey` hodnotou "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Všimněte si, jak tento příklad očekává, že entita, kterou načítá, je typu `EmployeeEntity`.  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Načtení více entit pomocí LINQ
Můžete načíst více entit pomocí knihovny LINQ s klientskou knihovnou úložiště a zadat dotaz s klauzulí **WHERE** . Abyste se vyhnuli prohledávání tabulky, měli byste vždycky do klauzule WHERE zahrnout hodnotu `PartitionKey` a pokud je to možné, `RowKey` hodnotu, abyste se vyhnuli prohledávání tabulek a oddílů. Úložiště tabulek podporuje omezené sady relačních operátorů (větší než, je větší nebo rovno, je menší než, je menší než nebo rovno, rovno a není rovno) pro použití v klauzuli WHERE. Následující C# fragment kódu vyhledá všechny zaměstnance, jejichž příjmení začíná písmenem "B" (za předpokladu, že `RowKey` ukládá příjmení) do prodejního oddělení (za předpokladu, že `PartitionKey` ukládá název oddělení):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Všimněte si, jak dotaz určuje `RowKey` i `PartitionKey`, aby bylo zajištěno lepší výkon.  

Následující ukázka kódu ukazuje ekvivalentní funkce pomocí rozhraní Fluent API (Další informace o rozhraních API Fluent najdete v tématu [osvědčené postupy pro návrh rozhraní Fluent API](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

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
> Ukázka vnořování více metod `CombineFilters`, aby zahrnovaly tři podmínky filtru.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Načtení velkého počtu entit z dotazu
Optimální dotaz vrátí jednotlivou entitu na základě `PartitionKey` hodnoty a `RowKey` hodnoty. V některých scénářích ale může být potřeba, abyste vrátili mnoho entit ze stejného oddílu, nebo dokonce i z mnoha oddílů. V takových situacích by měl vždy plně testování výkonu vaší aplikace.  

Dotaz na úložiště tabulek může vracet maximálně 1 000 entit najednou a běžet po dobu maximálně pěti sekund. Table Storage vrátí token pro pokračování, který umožní klientské aplikaci požádat o další sadu entit, pokud platí některá z následujících podmínek:

- Sada výsledků dotazu obsahuje více než 1 000 entit.
- Dotaz nebyl dokončen do pěti sekund.
- Dotaz překračuje hranici oddílu. 

Další informace o tom, jak fungují tokeny pro pokračování, najdete v tématu [časový limit dotazu a stránkování](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Pokud používáte klientskou knihovnu pro úložiště, může automaticky zpracovat tokeny pro pokračování, protože vrací entity z úložiště tabulek. Například následující C# ukázka kódu automaticky zpracovává tokeny pokračování, pokud je služba Table Storage vrací v odpovědi:  

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

Následující kód jazyka C# explicitně zpracovává pokračování tokeny:  

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

Pomocí explicitně pokračování tokenů, můžete řídit, když aplikace načte další segment data. Například pokud vaše klientská aplikace umožňuje uživatelům stránkovat prostřednictvím entit uložených v tabulce, uživatel se může rozhodnout, že neprojde všemi entitami načtenými dotazem. Vaše aplikace by používala token pro pokračování k načtení dalšího segmentu v případě, že uživatel dokončil stránkování prostřednictvím všech entit v aktuálním segmentu. Tento přístup má několik výhod:  

* Můžete omezit množství dat, která se mají načíst z tabulkového úložiště, a přejít na síť.  
* V rozhraní .NET můžete provádět asynchronní vstupně-výstupní operace.  
* Token pro pokračování můžete serializovat na trvalé úložiště, takže můžete pokračovat v případě selhání aplikace.  

> [!NOTE]
> Token pro pokračování obvykle vrací segment obsahující 1 000 entit, i když může obsahovat méně. To platí i v případě, že omezíte počet položek, které dotaz vrátí, pomocí příkazu **přijmout** vrátí prvních n entit, které odpovídají kritériím vyhledávání. Úložiště tabulek může vracet segment obsahující méně než n entit spolu s tokenem pokračování, který vám umožní načíst zbývající entity.  
> 
> 

Následující kód jazyka C# ukazuje, jak upravit počtu entit vrácených v segmentu:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projekce na straně serveru
Jedna entita může mít nastavenou vlastnost až 255 a mít velikost až 1 MB. Při dotazování tabulky a načtení entit nemusíte potřebovat všechny vlastnosti a můžete se vyhnout nutnosti přenášet data zbytečně (což snižuje latenci a náklady). Projekce na straně serveru můžete použít pro přenos pouze vlastnosti, které potřebujete. Následující příklad načte pouze vlastnost `Email` (společně s `PartitionKey`, `RowKey`, `Timestamp`a `ETag`) z entit vybraných dotazem.  

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

Všimněte si, jak je k dispozici `RowKey` hodnota, i když není obsažena v seznamu vlastností, které mají být načteny.  

### <a name="modify-entities"></a>Úpravy entit
Klientská knihovna pro úložiště umožňuje upravovat entity uložené v úložišti tabulek vložením, odstraněním a aktualizací entit. EGTs můžete použít ke dávkovému zpracování vícenásobných operací vložení, aktualizace a odstranění a snížit tak počet požadovaných požadavků na odezvy a zvýšit výkon vašeho řešení.  

Výjimky, které jsou vyvolány, když klientská knihovna pro úložiště spustí EGT, obvykle zahrnuje index entity, která způsobila selhání dávky. To je užitečné při ladění kódu, který používá EGTs.  

Měli byste také zvážit, jak váš návrh má vliv na způsob, jakým klientské aplikace zpracovává operace souběžnosti a aktualizace.  

#### <a name="managing-concurrency"></a>Správa souběžnosti
Ve výchozím nastavení implementuje úložiště tabulek při operacích vložení, sloučení a odstranění optimistické kontroly souběžnosti na úrovni jednotlivých entit, i když je možné, že klient vynutí obejít tyto kontroly v úložišti tabulek. Další informace najdete v tématu [Správa souběžnosti v Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Sloučení nebo nahradit
Metoda `Replace` třídy `TableOperation` vždy nahrazuje kompletní entitu v úložišti tabulek. Pokud do žádosti v případě, že tato vlastnost existuje v uložené entitě, nezadáte vlastnost, požadavek tuto vlastnost odebere z uložené entity. Pokud chcete explicitně odebrání vlastnosti z uložené entity, je nutné zahrnout každou vlastnost v požadavku.  

Pomocí metody `Merge` třídy `TableOperation` můžete omezit množství dat, která odesíláte do úložiště tabulek, pokud chcete entitu aktualizovat. Metoda `Merge` nahrazuje všechny vlastnosti v uložené entitě hodnotami vlastností z entity zahrnuté v žádosti. Tato metoda opustí nedotčené vlastnosti v uložené entitě, které nejsou zahrnuté v žádosti. To je užitečné, pokud máte velké entity a potřebujete aktualizovat pouze malý počet vlastností v žádosti.  

> [!NOTE]
> Metody `*Replace` a `Merge` selžou, pokud entita neexistuje. Alternativně můžete použít metody `InsertOrReplace` a `InsertOrMerge`, které vytvoří novou entitu, pokud neexistuje.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Práce s heterogenními typy entit
Table Storage je úložiště tabulek *bez schématu* . To znamená, že jedna tabulka může ukládat entity různých typů, což poskytuje skvělou flexibilitu v návrhu. Následující příklad ukazuje tabulku ukládání zaměstnanci a oddělení entity:  

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
<th>Jméno</th>
<th>Příjmení</th>
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
<th>Jméno</th>
<th>Příjmení</th>
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
<th>EmployeeCount</th>
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
<th>Jméno</th>
<th>Příjmení</th>
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

Každá entita musí mít stále hodnoty `PartitionKey`, `RowKey`a `Timestamp`, ale může mít libovolnou sadu vlastností. Kromě toho není nic označovat typ entity, pokud se nerozhodnete ukládat tyto informace někam. Existují dvě možnosti pro určení typu entity:  

* Předřaďte typ entity do `RowKey` (případně `PartitionKey`). Například `EMPLOYEE_000123` nebo `DEPARTMENT_SALES` jako `RowKey` hodnoty.  
* Použijte samostatnou vlastnost pro záznam typu entity, jak je znázorněno v následující tabulce.  

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
<th>Typ entity</th>
<th>Jméno</th>
<th>Příjmení</th>
<th>Věk</th>
<th>E-mail</th>
</tr>
<tr>
<td>Zaměstnance</td>
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
<th>Typ entity</th>
<th>Jméno</th>
<th>Příjmení</th>
<th>Věk</th>
<th>E-mail</th>
</tr>
<tr>
<td>Zaměstnance</td>
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
<th>Typ entity</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
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
<th>Typ entity</th>
<th>Jméno</th>
<th>Příjmení</th>
<th>Věk</th>
<th>E-mail</th>
</tr>
<tr>
<td>Zaměstnance</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

První možnost, předčekání typu entity na `RowKey`, je užitečná, pokud existuje možnost, že dvě entity různých typů mohou mít stejnou hodnotu klíče. Také skupin entit stejného typu společně v oddílu.  

Techniky popsané v této části jsou obzvláště důležité pro diskuzi o[vztazích dědičnosti](#inheritance-relationships).  

> [!NOTE]
> Pokud chcete, aby klientské aplikace POCO objekty a pracovaly s různými verzemi, zvažte zahrnutí čísla verze do hodnoty typu entity.  
> 
> 

Zbývající část Tato část popisuje některé funkce v klientské knihovně pro úložiště, které usnadňují práci s více typy entit ve stejné tabulce.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Načtení heterogenních typů entit
Pokud používáte klientskou knihovnu pro úložiště, máte k dispozici tři možnosti pro práci s více typy entit.  

Pokud znáte typ entity uložené s určitými hodnotami `RowKey` a `PartitionKey`, můžete při načtení entity zadat typ entity. Viděli jste to v předchozích dvou příkladech, které načítají entity typu `EmployeeEntity`: [Spusťte dotaz Point pomocí klientské knihovny úložiště](#run-a-point-query-by-using-the-storage-client-library) a [načtěte více entit pomocí LINQ](#retrieve-multiple-entities-by-using-linq).  

Druhou možností je použít typ `DynamicTableEntity` (kontejner objektů a dat) místo konkrétního typu entity POCO. Tato možnost může také zvýšit výkon, protože není nutné serializovat a deserializovat entitu na typy .NET. Následující C# kód potenciálně načte více entit různých typů z tabulky, ale vrátí všechny entity jako instance `DynamicTableEntity`. Poté pomocí vlastnosti `EntityType` určí typ každé entity:  

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

Chcete-li načíst další vlastnosti, je nutné použít metodu `TryGetValue` pro vlastnost `Properties` třídy `DynamicTableEntity`.  

Třetí možností je kombinovat použití typu `DynamicTableEntity` a instance `EntityResolver`. To umožňuje řešení pro více typů POCO ve stejném dotazu. V tomto příkladu `EntityResolver` delegát používá vlastnost `EntityType` k odlišení dvou typů entit, které dotaz vrátí. Metoda `Resolve` používá delegáta `resolver` k překladu instancí `DynamicTableEntity` na instance `TableEntity`.  

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
Nemusíte znát typ entity, kterou chcete odstranit, a při vložení vždy znát typ entity. Můžete však použít typ `DynamicTableEntity` k aktualizaci entity bez znalosti jejího typu a bez použití třídy entity POCO. Následující ukázka kódu načte jednu entitu a před aktualizací kontroluje, zda existuje vlastnost `EmployeeCount`.  

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
Tokeny sdíleného přístupového podpisu (SAS) můžete použít, chcete-li klientským aplikacím povolit přímou úpravu (a dotazování) entit tabulky, aniž by bylo nutné ověřovat přímo v úložišti tabulek. Obvykle jsou k dispozici tři hlavní výhody použití SAS ve vaší aplikaci:  

* Nemusíte distribuovat klíč účtu úložiště na nezabezpečenou platformu (například mobilní zařízení), aby toto zařízení mělo přístup k entitám v úložišti tabulek a jejich úpravy.  
* Můžete přesměrovat část práce, kterou webové a pracovní role provádějí při správě entit. Můžete přesměrovat na klientská zařízení, jako jsou počítače koncového uživatele a mobilní zařízení.  
* Klientovi můžete přiřadit omezené a časově omezenou sadu oprávnění (například povolit přístup jen pro čtení ke konkrétním prostředkům).  

Další informace o použití tokenů SAS s tabulkovým úložištěm najdete v tématu [použití sdílených přístupových podpisů (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Přesto však musíte vygenerovat tokeny SAS, které klientským aplikacím udělují entity v úložišti tabulek. Provedete to v prostředí, které má zabezpečený přístup k klíčům účtu úložiště. Obvykle použijete webové nebo pracovní role generovat tokeny SAS a doručujte je na klientských aplikací, které potřebují přístup k vaší entity. Vzhledem k tomu, že se stále vyžaduje režii účastnící se vytváření a doručování tokeny SAS pro klienty, zvažte, jak nejlépe omezit tato režie, zejména v velkoobjemových scénářů.  

Je možné vygenerovat token SAS, který uděluje přístup podmnožině entit v tabulce. Ve výchozím nastavení vytvoříte token SAS pro celou tabulku. Je ale také možné určit, že token SAS uděluje přístup buď k rozsahu hodnot `PartitionKey`, nebo k rozsahu `PartitionKey` a `RowKey` hodnot. Můžete zvolit generování tokenů SAS pro jednotlivé uživatele systému, takže token SAS každého uživatele umožňuje přístup pouze k vlastním entitám v úložišti tabulek.  

### <a name="asynchronous-and-parallel-operations"></a>Asynchronní a paralelní operace
Pokud jsou rozprostírá vaše požadavky napříč několika oddíly, propustnosti a klienta reakce můžete zlepšit pomocí asynchronní a paralelní dotazy.
Například může mít dvě nebo víc instancí rolí pracovního procesu přístup k vaší tabulky paralelně. Můžete mít jednotlivé role pracovních procesů, které jsou zodpovědné za konkrétní sady oddílů, nebo jenom mít několik instancí role pracovního procesu, každý může mít přístup ke všem oddílům v tabulce.  

V rámci instance klienta můžete zvýšit propustnost spuštěním operací úložiště asynchronně. Klientská knihovna pro úložiště usnadňuje zápis asynchronní dotazy a úpravy. Můžete například začít s synchronní metodou, která načte všechny entity v oddílu, jak je znázorněno v následujícím C# kódu:  

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

Tento kód můžete snadno upravit tak, aby dotaz běžel asynchronně, a to takto:  

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

V tomto příkladu asynchronní uvidíte následující změny z synchronní verze:  

* Signatura metody teď obsahuje modifikátor `async` a vrací instanci `Task`.  
* Namísto volání metody `ExecuteSegmented` pro načtení výsledků metoda nyní volá metodu `ExecuteSegmentedAsync`. Metoda používá modifikátor `await` k asynchronnímu načtení výsledků.  

Klientská aplikace může zavolat tuto metodu vícekrát, s různými hodnotami pro parametr `department`. Každý dotaz se spouští v samostatném vlákně.  

Ve třídě `TableQuery` neexistuje žádná asynchronní verze metody `Execute`, protože rozhraní `IEnumerable` nepodporuje asynchronní výčet.  

Můžete také vložit, aktualizovat a odstraňovat entity asynchronně. Následující příklad jazyka C# ukazuje jednoduchý, která je synchronní metoda vložení nebo nahrazení entity zaměstnance:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Tento kód můžete snadno upravit tak, aby se aktualizace spouštěla asynchronně, jak je znázorněno níže:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

V tomto příkladu asynchronní uvidíte následující změny z synchronní verze:  

* Signatura metody teď obsahuje modifikátor `async` a vrací instanci `Task`.  
* Namísto volání metody `Execute` pro aktualizaci entity nyní metoda volá metodu `ExecuteAsync`. Metoda používá modifikátor `await` k asynchronnímu načtení výsledků.  

Klientská aplikace může volat několik asynchronních metod, jako je tato, a každá metoda vyvolání se spouští v samostatném vlákně.  


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

