---
title: Navrhněte škálovatelné a výkonné tabulky ve službě Azure Table Storage. | Dokumenty Microsoft
description: Navrhněte škálovatelné a výkonné tabulky ve službě Azure Table Storage.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 03/09/2020
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8df639eea757c374554fa19e57c43cef79308e98
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255142"
---
# <a name="design-scalable-and-performant-tables"></a>Návrh škálovatelných a výkonných tabulek

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Chcete-li navrhovat škálovatelné a výkonné tabulky, je třeba vzít v úvahu faktory, jako je výkon, škálovatelnost a náklady. Pokud jste už dříve navrhli schémata pro relační databáze, jsou tato doporučení známá, ale v některých případech se jedná o podobnost modelu úložiště Azure Table service a relačních modelů. Existují také důležité rozdíly. Tyto rozdíly obvykle vedou k různým návrhům, které mohou být pro uživatele obeznámené s relačními databázemi velmi intuitivní nebo špatné, a to i v případě, že navrhujete pro NoSQL úložiště typu klíč/hodnota, jako je například Azure Table service. Mnohé z vašich rozdílů v návrhu odráží skutečnost, že Table service je navržená tak, aby podporovala cloudové aplikace, které mohou obsahovat miliardy entit (nebo řádků v terminologii relačních databází) nebo datových sad, které musí podporovat vysokou transakci. svazků. Proto je potřeba si představit jinak, jak budete ukládat data a pochopit, jak Table service fungují. Dobře navržené úložiště dat NoSQL může umožňovat, aby vaše řešení bylo mnohem dalšího škálovatelné a za nižší náklady než řešení, které používá relační databázi. Tento průvodce vám pomůže s těmito tématy.  

## <a name="about-the-azure-table-service"></a>Informace o službě Azure Table service
Tato část ukazuje některé klíčové funkce služby Table service, které jsou obzvláště důležité pro navrhování pro výkon a škálovatelnost. Pokud Azure Storage a Table service teprve začínáte, nejprve si přečtěte [Úvod do Microsoft Azure Storage](../../storage/common/storage-introduction.md) a začněte [s Azure Table Storage pomocí .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) , než si přečtete zbývající část tohoto článku. I když se tato příručka zaměřuje na Table service, zahrnuje diskuzi o službě Azure Queue a objektů BLOB a o tom, jak je můžete používat s Table service.  

Co je služba Table service? Jak byste asi očekávali od názvu, služby Table service používá k ukládání dat formátu tabulky. Ve standardní terminologie každý řádek v tabulce představuje entitu a sloupce, které ukládat různé vlastnosti dané entity. Každá entita má pár klíčů k jednoznačné identifikaci a sloupec časového razítka, které Table service používá ke sledování, kdy byla entita naposledy aktualizována. Časové razítko se aplikuje automaticky a časové razítko nemůžete ručně přepsat libovolnou hodnotou. Služba Table service používá tento last-modified časové razítko (LMT) ke správě optimistického řízení souběžnosti.  

> [!NOTE]
> Operace Table service REST API také vracejí hodnotu **ETag** , která je odvozena od LMT. Tento dokument používá výrazy ETag a LMT zaměnitelné, protože odkazují na stejná základní data.  
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
<th>Stáří</th>
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
<th>FirstName</th>
<th>LastName</th>
<th>Stáří</th>
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
<th>Stáří</th>
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


