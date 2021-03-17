---
title: Návrh Azure Cosmos DBch tabulek pro škálování a výkon
description: 'Průvodce návrhem služby Azure Table Storage: škálovatelné a výkonné tabulky v Azure Cosmos DB a v úložišti tabulek v Azure'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 06/19/2020
author: sakash279
ms.author: akshanka
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 603c891e53e5712d489fcef8415e3db55328c9ad
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988424"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Průvodce návrhem tabulek v Azure Table Storage: Škálovatelné a výkonné tabulky
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Při návrhu škálovatelných a výkonných tabulek je potřeba zvážit řadu faktorů, včetně nákladů. Pokud jste už někdy navrhovali schémata pro relační databáze, tyto aspekty vám budou povědomé. Přestože však mezi službou Azure Table Storage a relačními modely existují určité podobnosti, je mezi nimi i řada důležitých rozdílů. Tyto rozdíly obvykle vedou k různým návrhům, které pro někoho se zkušenostmi s relačními databázemi můžou působit nelogicky nebo špatně, ale které dávají smysl v případě návrhu pro úložiště NoSQL párů klíč-hodnota, jako je služba Table Storage.

Table Storage je navržený tak, aby podporoval aplikace v cloudovém měřítku, které mohou obsahovat miliardy entit ("řádky" v terminologii relačních databází) dat nebo datové sady, které musí podporovat vysoké objemy transakcí. Proto je potřeba se rozmyslet, jak se data ukládají, a pochopit, jak funguje úložiště tabulek. Dobře navržené úložiště dat NoSQL může umožňovat, aby vaše řešení bylo mnohem dalšího škálovatelné (a za nižší náklady) než řešení, které používá relační databázi. Tato příručka vám pomůže s těmito tématy.  

## <a name="about-azure-table-storage"></a>Informace o službě Azure Table Storage
Tato část popisuje některé klíčové funkce úložiště tabulek, které jsou obzvláště důležité pro navrhování výkonu a škálovatelnosti. Pokud Azure Storage a úložiště tabulek teprve začínáte, přečtěte si téma [Úvod do Microsoft Azure Storage](../storage/common/storage-introduction.md) a [Začínáme s Azure Table Storage pomocí rozhraní .NET](./tutorial-develop-table-dotnet.md) ještě před přečtením zbývající části tohoto článku. I když se tato příručka zaměřuje na úložiště tabulek, obsahuje několik diskuzí o službě Azure Queue Storage a Azure Blob Storage a o tom, jak je můžete používat spolu s tabulkovým úložištěm v řešení.  

Table Storage používá k ukládání dat tabulkový formát. V rámci standardní terminologie představuje každý řádek tabulky entitu a sloupce ukládají různé vlastnosti této entity. Každá entita má dvojici klíčů, které ji jednoznačně identifikují, a sloupec časového razítka, které služba Table Storage používá ke sledování, kdy se entita naposledy aktualizovala. Pole časového razítka se přidá automaticky a časové razítko nemůžete ručně přepsat libovolnou hodnotou. Table Storage používá toto poslední upravené časové razítko (LMT) ke správě optimistické souběžnosti.  

> [!NOTE]
> Operace REST API Table Storage také vrací `ETag` hodnotu, která je odvozena od LMT. V tomto dokumentu se výrazy ETag a LMT používají zaměnitelné, protože odkazují na stejná základní data.  
> 
> 

Následující příklad ukazuje jednoduchý návrh tabulky pro ukládání entit zaměstnanců a oddělení. Mnohé z příkladů, které jsou uvedeny dále v této příručce, jsou založeny na tomto jednoduchém návrhu.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014 – 08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>E-mail</th>
</tr>
<tr>
<td>Zobrazeny</td>
<td>Bourárn</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014 – 08-22T00:50:34Z</td>
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
<td>2014 – 08-22T00:50:30Z</td>
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
<td>2014 – 08-22T00:50:44Z</td>
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


