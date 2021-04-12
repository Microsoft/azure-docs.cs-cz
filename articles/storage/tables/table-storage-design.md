---
title: Navrhněte škálovatelné a výkonné tabulky ve službě Azure Table Storage. | Dokumentace Microsoftu
description: Naučte se navrhovat škálovatelné a výkonné tabulky ve službě Azure Table Storage. Zkontrolujte oddíly tabulky, transakce skupiny entit a požadavky na kapacitu a náklady.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 03/09/2020
ms.subservice: tables
ms.openlocfilehash: 8f3bd2a998066804bfb589e3262ac5e68db601fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93306937"
---
# <a name="design-scalable-and-performant-tables"></a>Návrh škálovatelných a výkonných tabulek

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Chcete-li navrhovat škálovatelné a výkonné tabulky, je třeba vzít v úvahu faktory, jako je výkon, škálovatelnost a náklady. Pokud jste už dříve navrhli schémata pro relační databáze, jsou tato doporučení známá, ale v některých případech se jedná o podobnost modelu úložiště Azure Table service a relačních modelů. Existují také důležité rozdíly. Tyto rozdíly obvykle vedou k různým návrhům, které mohou být pro uživatele obeznámené s relačními databázemi velmi intuitivní nebo špatné, a to i v případě, že navrhujete pro NoSQL úložiště typu klíč/hodnota, jako je například Azure Table service. Mnohé z vašich rozdílů v návrhu odráží skutečnost, že Table service je navržená tak, aby podporovala cloudové aplikace, které mohou obsahovat miliardy entit (nebo řádků v terminologii relačních databází) nebo datových sad, které musí podporovat vysoké objemy transakcí. Proto je potřeba si představit jinak, jak budete ukládat data a pochopit, jak Table service fungují. Dobře navržené úložiště dat NoSQL může umožňovat, aby vaše řešení bylo mnohem dalšího škálovatelné a za nižší náklady než řešení, které používá relační databázi. Tato příručka vám pomůže s těmito tématy.  

## <a name="about-the-azure-table-service"></a>Informace o Table service Azure
Tato část popisuje některé klíčové funkce Table service, které jsou obzvláště důležité pro navrhování výkonu a škálovatelnosti. Pokud Azure Storage a Table service teprve začínáte, nejprve si přečtěte [Úvod do Microsoft Azure Storage](../../storage/common/storage-introduction.md) a začněte [s Azure Table Storage pomocí .NET](../../cosmos-db/tutorial-develop-table-dotnet.md) , než si přečtete zbývající část tohoto článku. I když se tato příručka zaměřuje na Table service, zahrnuje diskuzi o službě Azure Queue a objektů BLOB a o tom, jak je můžete používat s Table service.  

Co je Table service? Jak je možné očekávat od názvu, Table service pro ukládání dat používá tabulkový formát. V rámci standardní terminologie představuje každý řádek tabulky entitu a sloupce ukládají různé vlastnosti této entity. Každá entita má pár klíčů k jednoznačné identifikaci a sloupec časového razítka, které Table service používá ke sledování, kdy byla entita naposledy aktualizována. Časové razítko se aplikuje automaticky a časové razítko nemůžete ručně přepsat libovolnou hodnotou. Table service používá toto časové razítko naposledy upraveno (LMT) ke správě optimistické souběžnosti.  

> [!NOTE]
> Operace Table service REST API také vracejí hodnotu **ETag** , která je odvozena od LMT. Tento dokument používá výrazy ETag a LMT zaměnitelné, protože odkazují na stejná základní data.  
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


