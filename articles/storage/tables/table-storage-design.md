---
title: Návrh škálovatelných a výkonných tabulek ve službě Azure table storage. | Dokumenty Microsoft
description: Návrh škálovatelných a výkonných tabulek ve službě Azure table storage.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8387e41d57edfa0e54ac930c9462714aca571f2a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472554"
---
# <a name="design-scalable-and-performant-tables"></a>Návrh škálovatelných a výkonných tabulek

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Návrh škálovatelných a výkonných tabulek musíte zvážit faktory, jako je výkon, škálovatelnost a náklady. Pokud jste vytvořili dříve schémata pro relační databáze, tyto aspekty jsou známé, ale i když existují některé podobné rysy mezi model úložiště Azure Table service a relačních modelů, jsou zde i důležité rozdíly. Tyto rozdíly obvykle dojít k různých návrzích, které může vypadat counter-intuitive nebo nesprávné někomu zkušenosti s relačními databázemi, ale dávat smysl, pokud navrhujete pro NoSQL s dvojicí klíč hodnota jako je například služba Azure Table service. Mnoho rozdílů návrh s ohledem na skutečnost, že služba Table service je navržen pro podporu aplikace v cloudovém měřítku, které mohou obsahovat miliardy entity (nebo řádky v, řečeno terminologií relační databáze) dat nebo datových sad, které musí podporovat vysokou transakční svazky. Proto musíte jinak přemýšlet o tom, jak ukládat data a pochopit, jak funguje služba Table service. Dobře navržené úložiště dat typu NoSQL můžete povolit řešení škálování ještě mnohem víc a s nižšími náklady než řešení, které používá relační databáze. Tento průvodce vám pomůže s těmito tématy.  

## <a name="about-the-azure-table-service"></a>Informace o službě Azure Table service
Tato část ukazuje některé klíčové funkce služby Table service, které jsou obzvláště důležité pro navrhování pro výkon a škálovatelnost. Pokud začínáte do Azure Storage a služby Table service, nejdřív přečíst [Úvod do Microsoft Azure Storage](../../storage/common/storage-introduction.md) a [Začínáme s Azure Table Storage pomocí .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) před čtením zbývající část tohoto článku . Přestože sada Tato příručka je zaměřena služby Table Service, obsahuje informace o službě fronty Azure a objektů Blob a jak je můžou využívat pomocí služby Table service.  

Co je služba Table service? Jak byste asi očekávali od názvu, služby Table service používá k ukládání dat formátu tabulky. Ve standardní terminologie každý řádek v tabulce představuje entitu a sloupce, které ukládat různé vlastnosti dané entity. Každá entita obsahuje pár klíčů k jednoznačné identifikaci a sloupec časového razítka, která používá služby Table service můžete sledovat, kdy došlo k poslední aktualizaci entity. Časové razítko se použijí automaticky a je nelze přepsat ručně časové razítko je libovolná hodnota. Služba Table service používá tento last-modified časové razítko (LMT) ke správě optimistického řízení souběžnosti.  

> [!NOTE]
> Operace rozhraní REST API služby tabulky také vrátit **ETag** hodnotu, která se odvozuje od LMT. Tento dokument používá podmínky ETag a LMT Zaměnitelně, protože odkazují na stejná podkladová data.  
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


