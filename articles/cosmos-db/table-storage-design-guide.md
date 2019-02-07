---
title: Návrh tabulek Azure Cosmos DB pro podporu škálování a výkonu
description: 'Průvodce návrhem tabulky Azure Storage: Návrh škálovatelných a výkonných tabulek ve službě Azure Cosmos DB a Azure Storage Table'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 12/07/2018
author: wmengmsft
ms.author: wmeng
ms.custom: seodec18
ms.openlocfilehash: 433f99d72feb7dc697050049817478a8c8b679e6
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820959"
---
# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Průvodce návrhem tabulky Azure Storage: Návrh škálovatelných a výkonných tabulek

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Návrh škálovatelných a výkonných tabulek je třeba zvážit několik faktorů, jako je například výkon, škálovatelnost a náklady. Pokud jste vytvořili dříve schémata pro relační databáze, tyto aspekty se známými, ale i když existují některé podobné rysy mezi model úložiště Azure Table service a relačních modelů, existují také mnoho důležitých rozdílů. Tyto rozdíly obvykle dojít k různých návrzích, který může vypadat counter-intuitive nebo nesprávné někomu zkušenosti s relačními databázemi, ale pokud navrhujete pro NoSQL s dvojicí klíč hodnota jako je například služba Azure Table service, které usnadňují představu. Mnoho rozdílů návrhu bude odrážet skutečnost, že služba Table service je navržen pro podporu aplikace v cloudovém měřítku, které mohou obsahovat miliardách entit (řádky v, řečeno terminologií relační databáze), dat nebo datových sad, které musí podporovat vysokou transakční svazky: proto je potřeba uvažovat jinak o jak ukládat data a porozumět tomu, jak funguje služba Table service. Dobře navržená úložiště dat typu NoSQL můžete povolit řešení škálování mnohem víc (a se s nižšími náklady) než řešení, které používá relační databáze. Tento průvodce vám pomůže s těmito tématy.  

## <a name="about-the-azure-table-service"></a>Informace o službě Azure Table service
Tato část ukazuje některé klíčové funkce služby Table service, které jsou obzvláště důležité pro navrhování pro výkon a škálovatelnost. Pokud jste ještě do Azure Storage a služby Table service, nejdřív přečíst [Úvod do Microsoft Azure Storage](../storage/common/storage-introduction.md) a [Začínáme s Azure Table Storage pomocí .NET](table-storage-how-to-use-dotnet.md) před čtením zbývající část tohoto článku . Přestože sada Tato příručka je zaměřena služby Table Service, bude obsahovat některé informace o službě fronty Azure a objektů Blob a jak je možné použít spolu s služby Table service v řešení.  

Co je služba Table service? Jak byste asi očekávali od názvu, služby Table service používá k ukládání dat formátu tabulky. Ve standardní terminologie každý řádek v tabulce představuje entitu a sloupce, které ukládat různé vlastnosti dané entity. Každá entita obsahuje pár klíčů, aby byl jednoznačně identifikovaný a aktualizovat sloupec časového razítka, která používá služby Table service můžete sledovat, kdy posledního entitu (se automaticky přidá pole časového razítka a nelze ručně přepsat časové razítko s libovolnou hodnotu). Služba Table service používá tento last-modified časové razítko (LMT) ke správě optimistického řízení souběžnosti.  

> [!NOTE]
> Operace rozhraní REST API služby tabulky také vrátit **ETag** hodnotu, která se odvozuje od časové razítko poslední změny (LMT). V tomto dokumentu si povšimněte ETag a LMT podmínky Zaměnitelně protože odkazují na stejná podkladová data.  
> 
> 

Následující příklad ukazuje, jednoduché tabulky návrhu k uložení entity zaměstnanci a oddělení. Mnoho příkladů uvedených dál v této příručce jsou založeny na toto jednoduché konstrukce.  

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
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>Email</th>
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
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>Email</th>
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
<th>Název oddělení</th>
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
<td>Prodej</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>Email</th>
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