Zatím se tato data zobrazují podobně jako tabulka v relační databázi s klíčovými rozdíly, které jsou povinnými sloupci, a možnost ukládat více typů entit do stejné tabulky. Každá z uživatelsky definovaných vlastností, jako je například **FirstName** nebo **věk** , má datový typ, jako je třeba celé číslo nebo řetězec, stejně jako sloupec v relační databázi. I když se na rozdíl od relační databáze nejedná o schéma bez schématu Table service znamená, že vlastnost nemusí mít stejný datový typ pro každou entitu. Pro uložení složitých datových typů do jediné vlastnosti je nutné použít serializovaný formát, jako je JSON nebo XML. Další informace o službě Table Service, jako jsou podporované datové typy, podporované rozsahy dat, pravidla pojmenování a omezení velikosti, najdete v článku [Principy datového modelu služby Table Service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

Vaše volba **PartitionKey** a **RowKey** je zásadní pro dobrý návrh tabulek. Každá entita uložená v tabulce musí mít jedinečnou kombinaci hodnot **PartitionKey** a **RowKey**. Stejně jako u klíčů v tabulce relační databáze jsou hodnoty **PartitionKey** a **RowKey** indexovány pro vytvoření clusterovaného indexu, který umožňuje rychlé hledání. Table service ale nevytvoří žádné sekundární indexy, takže jsou **PartitionKey** a **RowKey** jedinými indexovanými vlastnostmi. Některé ze vzorů popsaných v tématu [vzory návrhu tabulky](table-storage-design-patterns.md) ukazují, jak toto zjevné omezení obejít.  

Tabulka sestává z jednoho nebo více oddílů a mnohé z rozhodnutí o návrhu budou kolem výběru vhodné **PartitionKey** a **RowKey** k optimalizaci vašeho řešení. Řešení se může skládat z jedné tabulky, která obsahuje všechny vaše entity uspořádané do oddílů, ale obvykle má řešení více tabulek. Tabulky vám pomůžou logicky organizovat vaše entity, pomůžou vám spravovat přístup k datům pomocí seznamů řízení přístupu a můžete vyřadit celou tabulku pomocí jediné operace úložiště.  

## <a name="table-partitions"></a>Oddíly tabulky
Název účtu, název tabulky a **PartitionKey** společně identifikují oddíl v rámci služby úložiště, kde služba Table Service ukládá entitu. Stejně jako součást schématu adresování pro entity, oddíly definují obor pro transakce (viz [transakce skupiny entit](#entity-group-transactions) níže) a tvoří základ způsobu, jakým služba tableing Service škáluje. Další informace o oddílech najdete v tématu [Kontrolní seznam pro výkon a škálovatelnost pro úložiště tabulek](storage-performance-checklist.md).  

V Table service jednotlivé uzly Services jeden nebo několik úplných oddílů a služba se škáluje podle dynamicky vyrovnávání zatížení oddílů mezi uzly. Pokud je uzel v zatížení, může služba Table Service *rozdělit* rozsah oddílů, které služba obsluhuje, do různých uzlů. Když se provoz nacházejí, může služba *Sloučit* rozsahy oddílů z tichých uzlů zpátky do jednoho uzlu.  

Další informace o interních podrobnostech Table service a zejména o tom, jak služba spravuje oddíly, najdete v dokumentu [Microsoft Azure Storage: vysoce dostupná služba cloudového úložiště s silnou konzistencí](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency).  

## <a name="entity-group-transactions"></a>Transakce skupiny entit
V Table service jsou transakce skupiny entit (EGTs) jediným integrovaným mechanismem pro provádění atomických aktualizací v několika entitách. EGTs se někdy také označují jako *transakce Batch*. EGTs může pracovat pouze s entitami uloženými ve stejném oddílu (tj. sdílet stejný klíč oddílu v dané tabulce). Takže kdykoli budete potřebovat atomické transakční chování napříč několika entitami, musíte zajistit, aby tyto entity byly ve stejném oddílu. To je často důvod pro udržení více typů entit ve stejné tabulce (a oddílu) a nepoužití více tabulek pro různé typy entit. Jeden EGT může pracovat s maximálně 100 entitami.  Pokud odešlete více souběžných EGTs ke zpracování, je důležité zajistit, aby tyto EGTs nepracovaly s entitami, které jsou společné napříč EGTs. v opačném případě může dojít ke zpoždění zpracování.

EGTs také zavede potenciální kompromis k vyhodnocení v návrhu. To znamená, že použití více oddílů zvyšuje škálovatelnost vaší aplikace, protože Azure nabízí více příležitostí pro žádosti o vyrovnávání zatížení napříč uzly. Ale použití více oddílů může omezit schopnost vaší aplikace provádět atomické transakce a udržovat velkou konzistenci pro vaše data. Kromě toho existují konkrétní cíle škálovatelnosti na úrovni oddílu, která může omezit propustnost transakcí, které můžete očekávat pro jeden uzel. Další informace o cílech škálovatelnosti pro účty Azure Standard Storage najdete v tématu [cíle škálovatelnosti pro účty úložiště úrovně Standard](../common/scalability-targets-standard-account.md). Další informace o cílech škálovatelnosti pro Table service najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště tabulek](scalability-targets.md).

## <a name="capacity-considerations"></a>Důležité informace o kapacitách

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="cost-considerations"></a>Důležité informace o nákladech
Tabulka úložiště je poměrně nákladná, ale měli byste zahrnout odhadované náklady na využití kapacity a množství transakcí v rámci vyhodnocení jakéhokoli Table service řešení. V mnoha scénářích ale ukládání denormalizovaných nebo duplicitních dat, aby se zlepšil výkon nebo škálovatelnost vašeho řešení, je platný přístup. Další informace o cenách najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Další kroky

- [Vzory návrhu tabulek](table-storage-design-patterns.md)
- [Modelování relací](table-storage-design-modeling.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrování dat tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)