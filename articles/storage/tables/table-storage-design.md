---
title: Navrhujte škálovatelné a výkonné tabulky v úložišti tabulek Azure. | Dokumentace Microsoftu
description: Navrhujte škálovatelné a výkonné tabulky v úložišti tabulek Azure.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 03/09/2020
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8df639eea757c374554fa19e57c43cef79308e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255142"
---
# <a name="design-scalable-and-performant-tables"></a>Návrh škálovatelných a výkonných tabulek

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Chcete-li navrhnout škálovatelné a výkonné tabulky, je třeba zvážit faktory, jako je výkon, škálovatelnost a náklady. Pokud jste dříve navrhli schémata pro relační databáze, tyto aspekty jsou známé, ale i když existují některé podobnosti mezi modelem úložiště služby Azure Table a relačními modely, existují také důležité rozdíly. Tyto rozdíly obvykle vedou k různým návrhům, které mohou vypadat neintuitivně nebo nesprávně pro někoho, kdo je obeznámen s relačními databázemi, ale mají smysl, pokud navrhujete úložiště klíčů a hodnot NoSQL, jako je služba Azure Table. Mnohé z vašich rozdílů v návrhu odrážejí skutečnost, že služba Table je navržena tak, aby podporovala cloudové aplikace, které mohou obsahovat miliardy entit (nebo řádků v terminologii relační databáze) dat nebo pro datové sady, které musí podporovat vysoké transakce Svazky. Proto je třeba přemýšlet jinak o tom, jak ukládat data a pochopit, jak funguje služba Table. Dobře navržené úložiště dat NoSQL může umožnit vašemu řešení škálovat mnohem dále a za nižší cenu než řešení, které používá relační databázi. Tato příručka vám pomůže s těmito tématy.  

## <a name="about-the-azure-table-service"></a>O službě Azure Table
Tato část upozorňuje na některé klíčové funkce služby Table, které jsou obzvláště důležité pro navrhování výkonu a škálovatelnosti. Pokud jste s Azure Storage a table service teprve tak, přečtěte si před přečtením zbývající části tohoto článku [úvod do úložiště Microsoft Azure](../../storage/common/storage-introduction.md) a [začínáme s Azure Table Storage pomocí rozhraní .NET.](../../cosmos-db/table-storage-how-to-use-dotnet.md) Přestože se tato příručka zaměřuje na službu Table, zahrnuje diskusi o službách Azure Queue a Blob a o tom, jak je můžete použít se službou Table.  

Co je služba Table service? Jak můžete očekávat od názvu, služba Table používá k ukládání dat tabulkový formát. Ve standardní terminologii představuje každý řádek tabulky entitu a sloupce ukládají různé vlastnosti této entity. Každá entita má dvojici klíčů k jeho jednoznačné identifikaci a sloupec časového razítka, který služba Table používá ke sledování, kdy byla entita naposledy aktualizována. Časové razítko je použito automaticky a nelze ručně přepsat časové razítko libovolnou hodnotou. Služba Table používá toto poslední upravené časové razítko (LMT) ke správě optimistické souběžnosti.  

> [!NOTE]
> Operace rozhraní REST API služby Table service také vrátí hodnotu **ETag,** která je odvozena z LMT. Tento dokument používá termíny ETag a LMT zaměnitelně, protože odkazují na stejná podkladová data.  
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