Tato struktura se až dosud podobá tabulce v relační databázi, přičemž hlavní rozdíly jsou povinné sloupce a možnost ukládat více typů entit ve stejné tabulce. Navíc má každá uživatelem definovaná vlastnost (například **Jméno** nebo **Věk**) svůj datový typ (například integer nebo string), což odpovídá sloupcům v relační databázi. I když na rozdíl od v relační databázi, bez schématu povaha služby Table service znamená, že vlastnost nemusí mít stejný datový typ. u každé entity. Pokud chcete uložit komplexních datových typů v jedné vlastnosti, musíte použít serializovaný formát jako je JSON nebo XML. Další informace o tabulce služby, například podporované datové typy, podporovaných rozsahů, pravidla pojmenování a omezením způsobeným velikostí najdete v tématu [Principy datového modelu služby Table Service](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Jak se zobrazí, podle vašeho výběru **PartitionKey** a **RowKey** je zásadní pro návrh dobrý tabulky. Každá entita uložena v tabulce musí mít jedinečnou kombinaci **PartitionKey** a **RowKey**. Stejně jako u klíče v tabulce relační databáze, **PartitionKey** a **RowKey** hodnoty jsou indexovány pro vytvoření clusterovaného indexu umožňující rychlé look-ups; však služby Table service nevytvoří žádné sekundární indexy, jedná se pouze dvě indexované vlastnosti (některé vzory popsané dále zobrazit jak obejít tato omezení zřejmý).  

Tabulku tvoří jeden nebo více oddílů a jak se zobrazí, řadu rozhodnutí o návrhu provedete bude kolem zvolíte vhodnou **PartitionKey** a **RowKey** k optimalizaci vašeho řešení. Řešení může obsahovat pouze jednu tabulku, která obsahuje všechny vaše entity, které jsou uspořádány do oddílů, ale obvykle řešení bude mít více tabulek. Tabulky umožňují logicky uspořádat entity, vám pomůžou spravovat přístup k datům pomocí seznamů řízení přístupu a můžete vložit pomocí operace s jediným úložištěm celou tabulku.  

### <a name="table-partitions"></a>Oddíly tabulky
Název účtu, název tabulky a **PartitionKey** společně identifikovat oddílu v rámci služby úložiště, kde služby table service obsahuje entitu. A také se zapojil schéma adresování pro entity, oddíly definovat rozsah transakce (viz [transakcí skupin entit](#entity-group-transactions) níže), a tvoří základ jak škálování služby table service. Další informace o oddílech najdete v tématu [Azure Storage škálovatelnost a cíle výkonnosti](../storage/common/storage-scalability-targets.md).  

Ve službě Table service, služby jednotlivých uzlů jeden nebo více dokončení oddíly a škálování služby pomocí dynamické vyrovnávání zatížení oddílů mezi uzly. Pokud uzel je zatížení, můžete služby table service *rozdělit* rozsahem oddílů obsluhovány pomocí tohoto uzlu na různých uzlech; při provozu poklesne, můžete službu *sloučení* rozsahů oddílů z quiet uzlů zpět na jeden uzel.  

Další informace o interní informace služby Table service, zejména způsob, jakým služba spravuje oddíly, najdete v dokumentu paper [Microsoft Azure Storage: Služby s vysokou dostupností cloudového úložiště se silnou konzistenci](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Transakcí skupin entit
Ve službě Table service transakcí skupin entit (EGTs) jsou pouze předdefinovaný mechanismus pro provádění atomické aktualizace napříč více entit. EGTs se také označují jako *dávkové transakce* v některé dokumentaci. EGTs může pracovat pouze s entitami, které jsou uložené ve stejném oddílu (sdílená složka stejným klíčem oddílu v dané tabulce), tak kdykoli potřebujete atomic transakční chování napříč více entit, je nutné se ujistit, že jsou tyto entity do stejného oddílu. To je často důvod pro udržování několik typů entit ve stejné tabulce (a oddílu) a bez použití více tabulek pro typy jiné entity. Jeden EGT může pracovat na maximálně 100 entit.  Pokud uvedete více souběžných EGTs pro zpracování, je důležité zajistit, že tyto EGTs není pracovat u entit, které jsou společné napříč EGTs, protože jinak zpracování může zpozdit.

EGTs přinášejí s sebou potenciální kompromis pro vyhodnocení vašeho návrhu: pomocí dalších oddílů bude zvýšení škálovatelnosti aplikace vzhledem k tomu, že Azure má více příležitostí pro vyrovnávání zatížení požadavků mezi uzly, ale může omezit schopnost vaše aplikace provádět atomické transakce a udržovat silnou konzistenci pro vaše data. Navíc existují konkrétní škálovatelnost cíle na úrovni oddíl, který může být omezen propustnost transakcí, můžete očekávat, že pro jednotlivé uzly: Další informace o cíle škálovatelnosti pro účty úložiště Azure a služby table service, najdete v článku [Škálovatelnost a cíle výkonnosti azure Storage](../storage/common/storage-scalability-targets.md). Novější části této příručky popisují různé návrhu strategie, které vám pomůžou spravovat kompromisy, jako je například tento a diskutovat o tom, jak nejlépe zvolit klíč oddílu na základě konkrétních požadavků klientské aplikace.  

### <a name="capacity-considerations"></a>Důležité informace o kapacity
Následující tabulka obsahuje některé z klíčových hodnot je potřeba vědět při navrhování řešení pro službu tabulky:  

| Celková kapacita účtu služby Azure storage | 500 TB |
| --- | --- |
| Počet tabulek v účtu služby Azure storage |Omezeno pouze kapacity účtu úložiště |
| Počet oddílů v tabulce |Omezeno pouze kapacity účtu úložiště |
| Počet entit v oddílu |Omezeno pouze kapacity účtu úložiště |
| Velikost jednotlivých entit |Až 1 MB s délkou maximálně 255 vlastnosti (včetně **PartitionKey**, **RowKey**, a **časové razítko**) |
| Velikost **PartitionKey** |Řetězec, velikost až 1 KB |
| Velikost **RowKey** |Řetězec, velikost až 1 KB |
| Velikost transakce skupin entit |Transakce může obsahovat maximálně 100 entit a datová část musí být menší než 4 MB. EGT lze aktualizovat pouze entity jednou. |

Další informace najdete v tématu [Vysvětlení datového modelu služby Table Storage](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Důležité informace o nákladech
Table storage je relativně levný, ale měli byste zahrnout odhady nákladů na využití kapacity a množství transakcí jako součást vaší zkušební verze řešení, která používá služby Table service. V mnoha scénářích ukládání Nenormalizovaná nebo duplicitních dat za účelem zlepšení výkonu nebo škálovatelnost řešení ale platný přístup umožní. Další informace o cenách najdete v tématu [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Pokyny pro návrh tabulek
Tyto seznamy shrnují některé z klíčových pokyny, které je třeba mít na paměti při návrhu tabulek a tato příručka se jimi bude zabývat všechny podrobněji později. Tyto pokyny se liší od pokynů, které obvykle byste použili pro relační databázi.  

Navrhování řešení Table service bude *čtení* efektivní:

* ***Návrh pro dotazování aplikace náročné na čtení.*** Při návrhu tabulek, rozmyslete si dotazy (zejména latence citlivé ty), které budou spouštět předtím, než si myslíte o tom, jak budete aktualizovat entity. Obvykle v důsledku efektivní a výkonné řešení.  
* ***Zadejte PartitionKey a rowkey používají v dotazech.*** *Bod dotazy* jako jedná se o nejúčinnější dotazy table service.  
* ***Zvažte uložení duplicitní kopie entity.*** Table storage je levné proto zvažte uložení stejná entita více než jednou (s různými klíči) umožňující účinnější dotazy.  
* ***Vezměte v úvahu denormalizing vaše data.*** Table storage je levné proto zvažte denormalizing vaše data. Například ukládat souhrn entit, aby se dotazy pro agregovaná data, která stačí pro přístup k jedné entity.  
* ***Použijte složené klíčové hodnoty.*** Jsou jenom klíče máte **PartitionKey** a **RowKey**. Například použijte složené klíčové hodnoty a povolit alternativní s klíči přístup cesty k entitám.  
* ***Použijte dotaz projekce.*** Můžete snížit množství dat, která přenos přes síť pomocí dotazů, které vybírají pouze pole, které potřebujete.  

Navrhování řešení Table service bude *zápisu* efektivní:  

* ***Nevytvářejte aktivních oddílů.*** Zvolte klíče, které vám umožní rozdělit mezi několik oddílů v některém okamžiku vašich požadavků.  
* ***Vyhněte se špičkám provozu.*** Provoz uhladil určité přiměřené době a vyhnout se špičkám provozu.
* ***Není nutně vytvořit samostatnou tabulku pro každý typ entity.*** Pokud požadujete atomické transakce mezi typy entit, můžete uložit tyto několik typů entit ve stejném oddílu ve stejné tabulce.
* ***Vezměte v úvahu maximální propustnost, kterou musí dosáhnout.*** Musí být vědomi cíle škálovatelnosti pro služby Table service a ujistěte se, že váš návrh nezpůsobí překročíte je.  

Jak si ji přečtete, zobrazí se příklady, které všechny tyto zásady vložit do praxe.  

## <a name="design-for-querying"></a>Návrh pro dotazování
Náročné na prostředky, zápis náročné na prostředky nebo kombinaci obou, může se načíst řešení Table service. Tato část se zaměřuje na věcí berte v úvahu při návrhu vaší služby Table service pro podporu operací čtení efektivně. Návrh, že podporuje operace čtení efektivně je obvykle také efektivní pro operace zápisu. Existují však další důležité informace k berte v úvahu při navrhování pro podporu operací zápisu, popsané v další části [návrhu pro úpravu dat](#design-for-data-modification).

Dobrým výchozím bodem pro návrh vašeho řešení služby Tabulka vám umožní číst data efektivně, je zeptat "jaké dotazy se Moje aplikace potřebujete ke spuštění, aby se načetla data, které potřebuje ze služby Table service?"  

> [!NOTE]
> Pomocí služby Table service je potřeba získat návrh správné ještě před zahájením vzhledem k tomu je složité a nákladné později změnit. Například v relační databázi, často je možné pro řešení potíží s výkonem jednoduše tak, že přidání indexů do existující databáze: to není možné pomocí služby Table service.  
> 
> 

Tato část se zaměřuje na klíčových otázek, které je třeba vyřešit při návrhu tabulek pro dotazování. Obsahuje následující témata v této části:

* [Jak ovlivňuje výkon dotazů PartitionKey a RowKey podle vašeho výběru](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Výběr vhodné PartitionKey](#choosing-an-appropriate-partitionkey)
* [Optimalizace dotazů pro služby Table service](#optimizing-queries-for-the-table-service)
* [Řazení dat ve službě Table service](#sorting-data-in-the-table-service)

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Jak ovlivňuje výkon dotazů PartitionKey a RowKey podle vašeho výběru
Následující příklady předpokládají, že služba table service je ukládání entity zaměstnance s následující strukturou (vynechat většina příkladů **časové razítko** vlastnost pro přehlednost):  

| *Název sloupce* | *Datový typ* |
| --- | --- |
| **PartitionKey** (název oddělení) |Řetězec |
| **RowKey** (Id zaměstnance) |Řetězec |
| **Jméno** |Řetězec |
| **LastName** |Řetězec |
| **Stáří** |Integer |
| **EmailAddress** |String |

Předchozí Přehled služby Azure Table část popisuje některé klíčové funkce služby Azure Table service, které mají přímý vliv na návrh pro dotaz. Tyto za následek následující obecné pokyny pro návrh služby dotazy na tabulku. Syntaxe filtru použít v následujících příkladech je z rozhraní REST API služby tabulky pro další informace najdete v tématu [dotazu entity](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* A ***dotazu bodu*** je nejúčinnější vyhledávání používat a doporučuje se použít pro vyhledávání velkého rozsahu nebo vyhledávání, které vyžadují nejnižší latenci. Takový dotaz lze použít indexy, které k vyhledání jednotlivých entit efektivně tak, že určíte, jak **PartitionKey** a **RowKey** hodnoty. Příklad: $filter = (PartitionKey eq "Prodeje") a (RowKey eq '2')  
* Za druhé nejlepší je ***dotazu na rozsah*** , která používá **PartitionKey** a filtry na mnoha různých **RowKey** hodnoty k vrácení více než jednu entitu. **PartitionKey** hodnota označuje konkrétní oddíl a **RowKey** hodnoty identifikaci podmnožiny entity v tomto oddílu. Příklad: $filter = PartitionKey eq "Prodeje a RowKey ge" a RowKey lt 'T'  
* Je třetí nejlepší ***oddílu kontrolovat*** , která používá **PartitionKey** a filtry v jiné neklíčovým vlastnosti, které může vrátit více než jednu entitu. **PartitionKey** identifikuje hodnotu konkrétního oddílu, a vlastnost hodnot vyberte pro celou dílčí sadu entit v oddílu. Příklad: $filter = PartitionKey eq "Prodeje" a LastName eq: Váša.  
* A ***prohledávání tabulky*** nezahrnuje **PartitionKey** je neefektivní, protože vyhledá všechny oddíly, které tvoří tabulky pak u všech odpovídajících entit. Bude provedeno prohledání tabulky bez ohledu na to, jestli váš filtr používá **RowKey**. Příklad: $filter = LastName eq "Jones"  
* Azure Table Storage dotazy, které vracejí více entit je řazení v vrátit **PartitionKey** a **RowKey** pořadí. Abyste se vyhnuli nutnosti uchýlit se entity v klientovi, zvolte **RowKey** , který definuje nejběžnější pořadí řazení. Výsledky dotazu vrácená rozhraním API tabulky Azure v Azure Cosmso DB nejsou seřazené podle klíče oddílu a klíče řádku. Podrobný seznam rozdílů funkcí najdete v tématu [rozdíly mezi rozhraní Table API ve službě Azure Cosmos DB a Azure Table storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Pomocí "**nebo**" zadat filtr na základě **RowKey** hodnoty výsledky v oddílu kontroly a není považován za rozsahového dotazu. Proto byste se měli vyhnout dotazů, které používají jako filtry: $filter = PartitionKey eq "Prodeje" a (RowKey eq "121" nebo RowKey eq "322")  

Příklady kódu na straně klienta, které používají klientskou knihovnu pro úložiště k provádění efektivních dotazů najdete v tématu:  

* [Provádění dotazu bodu pomocí klientskou knihovnu pro úložiště](#executing-a-point-query-using-the-storage-client-library)
* [Načítání více entit pomocí jazyka LINQ](#retrieving-multiple-entities-using-linq)
* [Projekce na straně serveru](#server-side-projection)  

Příklady kódu na straně klienta, který dokáže zpracovat víc typů entit, které jsou uloženy ve stejné tabulce naleznete v tématu:  

* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Výběr vhodné PartitionKey
Podle vaší volby **PartitionKey** proti požadavku k distribuci entity napříč několika oddíly (k zajištění škálovatelných řešení) by měl vyvážily potřeby povolit používání EGTs (k zajištění konzistence).  

Na jeden extreme všech entit může ukládat do jednoho oddílu, ale to může omezit škálovatelnost řešení a služby table service by jinak znemožňovaly tomu nebudou moct Vyrovnávání zatížení požadavků. V jiných extreme může uchovávat na oddíl, který by být vysoce škálovatelné a umožňující služby table service pro vyrovnávání zatížení požadavků, ale pomocí transakcí skupin entit, které by jinak znemožňovaly jste jednu entitu.  

Ideální **PartitionKey** je jedna, která vám umožní použít efektivní dotazy a má dostatek oddílů, aby zajistit je škálovatelné řešení. Obvykle zjistíte, zda bude mít vaše entity vhodný vlastnost, která distribuuje napříč oddíly dostatečná entity.

> [!NOTE]
> V systému, která uchovává informace o uživateli nebo zaměstnanců, například ID uživatele může být vhodné PartitionKey. Můžete mít několik entit, které používají dané ID uživatele jako klíč oddílu. Každá entita, která ukládá data o uživateli jsou rozděleny do jednoho oddílu, a proto tyto entity jsou přístupné přes transakcí skupin entit, ale stále vysoce škálovatelné.
> 
> 

Existují ještě další důležité okolnosti ve podle vaší volby **PartitionKey** , které se týkají jak bude vkládat, aktualizovat a odstraňovat entity: naleznete v části [návrhu pro úpravu dat](#design-for-data-modification) níže.  

### <a name="optimizing-queries-for-the-table-service"></a>Optimalizace dotazů pro služby Table service
Služba Table service automaticky indexuje entit pomocí **PartitionKey** a **RowKey** v jeden clusterovaný index, proto příčiny, které odkazují dotazy jsou hodnoty použít co nejúčinnější. Však nejsou žádné indexy než na clusterovaný index na **PartitionKey** a **RowKey**.

Řada návrhů musí splňovat požadavky na povolení vyhledávání entit na základě několika kritérií. Například hledání entit zaměstnanců podle e-mailu, id zaměstnance nebo příjmení. Následující vzory v části [vzory návrhu v tabulce](#table-design-patterns) vyřešit tyto typy požadavků a popisují způsoby práce kolem skutečnost, že služba Table service neposkytuje sekundární indexy:  

* [Vzor sekundární index oddílu uvnitř](#intra-partition-secondary-index-pattern) -Store několik kopií každého entitu s využitím různých **RowKey** hodnot (ve stejném oddílu) Chcete-li povolit rychlé a efektivní vyhledávání a alternativní pořadí řazení s použitím různých **RowKey** hodnoty.  
* [Sekundární index oddílu mezi vzor](#inter-partition-secondary-index-pattern) -Store několik kopií každého entitu s využitím různých **RowKey** hodnoty v samostatných oddílů nebo v samostatné tabulky, které umožňují rychlé a efektivní vyhledávání a alternativní řazení objednávky s použitím různých **RowKey** hodnoty.  
* [Model entity indexů](#index-entities-pattern) -udržovat index entity, které umožňují efektivní prohledávání, které vrací seznam entit.  

### <a name="sorting-data-in-the-table-service"></a>Řazení dat ve službě Table service

Vrácené služby Table service výsledky dotazu jsou seřazené ve vzestupném pořadí podle **PartitionKey** a potom podle **RowKey**.

> [!NOTE]
> Výsledky dotazu vrácená rozhraním API tabulky Azure v Azure Cosmso DB nejsou seřazené podle klíče oddílu a klíče řádku. Podrobný seznam rozdílů funkcí najdete v tématu [rozdíly mezi rozhraní Table API ve službě Azure Cosmos DB a Azure Table storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Klíče v tabulce Azure Storage jsou řetězcové hodnoty a aby číselných hodnot řadily správně, musí je převést na pevnou délkou a vyplnění nulami. Například, pokud hodnota id zaměstnance použijete jako **RowKey** je celočíselná hodnota, je třeba id zaměstnance převést **123** k **00000123**. 

Mnoho aplikací mají požadavky na používání dat v různém pořadí řazení: podle názvu nebo díky připojení ke službě data, například řazení zaměstnanci. Následující vzory v části [vzory návrhu v tabulce](#table-design-patterns) adres jak alternativní pořadí řazení pro vaší entity:  

* [Vzor sekundární index oddílu uvnitř](#intra-partition-secondary-index-pattern) - Store několik kopií Každá entita s použitím různých hodnot RowKey (ve stejném oddílu) a umožňuje rychlé a efektivní vyhledávání a řazení alternativní objednávky s použitím různých RowKey hodnot.  
* [Sekundární index oddílu mezi vzor](#inter-partition-secondary-index-pattern) - Store několik kopií Každá entita s použitím různých hodnot RowKey v samostatných oddílech v samostatných tabulkách a umožňuje rychlé a efektivní vyhledávání a řazení alternativní objednávky s použitím různých hodnot RowKey .
* [Vzor log tail](#log-tail-pattern) – načíst *n* naposledy přidaný do oddílu s použitím entity **RowKey** hodnotu, která seřadí reverzní datum a čas objednávky.  

## <a name="design-for-data-modification"></a>Návrh pro úpravu dat
Tato část se zaměřuje na aspekty návrhu pro optimalizaci vložení, aktualizace a odstranění. V některých případech budete muset vyhodnotit kompromis mezi návrhy, které je optimální pro dotazování na návrhy, které je optimální pro úpravu dat stejně jako je tomu v návrhy pro relační databáze (i když jsou techniky pro správu kompromisy návrhu různé v relační databázi). V části [vzory návrhu v tabulce](#table-design-patterns) popisuje některé podrobné návrhové vzory pro služby Table service a zdůrazňuje některé z těchto kompromisy. V praxi zjistíte, že řada návrhů, které jsou optimalizované pro dotazování entity také fungovat dobře pro úpravu entity.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>Optimalizace výkonu příkazu insert, update a delete operace
Aktualizovat nebo odstranit entitu, musíte být schopni identifikovat pomocí **PartitionKey** a **RowKey** hodnoty. V tomto ohledu podle vaší volby **PartitionKey** a **RowKey** úprava entit by podle podobných kritérií zadaným hodnotám pro podporu bodových dotazů, protože chcete, k identifikaci entity jako efektivní nejvíce. Nechcete použijte neefektivní kontroly oddíl nebo tabulky a vyhledejte entitu Chcete-li vyhledat **PartitionKey** a **RowKey** hodnoty je potřeba aktualizovat nebo odstranit.  

Následující vzory v části [vzory návrhu v tabulce](#table-design-patterns) odstraňte optimalizace výkonu nebo váš insert, update a operace odstranění:  

* [Velký objem odstranit vzor](#high-volume-delete-pattern) -Povolit odstranění k velkému počtu entit uložením všechny entity pro souběžné odstranění vlastních samostatné tabulky, odstranit entity odstraněním tabulky.  
* [Vzor data řady](#data-series-pattern) -series kompletní data Store v jedné entity, chcete-li minimalizovat počet požadavků, které provedete.  
* [Vzor široké entity](#wide-entities-pattern) – použití více fyzických entit k ukládání logické entity s více než 252 vlastností.  
* [Vzor velkých entit](#large-entities-pattern) – použití úložiště objektů blob pro uložení hodnoty vlastnosti velké.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>Zajištění konzistence v entitách uložené
Klíčovým faktorem, který ovlivňuje podle vašeho výběru klíče pro optimalizaci změny dat je jak zajistit soulad s použitím atomické transakce. Můžete použít pouze EGT pracovat u entit se ukládají do stejného oddílu.  

Následující vzory v části [vzory návrhu v tabulce](#table-design-patterns) adresu Správa konzistence:  

* [Vzor sekundární index oddílu uvnitř](#intra-partition-secondary-index-pattern) -Store několik kopií každého entitu s využitím různých **RowKey** hodnot (ve stejném oddílu) Chcete-li povolit rychlé a efektivní vyhledávání a alternativní pořadí řazení s použitím různých **RowKey** hodnoty.  
* [Sekundární index oddílu mezi vzor](#inter-partition-secondary-index-pattern) - Store několik kopií každé entity pomocí různých hodnot RowKey v samostatných oddílech nebo v samostatných tabulkách a umožňuje rychlé a efektivní vyhledávání a řazení alternativní objednávky s použitím různých **RowKey** hodnoty.  
* [Vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern) -povolit konzistentní chování v rámci hranic oddílů nebo systému hranice úložiště pomocí front Azure.
* [Model entity indexů](#index-entities-pattern) -udržovat index entity, které umožňují efektivní prohledávání, které vrací seznam entit.  
* [Vzor denormalizace](#denormalization-pattern) -kombinování souvisejících dat společně v jedné entitě umožňuje načíst všechna data, je nutné pomocí dotazu jediný bod.  
* [Vzor data řady](#data-series-pattern) -series kompletní data Store v jedné entity, chcete-li minimalizovat počet požadavků, které provedete.  

Informace o transakcí skupin entit, najdete v části [transakcí skupin entit](#entity-group-transactions).  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Zajištění návrhu pro efektivní úpravy zajišťuje efektivní dotazy
V mnoha případech by měl návrh pro efektivní dotazování výsledků v efektivní úpravy, ale vždy vyhodnoťte, jestli to platí pro váš konkrétní scénář. Některé tyto vzory se dají v části [vzory návrhu v tabulce](#table-design-patterns) explicitně vyhodnotit kompromisy mezi dotazování a úprava entit a by měl vždy vzít v úvahu počty jednotlivých typů operací.  

Následující vzory v části [vzory návrhu v tabulce](#table-design-patterns) kompromisy mezi návrhu pro efektivní dotazy a návrh pro úpravu dat efektivní adresa:  

* [Složené klíče vzor](#compound-key-pattern) – použití složené **RowKey** hodnoty umožňují klientům vyhledat související data pomocí dotazu jediný bod.  
* [Vzor log tail](#log-tail-pattern) – načíst *n* naposledy přidaný do oddílu s použitím entity **RowKey** hodnotu, která seřadí reverzní datum a čas objednávky.  

## <a name="encrypting-table-data"></a>Šifrování dat tabulky
Klientská knihovna .NET Azure Storage podporuje šifrování vlastností entity řetězce pro vložení a nahrazovat operace. Šifrované řetězce jsou uložené ve službě jako binární vlastnosti a jsou převedeny na řetězce zpět po dešifrování.    

Pro tabulky, vedle zásady šifrování musí uživatelé zadat vlastnosti, které mají být šifrována. To lze provést zadáním buď atribut [EncryptProperty] \(pro entity objektů POCO, které jsou odvozeny od TableEntity) nebo šifrování překladač v žádosti o možnostech. Překladač šifrování je delegát, který má klíč oddílu, klíč řádku a název vlastnosti a vrátí logickou hodnotu, která určuje, jestli by měl být šifrovaná tuto vlastnost. Při šifrování klientské knihovny použije tyto informace se rozhodnout, zda vlastnost by se měla šifrovat během zápisu lince. Delegát také poskytuje možnost logiky po tom, jak jsou zašifrované vlastnosti. (Například, pokud X, pak šifrování vlastnost A; v opačném případě šifrování vlastnosti A a B.) Není nutné poskytnout tyto informace při čtení nebo dotazování entit.

Sloučení se momentálně nepodporuje. Protože podmnožinu vlastností mohou byla zašifrována pomocí dříve za jiný klíč, jednoduše slučování nové vlastnosti a metadata aktualizace způsobí ztrátu dat. Slučují se buď vyžaduje, aby volání další služby do existující entity načíst ze služby nebo pomocí nového klíče pro jednu vlastnost, které nejsou vhodné z důvodů výkonu.     

Informace o šifrování dat tabulky najdete v tématu [šifrování na straně klienta a služby Azure Key Vault pro Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md).  

## <a name="modeling-relationships"></a>Modelování relací
Vytváření modelů domény je klíče krokem návrhu komplexních systémů. Proces modelování se obvykle používají k identifikaci entit a vztahů mezi nimi jako způsob, jak pochopit obchodní domény a informujte návrhu vašeho systému. Tato část se zaměřuje na způsob lze přeložit některé nejčastější typy relací v doméně modelů a návrhů pro služby Table service. Proces mapování ze logický datový model do fyzické SQL Server – modelu dat se liší od, který používá při navrhování relační databáze. Návrh relačních databází obvykle předpokládá optimalizována pro minimalizaci redundance – a deklarativní zjišťování schopností, který získává implementaci jak databázi fungování procesu normalizace data.  

### <a name="one-to-many-relationships"></a>Vztah jeden mnoho
1 n vztahy mezi objekty obchodní domény docházet často: například jeden oddělení má mnoho zaměstnanců. Existuje několik způsobů, jak implementovat vztahů jednoho k několika ve službě Table service každý s výhody a nevýhody, které můžou být relevantní pro konkrétní scénář.  

Podívejte se na příklad velké korporace zemí s desítkami tisíc oddělení a zaměstnance entit, kde každé oddělení má mnoho zaměstnance a zaměstnance, jako přidružený k jedné konkrétní oddělení. Jedním z přístupů je uložit samostatné oddělení a entity zaměstnance například tyto:  

![Oddělení a zaměstnance entity][1]

Tento příklad ukazuje implicitní vztah jeden mnoho mezi typy, na základě **PartitionKey** hodnotu. Každé oddělení může mít mnoho zaměstnanců.  

Tento příklad také uvádí oddělení entity a její související zaměstnance entity do stejného oddílu. Můžete se rozhodnout používat různé oddíly, tabulky nebo účty úložiště i pro typy jiné entity.  

Alternativním přístupem je denormalizovat data a ukládat jenom zaměstnance entity s Nenormalizovaná oddělení dat, jak je znázorněno v následujícím příkladu. V našem konkrétním scénáři tento denormalizovaném přístupu nemusí být nejlepší, pokud máte požadavek, nebude moct změnit podrobnosti o vedoucí oddělení, protože k tomu je potřeba aktualizovat každý zaměstnanec v oddělení.  

![Zaměstnanec entity][2]

Další informace najdete v tématu [Denormalizace vzor](#denormalization-pattern) dále v tomto průvodci.  

Následující tabulka shrnuje výhody a nevýhody jednotlivých uvedených výše pro ukládání zaměstnanci a oddělení entity, které mají vztah jeden mnoho přístupů. Také byste měli zvážit, jak často předpokládáte můžete provádět různé operace: může být přijatelný mít návrh, který zahrnuje náročná operace, je-li tuto operaci situace nastane pouze zřídka.  

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
<li>Budete muset načíst zaměstnanci a oddělení entity pro některé činnosti klienta.</li>
<li>Operace úložiště stát do stejného oddílu. Na svazcích vysokou transakční to může vést hotspot.</li>
<li>Zaměstnanec nelze přesunout do jiného oddělení pomocí EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Typy entit, různých oddílů, nebo tabulky nebo účty úložiště</td>
<td>
<ul>
<li>Oddělení entity nebo zaměstnance entity můžete aktualizovat pomocí jedné operace.</li>
<li>Na vysokou transakční svazky to může pomoct rozložit zatížení mezi více oddílů.</li>
</ul>
</td>
<td>
<ul>
<li>Budete muset načíst zaměstnanci a oddělení entity pro některé činnosti klienta.</li>
<li>Nelze použít EGTs zajistit konzistenci při můžete aktualizace/insert/delete zaměstnance a aktualizace oddělení. Aktualizuje se například počet zaměstnanců v entitě oddělení.</li>
<li>Zaměstnanec nelze přesunout do jiného oddělení pomocí EGT.</li>
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
<li>Může být nákladné můžete zachovat konzistenci, pokud je potřeba aktualizovat informace o oddělení (to bude potřeba aktualizovat všechny zaměstnance v oddělení).</li>
</ul>
</td>
</tr>
</table>

Jak zvolit mezi tyto možnosti a které z výhody a nevýhody jsou nejvýznamnější, závisí na konkrétní aplikaci scénáře. Například jak často upravíte entity oddělení; potřebují všechny vaše dotazy zaměstnance dalších informací o oddělení; jak blízko jste omezení škálovatelnosti na oddíly nebo účtu úložiště?  

### <a name="one-to-one-relationships"></a>Relace 1: 1
Doménových modelů může obsahovat mezi entitami relace 1: 1. Pokud potřebujete implementovat vztah 1: 1 ve službě Table service, musíte také zvolit způsob propojení dvě související entity, pokud je třeba je oba načíst. Tento odkaz může být implicitní, podle úmluvy v hodnotách klíčů nebo explicitní uložením odkaz ve formě **PartitionKey** a **RowKey** hodnoty v jednotlivých entit k jeho související entity. Informace o tom, jestli byste měli uložit související entity do stejného oddílu, naleznete v části [jeden mnoho relací](#one-to-many-relationships).  

Existují také důležité informace o implementaci, které by mohly vést k implementaci relace 1: 1 ve službě Table service:  

* Zpracování velkých entit (Další informace najdete v tématu [velkých entit vzor](#large-entities-pattern)).  
* Implementace řízení přístupu (Další informace najdete v tématu [řízení přístupu se sdílenými přístupovými podpisy](#controlling-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Připojte se k v klientovi
I když existují způsoby pro modelování vztahů ve službě Table service, by neměla zapomenete, že jsou dva primární důvody pro použití služby Table service škálovatelnost a výkon. Pokud zjistíte, že jsou modelována mnoho vztahů, které ohrožují výkon a škálovatelnost řešení, které by měl položte si otázku: Pokud je potřeba vytvořit všechny vztahy mezi daty do návrhu tabulky. Je možné, zjednodušuje návrh a zlepšit škálovatelnost a výkon vašeho řešení, pokud jste povolili klientské aplikace provádět žádné nezbytné spojení.  

Například pokud máte malé tabulky obsahující data, která se nemění často, potom můžete tato data načíst jednou a uložení do mezipaměti na straně klienta. To se můžete vyhnout opakované výměn dat k načtení stejná data. V příkladech, které jsme v této příručce se podívat na sadu oddělení v malé organizaci je pravděpodobně být malé a zřídka díky tomu je vhodným kandidátem pro data, která můžete po stažení aplikace pro klienta a mezipaměti jako data vyhledávání změnit.  

### <a name="inheritance-relationships"></a>Vztahy dědičnosti
Pokud vaše klientská aplikace využívá sadu tříd, které tvoří součást vztah dědičnosti představující obchodní entity, je možné snadno zachovat těchto entit ve službě Table service. Například můžete mít následující sadu tříd definovaných v klientské aplikaci kde **osoba** je abstraktní třída.

![ER diagram vztahů dědičnosti][3]

Je možné zachovat instancemi dvou tříd konkrétní ve službě Table service pomocí jedné osobě tabulky pomocí entit v tento vzhled takto:  

![Diagram entity zákazník a zaměstnance entity][4]

Další informace o práci s více typy entit ve stejné tabulce v kódu klienta najdete v části [práce s typy entit heterogenní](#working-with-heterogeneous-entity-types) dále v tomto průvodci. To poskytuje příklady toho, jak rozpoznat typ entity v klientském kódu.  

## <a name="table-design-patterns"></a>Vzory návrhu tabulky
V předchozí části jste viděli některé podrobné diskuze o tom, jak optimalizovat návrhu tabulek pro data i načítání entit pomocí dotazů a pro vkládání, aktualizaci a odstraňování dat entity. Tato část popisuje některé vzory, které jsou vhodné pro použití s řešeními služby tabulky. Kromě toho se zobrazí, jak se prakticky vyřešit některé problémy a kompromisy vyvolána dříve v tomto průvodci. Následující diagram obsahuje souhrn vztahy mezi různé vzorce:  

![Obrázek vzory návrhu tabulky][5]

Vzorek mapy nad zvýrazní některých relací mezi (modrá) modely a antimodely (oranžová), které jsou popsané v této příručce. Jsou samozřejmě mnoha způsoby, které stojí za zvážení. Například jeden z klíčových scénářů pro službu Table Service je použít [model Materializovaného zobrazení](https://msdn.microsoft.com/library/azure/dn589782.aspx) z [příkaz oddělení pro odpovědnost dotazů (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) vzor.  

### <a name="intra-partition-secondary-index-pattern"></a>Model sekundárních indexů uvnitř oddílu
Store několik kopií každého entitu s využitím různých **RowKey** hodnot (ve stejném oddílu) umožňují rychlé a efektivní vyhledávání a alternativní pořadí řazení s použitím různých **RowKey** hodnoty. Aktualizace mezi kopie může udržovat konzistentní vzhledem k aplikacím pomocí EGTs.  

#### <a name="context-and-problem"></a>Kontext a problém
Automaticky indexuje entit s využitím služby Table service **PartitionKey** a **RowKey** hodnoty. To umožňuje klientské aplikaci k načtení entity efektivně pomocí těchto hodnot. Například struktura tabulky je uvedeno níže, klientské aplikace pomocí bodu dotaz k načtení entity jednotlivých zaměstnanců pomocí názvu oddělení a id zaměstnance ( **PartitionKey** a **RowKey**  hodnoty). Klienta můžete také načíst entity seřazené podle id zaměstnance v rámci každé oddělení.

![Zaměstnanec entity][6]

Pokud chcete také moct vyhledat entitu zaměstnanci založena na hodnotě jiné vlastnosti, jako jsou e-mailovou adresu, musíte použít méně efektivní prohledávání oddílu pro vyhledání shody. Je to proto, že služba table service neposkytuje sekundární indexy. Kromě toho není žádná možnost žádost o seznam zaměstnanců řazení v jiném pořadí než **RowKey** pořadí.  

#### <a name="solution"></a>Řešení
Obejít chybějící sekundární indexy, můžete uložit několik kopií Každá entita se každá kopie použitím různých **RowKey** hodnotu. Pokud ukládáte entity s struktury je uvedeno níže, můžete efektivně načítat zaměstnance entity na základě id e-mailovou adresu nebo zaměstnanců. Předpona hodnoty **RowKey**, "empid_" a "email_" umožňují dotazování jednoho zaměstnance nebo rozsah zaměstnanců pomocí celé řady e-mailové adresy nebo ID zaměstnance.  

![Zaměstnanec entity s různými hodnotami RowKey][7]

Následující kritéria filtru dva (jeden vyhledávání podle id zaměstnance a jeden vyhledávání pomocí e-mailová adresa) zadejte oba bodových dotazů:  

* $filter = (PartitionKey eq "Prodeje") a (RowKey eq "empid_000223")  
* $filter = (PartitionKey eq "Prodeje") a (RowKey eq 'email_jonesj@contoso.com")  

Když odešlete dotaz rozsahu entit zaměstnanců, můžete zadat rozsah seřazené podle id zaměstnance, nebo rozsah seřazené podle e-mailové adresy pomocí dotazu pro entity příslušnou předponu v **RowKey**.  

* Najít všechny zaměstnance v prodejní oddělení s id zaměstnanců používá rozsah 000100 k 000199: $filter = (PartitionKey eq "Prodeje") a (RowKey ge "empid_000100") a (RowKey le "empid_000199")  
* Najít všechny zaměstnance z oddělení prodeje s e-mailovou adresu, začíná písmenem "a" použití: $filter = (PartitionKey eq "Prodeje") a (RowKey ge "email_a") a (RowKey lt "email_b")  
  
  Filtr syntaxe používané ve výše uvedených příkladech je z rozhraní REST API služby tabulky pro další informace najdete v tématu [dotazu entity](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Table storage je relativně levné, abyste mohli režii náklady na uložení duplicitních dat nesmí být závažný problém. By ale vždy vyhodnoceny náklady na návrhu na základě vašich požadavků na vytížení úložiště a pouze přidat duplicitní entity na podporu dotazů, které se spustí klientské aplikace.  
* Protože sekundární index entity, které se ukládají do stejného oddílu jako původní entity, měli byste zajistit nepřekročí cíle škálovatelnosti pro jednotlivé oddíl.  
* Abyste mohli duplicitní položky konzistentní mezi sebou pomocí EGTs atomicky aktualizovat dvě kopie entity. Z toho vyplývá, že byste měli uložit všechny kopie entity do stejného oddílu. Další informace najdete v části [pomocí transakcí skupin entit](#entity-group-transactions).  
* Hodnota použitá **RowKey** musí být jedinečný pro každou entitu. Zvažte použití složené klíčové hodnoty.  
* Odsazení číselných hodnot v **RowKey** (třeba id zaměstnance 000223), opravte umožňuje řazení a filtrování na základě horní a dolní meze.  
* Nutně není potřeba duplicitní vlastnosti vaší entity. Například, pokud adresa dotazy, které vyhledávají entity pomocí e-mailu v **RowKey** nikdy nepotřebují zaměstnance věk, může tyto entity mají následující strukturu:

![Zaměstnanec entity][8]

* Je obvykle vhodnější ukládat duplicitních dat a ujistěte se, že můžete načíst všechna data, která potřebujete, s pomocí jediného dotazu a než chcete použijte jednoho dotazu a vyhledejte entitu a druhý k vyhledání požadovaná data.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když klientská aplikace potřebuje k načtení entit s využitím širokou škálu různých klíčů, když klient potřebuje načítání entit na jiné pořadí řazení, a pokud chcete zjistit, každá entita, využít celou řadu jedinečné hodnoty. Nicméně byste měli jistotu, že nedošlo k překročení limitů škálovatelnosti oddílu při provádění vyhledávání entit pomocí různých **RowKey** hodnoty.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Model mezi oddílu sekundárních indexů](#inter-partition-secondary-index-pattern)
* [Složené sekvence klíče](#compound-key-pattern)
* [Transakcí skupin entit](#entity-group-transactions)
* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Model mezi oddílu sekundárních indexů
Store několik kopií každého entitu s využitím různých **RowKey** hodnoty v samostatných oddílů nebo v samostatné tabulky, které umožňují rychlé a efektivní vyhledávání a alternativní pořadí řazení s použitím různých **RowKey**hodnoty.  

#### <a name="context-and-problem"></a>Kontext a problém
Automaticky indexuje entit s využitím služby Table service **PartitionKey** a **RowKey** hodnoty. To umožňuje klientské aplikaci k načtení entity efektivně pomocí těchto hodnot. Například struktura tabulky je uvedeno níže, klientské aplikace pomocí bodu dotaz k načtení entity jednotlivých zaměstnanců pomocí názvu oddělení a id zaměstnance ( **PartitionKey** a **RowKey**  hodnoty). Klienta můžete také načíst entity seřazené podle id zaměstnance v rámci každé oddělení.  

![Zaměstnanec entity][9]

Pokud chcete také moct vyhledat entitu zaměstnanci založena na hodnotě jiné vlastnosti, jako jsou e-mailovou adresu, musíte použít méně efektivní prohledávání oddílu pro vyhledání shody. Je to proto, že služba table service neposkytuje sekundární indexy. Kromě toho není žádná možnost žádost o seznam zaměstnanců řazení v jiném pořadí než **RowKey** pořadí.  

Jsou předvídání k velkému počtu transakcí proti takové instituci a chcete minimalizovat riziko omezení klienta služby míry tabulky.  

#### <a name="solution"></a>Řešení
Obejít chybějící sekundární indexy, můžete uložit několik kopií Každá entita s každou kopii pomocí různých **PartitionKey** a **RowKey** hodnoty. Pokud ukládáte entity s struktury je uvedeno níže, můžete efektivně načítat zaměstnance entity na základě id e-mailovou adresu nebo zaměstnanců. Předpona hodnoty **PartitionKey**, "empid_" a "email_" umožňují určit index, který chcete použít pro dotaz.  

![Entita zaměstnance s primární index a zaměstnance entita s sekundární index][10]

Následující kritéria filtru dva (jeden vyhledávání podle id zaměstnance a jeden vyhledávání pomocí e-mailová adresa) zadejte oba bodových dotazů:  

* $filter = (PartitionKey eq ' empid_Sales") a (RowKey eq"000223")
* $filter = (PartitionKey eq ' email_Sales") a (RowKey eq 'jonesj@contoso.com")  

Když odešlete dotaz rozsahu entit zaměstnanců, můžete zadat rozsah seřazené podle id zaměstnance, nebo rozsah seřazené podle e-mailové adresy pomocí dotazu pro entity příslušnou předponu v **RowKey**.  

* Najít všechny zaměstnance z oddělení prodeje s id zaměstnance v rozsahu **000100** k **000199** seřazeny vzestupně v zaměstnance id pořadí použití: $filter = (PartitionKey eq ' empid_Sales") a (RowKey ge"000100") a (RowKey le "000199")  
* Najít všechny zaměstnance z oddělení prodeje s e-mailovou adresu, která začíná textem "a" v e-mailovou adresu pořadí použijte seřazený: $filter = (PartitionKey eq ' email_Sales") a (RowKey ge"a") a (RowKey lt"b")  

Všimněte si, že filtr syntaxe používané ve výše uvedených příkladech je z rozhraní REST API služby tabulky pro další informace najdete v tématu [dotazu entity](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Můžete ponechat duplicitní položky konzistentní mezi sebou pomocí [konečnou konzistenci transakcí vzor](#eventually-consistent-transactions-pattern) udržovat entity primárních a sekundárních indexů.  
* Table storage je relativně levné, abyste mohli režii náklady na uložení duplicitních dat nesmí být závažný problém. By ale vždy vyhodnoceny náklady na návrhu na základě vašich požadavků na vytížení úložiště a pouze přidat duplicitní entity na podporu dotazů, které se spustí klientské aplikace.  
* Hodnota použitá **RowKey** musí být jedinečný pro každou entitu. Zvažte použití složené klíčové hodnoty.  
* Odsazení číselných hodnot v **RowKey** (třeba id zaměstnance 000223), opravte umožňuje řazení a filtrování na základě horní a dolní meze.  
* Nutně není potřeba duplicitní vlastnosti vaší entity. Například pokud dotazy tento vyhledávací entity pomocí e-mailu adresa v **RowKey** nikdy nepotřebují zaměstnance věk, může tyto entity mají následující strukturu:
  
  ![Entita zaměstnance s sekundární index][11]
* Je obvykle vhodnější duplicitními daty a ujistěte se, že můžete načíst všechna data, která potřebujete, s pomocí jediného dotazu než chcete použijte jednoho dotazu a vyhledejte entitu pomocí sekundární index a druhou pro vyhledávání požadovaných dat v primární index.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když klientská aplikace potřebuje k načtení entit s využitím širokou škálu různých klíčů, když klient potřebuje načítání entit na jiné pořadí řazení, a pokud chcete zjistit, každá entita, využít celou řadu jedinečné hodnoty. Tento model použijte, pokud chcete, aby nedošlo k překročení limitů škálovatelnosti oddílu jsou při vyhledávání entit pomocí různých **RowKey** hodnoty.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern)  
* [Model sekundárních indexů uvnitř oddílu](#intra-partition-secondary-index-pattern)  
* [Složené sekvence klíče](#compound-key-pattern)  
* [Transakcí skupin entit](#entity-group-transactions)  
* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Vzor konečnou konzistenci transakcí
Pomocí front Azure umožňuje konzistentní chování napříč hranice oddílů nebo systému hranice úložiště.  

#### <a name="context-and-problem"></a>Kontext a problém
EGTs umožňuje atomické transakce napříč více entit, které sdílejí stejný klíč oddílu. Pro výkon a škálovatelnost, můžete se rozhodnout pro uložení entity, které mají na konzistenci v samostatných oddílech nebo v samostatné úložiště systému: v takové situaci nelze použít EGTs můžete zachovat konzistenci. Například můžete mít povinnost udržovat konečnou konzistenci mezi:  

* Entity uložená ve dvou různých oddílů ve stejné tabulce, různých tabulek nebo jiný účet úložiště.  
* Na entitu uloženou ve službě Table a blob uložených ve službě Blob service.  
* Na entitu uloženou ve službě Table service a soubor v systému souborů.  
* Úložiště entity ve službě Table service dosud indexovány pomocí služby Azure Search.  

#### <a name="solution"></a>Řešení
Pomocí front Azure můžete implementovat řešení, které zajišťuje konečnou konzistenci napříč dvěma nebo více oddílů nebo úložných systémů.
Pro ilustraci tento přístup předpokládá, že máte požadavek mohli archivovat starší zaměstnance entity. Starší zaměstnance entity jsou zřídka dotazovat a má být vyloučen z jakékoli činnosti, které pracují s aktuální zaměstnanci. Chcete-li implementovat tento požadavek můžete ukládat aktivních zaměstnanců v **aktuální** tabulky a staré zaměstnanci v **archivu** tabulky. Archivace zaměstnanec je potřeba odstranit entitu z **aktuální** tabulku a přidat entita, která má **archivu** tabulky, ale nelze použít EGT provádět tyto dvě operace. Aby nevznikalo riziko, způsobující selhání entity se zobrazí v obou nebo ani jedno z těchto tabulek, musí být operace archivování konečnou konzistenci. Následující sekvence diagramu popisuje kroky v této operaci. Podrobněji se poskytuje pro cest výjimek. v následujícím textu.  

![Diagram řešení pro konečné konzistence][12]

Klient spustí operaci archivu tak, že zprávu ve frontě Azure, v tomto příkladu pro archivaci zaměstnance #456. Role pracovního procesu dotazuje fronty na nové zprávy. Po nalezení znaku jednu, přečte zprávu a ponechá skrytá kopie ve frontě. Role pracovního procesu načítá další kopie entity z **aktuální** tabulky, vloží kopii **archivu** tabulku a pak odstraní původní z **aktuální** tabulky. Nakonec pokud nebyly zjištěny žádné chyby v předchozích krocích, role pracovního procesu odstraní skrytou zprávu z fronty.  

Krok 4 v tomto příkladu vloží zaměstnance do **archivu** tabulky. Zaměstnanec ho přidat do souboru v systému souborů nebo objekt blob ve službě Blob service.  

#### <a name="recovering-from-failures"></a>Zotavení z chyby
Je důležité, které operace v krocích **4** a **5** musí být *idempotentní* v případě, že role pracovního procesu je nutné restartovat archivní operace. Pokud používáte služby Table service pro krok **4** byste měli použít operace "Vložit nebo nahradit"; krok **5** byste měli používat "odstranit, pokud existuje" operace v knihovně klienta, který používáte. Pokud používáte jiný úložný systém, musíte použít odpovídající idempotentní operace.  

Pokud role pracovního procesu nikdy dokončení kroku **6**, pak po vypršení časového limitu se zpráva zobrazí znovu ve frontě připravené pro roli pracovního procesu se pokuste ji znovu zpracovat. Role pracovního procesu můžete zkontrolovat, kolikrát zprávu ve frontě byla čtení a v případě potřeby příznak je "nezpracovatelná" zpráva pro šetření a odeslat ho do samostatné fronty. Další informace o čtení zprávy fronty a kontrola počet odstranění z fronty, naleznete v tématu [získání zpráv](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Některé chyby z tabulky a fronty služby jsou přechodné chyby a klientské aplikace by měla obsahovat logiku opakování vhodné jejich zpracování.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení neposkytuje pro transakci izolace. Například může klienta číst **aktuální** a **archivu** tabulky při role pracovního procesu se mezi jednotlivými kroky **4** a **5**a podívejte se nekonzistentní zobrazení dat. Nakonec bude konzistentní data.  
* Je nutné zajistit, že kroky 4 a 5 jsou idempotentní, aby bylo možné zajistit konzistenci typu případné.  
* Řešení můžete škálovat s použitím více front a instancí rolí pracovního procesu.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, pokud chcete zajistit konečnou konzistenci mezi entitami, které existují v různých oddílů nebo tabulky. Tento model zajistit konzistenci typu případné pro operace ve službě Table service a službu Blob service a jiné než Azure úložiště zdroje dat, jako jsou databáze nebo systému souborů můžete rozšířit.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakcí skupin entit](#entity-group-transactions)  
* [Sloučení nebo nahradit](#merge-or-replace)  

> [!NOTE]
> Transakce izolace je důležité, abyste vaše řešení, měli byste zvážit, realizace vám umožní používat EGTs tabulek.  
> 
> 

### <a name="index-entities-pattern"></a>Model entity indexů
Udržujte index entity, které umožňují efektivní prohledávání, které vrací seznam entit.  

#### <a name="context-and-problem"></a>Kontext a problém
Automaticky indexuje entit s využitím služby Table service **PartitionKey** a **RowKey** hodnoty. To umožňuje klientské aplikaci k načtení entity efektivně pomocí dotazu bodu. Například pomocí struktura tabulky je uvedeno níže, klientské aplikace můžou efektivně načítat entity jednotlivých zaměstnanců pomocí názvu oddělení a id zaměstnance ( **PartitionKey** a **RowKey**).  

![Zaměstnanec entity][13]

Pokud chcete také moct načíst seznam entit zaměstnance založena na hodnotě jiné vlastnosti není jedinečný, jako je například poslední název, musíte použít méně efektivní prohledávání oddílu najít odpovídá místo použití indexu je vyhledat přímo. Je to proto, že služba table service neposkytuje sekundární indexy.  

#### <a name="solution"></a>Řešení
Povolit vyhledávání podle příjmení pomocí struktury entit, které jsou uvedené výše, musíte mít seznam ID zaměstnance. Pokud budete chtít získat entity zaměstnance poslední název, jako je například Jones, musí nejprve vyhledat seznam ID zaměstnance pro zaměstnance s Jones jako jejich příjmení a následně načíst tyto entity zaměstnance. Existují tři hlavní možnosti pro ukládání seznamů ID zaměstnance:  

* Používání úložiště blob.  
* Vytvořte index entity do stejného oddílu jako entity zaměstnance.  
* Vytvoření indexu entit v samostatném oddílu nebo tabulky.  

<u>Možnost #1: Použití služby blob storage</u>  

Pro první možnost se vám vytvoření objektu blob pro každou jedinečnou příjmení a v každé úložiště objektů blob v seznamu **PartitionKey** (department) a **RowKey** hodnoty (id zaměstnance) pro zaměstnance, kteří mají tento poslední název. Při přidání nebo odstranění zaměstnanec, měli byste zajistit, že obsah objektu blob relevantní je konzistentní s entitami zaměstnance.  

<u>Možnost #2:</u> Vytvoření indexu entit ve stejném oddílu  

Pro druhou možnost použijte index entity, které ukládat následující data:  

![Zaměstnanec entity s řetězcem, který obsahuje seznam ID zaměstnanců se stejným názvem poslední][14]

**EmployeeIDs** vlastnost obsahuje seznam ID zaměstnance pro zaměstnance s poslední název uložený v **RowKey**.  

Následující kroky popisují proces, kterým byste měli postupovat při přidávání nového zaměstnance Pokud používáte druhou možnost. V tomto příkladu přidáváme zaměstnance s Id 000152 a příjmení Jones prodejního oddělení:  

1. Načtení entity index s **PartitionKey** hodnotu "Prodeje" a **RowKey** hodnotu "Jones." Uložte ETag tuto entitu pro použití v kroku 2.  
2. Vytvoření skupiny transakce entity (to znamená, dávkové operace), která vloží nové zaměstnance entity (**PartitionKey** hodnotu "Prodeje" a **RowKey** hodnotu "000152") a aktualizuje index entity (**PartitionKey** hodnotu "Prodeje" a **RowKey** hodnotu "Novák") tak, že přidáte do seznamu v poli EmployeeIDs nové id zaměstnance. Další informace o transakcí skupin entit najdete v tématu [transakcí skupin entit](#entity-group-transactions).  
3. Pokud transakce skupiny entit selže z důvodu chyby optimistického řízení souběžnosti (někdo jiný změnil index entity), budete muset začít v kroku 1.  

Podobný přístup se odstraňuje zaměstnanec, pokud používáte druhou možnost můžete použít. Změna zaměstnance příjmení je o něco složitější, protože budete muset provést transakci skupiny entity, která aktualizuje entity tři: entity zaměstnance, index entity pro staré příjmení a index entitu pro nové příjmení. Každá entita musí získat před provedením jakýchkoli změn získat značku ETag hodnoty, které pak můžete provádět aktualizace pomocí optimistického řízení souběžnosti.  

Následující kroky popisují proces, kterým byste měli postupovat, když budete chtít vyhledat všechny zaměstnance se daný příjmení v oddělení, pokud používáte druhou možnost. V tomto příkladu jsme se všichni zaměstnanci s příjmení Jones prodejního oddělení vyhledávání:  

1. Načtení entity index s **PartitionKey** hodnotu "Prodeje" a **RowKey** hodnotu "Jones."  
2. Parsovat seznam ID v poli EmployeeIDs zaměstnanců.  
3. Pokud potřebujete další informace o každém z těchto zaměstnanci (například jejich e-mailové adresy), načíst všechny entity zaměstnance pomocí **PartitionKey** hodnotu "Prodeje" a **RowKey** hodnoty z seznam zaměstnanců, které jste získali v kroku 2.  

<u>Možnost #3:</u> Vytvoření indexu entit v samostatném oddílu nebo tabulky  

Třetí možnost použití indexu entity, které ukládat následující data:  

![Zaměstnanec entity s řetězcem, který obsahuje seznam ID zaměstnanců se stejným názvem poslední][15]

**EmployeeIDs** vlastnost obsahuje seznam ID zaměstnance pro zaměstnance s poslední název uložený v **RowKey**.  

Pomocí třetí možnost nelze použít EGTs můžete zachovat konzistenci, protože index entity, které jsou v samostatném oddílu z entity, které zaměstnanec. Měli byste zajistit, že entity indexu jsou konzistentní s entitami zaměstnance.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení vyžaduje alespoň dva dotazy pro načtení odpovídajících entit: jednu k dotazování indexu entity, které chcete získat seznam **RowKey** hodnoty a potom dotazy k načtení jednotlivých entit v seznamu.  
* Vzhledem k tomu, že jednotlivé entity má maximální velikosti 1 MB, možnost #2 a možnost #3 v řešení se předpokládá, že seznam ID zaměstnance pro danou příjmení je nikdy větší než 1 MB. Pokud seznam ID zaměstnance bude pravděpodobně do 1 MB přesahovat, použijte možnost #1 a ukládat data indexu ve službě blob storage.  
* Pokud použijete možnost #2 (pomocí EGTs zpracování přidávání a odstraňování zaměstnanci a změna zaměstnance příjmení) naplňují Pokud objem transakcí se přístup omezení škálovatelnosti v daném oddílu. Pokud je to tento případ, měli byste zvážit konzistentní řešení typu (možnost #1 nebo #3), která používá fronty pro zpracování žádosti o aktualizaci a umožňuje ukládání entit indexu v samostatném oddílu z entit zaměstnance.  
* Možnost #2 v tomto řešení se předpokládá, že chcete vyhledat podle jména v rámci oddělení: například chcete načíst seznam zaměstnancům příjmení Jones prodejního oddělení. Pokud chcete vyhledat všechny zaměstnance se příjmení Jones v celé organizaci, použijte buď možnost #1 nebo #3.
* Můžete implementovat řešení na základě fronty, která poskytuje konečnou konzistenci (najdete v článku [vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern) další podrobnosti).  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, pokud chcete vyhledat sadu entit, které sdílejí společné hodnoty vlastnosti, jako je například všechny zaměstnance se příjmení Jones.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Složené sekvence klíče](#compound-key-pattern)  
* [Vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern)  
* [Transakcí skupin entit](#entity-group-transactions)  
* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Vzor denormalizace
Kombinování souvisejících dat společně v jedné entitě umožňuje načíst všechna data, které potřebujete, s jediným bodem dotazu.  

#### <a name="context-and-problem"></a>Kontext a problém
V relační databázi obvykle normalizovat data odebrat duplicity, které jsou výsledkem dotazů, které načítají data z více tabulek. Pokud jste normalizovat data do tabulek Azure, musíte udělat více výměn od klienta k serveru a související data načíst. Například s struktura tabulky níže budete potřebovat dva zpátečních cest k načtení podrobností pro oddělení: jednu k načtení entity oddělení, která zahrnuje id správce a pak další požadavek k načtení podrobností manažera v entitě zaměstnance.  

![Oddělení entitou a entitou zaměstnance][16]

#### <a name="solution"></a>Řešení
Místo ukládání dat v rámci dvě samostatné entity, denormalizovat data a ponechat si kopii manažera podrobnosti v entitě oddělení. Příklad:  

![Nenormalizovaná a kombinované oddělení entity][17]

S entitami oddělení uložená s těmito vlastnostmi můžete teď načíst všechny podrobnosti, potřebné informace o oddělení pomocí dotazu bodu.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Existuje nějaké náklady režie spojené s ukládáním některá data dvakrát. Výhody výkonu (vyplývající z menší počet požadavků na službu storage) obvykle větší váhu než minimálním ziskem zvýšení nákladů na úložiště (a snížení počtu transakcí, které budete potřebovat k načtení podrobností oddělení částečně posunut tyto náklady ).  
* Konzistence dvě entity, které ukládají informace o správcích, musíte mít. Problém konzistence může zpracovávat pomocí EGTs aktualizovat několik entit v jediné atomické transakce: v tomto případě oddělení entitu a entitu zaměstnanci pro vedoucí oddělení se ukládají do stejného oddílu.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když je často potřeba vyhledat související informace. Tento model snižuje počet dotazů, které váš klient musí Ujistěte se, aby se načetla data, které vyžaduje.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Složené sekvence klíče](#compound-key-pattern)  
* [Transakcí skupin entit](#entity-group-transactions)  
* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Složené sekvence klíče
Použití složeného **RowKey** hodnoty umožňují klientům vyhledat související data pomocí dotazu jediný bod.  

#### <a name="context-and-problem"></a>Kontext a problém
V relační databázi je přirozeně použít spojení v dotazech, který vrátí související časti dat klientovi v jediném dotazu. Id zaměstnance můžete například použít k vyhledání seznam související entity, které obsahují výkon a kontrolovat data pro zaměstnance.  

Předpokládejme, že ukládáte zaměstnance entit ve službě Table service pomocí následující struktury:  

![Zaměstnanec entity][18]

Musíte také ukládat historická data týkající se kontroly a výkonu pro jednotlivé roky, které zaměstnanec pracoval pro vaši organizaci, a musíte být schopni přistupovat k těmto informacím podle roku. Jednou z možností je vytvoření další tabulky, která ukládá entity s následující strukturou:  

![Entita recenze zaměstnance][19]

Všimněte si, že s tímto přístupem budete muset duplikovat některé informace (například křestní jméno a příjmení) v nové entity umožňující vám umožní načíst dat s využitím jedné žádosti. Nelze však udržovat silnou konzistenci, protože EGT nelze použít k aktualizaci těchto dvou entitách atomicky.  

#### <a name="solution"></a>Řešení
Nový typ entity Store v původní tabulce pomocí entit s následující strukturou:  

![Entita zaměstnance s složený klíč][20]

Všimněte si, že jak **RowKey** je nyní složený klíč skládá z id zaměstnance a rok revize data, která umožňuje načíst zaměstnance výkon a kontrolovat data jedním požadavkem pro jednu entitu.  

Následující příklad popisuje, jak můžete načíst všechna data kontroly pro zaměstnance (třeba 000123 zaměstnanci z oddělení prodeje):  

$filter = (PartitionKey eq "Prodeje") a (RowKey ge "empid_000123") a (RowKey lt 'empid_000124') & $select = RowKey, správce hodnocení, Peer hodnocení, komentáře  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Používejte vhodný oddělovací znak, který usnadňuje parse **RowKey** hodnota: například **000123_2012**.  
* Tato entita jsou také ukládání do stejného oddílu jako další entity, které obsahují související data pro stejné zaměstnance, což znamená, že vám pomůže EGTs udržovat silnou konzistenci.
* Měli byste zvážit, jak často se zadávat dotazy na data k určení, zda tento vzor odpovídá.  Například pokud budete využívat data recenzí zřídka a daty o zaměstnancích hlavní často je nutné jej uschovat jako samostatné entity.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když potřebujete ukládat jeden nebo více souvisejících entit dotazu často.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakcí skupin entit](#entity-group-transactions)  
* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)  
* [Vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Vzor log tail
Načíst *n* naposledy přidaný do oddílu s použitím entity **RowKey** hodnotu, která seřadí reverzní datum a čas objednávky.  

#### <a name="context-and-problem"></a>Kontext a problém
Běžné požadavky, je moct načíst nedávno vytvořené entity, třeba posledních deset výdaje odeslal zaměstnanec deklarací identity. Dotazy podpory **$top** dotazové operace do vrátí první *n* entity ze sady: neexistuje žádná odpovídající dotaz operace vrátit poslední n entity v sadě.  

#### <a name="solution"></a>Řešení
Store entity pomocí **RowKey** přirozeně seřadí v pořadí reverzní datum a čas pomocí tak nejnovější položky je vždy první z nich v tabulce.  

Například aby bylo možné načíst deset nejnovější deklarací výdajů odeslal zaměstnanec, můžete použít reverzní značek hodnotou odvozenou od aktuálního data a času. Následující vzorový kód jazyka C# ukazuje jeden ze způsobů vytvoření vhodnou hodnotu "obrácený značky" pro **RowKey** , která řadí od nejnovější do nejstaršího:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Můžete získat zpět na hodnotu data a času pomocí následujícího kódu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Tabulka dotaz vypadá takto:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Musíte vyplnění zpětného značek hodnotu s úvodní nuly Ujistěte se, že hodnotu řetězce seřadí podle očekávání.  
* Musíte být vědomi cíle škálovatelnosti na úrovni oddílu. Buďte opatrní vytváření oddílů aktivního bodu.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když budete potřebovat pro přístup k entity v pořadí reverzní data a času nebo když potřebujete pro přístup k naposledy přidané entity.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Předřaďte / připojovat proti vzor](#prepend-append-anti-pattern)  
* [Načítání entit](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Vzor vysoké objemy delete
Povolit odstranění k velkému počtu entit uložením všechny entity pro souběžné odstranění vlastních samostatné tabulky; odstranit entity odstraněním tabulky.  

#### <a name="context-and-problem"></a>Kontext a problém
Mnoho aplikací odstranit stará data, která již nemusí být k dispozici pro klientské aplikace, nebo aplikace má archivovat do jiné úložné médium. Obvykle identifikaci těchto dat pomocí datum: například máte požadavek na odstranění záznamů všech žádostí o přihlášení, které jsou starší než 60 dní.  

Jeden je to možné je použít datum a čas přihlášení na požadavek na **RowKey**:  

![Entita pokus o přihlášení][21]

Tento přístup vyhnete hotspotům oddílu, protože aplikace může vkládat a odstraňovat přihlášení entity pro každého uživatele v samostatném oddílu. Tento přístup však může být drahá a časově náročné Pokud máte velké množství entit, protože nejdřív je potřeba provést prohledávání tabulky za účelem zjištění všech entity, které chcete odstranit, a pak musíte odstranit každé staré entity. Můžete snížit počet zpátečních cest k serveru muset odstranit staré entity do EGTs dávkování víc požadavků delete.  

#### <a name="solution"></a>Řešení
Do samostatné tabulky použijte pro každý den pokusů o přihlášení. Výše uvedené entity návrhu můžete použít při vkládání entit a odstraňování starých entity je nyní jednoduše dotaz odstranění jedné tabulky každý den, aby hotspotům (úložiště jediné operace) namísto hledání a odstraňování stovkami a tisíci osoba značky entit každý den.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Podporuje váš návrh dalších způsobů, jak bude aplikace používat data, jako je například vyhledávání konkrétních entit, propojení s jinými dat nebo generování souhrnné informace?  
* Váš návrh vyhnout aktivní body při vkládání nové entity?  
* Pokud chcete znovu použít stejný název tabulky po jejím odstranění očekávat, že ke zpoždění. Je lepší, vždy používali unikátní tabulku názvů.  
* Můžete očekávejte některé rychlosti při prvním použití novou tabulku služby Table service učí vzorce přístupu a distribuuje oddílů mezi uzly. Měli byste zvážit, jak často je potřeba vytvořit nové tabulky.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že máte velký počet entit, které je nutné odstranit ve stejnou dobu.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakcí skupin entit](#entity-group-transactions)
* [Úprava entit](#modifying-entities)  

### <a name="data-series-pattern"></a>Vzor data řady
Řada kompletní data Store v jedné entity, chcete-li minimalizovat počet požadavků, které provedete.  

#### <a name="context-and-problem"></a>Kontext a problém
Běžný scénář, kdy je aplikace k ukládání řadu data, která je obvykle potřeba načíst všechny najednou. Vaše aplikace například může zaznamenat kolik zasílání Rychlých zpráv každý zaměstnanec odešle každou hodinu a pak tyto informace slouží k vykreslení počet zpráv každý uživatel odesílá přes předchozími 24 hodinami. Jeden návrhu může být k ukládání 24 entity pro každý zaměstnanec:  

![Entity statistiky zpráv][22]

V tomto návrhu může snadno najít a aktualizovat se pro každý zaměstnanec pokaždé, když aplikace potřebuje aktualizovat hodnota počtu zpráv. K načtení informací k vykreslení grafu aktivity za předchozích 24 hodin, ale musíte načíst 24 entity.  

#### <a name="solution"></a>Řešení
K uložení počet zpráv pro každou hodinu pomocí samostatných vlastnost následující:  

![Statistika entity zprávu s oddělenými vlastnosti][23]

Operace sloučení s tímto návrhem slouží k aktualizaci počet zpráv pro zaměstnance jenom konkrétní hodiny. Nyní můžete načíst všechny informace potřebné k vykreslení grafu pomocí žádosti pro jednu entitu.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Pokud dokončení datové řady se nevejde do jedné entity (entita může mít až 252 vlastností), použijte alternativní úložiště třeba jako objekt blob.  
* Pokud máte víc klientů současně aktualizaci entity, je potřeba použít **ETag** implementace optimistického řízení souběžnosti. Pokud máte mnoho klientů, může docházet vysokou kolize.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když potřebujete aktualizovat a načíst datové řady přidružené jednotlivých entit.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor velkých entit](#large-entities-pattern)  
* [Sloučení nebo nahradit](#merge-or-replace)  
* [Vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern) (Pokud ukládáte datové řady do objektu BLOB)  

### <a name="wide-entities-pattern"></a>Vzor široké entity
Slouží k ukládání logické entity s více než 252 vlastností více fyzických entit.  

#### <a name="context-and-problem"></a>Kontext a problém
Jednotlivých entit může mít maximálně 252 vlastností (s výjimkou vlastnosti povinné systému) a nejde uložit více než 1 MB dat celkem. V relační databázi by získáte zpravidla round žádné omezení velikosti řádku přidat novou tabulku a vynucování vztah 1: 1 mezi nimi.  

#### <a name="solution"></a>Řešení
Použití služby Table service, můžete uložit více entity, které představují jeden velký podnik objekt s více než 252 vlastností. Například pokud chcete uložit počet Rychlých zpráv odesílaných jednotliví zaměstnanci za posledních 365 dnů, můžete použít následující návrhu, který používá dvě entity s různými schématy:  

![Entity statistiky zprávu s Rowkey 01 a zprávu stav entity s Rowkey 02][24]

Pokud potřebujete provést změnu, která vyžaduje aktualizaci obě entity k zachování pro synchronizaci mezi sebou, můžete použít EGT. V opačném případě můžete použít jeden sloučených aktualizovat počet zpráv pro určitý den. Načíst všechna data pro jednotlivé zaměstnance musí načíst obě entity, které vám pomůžou s dva efektivní požadavky, které obě používají **PartitionKey** a **RowKey** hodnotu.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Načítání kompletní logická entita vyžaduje alespoň dva transakce služby storage: jednu k načtení každá fyzická entita.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě potřeba ukládat entity, jejichž velikost nebo počet vlastností překročí limity pro jednotlivé entity ve službě Table service.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakcí skupin entit](#entity-group-transactions)
* [Sloučení nebo nahradit](#merge-or-replace)

### <a name="large-entities-pattern"></a>Vzor velkých entit
Používání úložiště blob k ukládání hodnot vlastností velké.  

#### <a name="context-and-problem"></a>Kontext a problém
Jednotlivé entity Nejde uložit více než 1 MB dat celkem. Pokud jeden nebo několik z vlastností ukládání hodnot, které způsobují celková velikost překročí tuto hodnotu vaší entity, nelze ukládat celé entity ve službě Table service.  

#### <a name="solution"></a>Řešení
Pokud entita 1 MB překračuje velikost protože jednu nebo více vlastností obsahovat velké množství dat, můžete ukládat data ve službě Blob service a potom ukládání adresy objektu blob ve vlastnosti v entitě. Například můžete ukládat fotky zaměstnanec v úložišti objektů blob a ukládání odkaz fotografii v **fotografii** vlastnosti vaší entity zaměstnance:  

![Zaměstnanec entity s řetězcem pro fotografii směřující do úložiště objektů blob][25]

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Chcete-li zachovat konečné konzistenci mezi entity ve službě Table service a data ve službě Blob service, použijte [konečnou konzistenci transakcí vzor](#eventually-consistent-transactions-pattern) udržovat vaše entity.
* Načítání úplnou entitu zahrnuje nejméně dva transakce služby storage: jednu k načtení entity a z nich se má načíst data objektů blob.  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když budete chtít ukládat entity, jejichž velikost přesahuje limity pro jednotlivé entity ve službě Table service.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern)  
* [Vzor široké entity](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Předřaďte/připojovat proti vzor
Až budete mít k velkému počtu vloží tím, že rozprostírá vložení informací napříč několika oddíly, zvýšení škálovatelnosti.  

#### <a name="context-and-problem"></a>Kontext a problém
Předřazení nebo připojením entit k uložené entity obvykle za následek přidání nové entity na první nebo poslední oddíl posloupnost oddíly aplikace. V takovém případě všechny operace vložení v daném okamžiku je možné místo do stejného oddílu, vytváření aktivního bodu, který brání Vyrovnávání zatížení služby table service vloží napříč několika uzly a pravděpodobně způsobuje aplikaci k dosažení cíle škálovatelnosti pro oddíl. Například pokud máte aplikaci, která zaměstnanci přistupovat k protokoly sítě a prostředků, pak struktury entit, jak je znázorněno níže může způsobit oddílu do aktuální hodiny, stane hotspot, pokud objem transakcí dosáhne cíle škálovatelnosti pro jednotlivé oddíl:  

![Zaměstnanec entity][26]

#### <a name="solution"></a>Řešení
Následující strukturu alternativní entity se vyhnete aktivní bod na žádný konkrétní oddíl jako protokoly událostí aplikace:  

![Entita zaměstnance s RowKey rozkladu slov, rok, měsíc, den, hodinu a ID události][27]

Všimněte si, že v tomto příkladu jak i **PartitionKey** a **RowKey** jsou složené klíče. **PartitionKey** id oddělení a zaměstnanců používá k distribuci protokolování napříč několika oddíly.  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Alternativní klíče struktura, která zabraňuje vytváření aktivních oddílů na vloží efektivně podporuje dotazy, které klientská aplikace odešle?  
* Váš předpokládaný objemu transakcí znamená, že budete pravděpodobně k dosažení cíle škálovatelnosti pro jednotlivé oddíl a omezí službou storage?  

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Připojení/prepend proti vzor vyhněte, když objem transakcí je nejspíš se tady službou storage při přístupu k horkými oddíly.  

#### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Složené sekvence klíče](#compound-key-pattern)  
* [Vzor log tail](#log-tail-pattern)  
* [Úprava entit](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Ochrana proti vzorek dat protokolu
Obvykle byste měli použít službu Blob service namísto služby Table service k ukládání dat protokolu.  

#### <a name="context-and-problem"></a>Kontext a problém
Běžný případ použití pro data protokolu, je načtení výběru položky protokolu pro konkrétní datum a čas rozsah: například chcete najít všechny chyby a kritické zprávy, které aplikace protokoluje mezi 15:04 a 15:06 v konkrétní den. Nechcete použít k určení oddílu, uložte protokol entity na datum a čas zprávy protokolu:, který vede horkými oddíly, protože v daném okamžiku se všechny entity protokolu sdílet stejný **PartitionKey** hodnotu (viz část [Prepend/připojovat proti vzor](#prepend-append-anti-pattern)). Například následující schéma entity pro zprávu protokolu za následek horkými oddíly vzhledem k tomu, že aplikace zapíše všechny zprávy protokolu do oddílu pro aktuální datum a hodiny:  

![Entity zpráv protokolu][28]

V tomto příkladu **RowKey** obsahuje datum a čas zprávy protokolu k zajištění, že zprávy protokolu jsou uloženy seřazeny vzestupně v pořadí datum a čas a id zprávy v případě, že více zpráv protokolu sdílet stejný datum a čas.  

Další možností je použít **PartitionKey** , což zajišťuje, že aplikace zapíše zpráv mezi širokou škálou oddíly. Například pokud zdroj zprávy protokolu zajišťuje distribuci zpráv do mnoho oddílů, můžete použít následující schéma entity:  

![Entity zpráv protokolu][29]

Problém s tímto schématem je však načíst všechny zprávy protokolu pro konkrétní časové období musí prohledávat každý oddíl v tabulce.

#### <a name="solution"></a>Řešení
V předchozí části zvýrazněné problém pokusu o použití služby Table service k ukládání položek protokolu a navrhované dvou, nevyhovující, návrhy. Jedno řešení, které vedly k horkými oddíly s rizikem nízký výkon zápis zprávy protokolu. Dalším řešením výsledkem dotazu nízký výkon díky zadanému požadavku kontrolovat každý oddíl v tabulce k načtení zprávy protokolu pro konkrétní časové období. BLOB storage nabízí lepší řešení pro takové scénáře a toto je Azure Storage Analytics ukládá data protokolu shromažďuje.  

Tato část popisuje, jak Storage Analytics ukládá data protokolu ve službě blob storage jako ilustraci tohoto přístupu k ukládání dat, která se obvykle dotazujete na rozsah.  

Analýza úložiště ukládá zprávy protokolu ve formátu s oddělovači v víc objektů BLOB. Formát odděleného usnadňuje klientské aplikaci parsovat data ve zprávě protokolu.  

Analýza úložiště používá zásady vytváření názvů pro objekty BLOB, které umožňuje vyhledat objekt blob (nebo objekty BLOB), které obsahují zprávy protokolu, které hledáte. Například objekt blob s názvem "queue/2014/07/31/1800/000001.log" obsahuje zprávy protokolu, které se vztahují ke službě fronty za hodinu od 18:00 do 31. července 2014. "000001" označuje, že toto je první soubor protokolu pro toto období. Analýza úložiště taky zaznamenává časová razítka první a poslední protokolové zprávy, uloženy v souboru metadat objektu blob v rámci. Rozhraní API pro objekt blob úložiště umožňuje vyhledat objekty BLOB v kontejneru na základě předpony názvu: Chcete-li vyhledat všechny objekty BLOB, které obsahují data protokolu fronty za hodinu od 18:00, můžete použít předponu "fronty/2014/07/31/1800."  

Vyrovnávací paměti úložišť Analytics interně protokolování zpráv a potom pravidelně aktualizovat odpovídající objekt blob nebo vytvoří nový s poslední dávku položky protokolu. To snižuje počet zápisů, které musíte provést na službu blob service.  

Pokud implementujete podobné řešení ve své aplikaci, musíte zvážit způsob správy kompromis mezi spolehlivosti (zápis každá položka protokolu do úložiště objektů blob v jejím průběhu) a náklady a škálovatelnost (ukládání do vyrovnávací paměti aktualizace pro vaše aplikace a zápis je do úložiště objektů blob v dávkách).  

#### <a name="issues-and-considerations"></a>Problémy a důležité informace
Při rozhodování o tom, jak ukládat data protokolu, zvažte následující body:  

* Pokud vytvoříte návrh tabulky, které se vyhýbají potenciální aktivních oddílů, může být pro vás data protokolu nelze efektivní přístup.  
* Ke zpracování dat protokolu, klient často potřebuje načíst mnoho záznamů.  
* I když je často strukturovaná data protokolu, úložiště objektů blob může být lepším řešením.  

### <a name="implementation-considerations"></a>Důležité informace o implementaci
Tato část popisuje některé důležité informace k berte v úvahu při implementaci vzorce popsané v předchozích částech. Většinu této části se používají příklady napsané v jazyce C#, které použijte klientskou knihovnu pro úložiště (verze 4.3.0 v době psaní).  

### <a name="retrieving-entities"></a>Načítání entit
Jak je popsáno v části [návrhu pro dotazování](#design-for-querying), efektivní dotaz je dotaz bodu. Nicméně v některých případech budete muset načíst více entit. Tato část popisuje některé běžné přístupy k načítání entit s využitím klientskou knihovnu pro úložiště.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>Provádění dotazu bodu pomocí klientskou knihovnu pro úložiště
Nejjednodušší způsob, jak provést dotaz bod je použít **načíst** operace tabulky, jak je znázorněno v následující jazyka C# fragment kódu, která načte entity **PartitionKey** hodnoty "Prodeje" a  **RowKey** hodnoty "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Všimněte si, jak očekává, že v tomto příkladu entity načte typu **EmployeeEntity**.  

#### <a name="retrieving-multiple-entities-using-linq"></a>Načítání více entit pomocí jazyka LINQ
Můžete načíst pomocí LINQ s klientskou knihovnu pro úložiště a zadat dotaz s více entit **kde** klauzuli. Chcete-li zabránit, prohledávání tabulky, by měla vždycky obsahovat **PartitionKey** hodnotu v poli kde klauzule a pokud je to možné **RowKey** hodnotu, aby prohledávání tabulky a oddílu. Služba table service podporuje omezenou sadu operátory porovnání (větší než, větší než nebo rovná, méně než, menší než nebo rovno, stejné a není rovno) pro použití v where – klauzule. Následující fragment kódu jazyka C# najde všechny zaměstnance, jejichž poslední jméno začíná písmenem "B" (za předpokladu, že **RowKey** ukládá příjmení) v prodejní oddělení (za předpokladu, že **PartitionKey** ukládá název oddělení):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Všimněte si, jak dotaz Určuje, jak **RowKey** a **PartitionKey** zajistit lepší výkon.  

Následující příklad kódu ukazuje ekvivalentní funkce s použitím rozhraní fluent API (Další informace o rozhraní fluent API obecné naleznete v tématu [osvědčené postupy pro navrhování Fluent API](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

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
> Ukázka vnoří více **CombineFilters** metody mají být zahrnuty tři podmínky filtru.  
> 
> 

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Načítání velkého počtu entit z dotazu
Optimální dotaz vrací jednotlivé entity na základě **PartitionKey** hodnotu a **RowKey** hodnotu. V některých případech ale může mít požadavek vrátit entity ze stejného oddílu nebo dokonce z mnoha oddílů.  

V takových situacích by měl vždy plně testování výkonu vaší aplikace.  

Dotaz vůči službě table service může vrátit maximálně 1 000 entit najednou a mohou spouštět maximálně pět sekund. Pokud sada výsledků obsahuje víc než 1 000 entity, pokud dotaz nebyla dokončena do pěti sekund, nebo pokud dotaz překročí hranice oddílu, vrátí služba Table service token pro pokračování umožňuje klientské aplikaci požádat o další sadu entit. Další informace o jak pokračování tokeny práce, naleznete v tématu [časový limit dotazu a stránkování](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Pokud používáte klientskou knihovnu pro úložiště, je pokračování tokeny automaticky zpracovat za vás jako vrátí entity ze služby Table service. Následující vzorový kód C# pomocí klientskou knihovnu pro úložiště automaticky zpracovává pokračování tokeny služby table service je vrátí v odpovědi:  

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

Pomocí explicitně pokračování tokenů, můžete řídit, když aplikace načte další segment data. Například pokud vaše klientská aplikace umožňuje uživatelům stránkovat entity, které jsou uložené v tabulce, uživatel se může rozhodnout přes všechny entity načíst pomocí dotazu tak, že vaše aplikace využije pouze token pro pokračování k načtení další stránky při segmentu uživatel měl dokončil stránkování prostřednictvím všechny entity v aktuálním segmentu. Tento přístup má několik výhod:  

* Je možné omezit objem dat pro načtení ze služby Table service umožňuje a přesunete přes síť.  
* To umožňuje provádět asynchronní vstupně-výstupní operace v rozhraní .NET.  
* Umožňuje vám k serializaci token pro pokračování do trvalého úložiště, takže můžete pokračovat v případě při selhání aplikace.  

> [!NOTE]
> Token pro pokračování obvykle vrátí segment obsahující 1 000 entity, i když může být méně. To platí také v případě, že omezíte počet položek, které dotaz vrátí pomocí **trvat** vrátit prvních n entity, které odpovídají vašim kritériím vyhledávání: služby table service může vrátit segment obsahující méně než n entity spolu s token pro pokračování umožňuje načíst zbývající entity.  
> 
> 

Následující kód jazyka C# ukazuje, jak upravit počtu entit vrácených v segmentu:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projekce na straně serveru
Jedna entita může mít nastavenou vlastnost až 255 a mít velikost až 1 MB. Při dotazování tabulky a načtení entit, nemusí potřebovat všechny vlastnosti a přenosu dat zbytečně (Chcete-li snížit latenci a náklady na) se můžete vyhnout. Projekce na straně serveru můžete použít pro přenos pouze vlastnosti, které potřebujete. Následující příklad načte jenom **e-mailu** vlastnosti (spolu s **PartitionKey**, **RowKey**, **časové razítko**a **ETag**) z entity vybrané v dotazu.  

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

Všimněte si, že jak **RowKey** hodnota není k dispozici, i když nebyla zahrnuta v seznamu vlastností, které mají načíst.  

### <a name="modifying-entities"></a>Úprava entit
Klientská knihovna pro úložiště umožňuje upravit entity ve službě table service ukládaná vkládání, odstraňování a aktualizaci entity. Vám pomůže EGTs dávkové více vložení, aktualizace a operace odstranění společně a snížit počet zpátečních cest vyžaduje a zvýšit výkon vašeho řešení.  

Výjimky vyvolané při klientskou knihovnu pro úložiště provede EGT obvykle zahrnují index entity, která způsobila batch selhání. To je užitečné při ladění kódu, který používá EGTs.  

Měli byste také zvážit, jak váš návrh má vliv na způsob, jakým klientské aplikace zpracovává operace souběžnosti a aktualizace.  

#### <a name="managing-concurrency"></a>Správa souběžnosti
Ve výchozím nastavení, implementuje optimistického řízení souběžnosti kontroly na úrovni jednotlivých entit pro služby table service **vložit**, **sloučit**, a **odstranit** operace, i když ho je možné pro klienta služby table service obejít tyto kontroly vynutí. Další informace o tom, jak služby table service spravují souběžnost najdete v tématu [Správa souběžnosti v Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Sloučení nebo nahradit
**Nahradit** metodu **TableOperation** třídy vždy nahradí kompletní entity ve službě Table service. Pokud neuvedete vlastnost v požadavku, tuto vlastnost existuje v uložených entity, požadavek Odebere tuto vlastnost z uložené entity. Pokud chcete explicitně odebrání vlastnosti z uložené entity, je nutné zahrnout každou vlastnost v požadavku.  

Můžete použít **sloučit** metodu **TableOperation** třídy ke snížení množství dat, která odesíláte do služby Table service, pokud chcete aktualizovat entitu. **Sloučit** metoda nahradí všechny vlastnosti v entitě uložené hodnoty vlastností z entity zahrnutý v požadavku, ale ponechá beze změn, všechny vlastnosti v uložených entity, které nejsou zahrnuté v požadavku. To je užitečné, pokud máte velké entity a stačí aktualizovat malý počet vlastností v požadavku.  

> [!NOTE]
> **Nahradit** a **sloučit** metody selhat, pokud entita neexistuje. Jako alternativu můžete použít **InsertOrReplace** a **InsertOrMerge** metody, které vytvářejí nové entity, pokud neexistuje.  
> 
> 

### <a name="working-with-heterogeneous-entity-types"></a>Práce s typy heterogenní entit
Služba Table service je *bez schématu* tabulky úložiště, to znamená, že jedné tabulky můžete ukládat entity více typů poskytuje skvělou flexibilitu při návrhu. Následující příklad ukazuje tabulku ukládání zaměstnanci a oddělení entity:  

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
<th>Email</th>
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
<th>Email</th>
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
<th>Název oddělení</th>
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
<th>Email</th>
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

Každá entita musí mít stále **PartitionKey**, **RowKey**, a **časové razítko** hodnoty, ale mohou mít libovolnou sadu vlastností. Kromě toho není nutné nic označují typ entity, pokud se nepřihlásíte k ukládání těchto informací někde. Existují dvě možnosti pro určení typu entity:  

* Předřaďte typ entity, který má **RowKey** (nebo případně **PartitionKey**). Například **EMPLOYEE_000123** nebo **DEPARTMENT_SALES** jako **RowKey** hodnoty.  
* Samostatné vlastnost slouží k zaznamenání typ entity, jak je znázorněno v následující tabulce.  

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
<th>Typ entity</th>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>Email</th>
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
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>Email</th>
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
<th>Název oddělení</th>
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
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>Email</th>
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

Typ první možnost předřazení subjektem, který **RowKey**, je užitečné, pokud je možné, že dvě entity, které různých typů může mít stejnou hodnotu klíče. Také skupin entit stejného typu společně v oddílu.  

Techniky popsané v této části jsou obzvláště důležité pro diskuse [vztahy dědičnosti](#inheritance-relationships) dříve v tomto průvodci v části modelovat vztahy.  

> [!NOTE]
> Měli byste zvážit, včetně číslo verze v hodnotě entity typu umožňují klientským aplikacím se vyvíjet objektů POCO a pracovat s různými verzemi aplikací.  
> 
> 

Zbývající část Tato část popisuje některé funkce v klientské knihovně pro úložiště, které usnadňují práci s více typy entit ve stejné tabulce.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Načítání typů heterogenní entity
Pokud používáte klientskou knihovnu pro úložiště, máte tři možnosti pro práci s více typy entit.  

Pokud je, že typ entity uložená s konkrétním **RowKey** a **PartitionKey** hodnoty, pokud načítáte entity, jak je znázorněno v předchozích dvou příkladech můžete zadat typ entity, která načtení entit typu **EmployeeEntity**: [Provádění dotazu bodu pomocí klientskou knihovnu pro úložiště](#executing-a-point-query-using-the-storage-client-library) a [načítání více entit pomocí jazyka LINQ](#retrieving-multiple-entities-using-linq).  

Druhou možností je použít **DynamicTableEntity** typ (kontejner objektů) místo konkrétní typ entity POCO (Tato možnost může také zvýšit výkon, protože není nutné k serializaci a deserializaci entita, která má typy rozhraní .NET). Následující kód jazyka C# potenciálně načte více entit různých typů z tabulky, ale vrací všechny entity jako **DynamicTableEntity** instancí. Poté použije **EntityType** a určí typ jednotlivých entit:  

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

Načíst další vlastnosti, je nutné použít **TryGetValue** metodu **vlastnosti** vlastnost **DynamicTableEntity** třídy.  

Třetí možností je Kombinujte pomocí **DynamicTableEntity** typ a **EntityResolver** instance. To umožňuje řešení pro více typů POCO ve stejném dotazu. V tomto příkladu **EntityResolver** delegát používá **EntityType** vlastnost k rozlišení mezi těmito dvěma typy entit, které dotaz vrátí. **Vyřešit** metoda používá **překladač** delegáta k vyřešení **DynamicTableEntity** instance na **TableEntity** instancí.  

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

#### <a name="modifying-heterogeneous-entity-types"></a>Úprava typy heterogenní entit
Není potřeba znát typ entity ho odstranit a budete vždycky vědět typ entity, při vložení. Můžete však použít **DynamicTableEntity** typ pro aktualizaci entity bez znalosti jeho typ a nemusíte psát třídu entity objektů POCO. Následující vzorový kód načte jednu entitu a zkontroluje, **EmployeeCount** existuje vlastnost před její aktualizací.  

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

### <a name="controlling-access-with-shared-access-signatures"></a>Řízení přístupu se sdílenými přístupovými podpisy
Povolit klientské aplikace upravit (a dotazování) tabulkové entity přímo bez nutnosti přímé ověření u služby table service můžete použít tokeny sdíleného přístupového podpisu (SAS). Obvykle jsou k dispozici tři hlavní výhody použití SAS ve vaší aplikaci:  

* Nepotřebujete distribuovat klíč účtu úložiště pro nezabezpečené platformy (jako jsou mobilní zařízení), aby toto zařízení pro přístup a úpravy entit ve službě Table service.  
* Snižování zátěže určitou část práce, která webové a pracovní role provádět správu vaší entity na klientských zařízeních, jako je například počítačích koncových uživatelů a mobilních zařízení.  
* Můžete přiřadit omezeného a čas omezenou sadu oprávnění pro klienta (například možnost povolit přístup jen pro čtení ke konkrétním prostředkům).  

Další informace o použití tokenů SAS pomocí služby Table service, najdete v části [použití sdílených přístupových podpisů (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Však musí i nadále generovat tokeny SAS, které udělují klientské aplikace do entity ve službě table service: to provést v prostředí, které se má zabezpečit přístup do své klíče účtu úložiště. Obvykle použijete webové nebo pracovní role generovat tokeny SAS a doručujte je na klientských aplikací, které potřebují přístup k vaší entity. Vzhledem k tomu, že se stále vyžaduje režii účastnící se vytváření a doručování tokeny SAS pro klienty, zvažte, jak nejlépe omezit tato režie, zejména v velkoobjemových scénářů.  

Je možné vytvořit token SAS, která uděluje přístup k podmnožině entit v tabulce. Ve výchozím nastavení, můžete vytvořit token SAS pro celou tabulku, ale je také možné zadat, že SAS token udělit přístup k buď celou řadu **PartitionKey** hodnoty nebo celou řadu **PartitionKey** a **RowKey** hodnoty. Můžete zvolit ke generování tokenů SAS pro jednotlivé uživatele systému tak, aby každý uživatel tokenu SAS pouze jim umožňuje přístup k vlastní entity ve službě table service.  

### <a name="asynchronous-and-parallel-operations"></a>Asynchronní a paralelní operace
Pokud jsou rozprostírá vaše požadavky napříč několika oddíly, propustnosti a klienta reakce můžete zlepšit pomocí asynchronní a paralelní dotazy.
Například může mít dvě nebo víc instancí rolí pracovního procesu přístup k vaší tabulky paralelně. Může mít jednotlivé pracovní role za konkrétní sady oddílů nebo jednoduše mít více instancí rolí pracovního procesu, každá mít přístup všechny oddíly v tabulce.  

V rámci instance klienta můžete zlepšit propustnost spuštěním storage operace asynchronně. Klientská knihovna pro úložiště usnadňuje zápis asynchronní dotazy a úpravy. Můžete například začít se synchronní metoda, která načte všechny entity v oddílu, jak je znázorněno v následujícím kódu C#:  

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

Můžete snadno upravit tento kód tak, aby spouští dotaz asynchronně následujícím způsobem:  

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

* Podpis metody zahrnuje nyní **asynchronní** modifikátor a vrátí **úloh** instance.  
* Namísto volání metody **ExecuteSegmented** volá metody k získání výsledků, metoda nyní **ExecuteSegmentedAsync** metody a použije **await** modifikátor načtěte výsledky asynchronně.  

Klientská aplikace může tuto metodu volat více než jednou (s různými hodnotami parametru **oddělení** parametr), a každý dotaz se spustí na samostatném vlákně.  

Neexistuje žádná asynchronní verze **Execute** metoda ve **TableQuery** třídy, protože **IEnumerable** rozhraní nepodporuje asynchronní výčtu.  

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

Můžete snadno upravit tento kód tak, aby aktualizace běží asynchronně následujícím způsobem:  

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

* Podpis metody zahrnuje nyní **asynchronní** modifikátor a vrátí **úloh** instance.  
* Namísto volání metody **Execute** metoda aktualizovat entitu, metoda teď volá **ExecuteAsync** metody a použije **await** modifikátor k načtení výsledků asynchronně.  

Klientská aplikace může volat více asynchronních metod, jako je ten, a každé volání metody se spustí na samostatném vlákně.  

### <a name="credits"></a>Závěrečné titulky
Rádi bychom vám chceme poděkovat následující členy týmu Azure za svoje příspěvky: Dominic Betts, Jason Hogg, Jean Ghanem, Jai Haridas, Jeff Yields, Vamshidhar Kommineni, Vinay Shah Serdar Ozler i Petr Hollander z Microsoft DX. 

Budeme také rádi následující MVPs Microsoftu pro své přínosné podněty během kontroly cykly: Igor Papirov a Edward Bakker.

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

