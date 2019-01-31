---
title: Návrh tabulky pro dotazy v úložišti Azure | Dokumentace Microsoftu
description: Návrh tabulek pro dotazy ve službě Azure table storage.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 97373f6f0138d3ed8028ed4327b7e6cf90ad76a7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470735"
---
# <a name="design-for-querying"></a>Návrh pro dotazování
Náročné na prostředky, zápis náročné na prostředky nebo kombinaci obou, může se načíst řešení Table service. Tento článek se zaměřuje na věcí berte v úvahu při návrhu vaší služby Table service pro podporu operací čtení efektivně. Návrh, že podporuje operace čtení efektivně je obvykle také efektivní pro operace zápisu. Existují však další důležité informace k berte v úvahu při navrhování pro podporu operací zápisu, popsanou v článku [návrhu pro úpravu dat](table-storage-design-for-modification.md).

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

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Jak ovlivňuje výkon dotazů PartitionKey a RowKey podle vašeho výběru
Následující příklady předpokládají, že služba table service je ukládání entity zaměstnance s následující strukturou (vynechat většina příkladů **časové razítko** vlastnost pro přehlednost):  

| *Název sloupce* | *Datový typ* |
| --- | --- |
| **PartitionKey** (název oddělení) |Řetězec |
| **RowKey** (Id zaměstnance) |Řetězec |
| **Jméno** |Řetězec |
| **LastName** |Řetězec |
| **Stáří** |Integer |
| **EmailAddress** |String |