Tato data se zatím jeví podobně jako tabulka v relační databázi, přičemž klíčovými rozdíly jsou povinné sloupce a možnost uložit více typů entit do stejné tabulky. Také každá z uživatelem definovaných vlastností, jako je **Například FirstName** nebo **Age** má datový typ, jako je například celé číslo nebo řetězec, stejně jako sloupec v relační databázi. I když na rozdíl od v relační databázi, bez schématu povahy Table service znamená, že vlastnost nemusí mít stejný datový typ na každou entitu. Chcete-li uložit složité datové typy do jedné vlastnosti, musíte použít serializovaný formát, například JSON nebo XML. Další informace o tabulkové službě, jako jsou podporované datové typy, podporovaná období, pravidla pojmenování a omezení velikosti, naleznete [v tématu Principy datového modelu služby Table Service](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Váš výběr **PartitionKey** a **RowKey** je zásadní pro dobrý návrh tabulky. Každá entita uložená v tabulce musí mít jedinečnou kombinaci **PartitionKey** a **RowKey**. Stejně jako u klíčů v tabulce relační databáze jsou indexovány hodnoty **PartitionKey** a **RowKey,** aby se vytvořil clusterovaný index, který umožní rychlé vyhledávání. Služba Table však nevytváří žádné sekundární indexy, takže **PartitionKey** a **RowKey** jsou pouze indexované vlastnosti. Některé vzory popsané v [návrhových vzorcích tabulky](table-storage-design-patterns.md) ilustrují, jak můžete toto zjevné omezení obejít.  

Tabulka obsahuje jeden nebo více oddílů a mnoho rozhodnutí o návrhu, které provedete, bude kolem výběru vhodného **partitionkey** a **RowKey** pro optimalizaci vašeho řešení. Řešení se může skládat z jedné tabulky, která obsahuje všechny entity uspořádané do oddílů, ale obvykle řešení má více tabulek. Tabulky vám pomohou logicky uspořádat entity, můžete spravovat přístup k datům pomocí seznamů řízení přístupu a můžete přetáhnout celou tabulku pomocí jediné operace úložiště.  

## <a name="table-partitions"></a>Oddíly tabulky
Název účtu, název tabulky a **PartitionKey** společně identifikují oddíl v rámci služby úložiště, kde služba table service ukládá entitu. Kromě toho, že jsou součástí adresovacího schématu pro entity, oddíly definují obor pro transakce (viz [Transakce skupiny entit](#entity-group-transactions) níže) a tvoří základ toho, jak se služba table service škáluje. Další informace o oddílech naleznete v [tématu Kontrola výkonu a škálovatelnosti pro úložiště tabulek](storage-performance-checklist.md).  

Ve službě Table služby jednotlivý uzel služby jeden nebo více úplných oddílů a služby škálování dynamicky vyrovnávání zatížení oddíly mezi uzly. Pokud je uzel pod zatížením, služba tabulky můžete *rozdělit* rozsah oddílů obsluhovaných tímto uzlem na různé uzly; když provoz odezní, služba může *sloučit* oddílrozsahy z tichých uzlů zpět na jeden uzel.  

Další informace o interních podrobnostech služby Table service a zejména o tom, jak služba spravuje oddíly, najdete v článku na papírovém [webu Microsoft Azure Storage: A Highly Available Cloud Storage Service se silnou konzistencí](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Transakce skupiny entit
Ve službě Tabulka transakce skupiny entit (EGTs) jsou pouze vestavěný mechanismus pro provádění atomických aktualizací napříč více entitami. EgT jsou někdy také *označovány*jako dávkové transakce . EGTs může pracovat pouze na entity uložené ve stejném oddílu (to znamená sdílet stejný klíč oddílu v dané tabulce). Takže kdykoli budete potřebovat atomické transakční chování napříč více entitami, musíte zajistit, že tyto entity jsou ve stejném oddílu. To je často důvodpro zachování více typů entit ve stejné tabulce (a oddíl) a nepoužíváte více tabulek pro různé typy entit. Jediné EGT může pracovat maximálně se 100 entitami.  Pokud odešlete více souběžných EGT ke zpracování, je důležité zajistit, aby tyto EGT nepracovaly na entitách, které jsou společné napříč EGT; v opačném případě může být zpracování zpožděno.

EGT s y také zavádějí potenciální kompromis, který můžete hodnotit ve svém návrhu. To znamená, že pomocí více oddílů zvyšuje škálovatelnost vaší aplikace, protože Azure má více příležitostí pro požadavky vyrovnávání zatížení napříč uzly. Ale pomocí více oddílů může omezit schopnost aplikace provádět atomické transakce a udržovat silnou konzistenci pro vaše data. Kromě toho existují konkrétní cíle škálovatelnosti na úrovni oddílu, který může omezit propustnost transakcí, které můžete očekávat pro jeden uzel. Další informace o cílech škálovatelnosti pro účty standardního úložiště Azure najdete v tématu [Škálovatelnost cíle pro účty standardního úložiště](../common/scalability-targets-standard-account.md). Další informace o cílech škálovatelnosti pro službu Table service naleznete v tématu [Škálovatelnost a výkonnostní cíle pro úložiště tabulek](scalability-targets.md).

## <a name="capacity-considerations"></a>Důležité informace o kapacitách

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="cost-considerations"></a>Důležité informace o nákladech
Úložiště tabulek je relativně levné, ale měli byste zahrnout odhady nákladů pro využití kapacity a množství transakcí jako součást hodnocení jakéhokoli řešení služby Table Service. V mnoha scénářích je však ukládání nenormalizovaných nebo duplicitních dat za účelem zlepšení výkonu nebo škálovatelnosti vašeho řešení platný přístup. Další informace o cenách najdete v [tématu Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Další kroky

- [Návrhové vzory tabulky](table-storage-design-patterns.md)
- [Modelování relací](table-storage-design-modeling.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrování dat tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)