Tato data zobrazuje zatím, podobně jako tabulka v relační databázi s hlavní rozdíly jsou povinné sloupce a umožnit ukládání více typů entit ve stejné tabulce. Všechny uživatelem definované vlastnosti, jako **FirstName** nebo **stáří** má datový typ, jako je například integer či string, stejně jako sloupec v relační databázi. I když na rozdíl od v relační databázi, bez schématu povaha služby Table service znamená, že vlastnost nemusí mít stejný datový typ. u každé entity. Pokud chcete uložit komplexních datových typů v jedné vlastnosti, musíte použít serializovaný formát jako je JSON nebo XML. Další informace o tabulce služby, například podporované datové typy, podporovaných rozsahů, pravidla pojmenování a omezením způsobeným velikostí najdete v tématu [Principy datového modelu služby Table Service](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Podle vaší volby **PartitionKey** a **RowKey** je zásadní pro návrh dobrý tabulky. Každá entita uložena v tabulce musí mít jedinečnou kombinaci **PartitionKey** a **RowKey**. Stejně jako u klíče v tabulce relační databáze, **PartitionKey** a **RowKey** hodnoty jsou indexovány pro vytvoření clusterovaného indexu umožňující rychlé look-ups. Ale služby Table service nevytvoří žádné sekundární indexy, takže **PartitionKey** a **RowKey** jsou pouze indexované vlastnosti. Některé tyto vzory se dají podle [vzory návrhu v tabulce](table-storage-design-patterns.md) ukazují, jak obejít tato omezení zřejmý.  

Tabulku tvoří jeden nebo více oddílů a řadu rozhodnutí o návrhu provedete budeme zvolíte vhodnou **PartitionKey** a **RowKey** k optimalizaci vašeho řešení. Řešení se může skládat z jedné tabulky, která obsahuje všechny vaše entity, které jsou uspořádány do oddílů, ale obvykle řešení obsahuje více tabulek. Tabulky umožňují logicky uspořádat entity, vám pomůžou spravovat přístup k datům pomocí seznamů řízení přístupu a můžete vložit pomocí operace s jediným úložištěm celou tabulku.  

## <a name="table-partitions"></a>Oddíly tabulky
Název účtu, název tabulky a **PartitionKey** společně identifikovat oddílu v rámci služby úložiště, kde služby table service obsahuje entitu. A také se zapojil schéma adresování pro entity, oddíly definovat rozsah transakce (viz [transakcí skupin entit](#entity-group-transactions) níže), a tvoří základ jak škálování služby table service. Další informace o oddílech najdete v tématu [Azure Storage škálovatelnost a cíle výkonnosti](../../storage/common/storage-scalability-targets.md).  

Ve službě Table service, služby jednotlivých uzlů jeden nebo více dokončení oddíly a škálování služby pomocí dynamické vyrovnávání zatížení oddílů mezi uzly. Pokud uzel je zatížení, můžete služby table service *rozdělit* rozsahem oddílů obsluhovány pomocí tohoto uzlu na různých uzlech; při provozu poklesne, můžete službu *sloučení* rozsahů oddílů z quiet uzlů zpět na jeden uzel.  

Další informace o interní informace služby Table service, zejména způsob, jakým služba spravuje oddíly, najdete v dokumentu paper [Microsoft Azure Storage: Služby s vysokou dostupností cloudového úložiště se silnou konzistenci](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Transakcí skupin entit
Ve službě Table service transakcí skupin entit (EGTs) jsou pouze předdefinovaný mechanismus pro provádění atomické aktualizace napříč více entit. EGTs jsou někdy označovány jako *dávkové transakce*. EGTs může pracovat pouze s entitami, které jsou uložené ve stejném oddílu (to znamená, sdílet stejný klíč oddílu z dané tabulky). Kdykoli budete potřebovat atomic transakční chování napříč více entit, ujistěte se, že jsou tyto entity do stejného oddílu. To je často důvod pro udržování několik typů entit ve stejné tabulce (a oddílu) a bez použití více tabulek pro typy jiné entity. Jeden EGT může pracovat na maximálně 100 entit.  Pokud uvedete více souběžných EGTs pro zpracování, je důležité zajistit, že tyto EGTs není pracovat u entit, které jsou společné pro EGTs; v opačném případě se může zpozdit zpracování.

EGTs přinášejí s sebou potenciální kompromis pro vyhodnocení vašeho návrhu. To znamená pomocí více oddílů zlepšuje škálovatelnost aplikace, protože Azure nabízí další možnosti pro vyrovnávání zatížení požadavků napříč uzly. Ale pomocí více oddílů může omezit schopnost aplikace provádět atomické transakce a udržovat silnou konzistenci pro vaše data. Na úrovni oddíl, který může být omezen propustnost transakcí, které můžete očekávat, že pro jednotlivé uzly jsou navíc cíle škálovatelnosti konkrétní. Další informace o cíle škálovatelnosti pro účty úložiště Azure a služby table service, najdete v části [Azure Storage škálovatelnost a cíle výkonnosti](../../storage/common/storage-scalability-targets.md).   

## <a name="capacity-considerations"></a>Důležité informace o kapacity
Následující tabulka popisuje některé klíčové hodnoty, které mají mít na paměti při navrhování řešení pro službu tabulky:  

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

## <a name="cost-considerations"></a>Důležité informace o nákladech
Table storage je relativně levný, ale jako součást vaší zkušební verze služby řešení tabulky by měl obsahovat odhady nákladů na využití kapacity a množství transakcí. Ale v mnoha případech ukládání Nenormalizovaná nebo duplicitních dat za účelem zlepšení výkonu nebo škálovatelnost řešení je platný přístup. Další informace o cenách najdete v tématu [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Další postup

- [Vzory návrhu tabulky](table-storage-design-patterns.md)
- [Modelování vztahů](table-storage-design-modeling.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrování dat tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)