Tento návrh se zatím bude podobat tabulce v relační databázi. Hlavní rozdíly jsou povinné sloupce a možnost ukládat více typů entit do stejné tabulky. Kromě toho má každý z uživatelem definovaných vlastností, jako je například **FirstName** nebo **věk**, datový typ, jako je například celé číslo nebo řetězec, stejně jako sloupec v relační databázi. Na rozdíl od relační databáze ale bez schématu pro úložiště tabulek je to, že vlastnost nemusí mít stejný datový typ pro každou entitu. Pro uložení složitých datových typů do jediné vlastnosti je nutné použít serializovaný formát, jako je JSON nebo XML. Další informace najdete v tématu [Principy datového modelu Table Storage](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

Vaše volba `PartitionKey` a `RowKey` je zásadní pro dobrý návrh tabulek. Každá entita uložená v tabulce musí mít jedinečnou kombinaci `PartitionKey` a `RowKey` . Stejně jako u klíčů v tabulce relační databáze `PartitionKey` `RowKey` jsou hodnoty a indexovány, aby se vytvořil clusterovaný index, který umožňuje rychlé vyhledávání. Table Storage ale nevytváří žádné sekundární indexy, takže se jedná o jediné dvě indexované vlastnosti (některé ze vzorů popsaných později ukazují, jak můžete toto zjevné omezení obejít).  

Tabulka se skládá z jednoho nebo více oddílů a mnohé z rozhodnutí o návrhu budou kolem výběru vhodné `PartitionKey` a `RowKey` optimalizuje vaše řešení. Řešení se může skládat pouze z jedné tabulky, která obsahuje všechny vaše entity uspořádané do oddílů, ale obvykle má řešení více tabulek. Tabulky vám pomůžou logicky organizovat vaše entity a pomáhat vám spravovat přístup k datům pomocí seznamů řízení přístupu. Celou tabulku můžete vyřadit pomocí jediné operace úložiště.  

### <a name="table-partitions"></a>Oddíly tabulky
Název účtu, název tabulky a `PartitionKey` společně identifikují oddíl v rámci služby úložiště, ve které je tato entita uložena v úložišti tabulek. Stejně jako součást schématu adresování pro entity, oddíly definují obor pro transakce (Další informace najdete v části dále v tomto článku, [transakce skupiny entit](#entity-group-transactions)) a tvoří základ způsobu, jakým se tabulkové úložiště škálují. Další informace o oddílech tabulky najdete v tématu [Kontrolní seznam pro výkon a škálovatelnost pro úložiště tabulek](../storage/tables/storage-performance-checklist.md).  

V tabulkovém úložišti má jednotlivé uzly služby jednoho nebo více úplných oddílů a služba se škáluje podle dynamicky vyrovnávání zatížení oddílů v uzlech. Pokud je uzel v zatížení, může tabulka úložiště rozdělit rozsah oddílů, které služba obsluhuje v tomto uzlu, na jiné uzly. V případě provozu může tabulka úložiště sloučit rozsahy oddílů z tichých uzlů zpátky do jednoho uzlu.  

Další informace o interních podrobnostech úložiště tabulek a zejména o tom, jak spravuje oddíly, najdete v tématu [Microsoft Azure Storage: vysoce dostupnou cloudovou službu cloudového úložiště se silnými konzistencí](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency).  

### <a name="entity-group-transactions"></a>Transakce skupiny entit
V úložišti tabulek jsou transakce skupin entit (EGTs) jediným integrovaným mechanismem pro provádění atomických aktualizací v několika entitách. EGTs se také označují jako *transakce Batch*. EGTs může pracovat pouze s entitami uloženými ve stejném oddílu (sdílení stejného klíče oddílu v konkrétní tabulce), takže kdykoli budete potřebovat atomické transakční chování napříč více entitami, ujistěte se, že jsou tyto entity ve stejném oddílu. To je často důvod pro udržení více typů entit ve stejné tabulce (a oddílu) a nepoužívá více tabulek pro různé typy entit. Jeden EGT může pracovat s maximálně 100 entitami.  Pokud odešlete více souběžných EGTs ke zpracování, je důležité zajistit, aby tyto EGTs nespolupracovaly s entitami, které jsou společné napříč EGTs. V opačném případě riskujete zpracování zpoždění.

EGTs také zavede potenciální kompromis k vyhodnocení v návrhu. Použití více oddílů zvyšuje škálovatelnost vaší aplikace, protože Azure nabízí více příležitostí pro žádosti o vyrovnávání zatížení napříč uzly. To ale může omezit schopnost vaší aplikace provádět atomické transakce a udržovat velkou konzistenci pro vaše data. Kromě toho existují konkrétní cíle škálovatelnosti na úrovni oddílu, která může omezit propustnost transakcí, které můžete očekávat pro jeden uzel.

Další informace o cílech škálovatelnosti pro účty Azure Storage najdete v tématu [cíle škálovatelnosti pro účty úložiště úrovně Standard](../storage/common/scalability-targets-standard-account.md). Další informace o cílech škálovatelnosti pro úložiště tabulek najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště tabulek](../storage/tables/scalability-targets.md). V dalších částech této příručky se dozvíte o různých strategiích návrhu, které vám pomůžou se správou kompromisů, jako je tato, a diskutovat, jak nejlépe zvolit klíč oddílu na základě konkrétních požadavků vaší klientské aplikace.  

### <a name="capacity-considerations"></a>Důležité informace o kapacitách
Následující tabulka obsahuje některé klíčové hodnoty, které je třeba znát při navrhování řešení úložiště tabulek:  

| Celková kapacita účtu úložiště Azure | 500 TB |
| --- | --- |
| Počet tabulek v účtu služby Azure Storage |Omezeno pouze na kapacitu účtu úložiště. |
| Počet oddílů v tabulce |Omezeno pouze na kapacitu účtu úložiště. |
| Počet entit v oddílu |Omezeno pouze na kapacitu účtu úložiště. |
| Velikost jednotlivé entity |Až 1 MB s maximálním počtem 255 vlastností (včetně `PartitionKey` , a `RowKey` `Timestamp` ). |
| Velikost `PartitionKey` |Velikost řetězce až 1 KB. |
| Velikost `RowKey` |Velikost řetězce až 1 KB. |
| Velikost transakce skupiny entit |Transakce může zahrnovat maximálně 100 entit a velikost datové části musí být menší než 4 MB. EGT může entitu aktualizovat pouze jednou. |

Další informace najdete v tématu [Principy datového modelu Table Service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).  

### <a name="cost-considerations"></a>Důležité informace o nákladech
Úložiště tabulek je poměrně levné, ale měli byste zahrnout odhadované náklady na využití kapacity a množství transakcí v rámci vyhodnocení jakéhokoli řešení, které využívá úložiště tabulek. V mnoha scénářích ale ukládání denormalizovaných nebo duplicitních dat, aby se zlepšil výkon nebo škálovatelnost vašeho řešení, je platný přístup, který je možné provést. Další informace o cenách najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Pokyny pro návrh tabulek
Tyto seznamy shrnují některé klíčové pokyny, které byste měli mít na paměti při navrhování tabulek. Tato příručka je podrobněji popsána dále v. Tyto pokyny se liší od pokynů, které byste obvykle dodržovali pro návrh relačních databází.  

Návrh tabulkového úložiště, aby bylo možné efektivně *číst* :

* **Návrh pro dotazování v aplikacích s vysokým oprávněním** Pokud navrhujete své tabulky, zamyslete se nad dotazy (zejména s těmi, které jsou u nich závislé), a teprve potom si myslíte, jak budete entity aktualizovat. To obvykle vede k efektivnímu a výkonnému řešení.  
* **Zadejte obojí `PartitionKey` i `RowKey` v dotazech.** *Dotazy na body* , jako jsou ty, nejúčinnější dotazy na úložiště tabulek.  
* **Zvažte uložení duplicitních kopií entit.** Úložiště tabulek je levné, proto zvažte uložení stejné entity vícekrát (s různými klíči), čímž umožníte efektivnější dotazy.  
* **Zvažte denormalizaci vašich dat.** Úložiště tabulek je levné, proto zvažte denormalizaci vašich dat. Můžete například uložit souhrnné entity, aby dotazy na agregovaná data měly jenom přístup k jedné entitě.  
* **Použijte hodnoty složených klíčů.** Jedinými klíči jsou `PartitionKey` a `RowKey` . Použijte například hodnoty složeného klíče k povolení alternativních přístupových cest k entitám.  
* **Použití projekce dotazu.** Množství dat přenášených přes síť můžete snížit pomocí dotazů, které vyberou pouze pole, která potřebujete.  

Návrh tabulkového úložiště pro efektivní *zápis* :  

* **Nevytvářejte aktivní oddíly.** Vyberte klíče, které vám umožní rozdělit požadavky do více oddílů kdykoli.  
* **Nepoužívejte špičky v provozu.** Distribuujte provoz v rozumné době a zabraňte špičkám v provozu.
* **Nevytvářejte nutně samostatnou tabulku pro každý typ entity.** Pokud požadujete atomické transakce napříč typy entit, můžete tyto typy entit Uložit do stejného oddílu ve stejné tabulce.
* **Vezměte v úvahu maximální propustnost, kterou je třeba dosáhnout.** Musíte znát cíle škálovatelnosti pro úložiště tabulek a zajistit, aby vám návrh nezpůsobí jejich překročení.  

Později v tomto průvodci uvidíte příklady, které obsahují všechny tyto zásady v praxi.  

## <a name="design-for-querying"></a>Návrh pro dotazování
Úložiště tabulek může být náročné na čtení, náročné na zápis nebo kombinace těchto dvou. V této části se předpokládá návrh na efektivní podporu operací čtení. Obecně platí, že návrh, který podporuje operace čtení efektivně, je také účinný pro operace zápisu. Při návrhu na podporu operací zápisu ale existují další okolnosti. Ty jsou popsány v následující části, [Návrh pro úpravu dat](#design-for-data-modification).

Dobrým výchozím bodem, který vám umožní efektivně číst data, je požádat o to, jaké dotazy bude aplikace muset spustit, aby se načetla data, která potřebuje?  

> [!NOTE]
> S tabulkovým úložištěm je důležité, abyste si před návrhem nastavili správné informace, protože je obtížné ji později změnit. Například v relační databázi je často možné vyřešit problémy s výkonem pouhým přidáním indexů do existující databáze. Nejedná se o možnost s tabulkovým úložištěm.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Způsob volby `PartitionKey` a `RowKey` vlivu na výkon dotazů
V následujících příkladech se předpokládá, že Table Storage ukládá entity zaměstnanců s následující strukturou (většina příkladů vynechává `Timestamp` vlastnost k přehlednost):  

| Název sloupce | Datový typ |
| --- | --- |
| `PartitionKey` (Název oddělení) |Řetězec |
| `RowKey` (ID zaměstnance) |Řetězec |
| `FirstName` |Řetězec |
| `LastName` |Řetězec |
| `Age` |Integer |
| `EmailAddress` |Řetězec |

Tady jsou některé obecné pokyny pro navrhování dotazů na úložiště tabulek. Syntaxe filtru použitá v následujících příkladech je z tabulkového úložiště REST API. Další informace najdete v tématu věnovaném [dotazování entit](/rest/api/storageservices/Query-Entities).  

* *Dotaz na bod* je nejúčinnější vyhledávání, které se má použít, a doporučuje se pro vyhledávání ve velkém objemu nebo vyhledávání vyžadující nejnižší latenci. Takový dotaz může pomocí indexů snadno vyhledat jednotlivou entitu zadáním `PartitionKey` `RowKey` hodnot a. Příklad: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* Druhý nejlepší je *dotaz na rozsah*. Pomocí `PartitionKey` filtrů a na základě rozsahu `RowKey` hodnot vrátí více než jednu entitu. `PartitionKey`Hodnota identifikuje konkrétní oddíl a `RowKey` hodnoty identifikují podmnožinu entit v tomto oddílu. Příklad: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* Třetí nejlepší je *Kontrola oddílu*. Používá `PartitionKey` filtry, a pro jinou neklíčovou vlastnost a může vracet více než jednu entitu. `PartitionKey`Hodnota určuje konkrétní oddíl a hodnoty vlastností se vyberou pro podmnožinu entit v tomto oddílu. Příklad: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* *Prohledávání tabulky* nezahrnuje `PartitionKey` a je neefektivní, protože vyhledává všechny oddíly, které tvoří tabulku pro všechny odpovídající entity. Provede prohledávání tabulky bez ohledu na to, zda filtr používá `RowKey` . Příklad: `$filter=LastName eq 'Jones'`.  
* Dotazy služby Azure Table Storage, které vracejí více entit, je seřadí v `PartitionKey` `RowKey` pořadí a. Chcete-li se vyhnout vyřazování entit v klientovi, vyberte `RowKey` , který definuje nejběžnější pořadí řazení. Výsledky dotazu vrácené službou Azure rozhraní API pro tabulky v Azure Cosmos DB nejsou seřazené podle klíče oddílu nebo klíče řádku. Podrobný seznam rozdílů funkcí najdete v tématu [rozdíly mezi rozhraní API pro tabulky v Azure Cosmos DB a v úložišti tabulek Azure](table-api-faq.md#table-api-vs-table-storage).

Použití "**nebo**" k určení filtru založeného na `RowKey` hodnotách má za následek kontrolu oddílu a nepovažuje se za dotaz na rozsah. Proto se vyhněte dotazům, které používají filtry jako: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')` .  

Příklady kódu na straně klienta, který používá klientskou knihovnu pro úložiště ke spouštění efektivních dotazů, najdete v těchto tématech:  

* [Spustit dotaz typu Point pomocí klientské knihovny pro úložiště](#run-a-point-query-by-using-the-storage-client-library)
* [Načtení více entit pomocí LINQ](#retrieve-multiple-entities-by-using-linq)
* [Projekce na straně serveru](#server-side-projection)  

Příklady kódu na straně klienta, který může zpracovávat více typů entit uložených ve stejné tabulce, naleznete zde:  

* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Zvolit vhodný `PartitionKey`
Vaše volba `PartitionKey` by měla vyvážit nutnost povolit používání EGTs (k zajištění konzistence) před tím, než bude nutné distribuovat vaše entity do několika oddílů (aby se zajistilo škálovatelné řešení).  

V jednom krajním případě můžete všechny entity ukládat do jednoho oddílu. To ale může omezit škálovatelnost vašeho řešení a zabránit tomu, aby úložiště tabulek mohlo umožňovat vyrovnávání zatížení požadavků. V ostatních extrémních případech můžete ukládat jednu entitu na oddíl. To je vysoce škálovatelné a umožňuje úložišti tabulek vyrovnávat zatížení požadavků, ale zabraňuje vám v použití transakcí skupin entit.  

Ideální `PartitionKey` možnost umožňuje používat efektivní dotazy a má dostatečné oddíly pro zajištění škálovatelnosti vašeho řešení. Obvykle zjistíte, že vaše entity budou mít vhodnou vlastnost, která distribuuje vaše entity mezi dostatečné oddíly.

> [!NOTE]
> V systému, který ukládá informace o uživatelích nebo zaměstnaních, `UserID` může být například dobrá `PartitionKey` . Je možné, že máte několik entit, které používají konkrétní `UserID` klíč oddílu. Každá entita, která ukládá data o uživateli, je seskupena do jednoho oddílu. Tyto entity jsou přístupné prostřednictvím EGTs, zatímco jsou pořád vysoce škálovatelné.
> 
> 

K dispozici jsou další informace `PartitionKey` , které se týkají způsobu vkládání, aktualizace a odstraňování entit. Další informace najdete v tématu [Návrh pro úpravu dat](#design-for-data-modification) dále v tomto článku.  

### <a name="optimize-queries-for-table-storage"></a>Optimalizace dotazů pro úložiště tabulek
Table Storage automaticky indexuje vaše entity pomocí `PartitionKey` hodnot a `RowKey` v jednom seskupeném indexu. Důvodem je to, že dotazy na daný bod jsou nejúčinnější pro použití. Nejsou však k dispozici žádné indexy jiné než v clusterovaných indexech v systémech `PartitionKey` a `RowKey` .

Mnoho návrhů musí splňovat požadavky na povolení vyhledávání entit na základě více kritérií. Například vyhledání entit zaměstnanců na základě e-mailu, ID zaměstnance nebo příjmení. Následující vzory [vzorů návrhu tabulek](#table-design-patterns) řeší tyto typy požadavků. Vzory také popisují způsob práce se skutečností, že služba Table Storage neposkytuje sekundární indexy.  

* [Vzor sekundárního indexu v rámci oddílu](#intra-partition-secondary-index-pattern): uložte více kopií každé entity pomocí různých `RowKey` hodnot (ve stejném oddílu). Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých `RowKey` hodnot.  
* [Vzor sekundárního indexu mezi oddíly](#inter-partition-secondary-index-pattern): uložte více kopií každé entity pomocí různých `RowKey` hodnot v samostatných oddílech nebo v samostatných tabulkách. Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých `RowKey` hodnot.  
* [Index entity model](#index-entities-pattern): Udržujte entity indexu, aby bylo možné efektivně vyhledávat, které vracejí seznam entit.  

### <a name="sort-data-in-table-storage"></a>Řazení dat v úložišti tabulek

Table Storage vrátí výsledky dotazu seřazené vzestupně podle `PartitionKey` a potom podle `RowKey` .

> [!NOTE]
> Výsledky dotazu vrácené službou Azure rozhraní API pro tabulky v Azure Cosmos DB nejsou seřazené podle klíče oddílu nebo klíče řádku. Podrobný seznam rozdílů funkcí najdete v tématu [rozdíly mezi rozhraní API pro tabulky v Azure Cosmos DB a v úložišti tabulek Azure](table-api-faq.md#table-api-vs-table-storage).

Klíče v úložišti tabulek jsou řetězcové hodnoty. Chcete-li zajistit, aby číselné hodnoty byly správně řazeny, je nutné je převést na pevnou délku a doplnit je nulami. Pokud například hodnota ID zaměstnance, kterou použijete, `RowKey` je celočíselná hodnota, měli byste převést ID zaměstnance **123** na **00000123**. 

Mnoho aplikací má požadavky na použití dat seřazených v různých objednávkách: například řazení zaměstnanců podle názvu nebo připojení k datu. Následující vzory [vzorů návrhu tabulek](#table-design-patterns) popisují, jak alternativní objednávky řazení pro vaše entity:  

* [Vzor sekundárního indexu v rámci oddílu](#intra-partition-secondary-index-pattern): uložte více kopií každé entity pomocí různých `RowKey` hodnot (ve stejném oddílu). Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých `RowKey` hodnot.  
* [Vzor sekundárního indexu mezi oddíly](#inter-partition-secondary-index-pattern): uložte více kopií každé entity pomocí různých `RowKey` hodnot v samostatných oddílech v samostatných tabulkách. Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých `RowKey` hodnot.
* [Vzor koncového protokolu](#log-tail-pattern): načte entity *n* naposledy přidané do oddílu pomocí `RowKey` hodnoty, která se seřadí v obráceném data a časovém pořadí.  

## <a name="design-for-data-modification"></a>Návrh pro úpravu dat
Tato část se zaměřuje na požadavky návrhu na optimalizaci vložení, aktualizace a odstranění. V některých případech budete muset vyhodnotit kompromis mezi návrhy, které se optimalizují pro dotazování na návrhy, které optimalizují úpravu dat. Toto hodnocení je podobné jako v návrzích pro relační databáze (i když postupy pro správu kompromisů v návrhu se liší v relační databázi). [Vzory návrhu tabulek](#table-design-patterns) oddílu popisují některé podrobné vzory návrhu pro úložiště tabulek a zvýrazňují některé z těchto kompromisů. V praxi zjistíte, že mnoho návrhů, které jsou optimalizované pro dotazování entit, také dobře fungují pro úpravy entit.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optimalizace výkonu operací vložení, aktualizace a odstranění
Chcete-li aktualizovat nebo odstranit entitu, musíte být schopni ji identifikovat pomocí `PartitionKey` hodnot a `RowKey` . V tomto ohledu byste si měli vybrat možnosti `PartitionKey` a `RowKey` pro úpravu entit, podle toho, jaká kritéria se mají zvolit pro dotazy na bod podpory. Chcete co nejefektivnější identifikaci entit. Nechcete vyhledat entitu, abyste mohli zjistit `PartitionKey` hodnoty a, `RowKey` které je třeba aktualizovat nebo odstranit, pomocí neefektivního vyhledávání oddílu nebo tabulky.  

Následující vzory [vzorů návrhu tabulek](#table-design-patterns) řeší optimalizaci výkonu operací vložení, aktualizace a odstranění:  

* [Vzor vysokého odstranění svazků](#high-volume-delete-pattern): umožňuje odstranit velké objemy entit uložením všech entit pro současné odstranění ve vlastní samostatné tabulce. Odstraňte entity odstraněním tabulky.  
* [Vzor datových řad](#data-series-pattern): uložte kompletní datové řady do jedné entity, abyste minimalizovali počet požadavků, které provedete.  
* [Model nejrůznějších entit](#wide-entities-pattern): k ukládání logických entit s více než 252 vlastnostmi použijte více fyzických entit.  
* [Vzor velkých entit](#large-entities-pattern): k ukládání velkých vlastností použijte úložiště objektů BLOB.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Zajištění konzistence uložených entit
Druhý faktor klíče, který má vliv na volbu klíčů pro optimalizaci úprav dat, je způsob, jak zajistit konzistenci pomocí atomických transakcí. EGT můžete použít jenom pro práci s entitami uloženými ve stejném oddílu.  

Následující modely v sekcích [vzory návrhu tabulek](#table-design-patterns) řeší konzistenci správy:  

* [Vzor sekundárního indexu v rámci oddílu](#intra-partition-secondary-index-pattern): uložte více kopií každé entity pomocí různých `RowKey` hodnot (ve stejném oddílu). Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých `RowKey` hodnot.  
* [Vzor sekundárního indexu mezi oddíly](#inter-partition-secondary-index-pattern): uložte více kopií každé entity pomocí různých `RowKey` hodnot v samostatných oddílech nebo v samostatných tabulkách. Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých `RowKey` hodnot.  
* [Vzor nakonec konzistentních transakcí](#eventually-consistent-transactions-pattern): umožňuje zajistit trvalé konzistentní chování napříč hranicemi oddílů nebo systémem úložiště pomocí front Azure.
* [Index entity model](#index-entities-pattern): Udržujte entity indexu, aby bylo možné efektivně vyhledávat, které vracejí seznam entit.  
* [Vzorek denormalizace](#denormalization-pattern): kombinovat související data společně v jedné entitě, abyste mohli načíst všechna potřebná data pomocí dotazu s jedním bodem.  
* [Vzor datových řad](#data-series-pattern): uložte kompletní datové řady do jedné entity, abyste minimalizovali počet požadavků, které provedete.  

Další informace najdete v tématu [transakce skupin entit](#entity-group-transactions) dále v tomto článku.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Ujistěte se, že návrh efektivních úprav usnadňuje efektivní dotazy.
V mnoha případech je návrh účinného dotazování v důsledku efektivních úprav, ale měli byste vždycky vyhodnotit, jestli se jedná o případ konkrétního scénáře. Některé ze vzorů v [vzorcích pro navrhování tabulek](#table-design-patterns) jsou explicitně vyhodnoceny kompromisy mezi dotazování a úpravou entit a měli byste vždycky vzít v úvahu počet jednotlivých typů operací.  

Následující vzory [vzorů návrhu tabulek](#table-design-patterns) řeší kompromisy mezi návrhem pro efektivní dotazy a návrhem pro efektivní úpravu dat:  

* [Vzor složeného klíče](#compound-key-pattern): pomocí složených `RowKey` hodnot můžete klientovi Povolit hledání souvisejících dat pomocí dotazu s jedním bodem.  
* [Vzor koncového protokolu](#log-tail-pattern): načte entity *n* naposledy přidané do oddílu pomocí `RowKey` hodnoty, která se seřadí v obráceném data a časovém pořadí.  

## <a name="encrypt-table-data"></a>Šifrovat data tabulky
Klientská knihovna Azure Storage .NET podporuje šifrování vlastností entity řetězce pro operace INSERT a nahrazování. Šifrované řetězce jsou uloženy ve službě jako binární vlastnosti a jsou převedeny zpět na řetězce po dešifrování.    

U tabulek (kromě zásad šifrování) musí uživatelé zadat vlastnosti, které mají být zašifrované. Buď zadejte `EncryptProperty` atribut (pro entity POCO odvozené od `TableEntity` ), nebo v možnostech žádosti zadejte překladač šifrování. Překladač šifrování je delegát, který převezme klíč oddílu, klíč řádku a název vlastnosti a vrátí logickou hodnotu, která označuje, jestli má být tato vlastnost zašifrovaná. Při šifrování používá Klientská knihovna tyto informace k rozhodnutí, jestli má být při zápisu do tohoto drátu zašifrovaná vlastnost. Delegát také poskytuje možnost logiky kolem způsobu šifrování vlastností. (Například pokud X, pak Šifrujte vlastnost A; jinak Zašifrujte vlastnosti a a B.) Při čtení nebo dotazování entit není nutné tyto informace zadávat.

Sloučení není aktuálně podporováno. Vzhledem k tomu, že podmnožina vlastností mohla být předtím zašifrovaná pomocí jiného klíče, stačí sloučit nové vlastnosti a aktualizace metadat bude mít za následek ztrátu dat. Slučování vyžaduje, aby dodatečná volání služby přečetla existující entita ze služby nebo používala nový klíč na vlastnost. Ani jedna z těchto možností není vhodná z důvodů výkonu.     

Informace o šifrování dat tabulky najdete v tématu [šifrování na straně klienta a Azure Key Vault pro Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Vztahy modelů
Vytváření doménových modelů je klíčový krok v rámci navrhování složitých systémů. Obvykle se používá proces modelování k identifikaci entit a vztahů mezi nimi jako způsob pochopení obchodní domény a informování o návrhu systému. Tato část se zaměřuje na to, jak můžete přeložit některé běžné typy vztahů nalezené v doménových modelech na návrhy pro úložiště tabulek. Proces mapování z logického datového modelu na fyzický datový model založený na NoSQL se liší od toho, který se používá při navrhování relační databáze. Návrh relačních databází obvykle předpokládá proces normalizace dat optimalizovaný pro minimalizaci redundance. Tento návrh také předpokládá deklarativní schopnost dotazování, která abstrakce implementaci způsobu fungování databáze.  

### <a name="one-to-many-relationships"></a>Relace 1: n
Relace 1: n mezi objekty obchodní domény se často vyskytují: například jedno oddělení má mnoho zaměstnanců. Existuje několik způsobů implementace vztahů 1: n v úložišti tabulek, z nichž každá má výhody a nevýhody, které mohou být relevantní pro konkrétní scénář.  

Vezměte v úvahu příklad velké nadnárodní společnosti s desítkami tisíců oddělení a entit zaměstnanců. Každé oddělení má mnoho zaměstnanců a každý zaměstnanec je přidružený k jednomu konkrétnímu oddělení. Jedním z možností je ukládat samostatné entity oddělení a zaměstnanců, například následující:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE01.png" alt-text="Obrázek znázorňující entitu oddělení a entitu zaměstnance":::

Tento příklad ukazuje implicitní vztah 1: n mezi typy, a to na základě `PartitionKey` hodnoty. Každé oddělení může mít mnoho zaměstnanců.  

Tento příklad také ukazuje entitu oddělení a její související entity zaměstnanců ve stejném oddílu. Pro různé typy entit se můžete rozhodnout pro použití různých oddílů, tabulek nebo dokonce účtů úložiště.  

Alternativním řešením je denormalizovat data a ukládat pouze entity zaměstnanců s denormalizovanými daty oddělení, jak je znázorněno v následujícím příkladu. V tomto konkrétním scénáři nemusí být tento denormalizovaný přístup nejlepší, pokud máte požadavek, abyste mohli změnit podrobnosti o manažerovi oddělení. K tomu je potřeba aktualizovat každého zaměstnance v oddělení.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE02.png" alt-text="Obrázek entity zaměstnanci, který ukazuje, jak odnormalizovat data a ukládat pouze entity zaměstnanců s denormalizovanými daty oddělení.":::

Další informace naleznete v části [vzory denormalizace](#denormalization-pattern) dále v této příručce.  

Následující tabulka shrnuje odborníky a nevýhody jednotlivých přístupů k ukládání entit zaměstnanců a oddělení, které mají vztah 1: n. Měli byste taky zvážit, jak často očekáváte provádění různých operací. Může být přijatelné, aby měl návrh, který zahrnuje náročnou operaci, pokud tato operace proběhne jenom zřídka.  

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
<li>Entitu oddělení můžete aktualizovat jedinou operací.</li>
<li>EGT můžete použít k udržení konzistence v případě, že máte požadavek na změnu entity oddělení vždy, když aktualizujete/vložíte/odstraníte entitu zaměstnance. Pokud například udržujete počet zaměstnanců oddělení pro každé oddělení.</li>
</ul>
</td>
<td>
<ul>
<li>Možná budete muset pro některé aktivity klienta načíst jak zaměstnance, tak i entitu oddělení.</li>
<li>K operacím úložiště dochází ve stejném oddílu. Ve velkém objemu transakcí může to mít za následek aktivní hotspot.</li>
<li>Zaměstnance nemůžete přesunout do nového oddělení pomocí EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Samostatné typy entit, různé oddíly nebo tabulky nebo účty úložiště</td>
<td>
<ul>
<li>Můžete aktualizovat entitu oddělení nebo entitu zaměstnanci jedinou operací.</li>
<li>Na vysoce transakčních svazcích to může přispět k rozložení zatížení napříč více oddíly.</li>
</ul>
</td>
<td>
<ul>
<li>Možná budete muset pro některé aktivity klienta načíst jak zaměstnance, tak i entitu oddělení.</li>
<li>EGTs se nedá použít k zachování konzistence při aktualizaci, vložení nebo odstranění zaměstnance a aktualizaci oddělení. Například aktualizace počtu zaměstnanců v entitě oddělení.</li>
<li>Zaměstnance nemůžete přesunout do nového oddělení pomocí EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Odnormalizovat do jedné entity typu</td>
<td>
<ul>
<li>Všechny potřebné informace můžete načíst pomocí jediného požadavku.</li>
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

### <a name="one-to-one-relationships"></a>Relace 1:1
Doménové modely můžou mezi entitami zahrnovat relace 1:1. Pokud potřebujete v úložišti tabulek implementovat relaci 1:1, musíte také zvolit, jak propojit tyto dvě související entity, když je potřebujete načítat. Tento odkaz může být buď implicitní, na základě konvence v hodnotách klíče, nebo explicitní, a to uložením odkazu ve formě `PartitionKey` a `RowKey` hodnot v každé entitě na související entitu. Diskuzi o tom, jestli byste měli související entity ukládat do stejného oddílu, najdete v části [relace 1: n](#one-to-many-relationships).  

K dispozici jsou také pokyny k implementaci, které by vám mohly vést k implementaci relací 1:1 v tabulce úložiště:  

* Zpracování velkých entit (Další informace najdete v tématu [vzor velkých entit](#large-entities-pattern)).  
* Implementace ovládacích prvků přístupu (Další informace najdete v tématu [řízení přístupu pomocí sdílených přístupových podpisů](#control-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Připojit se v klientovi
I když existují způsoby modelování vztahů v úložišti tabulek, nezapomeňte, že dva hlavní důvody pro použití služby Table Storage jsou škálovatelnost a výkon. Pokud zjistíte, že pracujete s modelem mnoha vztahů, které ohrožují výkon a škálovatelnost vašeho řešení, měli byste se dotazovat, pokud je nutné sestavit všechny relace dat do návrhu tabulky. Je možné, že budete moci zjednodušit návrh a zlepšit škálovatelnost a výkon vašeho řešení, pokud povolíte, aby klientská aplikace prováděla potřebná spojení.  

Například pokud máte malé tabulky obsahující data, která se nemění často, můžete tato data načíst jednou a uložit je do mezipaměti v klientovi. To se může vyhnout opakovaným zpětným vrácením, aby se načetla stejná data. V příkladech, které jsme prohlédli v tomto průvodci, se sada oddělení v malé organizaci bude pravděpodobně malým a často mění. Díky tomu je vhodným kandidátem na data, která může klientská aplikace Stáhnout, a uložit je jako data vyhledávání.  

### <a name="inheritance-relationships"></a>Vztahy dědičnosti
Pokud vaše klientská aplikace používá sadu tříd, které tvoří část vztahu dědičnosti k reprezentaci obchodních entit, můžete tyto entity snadno uchovat v úložišti tabulek. Například můžete mít následující sadu tříd, které jsou definovány v klientské aplikaci, kde `Person` je abstraktní třída.

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE03.png" alt-text="Diagram vztahů dědičnosti":::

Instance dvou konkrétních tříd v úložišti tabulek můžete zachovat pomocí jedné `Person` tabulky. Použijte entity, které vypadají jako následující:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE04.png" alt-text="Obrázek znázorňující entitu zákazníka a zaměstnance":::

Další informace o práci s více typy entit ve stejné tabulce v kódu klienta najdete v části [práce s heterogenními typy entit](#work-with-heterogeneous-entity-types) dále v této příručce. V této části najdete příklady, jak rozpoznat typ entity v kódu klienta.  

## <a name="table-design-patterns"></a>Způsoby návrhu tabulek
V předchozích částech jste se dozvěděli o tom, jak optimalizovat návrh tabulky pro načítání dat entity pomocí dotazů, a pro vkládání, aktualizaci a odstraňování dat entit. Tato část popisuje některé vzory vhodné pro použití s tabulkovým úložištěm. Kromě toho se dozvíte, jak můžete prakticky vyřešit některé problémy a kompromisy, které byly vyvolány dříve v této příručce. Následující diagram shrnuje vztahy mezi různými vzory:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE05.png" alt-text="Diagram vzorů návrhu tabulky":::

Mapa vzorů zvýrazňuje některé vztahy mezi vzory (modré) a antipatterns (oranžová), které jsou popsány v tomto průvodci. Existuje samozřejmě mnoho dalších vzorů, které je potřeba zvážit. Jedním z klíčových scénářů pro úložiště tabulek je například použití [schématu materializované zobrazení](/previous-versions/msp-n-p/dn589782(v=pandp.10)) ze vzoru [oddělení zodpovědnosti dotazu příkazu](/previous-versions/msp-n-p/jj554200(v=pandp.10)) .  

### <a name="intra-partition-secondary-index-pattern"></a>Vzor sekundárního indexu v rámci oddílu
Více kopií každé entity uložte pomocí různých `RowKey` hodnot (ve stejném oddílu). Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých `RowKey` hodnot. Aktualizace mezi kopiemi se můžou uchovávat konzistentně pomocí EGTs.  

#### <a name="context-and-problem"></a>Kontext a problém
Table Storage automaticky indexuje entity pomocí `PartitionKey` hodnot a `RowKey` . To umožňuje klientské aplikaci efektivně načíst entitu pomocí těchto hodnot. Například pomocí následující struktury tabulky může klientská aplikace použít dotaz typu Point k načtení konkrétní entity zaměstnance pomocí názvu oddělení a ID zaměstnance ( `PartitionKey` `RowKey` hodnoty a). Klient může také načíst entity seřazené podle ID zaměstnance v rámci každého oddělení.

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE06.png" alt-text="Obrázek entity zaměstnance, kde klientská aplikace může použít dotaz na bod k načtení jednotlivé entity zaměstnance pomocí názvu oddělení a ID zaměstnance (hodnoty PartitionKey a RowKey).":::

Pokud chcete také vyhledat entitu zaměstnance na základě hodnoty jiné vlastnosti, jako je například e-mailová adresa, je třeba použít méně efektivní kontrolu oddílů a vyhledat shodu. Důvodem je to, že služba Table Storage neposkytuje sekundární indexy. Kromě toho neexistuje možnost vyžádat si seznam zaměstnanců seřazených v jiném pořadí než v `RowKey` pořadí.  

#### <a name="solution"></a>Řešení
Pokud chcete obejít nedostatku sekundárních indexů, můžete uložit více kopií každé entity s každou kopií s použitím jiné `RowKey` hodnoty. Pokud uložíte entitu s následujícími strukturami, můžete efektivně načíst entity zaměstnanců na základě e-mailové adresy nebo ID zaměstnance. Hodnoty předpony pro `RowKey` , `empid_` a `email_` umožňují dotazovat se na jednoho zaměstnance nebo na určitou škálu zaměstnanců pomocí rozsahu e-mailových adres nebo ID zaměstnanců.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE07.png" alt-text="Obrázek znázorňující entitu zaměstnance s různými RowKey hodnotami":::

Následující dvě kritéria filtru (jedno vyhledávání podle ID zaměstnance a jedna z nich hledají e-mailovou adresu) určují obě dotazy na bod:  

* $filter = (PartitionKey EQ ' Sales ') a (RowKey EQ ' empid_000223 ')  
* $filter = (PartitionKey EQ ' Sales ') a (RowKey EQ ' email_jonesj@contoso.com ')  

Pokud se dotazuje na rozsah entit zaměstnanců, můžete určit rozsah seřazený v pořadí podle ID zaměstnance nebo rozsah seřazený v e-mailové adrese. Dotaz na entity s odpovídající předponou v `RowKey` .  

* Chcete-li najít všechny zaměstnance v rámci prodejního oddělení s ID zaměstnance v rozsahu 000100 až 000199, použijte: $filter = (PartitionKey EQ ' Sales ') a (RowKey GE ' empid_000100 ') a (RowKey Le ' empid_000199 ')  
* Chcete-li najít všechny zaměstnance v oddělení Sales s e-mailovou adresou začínající písmenem "a", použijte: $filter = (PartitionKey EQ ' Sales ') a (RowKey GE ' email_a ') a (RowKey lt ' email_b ')  
  
Syntaxe filtru použitá v předchozích příkladech je z tabulkového úložiště REST API. Další informace najdete v tématu věnovaném [dotazování entit](/rest/api/storageservices/Query-Entities).  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Úložiště tabulek je poměrně levné na použití, takže náklady na ukládání duplicitních dat by neměly být zásadním problémem. Měli byste ale vždycky vyhodnotit náklady na váš návrh na základě předpokládaných požadavků na úložiště a přidávat duplicitní entity jenom na podporu dotazů, které vaše klientská aplikace spustí.  
* Vzhledem k tomu, že se entity sekundárního indexu ukládají do stejného oddílu jako původní entity, ujistěte se, že nepřekračujete cíle škálovatelnosti pro jednotlivé oddíly.  
* Duplicitní entity můžete udržovat vzájemně konzistentní pomocí EGTs k aktualizaci dvou kopií této entity. To znamená, že byste měli ukládat všechny kopie entity do stejného oddílu. Další informace najdete v tématu [použití transakcí skupin entit](#entity-group-transactions).  
* Hodnota použitá pro `RowKey` musí být jedinečná pro každou entitu. Zvažte použití hodnot složených klíčů.  
* Vyplňování číselných hodnot v `RowKey` (například zaměstnance s ID 000223) umožňuje správné řazení a filtrování na základě horních a dolních mezí.  
* Nemusíte nutně Duplikovat všechny vlastnosti vaší entity. Pokud například dotazy, které hledají entity pomocí e-mailové adresy `RowKey` , nikdy nepotřebují věk zaměstnance, můžou mít tyto entity následující strukturu:

  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE08.png" alt-text="Obrázek entity zaměstnance":::

* Obvykle je lepší ukládat duplicitní data a zajistit, že všechna data, která potřebujete, můžete načíst pomocí jediného dotazu, než můžete použít jeden dotaz k vyhledání entity a jiné pro vyhledání požadovaných dat.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v těchto případech:

- Klientská aplikace musí načítat entity pomocí různých klíčů.
- Váš klient potřebuje načíst entity v různých objednávkách řazení.
- Každou entitu můžete identifikovat pomocí nejrůznějších jedinečných hodnot.

Ujistěte se však, že při provádění vyhledávání entit nepřekračujete omezení škálovatelnosti oddílu pomocí různých `RowKey` hodnot.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor sekundárního indexu mezi oddíly](#inter-partition-secondary-index-pattern)
* [Vzor složeného klíče](#compound-key-pattern)
* [Transakce skupiny entit](#entity-group-transactions)
* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Vzor sekundárního indexu mezi oddíly
Více kopií každé entity uložte pomocí různých `RowKey` hodnot v samostatných oddílech nebo v samostatných tabulkách. Díky tomu můžete rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých `RowKey` hodnot.  

#### <a name="context-and-problem"></a>Kontext a problém
Table Storage automaticky indexuje entity pomocí `PartitionKey` hodnot a `RowKey` . To umožňuje klientské aplikaci efektivně načíst entitu pomocí těchto hodnot. Například pomocí následující struktury tabulky může klientská aplikace použít dotaz typu Point k načtení konkrétní entity zaměstnance pomocí názvu oddělení a ID zaměstnance ( `PartitionKey` `RowKey` hodnoty a). Klient může také načíst entity seřazené podle ID zaměstnance v rámci každého oddělení.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE09.png" alt-text="Obrázek struktury entity zaměstnanců, která při použití může klientská aplikace použít dotaz na bod k načtení jednotlivé entity zaměstnance pomocí názvu oddělení a ID zaměstnance (hodnoty PartitionKey a RowKey)."::: 9

Pokud chcete také vyhledat entitu zaměstnance na základě hodnoty jiné vlastnosti, jako je například e-mailová adresa, je třeba použít méně efektivní kontrolu oddílů a vyhledat shodu. Důvodem je to, že služba Table Storage neposkytuje sekundární indexy. Kromě toho neexistuje možnost vyžádat si seznam zaměstnanců seřazených v jiném pořadí než v `RowKey` pořadí.  

Očekáváte velký objem transakcí na těchto entitách a chcete minimalizovat riziko, že míra úložiště tabulek omezuje vaše klienta.  

#### <a name="solution"></a>Řešení
Pokud chcete vymezit nedostatku sekundárních indexů, můžete ukládat víc kopií každé z těchto entit s každou kopií pomocí různých `PartitionKey` `RowKey` hodnot a. Pokud uložíte entitu s následujícími strukturami, můžete efektivně načíst entity zaměstnanců na základě e-mailové adresy nebo ID zaměstnance. Hodnoty předpony pro `PartitionKey` , `empid_` a `email_` umožňují určit, který index chcete použít pro dotaz.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE10.png" alt-text="Obrázek znázorňující entitu zaměstnance s primárním indexem a entitou zaměstnance se sekundárním indexem":::

Následující dvě kritéria filtru (jedno vyhledávání podle ID zaměstnance a jedna z nich hledají e-mailovou adresu) určují obě dotazy na bod:  

* $filter = (PartitionKey EQ ' empid_Sales ') a (RowKey EQ ' 000223 ')
* $filter = (PartitionKey EQ ' email_Sales ') a (RowKey EQ ' jonesj@contoso.com ')  

Pokud se dotazuje na rozsah entit zaměstnanců, můžete určit rozsah seřazený v pořadí podle ID zaměstnance nebo rozsah seřazený v e-mailové adrese. Dotaz na entity s odpovídající předponou v `RowKey` .  

* Chcete-li najít všechny zaměstnance v rámci prodejního oddělení s ID zaměstnance v rozsahu **000100** až **000199**, seřazené v pořadí ID zaměstnanců, použijte: $Filter = (PartitionKey EQ ' empid_Sales ') a (RowKey GE ' 000100 ') a (RowKey Le ' 000199 ')  
* Chcete-li najít všechny zaměstnance v prodejním oddělení pomocí e-mailové adresy, která začíná na "a" seřazené v e-mailové adrese, použijte: $filter = (PartitionKey EQ ' email_Sales ') a (RowKey GE ' a ') a (RowKey lt ' b ')  

Všimněte si, že syntaxe filtru použitá v předchozích příkladech je z tabulkového úložiště REST API. Další informace najdete v tématu věnovaném [dotazování entit](/rest/api/storageservices/Query-Entities).  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* V případě, že jsou vaše duplicitní entity trvale konzistentní, pomocí [vzoru nakonec konzistentní transakce](#eventually-consistent-transactions-pattern) Udržujte entity primárního a sekundárního indexu.  
* Úložiště tabulek je poměrně levné na použití, takže náklady spojené s ukládáním duplicitních dat by neměly být zásadním problémem. Na základě předpokládaných požadavků na úložiště ale vždy vyhodnoťte náklady na váš návrh a přidejte duplicitní entity, které budou podporovat dotazy, které klientská aplikace spustí.  
* Hodnota použitá pro `RowKey` musí být jedinečná pro každou entitu. Zvažte použití hodnot složených klíčů.  
* Vyplňování číselných hodnot v `RowKey` (například zaměstnance s ID 000223) umožňuje správné řazení a filtrování na základě horních a dolních mezí.  
* Nemusíte nutně Duplikovat všechny vlastnosti vaší entity. Pokud například dotazy, které hledají entity pomocí e-mailové adresy `RowKey` , nikdy nepotřebují věk zaměstnance, můžou mít tyto entity následující strukturu:
  
  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE11.png" alt-text="Obrázek znázorňující entitu zaměstnance se sekundárním indexem":::

* Obvykle je lepší ukládat duplicitní data a zajistit, abyste mohli načíst všechna potřebná data s jediným dotazem, než použijete jeden dotaz k vyhledání entity pomocí sekundárního indexu a další pro vyhledání požadovaných dat v primárním indexu.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v těchto případech:

- Klientská aplikace musí načítat entity pomocí různých klíčů.
- Váš klient potřebuje načíst entity v různých objednávkách řazení.
- Každou entitu můžete identifikovat pomocí nejrůznějších jedinečných hodnot.

Tento model použijte, pokud chcete vyhnout překročení omezení škálovatelnosti oddílu při provádění vyhledávání entit pomocí různých `RowKey` hodnot.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor nakonec konzistentních transakcí](#eventually-consistent-transactions-pattern)  
* [Vzor sekundárního indexu v rámci oddílu](#intra-partition-secondary-index-pattern)  
* [Vzor složeného klíče](#compound-key-pattern)  
* [Transakce skupiny entit](#entity-group-transactions)  
* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Vzor nakonec konzistentních transakcí
Pomocí front Azure povolte nakonec konzistentní chování napříč hranicemi oddílů nebo hranicemi systému úložiště.  

#### <a name="context-and-problem"></a>Kontext a problém
EGTs umožňují atomické transakce napříč několika entitami, které sdílejí stejný klíč oddílu. V zájmu výkonu a škálovatelnosti se můžete rozhodnout ukládat entity, které mají požadavky na konzistenci v samostatných oddílech nebo v samostatném systému úložiště. V takovém případě nemůžete použít EGTs k zachování konzistence. Například můžete mít požadavek na udržení konečné konzistence mezi:  

* Entity uložené ve dvou různých oddílech ve stejné tabulce, v různých tabulkách nebo v různých účtech úložiště.  
* Entita uložená v úložišti tabulek a objekt BLOB uložený v úložišti objektů BLOB  
* Entita uložená v úložišti tabulek a v souboru v systému souborů.  
* Entita uložená v úložišti tabulek, která je ještě indexována pomocí Azure Kognitivní hledání.  

#### <a name="solution"></a>Řešení
Pomocí front Azure můžete implementovat řešení, které poskytuje konečnou konzistenci ve dvou nebo více oddílech nebo systémech úložišť.

Pro ilustraci tohoto přístupu Předpokládejme, že máte požadavek na to, abyste mohli archivovat bývalé entity zaměstnanců. Bývalé entity zaměstnanců se dotazují zřídka a měly by být vyloučeny ze všech aktivit, které se týkají současných zaměstnanců. K implementaci tohoto požadavku ukládáte aktivní zaměstnance do **aktuální** tabulky a bývalé zaměstnance v **archivní** tabulce. Archivace zaměstnance vyžaduje, abyste odstranili entitu z **aktuální** tabulky a přidali entitu do **archivní** tabulky.

K provádění těchto dvou operací ale nemůžete použít EGT. Aby nedocházelo k riziku, že selhání způsobilo, že se entita objevila v obou nebo ani v tabulkách, musí být operace archivu nakonec konzistentní. Následující sekvenční diagram popisuje kroky v této operaci.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE12.png" alt-text="Diagram řešení pro případnou konzistenci":::

Klient inicializuje operaci archivu tím, že umístí zprávu do fronty Azure (v tomto příkladu pro archivaci #456 zaměstnanců). Role pracovního procesu se dotazuje fronty na nové zprávy; Když ho najde, přečte zprávu a ponechá ve frontě skrytou kopii. Role pracovního procesu Next načte kopii entity z **aktuální** tabulky, vloží kopii do **archivní** tabulky a odstraní původní z **aktuální** tabulky. Nakonec, pokud z předchozích kroků nedošlo k chybám, role pracovního procesu odstraní skrytou zprávu z fronty.  

V tomto příkladu krok 4 v diagramu vloží zaměstnance do **archivní** tabulky. Může přidat zaměstnance do objektu BLOB v úložišti objektů BLOB nebo v souboru v systému souborů.  

#### <a name="recover-from-failures"></a>Obnovit po selhání
Je důležité, aby operace v krocích 4-5 v diagramu byly *idempotentní* v případě, že role pracovního procesu potřebuje restartovat operaci archivace. Pokud používáte úložiště Table, měli byste pro krok 4 použít operaci vložení nebo nahrazení; v kroku 5 byste měli použít operaci odstranit, pokud existuje, v klientské knihovně, kterou používáte. Pokud používáte jiný systém úložiště, je nutné použít příslušnou operaci idempotentní.  

Pokud role pracovního procesu nikdy nedokončí krok 6 v diagramu, potom po vypršení časového limitu se tato zpráva zobrazí ve frontě připravené pro roli pracovního procesu a pokusí se ji znovu zpracovat. Role pracovního procesu může kontrolovat počet čtení zprávy ve frontě a v případě potřeby ji označit jako "nezpracovatelnou" zprávu pro účely šetření odesláním do samostatné fronty. Další informace o čtení zpráv fronty a o kontrole počtu vyřazování z fronty najdete v tématu [Get Messages](/rest/api/storageservices/Get-Messages).  

Některé chyby z tabulkového úložiště a úložiště front jsou přechodné chyby a vaše klientská aplikace by měla k jejich zpracování použít vhodnou logiku opakování.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení neposkytuje izolaci transakcí. Klient může například číst **aktuální** a **archivní** tabulky, pokud byla role pracovního procesu mezi kroky 4-5 v diagramu, a zobrazit nekonzistentní zobrazení dat. Data budou nakonec konzistentní.  
* Musíte mít jistotu, že kroky 4-5 jsou idempotentní, aby se zajistila konečná konzistence.  
* Řešení můžete škálovat pomocí několika front a instancí rolí pracovního procesu.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, pokud chcete zaručit konečnou konzistenci mezi entitami, které existují v různých oddílech nebo tabulkách. Tento model můžete roztáhnout, aby se zajistila konečná konzistence operací napříč tabulkovým úložištěm a úložištěm objektů BLOB a dalšími neAzure Storagemi zdroji dat, jako je databáze nebo systém souborů.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entit](#entity-group-transactions)  
* [Sloučit nebo nahradit](#merge-or-replace)  

> [!NOTE]
> Pokud je pro vaše řešení důležité oddělení transakcí, zvažte možnost změnit návrh tabulek, abyste mohli používat EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Vzor entit indexu
Udržujte entity indexu, abyste umožnili efektivní hledání, které vrací seznam entit.  

#### <a name="context-and-problem"></a>Kontext a problém
Table Storage automaticky indexuje entity pomocí `PartitionKey` hodnot a `RowKey` . To umožňuje klientské aplikaci efektivně načíst entitu pomocí dotazu na bod. Například pomocí následující struktury tabulek může klientská aplikace efektivně načíst jednotlivou entitu zaměstnance pomocí názvu oddělení a ID zaměstnance ( `PartitionKey` a `RowKey` ).  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE13.png" alt-text="Obrázek struktury entity zaměstnanců, kde klientská aplikace může efektivně načíst jednotlivou entitu zaměstnance pomocí názvu oddělení a ID zaměstnance (PartitionKey a RowKey).":::

Pokud chcete mít také přístup k seznamu entit zaměstnanců v závislosti na hodnotě jiné nejedinečné vlastnosti, jako je například příjmení, je nutné použít méně efektivní kontrolu oddílů. Tato kontrola vyhledá shodu a místo toho použije index k jejich přímému vyhledání. Důvodem je to, že služba Table Storage neposkytuje sekundární indexy.  

#### <a name="solution"></a>Řešení
Chcete-li povolit vyhledávání podle příjmení s předchozí strukturou entit, je nutné udržovat seznamy ID zaměstnanců. Pokud chcete načíst entity zaměstnanců s konkrétním názvem, jako je například Novotný, musíte nejprve vyhledat seznam ID zaměstnanců pro zaměstnance s názvem Novotný jako své příjmení a pak tyto entity zaměstnanců načíst. Existují tři hlavní možnosti ukládání seznamů ID zaměstnanců:  

* Použijte úložiště objektů BLOB.  
* Vytvořte entity indexu ve stejném oddílu jako entity zaměstnanců.  
* Vytvořte entity indexu v samostatném oddílu nebo tabulce.  

Možnost 1: použití úložiště objektů BLOB  

Vytvořte objekt BLOB pro každé jedinečné příjmení a v každém objektu BLOB uložte seznam `PartitionKey` hodnot (oddělení) a `RowKey` (ID zaměstnance) pro zaměstnance, kteří mají toto příjmení. Když zaměstnance přidáte nebo odstraníte, zajistěte, aby obsah relevantního objektu BLOB byl nakonec konzistentní s entitami zaměstnanců.  

Možnost 2: vytvoření entit indexu ve stejném oddílu  

Použijte entity indexů, které ukládají následující data:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE14.png" alt-text="Obrázek znázorňující entitu zaměstnance s řetězcem, který obsahuje seznam ID zaměstnanců se stejným posledním jménem":::

`EmployeeIDs`Vlastnost obsahuje seznam ID zaměstnanců pro zaměstnance s posledním jménem uloženým v `RowKey` .  

Následující kroky popisují postup, který byste měli provést při přidávání nového zaměstnance. V tomto příkladu přidáme zaměstnance s ID 000152 a názvem Novotný do oddělení Sales:  

1. Načtěte indexovou entitu s `PartitionKey` hodnotou Sales a `RowKey` hodnotou "Novotný". Uložte značku ETag této entity, která se použije v kroku 2.  
2. Vytvoření transakce skupiny entit (tj. operace dávky), která vloží novou entitu zaměstnance ( `PartitionKey` hodnota prodej a `RowKey` hodnota "000152") a aktualizuje entitu indexu ( `PartitionKey` hodnota prodej a `RowKey` hodnota "Novotný"). EGT to dělá přidáním nového ID zaměstnance do seznamu v poli ČísloZaměstnance. Další informace o EGTs najdete v tématu [transakce skupiny entit](#entity-group-transactions).  
3. Pokud se EGT nepovede kvůli optimistické chybě souběžnosti (to znamená, že entita indexu změnila někdo jiný), pak je potřeba začít znovu v kroku 1.  

Pokud používáte druhou možnost, můžete použít podobný přístup k odstranění zaměstnance. Změna příjmení zaměstnance je mírně složitější, protože potřebujete spustit EGT, který aktualizuje tři entity: entita zaměstnance, indexová entita pro staré příjmení a entitu indexu pro nové příjmení. Musíte načíst každou entitu před provedením jakýchkoli změn, aby bylo možné načíst hodnoty ETag, které pak můžete použít k provedení aktualizací pomocí optimistické souběžnosti.  

Následující kroky popisují postup, který byste měli provést, pokud potřebujete vyhledat všechny zaměstnance s určitým jménem v oddělení. V tomto příkladu prohledáváme všechny zaměstnance s názvem Novotný v oddělení Sales (prodej):  

1. Načtěte indexovou entitu s `PartitionKey` hodnotou Sales a `RowKey` hodnotou "Novotný".  
2. Analyzovat seznam ID zaměstnanců v `EmployeeIDs` poli.  
3. Pokud potřebujete další informace o každém z těchto zaměstnanců (například jejich e-mailové adresy), načtěte každou entitu zaměstnance pomocí `PartitionKey` hodnoty "prodej" a `RowKey` hodnot ze seznamu zaměstnanců, které jste získali v kroku 2.  

Možnost 3: vytvoření entit indexu v samostatném oddílu nebo tabulce  

Pro tuto možnost použijte entity indexů, které ukládají následující data:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE15.png" alt-text="Snímek obrazovky zobrazující entitu indexu zaměstnance obsahující seznam ID zaměstnanců pro zaměstnance s posledním jménem uloženým v RowKey a PartitionKey.":::

`EmployeeDetails`Vlastnost obsahuje seznam ID zaměstnanců a páry názvů oddělení pro zaměstnance s posledním jménem uloženým v `RowKey` .

EGTs se nedá použít k udržení konzistence, protože entity indexu jsou v samostatném oddílu od entit zaměstnanců. Zajistěte, aby entity indexu byly nakonec konzistentní s entitami zaměstnanců.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení vyžaduje aspoň dva dotazy, aby se načetly vyhovující entity: jednu pro dotazování entit indexu pro získání seznamu `RowKey` hodnot a pak dotazy pro načtení jednotlivých entit v seznamu.  
* Vzhledem k tomu, že každá entita má maximální velikost 1 MB, možnost 2 a možnost 3 v řešení, předpokládá, že seznam ID zaměstnanců pro jakékoli konkrétní příjmení není nikdy větší než 1 MB. Pokud je seznam ID zaměstnanců pravděpodobně větší než 1 MB, použijte možnost 1 a uložte data indexu do úložiště objektů BLOB.  
* Pokud použijete možnost 2 (pomocí EGTs k obsluze přidávání a odstraňování zaměstnanců a změníte příjmení zaměstnance), musíte vyhodnotit, jestli se objem transakcí bude přicházet k omezením škálovatelnosti v konkrétním oddílu. Pokud se jedná o tento případ, měli byste zvážit vhodné řešení (možnost 1 nebo možnost 3). Tyto fronty slouží ke zpracování požadavků na aktualizace a umožňují ukládat entity indexu do samostatného oddílu od entit zaměstnanců.  
* Možnost 2 v tomto řešení předpokládá, že chcete hledat podle příjmení v rámci oddělení. Například chcete načíst seznam zaměstnanců s posledním názvem Novotný v oddělení Sales (prodej). Pokud chcete mít možnost Hledat všechny zaměstnance s posledním názvem Novotný v celé organizaci, použijte jednu z možností 1 nebo 3.
* Můžete implementovat řešení založené na frontách, které poskytuje konečnou konzistenci. Další podrobnosti najdete ve [vzoru nakonec konzistentní transakce](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že chcete vyhledat sadu entit, které budou sdílet všechny společné hodnoty vlastností, jako jsou všichni zaměstnanci s posledním názvem Novotný.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor složeného klíče](#compound-key-pattern)  
* [Vzor nakonec konzistentních transakcí](#eventually-consistent-transactions-pattern)  
* [Transakce skupiny entit](#entity-group-transactions)  
* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Vzorek denormalizace
Kombinování souvisejících dat společně v jedné entitě vám umožní načíst všechna potřebná data pomocí dotazu s jedním bodem.  

#### <a name="context-and-problem"></a>Kontext a problém
V relační databázi obvykle Normalizujte data pro odstranění duplicit, ke kterým dochází, když dotazy načítají data z více tabulek. Pokud normalizete data v tabulkách Azure, je potřeba provést několik přenosů od klienta k serveru, aby se načetla vaše související data. Například s následující strukturou tabulky potřebujete dvě zpáteční cesty pro získání podrobností o oddělení. Jedna cesta načte entitu oddělení, která zahrnuje ID manažera, a druhá cesta načte podrobnosti správce v entitě zaměstnanec.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE16.png" alt-text="Grafika entity oddělení a entit zaměstnanců":::

#### <a name="solution"></a>Řešení
Místo uložení dat ve dvou samostatných entitách denormalizujte data a udržujte kopii podrobností manažera v entitě oddělení. Příklad:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE17.png" alt-text="Obrázek denormalizované a kombinované entity oddělení":::

S entitami oddělení uloženými s těmito vlastnostmi teď můžete načíst všechny podrobnosti, které potřebujete o oddělení, pomocí dotazu na bod.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* K ukládání dat dvakrát se účtují nějaké náklady. Výhoda výkonu vyplývající z menšího počtu požadavků na úložiště tabulek obvykle převažuje nad nárůstem nákladů na úložiště. Dále jsou tyto náklady částečně posunuty snížením počtu transakcí, které potřebujete k načtení podrobností o oddělení.  
* Je nutné zachovat konzistenci dvou entit, které ukládají informace o manažerech. Problém konzistence můžete zpracovat pomocí EGTs k aktualizaci více entit v jedné atomické transakci. V tomto případě se entita oddělení a entita zaměstnanci pro správce oddělení ukládají do stejného oddílu.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, pokud často potřebujete vyhledat související informace. Tento model snižuje počet dotazů, které musí váš klient učinit, aby mohl načíst data, která vyžaduje.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor složeného klíče](#compound-key-pattern)  
* [Transakce skupiny entit](#entity-group-transactions)  
* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Vzor složeného klíče
Pomocí složených `RowKey` hodnot můžete klientovi Povolit hledání souvisejících dat pomocí dotazu s jedním bodem.  

#### <a name="context-and-problem"></a>Kontext a problém
V relační databázi je použití spojení v dotazech k vrácení souvisejících částí dat klientovi v jednom dotazu. ID zaměstnance můžete například použít k vyhledání seznamu souvisejících entit, které obsahují údaje o výkonu a kontrole daného zaměstnance.  

Předpokládejme, že ukládáte entity zaměstnanců do úložiště tabulek pomocí následující struktury:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE18.png" alt-text="Grafika struktury entit zaměstnanců, kterou byste měli použít k ukládání entit zaměstnanců do úložiště tabulek.":::

Je také potřeba ukládat historická data týkající se kontrol a výkonu každého roku, který zaměstnanec pracoval pro vaši organizaci, a vy budete mít přístup k těmto informacím po rocích. Jednou z možností je vytvořit další tabulku, která obsahuje entity s následující strukturou:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE19.png" alt-text="Obrázek entity kontroly zaměstnance":::

S tímto přístupem se můžete rozhodnout duplikovat některé informace (například křestní jméno a příjmení) v nové entitě, abyste mohli data načíst pomocí jediného požadavku. Nemůžete ale zachovat silnou konzistenci, protože nemůžete použít EGT k tomu, aby se tyto dvě entity používaly atomicky.  

#### <a name="solution"></a>Řešení
Uložte do původní tabulky nový typ entity pomocí entit s následující strukturou:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE20.png" alt-text="Obrázek entity zaměstnance se složeným klíčem":::

Všimněte si `RowKey` , jak je nyní složený klíč, který se skládá z ID zaměstnance a roku revizních dat. Díky tomu můžete načíst výkon a zkontrolovat data pomocí jediné žádosti pro jednu entitu.  

Následující příklad popisuje, jak můžete načíst všechna data revize pro konkrétního zaměstnance (například zaměstnanec 000123 v prodejním oddělení):  

$filter = (PartitionKey EQ ' Sales ') a (RowKey GE ' empid_000123 ') a (RowKey lt ' empid_000124 ') &$select = RowKey, hodnocení manažera, partnerské hodnocení, komentáře  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Měli byste použít vhodný oddělovací znak, který usnadňuje analýzu `RowKey` hodnoty: například **000123_2012**.  
* Tuto entitu ukládáte i do stejného oddílu jako jiné entity, které obsahují související data pro stejného zaměstnance. To znamená, že můžete pomocí EGTs zachovat silnou konzistenci.
* Měli byste zvážit, jak často budete zadávat dotazy na data, abyste zjistili, jestli je tento model vhodný. Například pokud ke kontrole dat přistupujete nečasto a hlavním datům zaměstnanců, měli byste je uchovávat jako samostatné entity.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že potřebujete uložit jednu nebo více souvisejících entit, které často dotazují.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entit](#entity-group-transactions)  
* [Práce s heterogenními typy entit](#work-with-heterogeneous-entity-types)  
* [Vzor nakonec konzistentních transakcí](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Vzor koncového protokolu
Načtěte entity *n* naposledy přidané do oddílu pomocí `RowKey` hodnoty, která se seřadí v pořadí zpětného data a času.  

> [!NOTE]
> Výsledky dotazu vrácené službou Azure rozhraní API pro tabulky v Azure Cosmos DB nejsou seřazené podle klíče oddílu nebo klíče řádku. Takže i když je tento model vhodný pro úložiště tabulek, není vhodný pro Azure Cosmos DB. Podrobný seznam rozdílů funkcí najdete v tématu [rozdíly mezi rozhraní API pro tabulky v Azure Cosmos DB a v Azure Table Storage](table-api-faq.md#table-api-vs-table-storage).

#### <a name="context-and-problem"></a>Kontext a problém
Běžným požadavkem je, aby bylo možné načíst naposledy vytvořené entity, například deset největších nedávných deklarací identity odeslaných zaměstnancem. Dotazy tabulky podporují `$top` operaci dotazu, která vrací první *n* entit ze sady. Neexistuje žádná ekvivalentní operace dotazu, která by vrátila poslední *n* entit v sadě.  

#### <a name="solution"></a>Řešení
Uložte entity pomocí typu `RowKey` , který přirozeně seřadí v pořadí podle data a času, takže poslední položka je vždy první v tabulce.  

Pokud například chcete mít možnost načíst deset nejaktuálnějších deklarací výdajů odeslaných zaměstnancem, můžete použít reverzní hodnotu zahozenou od aktuálního data a času. Následující ukázka kódu jazyka C# ukazuje jeden ze způsobů, jak vytvořit vhodnou hodnotu "obrácené" značky " `RowKey` , která je seřazena od nejnovějšího po nejstarší:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

K hodnotě data a času se můžete vrátit pomocí následujícího kódu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Dotaz na tabulku vypadá takto:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Chcete-li zajistit, aby se řetězcová hodnota seřadí podle očekávání, je nutné obklopit hodnotu reverzní Tick počátečními nulami.  
* Musíte znát cíle škálovatelnosti na úrovni oddílu. Buďte opatrní, abyste nevytvořili oddíly s aktivním bodem.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že potřebujete získat přístup k entitám v pořadí zpětného data a času, nebo když potřebujete přístup k naposledy přidaným entitám.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Předřadit/připojit anti-Pattern](#prepend-append-anti-pattern)  
* [Načtení entit](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Vzor vysokého odstranění svazků
Umožněte odstranění velkého objemu entit tím, že uložíte všechny entity pro současné odstranění ve vlastní samostatné tabulce. Odstraňte entity odstraněním tabulky.  

#### <a name="context-and-problem"></a>Kontext a problém
Mnoho aplikací odstraní stará data, která už nejsou k dispozici pro klientskou aplikaci, nebo jestli se aplikace archivuje na jiné paměťové médium. Tato data obvykle Identifikujte podle data. Například máte požadavek na odstranění záznamů všech žádostí o přihlášení, které jsou starší než 60 dní.  

Jedním z možných návrhů je použití data a času žádosti o přihlášení v `RowKey` :  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE21.png" alt-text="Obrázek entity pokus o přihlášení":::

Tento přístup zabraňuje dělení hotspotů, protože aplikace může vkládat a odstraňovat přihlašovací entity pro každého uživatele v samostatném oddílu. Tento přístup ale může být nákladný a časově náročný, pokud máte velký počet entit. Nejprve je třeba provést prohledávání tabulky, aby bylo možné identifikovat všechny entity, které chcete odstranit, a pak musíte odstranit každou starou entitu. Můžete snížit počet zpátečních cest k serveru nutnému k odstranění starých entit dávkování více žádostí o odstranění do EGTs.  

#### <a name="solution"></a>Řešení
Pro každý den pokusů o přihlášení použijte samostatnou tabulku. Můžete použít předchozí návrh entity a vyhnout se tak hotspotům při vkládání entit. Odstranění starých entit je teď pouhou otázkou odstranění jedné tabulky každý den (operace jednoho úložiště) místo hledání a odstraňování stovek a tisíců jednotlivých entit přihlašování každý den.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Podporuje vaše návrh i další způsoby, kterými vaše aplikace bude data používat, jako je například vyhledávání konkrétních entit, propojení s ostatními daty nebo generování agregačních informací?  
* Vyloučí se při vkládání nových entit návrh?  
* Očekává zpoždění, pokud chcete po odstranění znovu použít stejný název tabulky. Je lepší vždycky používat jedinečné názvy tabulek.  
* Očekává se, že při prvním použití nové tabulky dojde k omezení četnosti, zatímco služba Table Storage se učí vzory přístupu a distribuuje oddíly mezi uzly. Měli byste zvážit, jak často potřebujete vytvořit nové tabulky.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že máte velké množství entit, které je nutné odstranit ve stejnou dobu.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entit](#entity-group-transactions)
* [Úpravy entit](#modify-entities)  

### <a name="data-series-pattern"></a>Vzor datových řad
Uložte kompletní datové řady do jedné entity, abyste minimalizovali počet požadavků, které provedete.  

#### <a name="context-and-problem"></a>Kontext a problém
Běžným scénářem je, že aplikace ukládá řadu dat, která obvykle potřebují k načtení všech najednou. Například vaše aplikace může zaznamenat, kolik zpráv IM každý zaměstnanec odesílá každou hodinu, a pak tyto informace použít k vykreslení počtu zpráv, které každý uživatel poslal za předchozích 24 hodin. Jeden návrh může být ukládat 24 entit pro každého zaměstnance:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE22.png" alt-text="Obrázek entity statistiky zprávy":::

S tímto návrhem můžete snadno vyhledat a aktualizovat entitu, která se má aktualizovat pro každého zaměstnance, kdykoli aplikace potřebuje aktualizovat hodnotu počet zpráv. Chcete-li však načíst informace pro vykreslení grafu aktivity za předchozích 24 hodin, je nutné načíst 24 entit.  

#### <a name="solution"></a>Řešení
Použijte následující návrh s samostatnou vlastností pro uložení počtu zpráv pro každou hodinu:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE23.png" alt-text="Obrázek znázorňující entitu statistiky zprávy s oddělenými vlastnostmi":::

S tímto návrhem můžete pomocí operace sloučení aktualizovat počet zpráv zaměstnance na určitou hodinu. Nyní můžete načíst všechny informace potřebné k vykreslení grafu pomocí žádosti o jednu entitu.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Pokud se vaše celá datová řada nevejde do jedné entity (entita může mít až 252 vlastností), použijte alternativní úložiště dat, jako je například objekt BLOB.  
* Pokud máte více klientů s aktualizací entity současně, použijte **značku ETag** k implementaci optimistického řízení souběžnosti. Pokud máte mnoho klientů, může docházet ke vysokému obsahu.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že potřebujete aktualizovat a načíst datovou řadu přidruženou k jednotlivé entitě.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor velkých entit](#large-entities-pattern)  
* [Sloučit nebo nahradit](#merge-or-replace)  
* [Vzorec pro nakonec konzistentní transakce](#eventually-consistent-transactions-pattern) (Pokud ukládáte datovou řadu do objektu BLOB)  

### <a name="wide-entities-pattern"></a>Model pro nejrůznější entity
K ukládání logických entit s více než 252 vlastnostmi použijte více fyzických entit.  

#### <a name="context-and-problem"></a>Kontext a problém
Jednotlivá entita nemůže mít více než 252 vlastností (kromě povinných vlastností systému) a nemůže uchovávat více než 1 MB dat. V relační databázi byste obvykle obejít omezení velikosti řádku přidáním nové tabulky a vynucením vztahu 1:1 mezi nimi.  

#### <a name="solution"></a>Řešení
Pomocí služby Table Storage můžete ukládat více entit, které reprezentují jeden velký obchodní objekt s více než 252 vlastnostmi. Pokud například chcete uložit počet zpráv IM odesílaných jednotlivými zaměstnanci po dobu posledních 365 dní, můžete použít následující návrh, který používá dvě entity s různými schématy:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE24.png" alt-text="Obrázek znázorňující entitu statistiky zprávy s Rowkey 01 a entitou Statistika zprávy s Rowkey 02":::

Pokud potřebujete provést změnu, která vyžaduje aktualizaci obou entit, aby byla vzájemně synchronizovaná, můžete použít EGT. V opačném případě můžete použít jednu operaci sloučení k aktualizaci počtu zpráv pro určitý den. Chcete-li načíst všechna data pro jednotlivé zaměstnance, je nutné načíst obě entity. To lze provést pomocí dvou efektivních požadavků, které používají `PartitionKey` a i `RowKey` .  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Při rozhodování, jak tento model implementovat, vezměte v úvahu následující body:  

* Načtení kompletní logické entity zahrnuje alespoň dvě transakce úložiště: jednu pro načtení každé fyzické entity.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že potřebujete uložit entity, jejichž velikost nebo počet vlastností překračuje limity pro jednotlivé entity v úložišti tabulek.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entit](#entity-group-transactions)
* [Sloučit nebo nahradit](#merge-or-replace)

### <a name="large-entities-pattern"></a>Vzor velkých entit
Úložiště objektů blob můžete použít k ukládání velkých hodnot vlastností.  

#### <a name="context-and-problem"></a>Kontext a problém
Jednotlivé entity nemůžou ukládat celkem víc než 1 MB dat. Pokud jedna nebo několik vlastností ukládá hodnoty, které způsobí, že celková velikost vaší entity překročí tuto hodnotu, nemůžete uložit celou entitu v úložišti tabulek.  

#### <a name="solution"></a>Řešení
Pokud má vaše entita velikost větší než 1 MB, protože jedna nebo více vlastností obsahuje velké množství dat, můžete ukládat data v úložišti objektů BLOB a potom uložit adresu objektu blob do vlastnosti v entitě. Můžete například uložit fotografii zaměstnance v úložišti objektů BLOB a Uložit odkaz na fotografii ve `Photo` vlastnosti vaší entity zaměstnance:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE25.png" alt-text="Obrázek znázorňující entitu zaměstnance s řetězcem pro fotografii ukazující na úložiště objektů BLOB":::

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Aby se zachovala konečná konzistence mezi entitou v úložišti tabulek a daty v úložišti objektů blob, použijte ke správě svých entit [model nakonec konzistentní transakce](#eventually-consistent-transactions-pattern) .
* Načtení kompletní entity zahrnuje alespoň dvě transakce úložiště: jednu pro načtení entity a jednu pro načtení dat objektu BLOB.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že potřebujete ukládat entity, jejichž velikost překračuje limity pro jednotlivé entity v úložišti tabulek.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor nakonec konzistentních transakcí](#eventually-consistent-transactions-pattern)  
* [Model pro nejrůznější entity](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Předřadit/připojit anti-Pattern
Když máte velký objem vložení, zvyšte škálovatelnost tím, že rozšíříte vložení mezi více oddílů.  

#### <a name="context-and-problem"></a>Kontext a problém
Nedokončené nebo připojené entity k uloženým entitám obvykle způsobí, že aplikace přidává nové entity do prvního nebo posledního oddílu sekvence oddílů. V tomto případě jsou všechna vložení v jakémkoli čase prováděna ve stejném oddílu a vytváří hotspot. Tím se zabrání tomu, aby úložiště tabulek z vyrovnávání zátěže vytvářely mezi více uzly, a případně způsobí, že aplikace dosáhne cílů škálovatelnosti pro oddíl. Zvažte například případ aplikace, která protokoluje přístup zaměstnanců k síti a prostředkům. Struktura entity, jako je například následující, může způsobit, že se oddíl aktuální hodiny stane aktivním rozsahem, pokud objem transakcí dosáhne cíle škálovatelnosti pro jednotlivé oddíly:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE26.png" alt-text="Obrázek struktury entity, která může mít za následek, že se oddíl aktuální hodiny stane aktivním rozsahem, pokud objem transakcí dosáhne cíle škálovatelnosti pro jednotlivé oddíly.":::

#### <a name="solution"></a>Řešení
Následující alternativní struktura entity zabraňuje hotspotu v jakémkoli konkrétním oddílu, protože aplikace protokoluje události:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE27.png" alt-text="Obrázek znázorňující entitu zaměstnance s RowKeyem složeným z kódu pro rok, měsíc, den, hodinu a ID události":::

V tomto příkladu si všimněte, jak `PartitionKey` `RowKey` jsou složené klíče a. K `PartitionKey` distribuci protokolování mezi více oddíly používá oddělení i ID zaměstnance.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Vychází se alternativní klíčovou strukturou, která zabraňuje vytváření aktivních oddílů při vložení, efektivně podporovat dotazy, které klientská aplikace dělá?  
* Znamená to, že se vám pravděpodobně dosáhnete cíle škálovatelnosti pro jednotlivý oddíl a omezit ho na úložiště tabulek?  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Nepoužívejte antipattern předplatného/připojovat, pokud je pravděpodobný objem transakcí v případě přístupu k aktivnímu oddílu omezením omezení podle úložiště tabulek.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor složeného klíče](#compound-key-pattern)  
* [Vzor koncového protokolu](#log-tail-pattern)  
* [Úpravy entit](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Anti-Pattern dat protokolu
K ukládání dat protokolu obvykle byste měli místo tabulkového úložiště používat úložiště objektů BLOB.  

#### <a name="context-and-problem"></a>Kontext a problém
Běžný případ použití pro data protokolu je načtení výběru položek protokolu pro určitý rozsah data a času. Například chcete najít všechny chybové a kritické zprávy, které aplikace zaznamenala mezi 15:04 a 15:06 v konkrétní datum. Nechcete pomocí data a času zprávy protokolu určit oddíl, do kterého ukládáte entity protokolu. To má za následek, že v libovolném konkrétním čase budou všechny entity protokolu sdílet stejnou `PartitionKey` hodnotu (viz [předplatná/připojit anti-Pattern](#prepend-append-anti-pattern)). Například následující schéma entity pro zprávu protokolu má za následek aktivní oddíl, protože aplikace zapisuje všechny zprávy protokolu do oddílu aktuálního data a hodiny:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE28.png" alt-text="Obrázek, který zobrazuje schéma entit pro zprávu protokolu, má za následek aktivní oddíl.":::

V tomto příkladu `RowKey` obsahuje datum a čas zprávy protokolu, aby se zajistilo, že se zprávy protokolu v pořadí podle data a času řadí. `RowKey`Zahrnuje také ID zprávy, v případě, že více zpráv protokolu sdílí stejné datum a čas.  

Další možností je použít `PartitionKey` , který zajišťuje, že aplikace zapisuje zprávy do celé řady oddílů. Pokud například zdroj zprávy protokolu poskytuje způsob, jak distribuovat zprávy v mnoha oddílech, můžete použít následující schéma entity:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE29.png" alt-text="Obrázek entity zprávy protokolu":::

Problém s tímto schématem je však, že k načtení všech zpráv protokolu pro určité časové období je nutné v tabulce vyhledat všechny oddíly.

#### <a name="solution"></a>Řešení
Předchozí část zdůraznila problém při pokusu o použití úložiště tabulek k ukládání položek protokolu a navržených dvou neuspokojivých návrhů. Jedno řešení vedlo k Hot oddílu s rizikem špatného výkonu při zápisu zpráv protokolu. Druhé řešení vedlo k nekvalitnímu výkonu dotazů, protože z důvodu požadavku na kontrolu každého oddílu v tabulce načítá zprávy protokolu pro určité časové období. Úložiště objektů BLOB nabízí pro tento typ scénáře lepší řešení a toto je způsob, jakým Azure Storage Analytics ukládá data protokolu, která shromažďuje.  

Tato část popisuje, jak Analytics Storage ukládá data protokolu do úložiště objektů blob, jako ilustraci tohoto přístupu k ukládání dat, která se obvykle dotazují podle rozsahu.  

Služba Storage Analytics ukládá zprávy protokolu ve formátu s oddělovači ve více objektech blob. Formát s oddělovači umožňuje klientské aplikaci snadno analyzovat data v protokolu.  

Služba Storage Analytics používá zásady vytváření názvů objektů blob, které umožňují vyhledat objekty BLOB (nebo objekty BLOB), které obsahují zprávy protokolu, pro které hledáte. Například objekt BLOB s názvem Queue/2014/07/31/1800/000001. log obsahuje zprávy protokolu, které se vztahují ke službě Queue za hodinu 18:00 od 31. července 2014. "000001" značí, že se jedná o první soubor protokolu pro toto období. Analýza úložiště také zaznamenává časová razítka první a poslední zprávy protokolu uložené v souboru jako součást metadat objektu BLOB. Rozhraní API pro úložiště objektů BLOB umožňuje vyhledat objekty BLOB v kontejneru na základě předpony názvu. K vyhledání všech objektů blob, které obsahují data protokolu fronty po dobu od 18:00, můžete použít předponu Queue/2014/07/31/1800.  

Služba Storage Analytics ukládá zprávy protokolu interně a následně pravidelně aktualizuje příslušný objekt BLOB nebo vytvoří nový s nejnovější dávkou položek protokolu. Tím se sníží počet zápisů, které musí provádět v úložišti objektů BLOB.  

Pokud implementujete podobné řešení ve své vlastní aplikaci, zvažte, jak spravovat kompromis mezi spolehlivostí a náklady a škálovatelností. Jinými slovy, můžete vyhodnotit účinek zápisu všech záznamů do úložiště objektů blob, a to v porovnání s aktualizacemi ukládání do vyrovnávací paměti v aplikaci a jejich zápisem do úložiště objektů BLOB v dávkách.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Při rozhodování, jak ukládat data protokolu, vezměte v úvahu následující body:  

* Pokud vytvoříte návrh tabulky, který se vyhne potenciálním neaktivním oddílům, můžete zjistit, že k datům protokolu nemůžete efektivně přistupovat.  
* Pro zpracování dat protokolu klient často potřebuje načíst mnoho záznamů.  
* I když jsou data protokolu často strukturovaná, může být lepším řešením úložiště objektů BLOB.  

### <a name="implementation-considerations"></a>Důležité informace o implementaci
Tato část popisuje některé z důležitých informací, které je potřeba mít na paměti při implementaci vzorů popsaných v předchozích částech. Většina tohoto oddílu používá příklady napsané v jazyce C#, které používají knihovnu klienta úložiště (verze 4.3.0 v době psaní).  

### <a name="retrieve-entities"></a>Načtení entit
Jak je popsáno v [návrhu oddílu pro dotazování](#design-for-querying), nejúčinnější dotaz je dotaz typu Point. V některých scénářích ale možná budete potřebovat načíst více entit. Tato část popisuje některé běžné přístupy k načítání entit pomocí klientské knihovny pro úložiště.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Spustit dotaz typu Point pomocí klientské knihovny pro úložiště
Nejjednodušší způsob, jak spustit dotaz na bod, je použít operaci **načíst** tabulku. Jak ukazuje následující fragment kódu jazyka C#, tato operace načte entitu s `PartitionKey` hodnotou "Sales" a `RowKey` hodnotou "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Všimněte si, jak tento příklad očekává, že entita, kterou načítá, je typu `EmployeeEntity` .  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Načtení více entit pomocí LINQ
Můžete načíst více entit pomocí knihovny LINQ s klientskou knihovnou úložiště a zadat dotaz s klauzulí **WHERE** . Abyste se vyhnuli prohledávání tabulky, měli byste vždycky zahrnout `PartitionKey` hodnotu do klauzule WHERE, a pokud je to možné, `RowKey` aby se zabránilo prohledávání tabulek a oddílů. Úložiště tabulek podporuje omezené sady relačních operátorů (větší než, je větší nebo rovno, je menší než, je menší než nebo rovno, rovno a není rovno) pro použití v klauzuli WHERE. Následující fragment kódu jazyka C# vyhledá všechny zaměstnance, jejichž příjmení začíná písmenem "B" (za předpokladu, že ukládá `RowKey` příjmení) do oddělení Sales (za předpokladu, že `PartitionKey` ukládá název oddělení):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Všimněte si, jak dotaz určuje, jak `RowKey` a, `PartitionKey` aby bylo zajištěno lepší výkon.  

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
> Ukázka vnořování více metod, `CombineFilters` aby zahrnovaly tři podmínky filtru.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Načtení velkého počtu entit z dotazu
Optimální dotaz vrátí jednotlivé entity na základě `PartitionKey` hodnoty a `RowKey` hodnoty. V některých scénářích ale může být potřeba, abyste vrátili mnoho entit ze stejného oddílu, nebo dokonce i z mnoha oddílů. V takových scénářích byste vždy měli plně testovat výkon vaší aplikace.  

Dotaz na úložiště tabulek může vracet maximálně 1 000 entit najednou a běžet po dobu maximálně pěti sekund. Table Storage vrátí token pro pokračování, který umožní klientské aplikaci požádat o další sadu entit, pokud platí některá z následujících podmínek:

- Sada výsledků dotazu obsahuje více než 1 000 entit.
- Dotaz nebyl dokončen do pěti sekund.
- Dotaz překračuje hranici oddílu. 

Další informace o tom, jak fungují tokeny pro pokračování, najdete v tématu [časový limit dotazu a stránkování](/rest/api/storageservices/Query-Timeout-and-Pagination).  

Pokud používáte klientskou knihovnu pro úložiště, může automaticky zpracovat tokeny pro pokračování, protože vrací entity z úložiště tabulek. Například následující ukázka kódu jazyka C# automaticky zpracovává tokeny pokračování, pokud je služba Table Storage vrací v odpovědi:  

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

Následující kód jazyka C# zpracovává tokeny pro pokračování explicitně:  

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

Pokud použijete tokeny pro pokračování explicitně, můžete řídit, kdy vaše aplikace načte další segment dat. Například pokud vaše klientská aplikace umožňuje uživatelům stránkovat prostřednictvím entit uložených v tabulce, uživatel se může rozhodnout, že neprojde všemi entitami načtenými dotazem. Vaše aplikace by používala token pro pokračování k načtení dalšího segmentu v případě, že uživatel dokončil stránkování prostřednictvím všech entit v aktuálním segmentu. Tento přístup má několik výhod:  

* Můžete omezit množství dat, která se mají načíst z tabulkového úložiště, a přejít na síť.  
* V rozhraní .NET můžete provádět asynchronní vstupně-výstupní operace.  
* Token pro pokračování můžete serializovat na trvalé úložiště, takže můžete pokračovat v případě selhání aplikace.  

> [!NOTE]
> Token pro pokračování obvykle vrací segment obsahující 1 000 entit, i když může obsahovat méně. To platí i v případě, že omezíte počet položek, které dotaz vrátí, pomocí příkazu **přijmout** vrátí prvních n entit, které odpovídají kritériím vyhledávání. Úložiště tabulek může vracet segment obsahující méně než n entit spolu s tokenem pokračování, který vám umožní načíst zbývající entity.  
> 
> 

Následující kód jazyka C# ukazuje, jak změnit počet entit vrácených v rámci segmentu:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projekce na straně serveru
Jedna entita může mít až 255 vlastností a musí mít velikost až 1 MB. Při dotazování tabulky a načtení entit nemusíte potřebovat všechny vlastnosti a můžete se vyhnout nutnosti přenášet data zbytečně (což snižuje latenci a náklady). Pomocí projekce na straně serveru můžete přenést jenom vlastnosti, které potřebujete. Následující příklad načte pouze `Email` vlastnost (společně s `PartitionKey` , `RowKey` , `Timestamp` a `ETag` ) z entit vybraných dotazem.  

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

Všimněte si, jak `RowKey` je tato hodnota dostupná, i když není zahrnutá v seznamu vlastností, které se mají načíst.  

### <a name="modify-entities"></a>Úpravy entit
Klientská knihovna pro úložiště umožňuje upravovat entity uložené v úložišti tabulek vložením, odstraněním a aktualizací entit. EGTs můžete použít ke dávkovému zpracování vícenásobných operací vložení, aktualizace a odstranění a snížit tak počet požadovaných požadavků na odezvy a zvýšit výkon vašeho řešení.  

Výjimky, které jsou vyvolány, když klientská knihovna pro úložiště spustí EGT, obvykle zahrnuje index entity, která způsobila selhání dávky. To je užitečné, když ladíte kód, který používá EGTs.  

Měli byste také zvážit, jak váš návrh ovlivňuje způsob, jakým vaše klientská aplikace zpracovává operace souběžnosti a aktualizace.  

#### <a name="managing-concurrency"></a>Správa souběžnosti
Ve výchozím nastavení implementuje úložiště tabulek při operacích vložení, sloučení a odstranění optimistické kontroly souběžnosti na úrovni jednotlivých entit, i když je možné, že klient vynutí obejít tyto kontroly v úložišti tabulek. Další informace najdete v tématu [Správa souběžnosti v Microsoft Azure Storage](../storage/blobs/concurrency-manage.md).  

#### <a name="merge-or-replace"></a>Sloučit nebo nahradit
`Replace`Metoda `TableOperation` třídy vždy nahrazuje kompletní entitu v úložišti tabulek. Pokud do žádosti v případě, že tato vlastnost existuje v uložené entitě, nezadáte vlastnost, požadavek tuto vlastnost odebere z uložené entity. Pokud nechcete odebrat vlastnost explicitně z uložené entity, musíte do žádosti zahrnout každou vlastnost.  

Pomocí `Merge` metody `TableOperation` třídy můžete omezit množství dat, která odesíláte do úložiště tabulky, když chcete entitu aktualizovat. `Merge`Metoda nahrazuje jakékoli vlastnosti v uložené entitě hodnotami vlastností z entity zahrnuté v žádosti. Tato metoda opustí nedotčené vlastnosti v uložené entitě, které nejsou zahrnuté v žádosti. To je užitečné, pokud máte velké entity a potřebujete aktualizovat pouze malý počet vlastností v žádosti.  

> [!NOTE]
> `*Replace`Metody a `Merge` selžou, pokud entita neexistuje. Alternativně můžete použít `InsertOrReplace` `InsertOrMerge` metody a, které vytvoří novou entitu, pokud neexistuje.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Práce s heterogenními typy entit
Table Storage je úložiště tabulek *bez schématu* . To znamená, že jedna tabulka může ukládat entity různých typů, což poskytuje skvělou flexibilitu v návrhu. Následující příklad znázorňuje tabulku, která ukládá entity zaměstnanci i oddělení:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
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

Každá entita musí mít stále `PartitionKey` `RowKey` hodnoty, a `Timestamp` , ale může mít libovolnou sadu vlastností. Kromě toho není nic označovat typ entity, pokud se nerozhodnete ukládat tyto informace někam. Existují dvě možnosti, jak identifikovat typ entity:  

* Předřaďte typ entity do objektu `RowKey` (nebo případně do `PartitionKey` ). Například `EMPLOYEE_000123` nebo `DEPARTMENT_SALES` jako `RowKey` hodnoty.  
* Použijte samostatnou vlastnost pro záznam typu entity, jak je znázorněno v následující tabulce.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
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

První možnost, před vyřízením typu entity `RowKey` , je užitečná, pokud existuje možnost, že dvě entity různých typů mohou mít stejnou klíčovou hodnotu. Také seskupuje entity stejného typu společně v oddílu.  

Techniky popsané v této části jsou obzvláště důležité pro diskuzi o[vztazích dědičnosti](#inheritance-relationships).  

> [!NOTE]
> Pokud chcete, aby klientské aplikace POCO objekty a pracovaly s různými verzemi, zvažte zahrnutí čísla verze do hodnoty typu entity.  
> 
> 

Zbývající část této části popisuje některé funkce v klientské knihovně pro úložiště, které usnadňují práci s více typy entit ve stejné tabulce.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Načtení heterogenních typů entit
Pokud používáte klientskou knihovnu pro úložiště, máte k dispozici tři možnosti pro práci s více typy entit.  

Pokud víte, jaký typ entity je uložený s konkrétní `RowKey` `PartitionKey` hodnotou a, můžete typ entity zadat při načtení entity. Viděli jste to v předchozích dvou příkladech, které načítají entity typu `EmployeeEntity` : [Spusťte dotaz Point pomocí klientské knihovny úložiště](#run-a-point-query-by-using-the-storage-client-library) a [načtěte více entit pomocí LINQ](#retrieve-multiple-entities-by-using-linq).  

Druhou možností je použít `DynamicTableEntity` typ (kontejner objektů vlastností) místo konkrétního typu entity POCO. Tato možnost může také zvýšit výkon, protože není nutné serializovat a deserializovat entitu na typy .NET. Následující kód jazyka C# potenciálně načte více entit různých typů z tabulky, ale vrátí všechny entity jako `DynamicTableEntity` instance. Pak použije `EntityType` vlastnost k určení typu každé entity:  

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

Chcete-li načíst další vlastnosti, je nutné použít `TryGetValue` metodu u `Properties` vlastnosti `DynamicTableEntity` třídy.  

Třetí možností je kombinovat použití `DynamicTableEntity` typu a `EntityResolver` instance. To umožňuje překládat na více typů POCO ve stejném dotazu. V tomto příkladu `EntityResolver` delegát používá `EntityType` vlastnost k rozlišení mezi dvěma typy entity, které dotaz vrátí. `Resolve`Metoda používá `resolver` delegáta k překladu `DynamicTableEntity` instancí na `TableEntity` instance.  

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
Nemusíte znát typ entity, kterou chcete odstranit, a při vložení vždy znát typ entity. Můžete však použít `DynamicTableEntity` typ pro aktualizaci entity bez znalosti jejího typu a bez použití třídy entity POCO. Následující ukázka kódu načte jednu entitu a před aktualizací kontroluje, zda `EmployeeCount` vlastnost existuje.  

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
Tokeny sdíleného přístupového podpisu (SAS) můžete použít, chcete-li klientským aplikacím povolit přímou úpravu (a dotazování) entit tabulky, aniž by bylo nutné ověřovat přímo v úložišti tabulek. Použití SAS ve vaší aplikaci má typicky tři hlavní výhody:  

* Nemusíte distribuovat klíč účtu úložiště na nezabezpečenou platformu (například mobilní zařízení), aby toto zařízení mělo přístup k entitám v úložišti tabulek a jejich úpravy.  
* Můžete přesměrovat část práce, kterou webové a pracovní role provádějí při správě entit. Můžete přesměrovat na klientská zařízení, jako jsou počítače koncového uživatele a mobilní zařízení.  
* Klientovi můžete přiřadit omezené a časově omezenou sadu oprávnění (například povolit přístup jen pro čtení ke konkrétním prostředkům).  

Další informace o použití tokenů SAS s tabulkovým úložištěm najdete v tématu [použití sdílených přístupových podpisů (SAS)](../storage/common/storage-sas-overview.md).  

Přesto však musíte vygenerovat tokeny SAS, které klientským aplikacím udělují entity v úložišti tabulek. Provedete to v prostředí, které má zabezpečený přístup k klíčům účtu úložiště. Obvykle se používá webová role nebo role pracovního procesu pro generování tokenů SAS a jejich doručování klientským aplikacím, které potřebují přístup k vašim entitám. Vzhledem k tomu, že je stále k dispozici režie při generování a poskytování tokenů SAS klientům, byste měli zvážit, jak nejlépe snížit tuto režii, zejména ve scénářích s vysokým objemem.  

Je možné vygenerovat token SAS, který uděluje přístup podmnožině entit v tabulce. Ve výchozím nastavení vytvoříte token SAS pro celou tabulku. Je ale také možné určit, že token SAS uděluje přístup buď k rozsahu `PartitionKey` hodnot, nebo rozsahu hodnot `PartitionKey` a `RowKey` . Můžete zvolit generování tokenů SAS pro jednotlivé uživatele systému, takže token SAS každého uživatele umožňuje přístup pouze k vlastním entitám v úložišti tabulek.  

### <a name="asynchronous-and-parallel-operations"></a>Asynchronní a paralelní operace
Za předpokladu, že rozšíříte žádosti napříč více oddíly, můžete zvýšit propustnost a odezvu klienta pomocí asynchronních nebo paralelních dotazů.
Můžete mít například dvě nebo víc instancí role pracovního procesu, které k vašim tabulkám přistupují paralelně. Můžete mít jednotlivé role pracovních procesů, které jsou zodpovědné za konkrétní sady oddílů, nebo jenom mít několik instancí role pracovního procesu, každý může mít přístup ke všem oddílům v tabulce.  

V rámci instance klienta můžete zvýšit propustnost spuštěním operací úložiště asynchronně. Klientská knihovna pro úložiště usnadňuje psaní asynchronních dotazů a úprav. Můžete například začít se synchronní metodou, která načte všechny entity v oddílu, jak je znázorněno v následujícím kódu jazyka C#:  

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

V tomto asynchronním příkladu vidíte následující změny z synchronní verze:  

* Signatura metody teď obsahuje `async` modifikátor a vrátí `Task` instanci.  
* Namísto volání `ExecuteSegmented` metody k načtení výsledků metoda nyní volá `ExecuteSegmentedAsync` metodu. Metoda používá `await` Modifikátor k asynchronnímu načtení výsledků.  

Klientská aplikace může tuto metodu zavolat víckrát s různými hodnotami pro `department` parametr. Každý dotaz se spouští v samostatném vlákně.  

Ve třídě neexistuje žádná asynchronní verze `Execute` metody `TableQuery` , protože `IEnumerable` rozhraní nepodporuje asynchronní výčet.  

Entity můžete také vkládat, aktualizovat a odstraňovat asynchronně. Následující příklad jazyka C# ukazuje jednoduchou, synchronní metodu pro vložení nebo nahrazení entity zaměstnance:  

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

V tomto asynchronním příkladu vidíte následující změny z synchronní verze:  

* Signatura metody teď obsahuje `async` modifikátor a vrátí `Task` instanci.  
* Namísto volání `Execute` metody pro aktualizaci entity nyní metoda volá `ExecuteAsync` metodu. Metoda používá `await` Modifikátor k asynchronnímu načtení výsledků.  

Klientská aplikace může volat několik asynchronních metod, jako je tato, a každá metoda vyvolání se spouští v samostatném vlákně.