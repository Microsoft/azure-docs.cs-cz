---
title: Návrh škálovatelné a původce tabulky ve službě Azure table storage. | Dokumenty Microsoft
description: Návrh škálovatelné a původce tabulky ve službě Azure table storage.
services: storage
documentationcenter: na
author: SnehaGunda
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: ba48283371ac099b162aeb3cbffd6127ccce1676
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660755"
---
# <a name="design-scalable-and-performant-tables"></a>Návrh škálovatelných a výkonných tabulek

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

K návrhu škálovatelné a původce tabulky je nutné zvážit faktory třeba výkon, škálovatelnost a náklady. Pokud dříve navrženy schémata pro relační databáze, jsou tyto aspekty obeznámeni, ale existuje některé podobnosti mezi modelu úložiště služby Azure Table a relační modely, jsou také důležité rozdíly. Tyto rozdíly jsou obvykle vést k různých návrzích, které může vypadat counter-intuitive nebo nesprávný někomu obeznámeni s relačních databází, ale smysl, pokud navrhujete pro úložiště dvojic klíč/hodnota NoSQL například službu Azure Table. Řadu rozdíly váš návrh s ohledem na skutečnost, že služby Table je navržen pro podporu škálování cloudové aplikace, které může obsahovat až miliardy entity (nebo řádků v terminologii relační databáze) data nebo datové sady, které musí podporovat vysoké transakce svazky. Musíte tedy jinak myslíte o tom, jak ukládat data a pochopit, jak funguje služba Table. Dobře navrženou úložiště dat typu NoSQL, můžete povolit řešení škálování mnohem dál a to při nižších nákladech, než řešení, které používá relační databáze. Tento průvodce vám pomůže s Tato témata.  

## <a name="about-the-azure-table-service"></a>O službě Azure Table
V této části jsou zdůrazněné některé klíčové funkce služby Table, které jsou obzvláště důležité pro návrh pro výkon a škálovatelnost. Pokud s Azure Storage a služby Table, nejdřív přečíst [Úvod do Microsoft Azure Storage](../../storage/common/storage-introduction.md) a [Začínáme s Azure Table Storage pomocí rozhraní .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) než si přečtete zbývající část tohoto článku . I když je aktivní tohoto průvodce služby Table, zahrnuje diskuzi o služby Azure Queue a objektů Blob a jak je může používat s služby Table.  

Co je služba Table? Jak byste očekávali od názvu služby Table k ukládání dat používá tabulkovém formátu. V standardní terminologie každý řádek v tabulce představuje entitu a sloupce ukládat různé vlastnosti dané entity. Každý entita má pár klíčů k jednoznačné identifikaci a sloupec časového razítka, který používá služby Table můžete sledovat, kdy poslední aktualizace entity. Časové razítko je použita automaticky a nelze ručně přepsat časové razítko s libovolnou hodnotou. Služba Table používá ke správě optimistickou metodu souběžného tento poslední úpravy časové razítko (LMT).  

> [!NOTE]
> Operace REST API služby Table se taky vrátit **značka ETag** hodnotu, která je odvozena z LMT. Tento dokument použije podmínky ETag a LMT zcela zaměnitelným významem, protože odkazují na stejnou základní data.  
> 
> 

Následující příklad ukazuje návrh jednoduché tabulky k ukládání entit zaměstnanců a oddělení. Řadu příklady uvedené v této příručce jsou založené na tento návrh jednoduché.  

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
<th>Příjmení</th>
<th>Věk</th>
<th>Email</th>
</tr>
<tr>
<td>Na ochranu</td>
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
<th>Příjmení</th>
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
<th>Příjmení</th>
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


