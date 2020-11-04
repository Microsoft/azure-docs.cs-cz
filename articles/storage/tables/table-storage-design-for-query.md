---
title: Návrh Azure Table Storage pro dotazy | Microsoft Docs
description: Navrhněte tabulky pro dotazy v úložišti tabulek Azure. Vyberte vhodný klíč oddílu, optimalizujte dotazy a seřaďte data pro Table service.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 43ae21d97bc9d8292270ae62006e649f4bcf540b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316148"
---
# <a name="design-for-querying"></a>Návrh pro dotazování
Table service řešení mohou být náročná na čtení, náročné na zápis nebo kombinace těchto dvou. Tento článek se zaměřuje na postupy, které je potřeba mít na paměti při navrhování Table service, aby bylo možné efektivně podporovat operace čtení. Obecně platí, že návrh, který podporuje operace čtení efektivně, je také účinný pro operace zápisu. Existují však i další okolnosti, které byste měli mít na paměti při navrhování pro podporu operací zápisu popsaných v článku [Návrh pro úpravu dat](table-storage-design-for-modification.md).

Dobrým výchozím bodem pro návrh řešení Table service, aby bylo možné efektivně číst data, je požádat o to, jaké dotazy bude aplikace muset spustit, aby se načetla data, která potřebuje z Table service? "  

> [!NOTE]
> S Table service je důležité, abyste si před návrhem nastavili správné nastavení, protože je obtížné ho později změnit. Například v relační databázi je často možné vyřešit problémy s výkonem pouhým přidáním indexů do existující databáze: to není možnost s Table service.  
> 
> 

Tato část se zaměřuje na klíčové problémy, které je potřeba řešit při návrhu tabulek pro dotazování. Témata, která jsou popsaná v tomto oddílu, zahrnují:

* [Jak vaše volba PartitionKey a RowKey ovlivňuje výkon dotazů](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Výběr vhodné PartitionKey](#choosing-an-appropriate-partitionkey)
* [Optimalizace dotazů pro Table service](#optimizing-queries-for-the-table-service)
* [Řazení dat v Table service](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Jak vaše volba PartitionKey a RowKey ovlivňuje výkon dotazů
V následujících příkladech se předpokládá, že služba Table Service ukládá entity zaměstnanců s následující strukturou (většina příkladů vynechává vlastnost **timestamp** pro přehlednost):  

| *Název sloupce* | *Datový typ* |
| --- | --- |
| **PartitionKey** (název oddělení) |Řetězec |
| **RowKey** (ID zaměstnance) |Řetězec |
| **FirstName** |Řetězec |
| **LastName** |Řetězec |
| **Age** (Věk) |Integer |
| **EmailAddress** |Řetězec |

Článek [Přehled služby Azure Table Storage](table-storage-overview.md) popisuje některé klíčové funkce Table Service Azure, které mají přímý vliv na návrh pro dotazy. Výsledkem jsou následující obecné pokyny pro navrhování Table service dotazů. Všimněte si, že syntaxe filtru použitá v níže uvedených příkladech pochází z REST API Table service, další informace najdete v tématu věnovaném [dotazům v entitě](/rest/api/storageservices/Query-Entities).  

* * **Bodový dotaz** _ je nejúčinnější vyhledávání, které se má použít, a doporučuje se ho použít pro vyhledávání ve velkém objemech nebo vyhledávání vyžadující nejnižší latenci. Takový dotaz může použít indexy k velmi efektivnímu vyhledání jednotlivých entit zadáním hodnot _ *PartitionKey* * a **RowKey** . Například: $filter = (PartitionKey EQ ' Sales ') a (RowKey EQ ' 2 ')  
* Druhým nejlepším je * **dotaz na rozsah** _, který používá _ *PartitionKey* * a filtry na rozsah hodnot **RowKey** pro vrácení více než jedné entity. Hodnota **PartitionKey** identifikuje konkrétní oddíl a hodnoty **RowKey** identifikují podmnožinu entit v tomto oddílu. Například: $filter = PartitionKey EQ "Sales" and RowKey GE 'S "and RowKey lt t"  
* Třetí nejlepší je * **prověřování oddílu** _, které používá _ *PartitionKey* * a filtry pro jinou neklíčovou vlastnost a která může vracet více než jednu entitu. Hodnota **PartitionKey** identifikuje konkrétní oddíl a hodnoty vlastností se vyberou pro podmnožinu entit v tomto oddílu. Například: $filter = PartitionKey EQ ' Sales ' a LastName EQ ' Smith '  
* Funkce * **prověřování v tabulce** _ neobsahuje _ *PartitionKey* * a je velmi neefektivní, protože prohledává všechny oddíly, které tvoří tabulku, a také všechny odpovídající entity. Provede kontrolu tabulky bez ohledu na to, zda filtr používá **RowKey**. Příklad: $filter = příjmení EQ ' Novák '  
* Dotazy, které vracejí více entit, je vrátí seřazené v pořadí **PartitionKey** a **RowKey** . Chcete-li se vyhnout vyřazování entit v klientovi, vyberte **RowKey** , který definuje nejběžnější pořadí řazení.  

Všimněte si, že použití " **nebo** " k určení filtru založeného na hodnotách **RowKey** má za následek kontrolu oddílu a nepovažuje se za dotaz na rozsah. Proto byste se měli vyhnout dotazům, které používají filtry jako: $filter = PartitionKey EQ ' Sales ' a (RowKey EQ ' 121 ' nebo RowKey EQ ' 322 ')  

Příklady kódu na straně klienta, který používá klientskou knihovnu pro úložiště ke spouštění efektivních dotazů, najdete v těchto tématech:  

* [Provedení dotazu na bod pomocí klientské knihovny pro úložiště](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Načtení více entit pomocí LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projekce na straně serveru](table-storage-design-patterns.md#server-side-projection)  

Příklady kódu na straně klienta, který může zpracovávat více typů entit uložených ve stejné tabulce, naleznete zde:  

* [Práce s heterogenními typy entit](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Výběr vhodné PartitionKey
Vaše volba **PartitionKey** by měla vyvážit nutnost povolit použití EGTs (k zajištění konzistence) s požadavkem na distribuci entit napříč více oddíly (aby se zajistilo škálovatelné řešení).  

V jednom krajním případě byste mohli uložit všechny své entity do jednoho oddílu, ale může to omezit škálovatelnost vašeho řešení a zabránit tomu, aby služba Table Service mohla vyrovnávat zatížení požadavků. V ostatních extrémních případech byste mohli ukládat jednu entitu na oddíl, která bude vysoce škálovatelná a která službě Table Service umožňuje vyrovnávat zatížení požadavků, ale to by zabránilo použití transakcí skupin entit.  

Ideální **PartitionKey** je ten, který umožňuje používat efektivní dotazy a má dostatečné oddíly pro zajištění škálovatelnosti vašeho řešení. Obvykle zjistíte, že vaše entity budou mít vhodnou vlastnost, která distribuuje vaše entity mezi dostatečné oddíly.

> [!NOTE]
> Například v systému, který ukládá informace o uživatelích nebo zaměstnaních, může být ID uživatele vhodné PartitionKey. Je možné, že máte několik entit, které používají daný identifikátor UserID jako klíč oddílu. Každá entita, která ukládá data o uživateli, je seskupena do jednoho oddílu, takže tyto entity budou přístupné prostřednictvím transakcí skupin entit, i když jsou pořád vysoce škálovatelné.
> 
> 

Existují další okolnosti, které si zvolíte v **PartitionKey** , které se týkají způsobu vkládání, aktualizace a odstraňování entit. Další informace najdete v tématu [navrhování tabulek pro úpravu dat](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Optimalizace dotazů pro Table service
Table service automaticky indexuje vaše entity pomocí hodnot **PartitionKey** a **RowKey** v jednom seskupeném indexu, a to proto, proč jsou dotazy na Point nejúčinnější, aby je bylo možné použít. Neexistují však žádné indexy jiné než v clusterovaných indexech na **PartitionKey** a **RowKey**.

Mnoho návrhů musí splňovat požadavky na povolení vyhledávání entit na základě více kritérií. Například vyhledání entit zaměstnanců na základě e-mailu, ID zaměstnance nebo příjmení. Vzory popsané v [vzorcích návrhu tabulky](table-storage-design-patterns.md) řeší tyto typy požadavků a popisují způsoby, jak se na skutečnost, že Table Service neposkytují sekundární indexy:  

* [Vzor sekundárního indexu v rámci oddílu](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – pomocí různých hodnot **RowKey** můžete pomocí různých hodnot **RowKey** (ve stejném oddílu) ukládat víc kopií každé entity, aby se povolily rychlé a efektivní vyhledávání a alternativní objednávky řazení.  
* [Vzor sekundárního indexu mezi oddíly](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – pomocí různých hodnot **RowKey** můžete ukládat několik kopií každé entity s použitím různých hodnot **RowKey** v samostatných oddílech nebo v samostatných tabulkách, abyste mohli rychle a efektivně vyhledávat a alternativní objednávky řazení.  
* [Index entity vzor](table-storage-design-patterns.md#index-entities-pattern) – Udržujte entity indexu, které umožňují efektivní hledání, které vrací seznam entit.  

## <a name="sorting-data-in-the-table-service"></a>Řazení dat v Table service
Table service vrátí entity seřazené ve vzestupném pořadí podle **PartitionKey** a pak podle **RowKey**. Tyto klíče jsou řetězcové hodnoty a k zajištění správného seřazení číselných hodnot byste je měli převést na pevnou délku a doplnit je nulami. Pokud například hodnota ID zaměstnance, kterou použijete jako **RowKey** , je celočíselná hodnota, měli byste převést ID zaměstnance **123** na **00000123**.  

Mnoho aplikací má požadavky na použití dat seřazených v různých objednávkách: například řazení zaměstnanců podle názvu nebo připojení k datu. Následující vzory řeší alternativní pořadí řazení pro vaše entity:  

* [Vzor sekundárního indexu v rámci oddílu](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – pomocí různých hodnot RowKey můžete pomocí různých hodnot RowKey (ve stejném oddílu) ukládat víc kopií každé entity, aby se povolily rychlé a efektivní vyhledávání a alternativní objednávky řazení.  
* [Vzorek sekundárního indexu mezi oddíly](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – uložte několik kopií každé entity pomocí různých hodnot RowKey v samostatných oddílech v samostatných tabulkách, abyste mohli rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých hodnot RowKey.
* [Vzor funkce log Tail](table-storage-design-patterns.md#log-tail-pattern) – načte entity *n* naposledy přidané do oddílu pomocí hodnoty **RowKey** , která se seřadí v pořadí podle data a času.  

## <a name="next-steps"></a>Další kroky

- [Způsoby návrhu tabulek](table-storage-design-patterns.md)
- [Modelování relací](table-storage-design-modeling.md)
- [Šifrovat data tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)