Tento článek [Přehled služby Azure Table storage](table-storage-overview.md) popisuje některé klíčové funkce služby Azure Table service, které mají přímý vliv na návrh pro dotaz. Tyto za následek následující obecné pokyny pro návrh služby dotazy na tabulku. Všimněte si, že se používá v následujících příkladech syntaxe parametru filter je z rozhraní REST API služby tabulky pro další informace najdete v tématu [dotazu entity](https://docs.microsoft.com/rest/api/storageservices/Query-Entities).  

* A ***dotazu bodu*** je nejúčinnější vyhledávání používat a doporučuje se použít pro vyhledávání velkého rozsahu nebo vyhledávání, které vyžadují nejnižší latenci. Takový dotaz použít k vyhledání jednotlivých entit velmi efektivně tak, že zadáte obě indexy, které **PartitionKey** a **RowKey** hodnoty. Příklad: $filter = (PartitionKey eq "Prodeje") a (RowKey eq '2')  
* Za druhé nejlepší je ***dotazu na rozsah*** , která používá **PartitionKey** a filtry na mnoha různých **RowKey** hodnoty k vrácení více než jednu entitu. **PartitionKey** hodnota označuje konkrétní oddíl a **RowKey** hodnoty identifikaci podmnožiny entity v tomto oddílu. Příklad: $filter = PartitionKey eq "Prodeje a RowKey ge" a RowKey lt 'T'  
* Je třetí nejlepší ***oddílu kontrolovat*** , která používá **PartitionKey** a filtry v jiné neklíčovým vlastnosti, které může vrátit více než jednu entitu. **PartitionKey** identifikuje hodnotu konkrétního oddílu, a vlastnost hodnot vyberte pro celou dílčí sadu entit v oddílu. Příklad: $filter = PartitionKey eq "Prodeje" a LastName eq: Váša.  
* A ***prohledávání tabulky*** nezahrnuje **PartitionKey** a je velmi neefektivní, protože prohledává všechny oddíly, které tvoří tabulky pak u všech odpovídajících entit. Bude provedeno prohledání tabulky bez ohledu na to, jestli váš filtr používá **RowKey**. Příklad: $filter = LastName eq "Jones"  
* Dotazy vracející více entit je řazení v vrátit **PartitionKey** a **RowKey** pořadí. Abyste se vyhnuli nutnosti uchýlit se entity v klientovi, zvolte **RowKey** , který definuje nejběžnější pořadí řazení.  

Všimněte si, že při použití "**nebo**" zadat filtr na základě **RowKey** hodnoty výsledky v oddílu kontroly a není považován za rozsahového dotazu. Proto byste se měli vyhnout dotazů, které používají jako filtry: $filter = PartitionKey eq "Prodeje" a (RowKey eq "121" nebo RowKey eq "322")  

Příklady kódu na straně klienta, které používají klientskou knihovnu pro úložiště k provádění efektivních dotazů najdete v tématu:  

* [Provedení dotazu bodu pomocí klientskou knihovnu pro úložiště](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Načtení více entit pomocí jazyka LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projekce na straně serveru](table-storage-design-patterns.md#server-side-projection)  

Příklady kódu na straně klienta, který dokáže zpracovat víc typů entit, které jsou uloženy ve stejné tabulce naleznete v tématu:  

* [Práce s typy heterogenní entit](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Výběr vhodné PartitionKey
Podle vaší volby **PartitionKey** proti požadavku k distribuci entity napříč několika oddíly (k zajištění škálovatelných řešení) by měl vyvážily potřeby povolit používání EGTs (k zajištění konzistence).  

Na jeden extreme všech entit může ukládat do jednoho oddílu, ale to může omezit škálovatelnost řešení a služby table service by jinak znemožňovaly tomu nebudou moct Vyrovnávání zatížení požadavků. V jiných extreme může uchovávat jednu entitu na oddíl, který by být vysoce škálovatelné a který umožňuje službě table service pro vyrovnávání zatížení požadavků, ale které by jinak znemožňovaly pomocí transakcí skupin entit.  

Ideální **PartitionKey** je jedna, která vám umožní použít efektivní dotazy a má dostatek oddílů, aby zajistit je škálovatelné řešení. Obvykle zjistíte, zda bude mít vaše entity vhodný vlastnost, která distribuuje napříč oddíly dostatečná entity.

> [!NOTE]
> V systému, která uchovává informace o uživateli nebo zaměstnanců, například ID uživatele může být vhodné PartitionKey. Můžete mít několik entit, které používají dané ID uživatele jako klíč oddílu. Každá entita, která ukládá data o uživateli jsou rozděleny do jednoho oddílu, a proto tyto entity jsou přístupné přes transakcí skupin entit, ale stále vysoce škálovatelné.
> 
> 

Existují ještě další důležité okolnosti ve podle vaší volby **PartitionKey** , které se týkají jak bude vkládat, aktualizovat a odstraňovat entity. Další informace najdete v tématu [návrh tabulky pro úpravu dat](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Optimalizace dotazů pro služby Table service
Služba Table service automaticky indexuje entit pomocí **PartitionKey** a **RowKey** v jeden clusterovaný index, proto příčiny, které odkazují dotazy jsou hodnoty použít co nejúčinnější. Však nejsou žádné indexy než na clusterovaný index na **PartitionKey** a **RowKey**.

Řada návrhů musí splňovat požadavky na povolení vyhledávání entit na základě několika kritérií. Například hledání entit zaměstnanců podle e-mailu, id zaměstnance nebo příjmení. Tyto vzory se dají podle [vzory návrhu v tabulce](table-storage-design-patterns.md) vyřešit tyto typy požadavků a popisují způsoby práce kolem skutečnost, že služba Table service neposkytuje sekundární indexy:  

* [Vzor sekundární index oddílu uvnitř](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -Store několik kopií každého entitu s využitím různých **RowKey** hodnot (ve stejném oddílu) Chcete-li povolit rychlé a efektivní vyhledávání a alternativní pořadí řazení s použitím různých **RowKey** hodnoty.  
* [Sekundární index oddílu mezi vzor](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) -Store několik kopií každého entitu s využitím různých **RowKey** hodnoty v samostatných oddílů nebo v samostatné tabulky, které umožňují rychlé a efektivní vyhledávání a alternativní řazení objednávky s použitím různých **RowKey** hodnoty.  
* [Model entity indexů](table-storage-design-patterns.md#index-entities-pattern) -udržovat index entity, které umožňují efektivní prohledávání, které vrací seznam entit.  

## <a name="sorting-data-in-the-table-service"></a>Řazení dat ve službě Table service
Služba Table service vrací entity ve vzestupném pořadí podle **PartitionKey** a potom podle **RowKey**. Tyto klíče jsou hodnoty řetězců a aby číselných hodnot řadily správně, musí je převést na pevnou délkou a vyplnění nulami. Například, pokud hodnota id zaměstnance použijete jako **RowKey** je celočíselná hodnota, je třeba id zaměstnance převést **123** k **00000123**.  

Mnoho aplikací mají požadavky na používání dat v různém pořadí řazení: podle názvu nebo díky připojení ke službě data, například řazení zaměstnanci. Následující vzory adres jak alternativní pořadí řazení pro vaší entity:  

* [Vzor sekundární index oddílu uvnitř](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Store několik kopií Každá entita s použitím různých hodnot RowKey (ve stejném oddílu) a umožňuje rychlé a efektivní vyhledávání a řazení alternativní objednávky s použitím různých RowKey hodnot.  
* [Sekundární index oddílu mezi vzor](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Store několik kopií Každá entita s použitím různých hodnot RowKey v samostatných oddílech v samostatných tabulkách a umožňuje rychlé a efektivní vyhledávání a řazení alternativní objednávky s použitím různých hodnot RowKey .
* [Vzor log tail](table-storage-design-patterns.md#log-tail-pattern) – načíst *n* naposledy přidaný do oddílu s použitím entity **RowKey** hodnotu, která seřadí reverzní datum a čas objednávky.  

## <a name="next-steps"></a>Další postup

- [Vzory návrhu tabulky](table-storage-design-patterns.md)
- [Modelování vztahů](table-storage-design-modeling.md)
- [Šifrování dat tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)