Zatím se tato data zobrazují podobně jako tabulka v relační databázi s klíčovými rozdíly, které jsou povinnými sloupci, a možnost ukládat více typů entit do stejné tabulky. Každá z uživatelsky definovaných vlastností, jako je například **FirstName** nebo **věk** , má datový typ, jako je třeba celé číslo nebo řetězec, stejně jako sloupec v relační databázi. I když na rozdíl od v relační databázi, bez schématu povaha služby Table service znamená, že vlastnost nemusí mít stejný datový typ. u každé entity. Pokud chcete uložit komplexních datových typů v jedné vlastnosti, musíte použít serializovaný formát jako je JSON nebo XML. Další informace o službě Table Service, jako jsou podporované datové typy, podporované rozsahy dat, pravidla pojmenování a omezení velikosti, najdete v článku [Principy datového modelu služby Table Service](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Vaše volba **PartitionKey** a **RowKey** je zásadní pro dobrý návrh tabulek. Každá entita uložená v tabulce musí mít jedinečnou kombinaci hodnot **PartitionKey** a **RowKey**. Stejně jako u klíčů v tabulce relační databáze jsou hodnoty **PartitionKey** a **RowKey** indexovány pro vytvoření clusterovaného indexu, který umožňuje rychlé hledání. Table service ale nevytvoří žádné sekundární indexy, takže jsou **PartitionKey** a **RowKey** jedinými indexovanými vlastnostmi. Některé ze vzorů popsaných v tématu [vzory návrhu tabulky](table-storage-design-patterns.md) ukazují, jak toto zjevné omezení obejít.  

Tabulka sestává z jednoho nebo více oddílů a mnohé z rozhodnutí o návrhu budou kolem výběru vhodné **PartitionKey** a **RowKey** k optimalizaci vašeho řešení. Řešení se může skládat z jedné tabulky, která obsahuje všechny vaše entity uspořádané do oddílů, ale obvykle má řešení více tabulek. Tabulky umožňují logicky uspořádat entity, vám pomůžou spravovat přístup k datům pomocí seznamů řízení přístupu a můžete vložit pomocí operace s jediným úložištěm celou tabulku.  

## <a name="table-partitions"></a>Oddíly tabulky
Název účtu, název tabulky a **PartitionKey** společně identifikují oddíl v rámci služby úložiště, kde služba Table Service ukládá entitu. Stejně jako součást schématu adresování pro entity, oddíly definují obor pro transakce (viz [transakce skupiny entit](#entity-group-transactions) níže) a tvoří základ způsobu, jakým služba tableing Service škáluje. Další informace o oddílech najdete v tématu [Kontrolní seznam pro výkon a škálovatelnost pro úložiště tabulek](storage-performance-checklist.md).  

V Table service jednotlivé uzly Services jeden nebo několik úplných oddílů a služba se škáluje podle dynamicky vyrovnávání zatížení oddílů mezi uzly. Pokud je uzel v zatížení, může služba Table Service *rozdělit* rozsah oddílů, které služba obsluhuje, do různých uzlů. Když se provoz nacházejí, může služba *Sloučit* rozsahy oddílů z tichých uzlů zpátky do jednoho uzlu.  

Další informace o interních podrobnostech Table service a zejména o tom, jak služba spravuje oddíly, najdete v dokumentu [Microsoft Azure Storage: vysoce dostupná služba cloudového úložiště s silnou konzistencí](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Transakcí skupin entit
Ve službě Table service transakcí skupin entit (EGTs) jsou pouze předdefinovaný mechanismus pro provádění atomické aktualizace napříč více entit. EGTs se někdy také označují jako *transakce Batch*. EGTs může pracovat pouze s entitami uloženými ve stejném oddílu (tj. sdílet stejný klíč oddílu v dané tabulce). Takže kdykoli budete potřebovat atomické transakční chování napříč několika entitami, musíte zajistit, aby tyto entity byly ve stejném oddílu. To je často důvod pro udržování několik typů entit ve stejné tabulce (a oddílu) a bez použití více tabulek pro typy jiné entity. Jeden EGT může pracovat na maximálně 100 entit.  Pokud odešlete více souběžných EGTs ke zpracování, je důležité zajistit, aby tyto EGTs nepracovaly s entitami, které jsou společné napříč EGTs. v opačném případě může dojít ke zpoždění zpracování.

EGTs také zavede potenciální kompromis k vyhodnocení v návrhu. To znamená, že použití více oddílů zvyšuje škálovatelnost vaší aplikace, protože Azure nabízí více příležitostí pro žádosti o vyrovnávání zatížení napříč uzly. Ale použití více oddílů může omezit schopnost vaší aplikace provádět atomické transakce a udržovat velkou konzistenci pro vaše data. Kromě toho existují konkrétní cíle škálovatelnosti na úrovni oddílu, která může omezit propustnost transakcí, které můžete očekávat pro jeden uzel. Další informace o cílech škálovatelnosti pro účty Azure Standard Storage najdete v tématu [cíle škálovatelnosti pro účty úložiště úrovně Standard](../common/scalability-targets-standard-account.md). Další informace o cílech škálovatelnosti pro Table service najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště tabulek](scalability-targets.md).

## <a name="capacity-considerations"></a>Důležité informace o kapacity

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="cost-considerations"></a>Důležité informace o nákladech
Tabulka úložiště je poměrně nákladná, ale měli byste zahrnout odhadované náklady na využití kapacity a množství transakcí v rámci vyhodnocení jakéhokoli Table service řešení. V mnoha scénářích ale ukládání denormalizovaných nebo duplicitních dat, aby se zlepšil výkon nebo škálovatelnost vašeho řešení, je platný přístup. Další informace o cenách najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Další kroky

- [Vzory návrhu tabulek](table-storage-design-patterns.md)
- [Vztahy modelování](table-storage-design-modeling.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrování dat tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)