Pokud tato data se zobrazí podobná tabulku v relační databázi s hlavní rozdíly jsou povinné sloupců a možnost ukládat více typů entit ve stejné tabulce. Navíc ke každé z vlastností uživatelem definované jako **FirstName** nebo **stáří** má datový typ jako celé číslo nebo řetězec, jenom jako sloupec v relační databázi. I když na rozdíl od v relační databázi, bez schématu povaha služby Table znamená, že vlastnost nemusí mít stejný datový typ. u každé entity. Pokud chcete komplexními datovými typy do vlastnosti jediné, musíte použít serializovaných formátu například XML nebo JSON. Další informace o tabulce služby, například podporované datové typy, podporované rozsahů, pravidla pojmenování a omezení velikosti najdete v tématu [Principy datového modelu služby Table](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Vaši volbu **PartitionKey** a **RowKey** je nezbytné, aby návrh dobrý tabulky. Každou entitu uložené v tabulce musí mít jedinečnou kombinaci **PartitionKey** a **RowKey**. Stejně jako u klíče v tabulce relační databáze, **PartitionKey** a **RowKey** vytvořit clusterovaný index umožňující rychlé look-ups jsou indexované hodnoty. Ale služby Table nevytvoří žádné sekundární indexy, takže **PartitionKey** a **RowKey** jsou pouze indexované vlastnosti. Některé vzory popsané v [vzory návrhu tabulky](table-storage-design-patterns.md) ilustrují, jak můžete obejít toto zřejmá omezení.  

Tabulka se skládá z jednoho nebo více oddílů a řadu rozhodnutí o návrhu provedete bude kolem výběr vhodný **PartitionKey** a **RowKey** k optimalizaci vašeho řešení. Řešení může skládat z jedné tabulky, který obsahuje všechny vaše entity, které jsou uspořádány do oddílů, ale obvykle řešení má několik tabulek. Tabulky umožňují logicky uspořádat vaší entity, které vám pomůžou spravovat přístup k datům pomocí seznamů řízení přístupu a mohou vyřadit celé tabulky pomocí operace jedno úložiště.  

## <a name="table-partitions"></a>Oddíly tabulky
Název účtu, název tabulky a **PartitionKey** společně identifikovat oddíl v rámci služby úložiště, kde služby table ukládá entity. A také se součástí schéma adresování pro entity, oddíly definování oboru pro transakce (najdete v části [Entity skupiny transakce](#entity-group-transactions) níže), a vytvoří základ pro jak škáluje služby table. Další informace o oddílech najdete v tématu [a cíle výkonnosti služby Azure Storage Scalability](../../storage/common/storage-scalability-targets.md).  

Ve službě Table jednotlivých uzel služeb jeden nebo více dokončete oddílů a škálují služby tak, že dynamicky Vyrovnávání zatížení oddíly mezi uzly. Pokud uzel je zatížení, můžete služby table *rozdělení* rozsahu oddílů obsluhovány pomocí daného uzlu do jiných uzlů; při provozu subvence, můžete službu *sloučení* back rozsahy oddílu z quiet uzlů do jednoho uzlu.  

Další informace o interní podrobnosti služby Table, zejména jak službu spravuje oddíly, najdete v dokumentu paper [Microsoft Azure Storage: A vysoce dostupné cloudového úložiště se silnou konzistencí](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Transakce skupiny entity
Ve službě Table Entity skupiny transakce (EGTs) jsou pouze integrované mechanismus pro provádění atomic aktualizace napříč více entit. EGTs se někdy také označují jako *dávky transakce*. EGTs mohou pracovat pouze u entit, které jsou uložené ve stejném oddílu (tedy sdílet stejný klíč oddílu v dané tabulce). Proto můžete kdykoli budete potřebovat atomic transakční chování napříč více entit, je nutné zajistit, že tyto entity jsou ve stejném oddílu. Toto je často důvod pro zachování více typů entit ve stejné tabulce (a oddíl) a nepoužíváte více tabulek pro typy různých entit. Jeden EGT mohou pracovat na maximálně 100 entit.  Pokud odešlete více souběžných EGTs pro zpracování, je důležité zajistit, že tyto EGTs se nevztahují na entity, které jsou společné pro EGTs; jinak můžete zpozdit zpracování.

EGTs také zavádět potenciální kompromis pro vyhodnocení při návrhu. To znamená pomocí více oddíly zlepšuje škálovatelnost vaší aplikace, protože Azure má většího počtu možností pro vyrovnávání zatížení požadavky napříč uzly. Můžete ale pomocí více oddíly může omezit schopnost aplikace provést jednotlivé transakce a udržovat silnou konzistenci pro vaše data. Na úrovni oddílu, který může omezit propustnost transakcí, které můžete očekávat v jednom uzlu kromě toho existují konkrétní škálovatelnost cíle. Další informace o cíle škálovatelnosti pro účty úložiště Azure a služby table najdete v tématu [a cíle výkonnosti služby Azure Storage Scalability](../../storage/common/storage-scalability-targets.md).   

## <a name="capacity-considerations"></a>Aspekty kapacity
Následující tabulka popisuje některé z hodnot klíčů znát při navrhování řešení pro službu tabulky:  

| Celkové kapacity účtu úložiště Azure | 500 TB |
| --- | --- |
| Počet tabulek v účtu úložiště Azure |Omezeno pouze kapacity účtu úložiště |
| Počet oddílů v tabulce |Omezeno pouze kapacity účtu úložiště |
| Počet entit v oddílu |Omezeno pouze kapacity účtu úložiště |
| Velikost jednotlivých entit |Až 1 MB delší než 255 vlastnosti (včetně **PartitionKey**, **RowKey**, a **časové razítko**) |
| Velikost **PartitionKey** |A řetězec velikost až 1 KB |
| Velikost **RowKey** |A řetězec velikost až 1 KB |
| Velikost skupiny Entity transakce |Transakce může obsahovat maximálně 100 entit a datové části musí být menší než 4 MB. EGT lze aktualizovat pouze entity jednou. |

Další informace najdete v tématu [Principy datového modelu služby Table](http://msdn.microsoft.com/library/azure/dd179338.aspx).  

## <a name="cost-considerations"></a>Aspekty náklady
Table storage je relativně levný, ale by měla obsahovat odhadované náklady pro využití kapacity a o množství transakcí v rámci zkušební verze řešení služby Table. V mnoha scénářích ukládání nenormalizované nebo duplicitní data za účelem zlepšení výkonu nebo škálovatelnost řešení je ale platný přístup. Další informace o cenách najdete v tématu [Azure Storage – ceny](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Další postup

- [Vzory návrhu tabulky](table-storage-design-patterns.md)
- [Modelování vztahů](table-storage-design-modeling.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrování dat v tabulce](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)