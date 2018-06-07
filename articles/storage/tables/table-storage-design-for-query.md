---
title: Návrh tabulky úložiště Azure pro dotazy | Microsoft Docs
description: Návrh tabulky pro dotazy v úložiště tabulek Azure.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: b8d2033b0b29caddf165f4b582c7d0578109480c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660764"
---
# <a name="design-for-querying"></a>Návrh pro dotazování
Řešení služby TABLE lze číst náročné na prostředky, náročné zápisu nebo kombinaci těchto dvou. Tento článek se zaměřuje na skutečnosti, které je berte v úvahu při navrhování vaší služby Table pro podporu operací čtení efektivně. Návrh podporuje efektivně zapisovacích operací je obvykle také efektivní pro operace zápisu. Existují však další aspekty a berte v úvahu při navrhování pro podporu operací, popsanou v článku zápisu [návrhu pro úpravu dat](table-storage-design-for-modification.md).

Je to dobrý výchozí bod pro návrh řešení služby Tabulka vám umožní číst data efektivně požádat "jaké dotazy bude Moje aplikace potřeba provést k načtení dat, které potřebuje ze služby Table?"  

> [!NOTE]
> Ve službě Table je potřeba získat návrh správné předem vzhledem k tomu, že je složitá a nákladná později změnit. Například v relační databázi je často možné problémy s výkonem adresu jednoduše přidáním indexy k existující databázi: Toto není nabízet služby Table.  
> 
> 

Tato část se zaměřuje na klíčových otázek, které musíte vyřešit, když tabulek pro zadávání dotazů. Obsahuje následující témata v této části:

* [Jak vaši volbu PartitionKey a RowKey ovlivňuje výkon dotazů](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Výběr vhodné PartitionKey](#choosing-an-appropriate-partitionkey)
* [Optimalizace dotazů pro služby Table](#optimizing-queries-for-the-table-service)
* [Řazení dat ve službě Table](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Jak vaši volbu PartitionKey a RowKey ovlivňuje výkon dotazů
Následující příklady předpokládají služby table je ukládání entit zaměstnanec s následující strukturou (většina příkladů vynechejte **časové razítko** vlastnost pro přehlednost):  

| *Název sloupce* | *Datový typ* |
| --- | --- |
| **PartitionKey** (název oddělení) |Řetězec |
| **RowKey** (Id zaměstnance) |Řetězec |
| **FirstName** |Řetězec |
| **LastName** |Řetězec |
| **stáří** |Integer |
| **EmailAddress** |Řetězec |

Článek [přehled Azure Table storage](table-storage-overview.md) popisuje některé klíčové funkce služby Azure Table, které mají přímý vliv na návrh pro dotaz. To mít za následek následující obecné pokyny pro návrh tabulky služby dotazy. Všimněte si, že syntaxe filtru použít v následujících příkladech je ze služby Table rozhraní REST API, další informace najdete v tématu [dotazu entity](https://docs.microsoft.com/rest/api/storageservices/Query-Entities).  

* A ***bodu dotazu*** je nejúčinnější vyhledávání používat a doporučuje se má být použit pro vysoký počet vyhledávání nebo vyhledávání, které vyžadují nejnižší latenci. Takové dotazy můžete použít indexy velmi efektivně najít jednotlivých entit zadáním i **PartitionKey** a **RowKey** hodnoty. Příklad: $filter = (PartitionKey eq 'Prodej') a (RowKey eq: 2.)  
* Druhý nejlépe je ***dotazu na rozsah*** používající **PartitionKey** a filtry pro celou řadu **RowKey** hodnot se má vrátit více než jedna entita. **PartitionKey** hodnota identifikuje na konkrétní oddíl a **RowKey** hodnoty identifikovat podmnožinu entity v oddílu. Příklad: $filter = PartitionKey eq 'prodeje a RowKey ge' a RowKey lt se.  
* Je třetí nejlepší ***oddílu kontrolovat*** používající **PartitionKey** a filtry na jiné neklíčovou vlastnost a který může vrátit více než jedna entita. **PartitionKey** hodnota identifikuje konkrétního oddílu a vyberte pro podmnožinu entity v oddílu hodnoty vlastnosti. Příklad: $filter = PartitionKey eq 'prodeje a LastName eq: Váša.  
* A ***tabulky kontrolovat*** nezahrnuje **PartitionKey** a je velmi neefektivní, protože všechny oddíly, které tvoří tabulku zase pro všechny odpovídající entity vyhledávání. Provede prohledávání tabulky bez ohledu na to, zda filtr používá **RowKey**. Příklad: $filter = LastName eq 'Petr.  
* Dotazy, které vracejí víc entit obnoví v nich seřazeny ve **PartitionKey** a **RowKey** pořadí. Abyste se vyhnuli, opětné řazení entity v klientovi, vyberte **RowKey** , který definuje nejběžnější pořadí řazení.  

Všimněte si, že pomocí "**nebo**" k zadání filtru na základě **RowKey** hodnoty výsledků v oddílu kontroly a nepovažuje se za dotazu na rozsah. Proto byste neměli dotazy, které pomocí filtrů, jako například: $filter = PartitionKey eq 'Prodej' a (RowKey eq '121' nebo RowKey eq "322")  

Příklady kódu na straně klienta, které používají Klientská knihovna pro úložiště na provedení efektivní dotazů najdete v tématu:  

* [Spuštění dotazu bodu pomocí klientské knihovny pro úložiště](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Načtení více entit pomocí LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projekce na straně serveru](table-storage-design-patterns.md#server-side-projection)  

Příklady kódu na straně klienta, který může zpracovat více typy entit, které jsou uloženy ve stejné tabulce najdete v tématu:  

* [Práce s typy heterogenní entit](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Výběr vhodné PartitionKey
Vaši volbu **PartitionKey** měli vyvážit potřeba povolit používání EGTs (k zajištění konzistence) proti požadavku, distribuovat vaší entity napříč více oddílů (aby škálovatelné řešení).  

V jedné extreme může ukládat všechny entity v jeden oddíl, ale to může omezit škálovatelnost řešení a by bránily schopnost Vyrovnávání zatížení požadavky služby table. V jiných extreme může ukládat jednu entitu na oddíl, který bude vysoce škálovatelné a která umožňuje služby table na Vyrovnávání zatížení požadavky, ale které by zabránit vám v použití transakcí skupiny entity.  

Ideálu **PartitionKey** jeden, který vám umožňuje používat efektivní dotazy a má dostatek oddíly pro zajištění škálovatelné řešení. Obvykle zjistíte, že vaší entity budou mít vhodný vlastnost, která distribuuje vaší entity v dostatečná oddíly.

> [!NOTE]
> V systému, která uchovává informace o uživatele nebo zaměstnancům, například ID uživatele může být dobrým PartitionKey. Může mít několik entit, které pomocí dané ID uživatele jako klíč oddílu. Každá entita, která ukládá data o uživateli se seskupují do jednoho oddílu, a proto tyto entity jsou přístupné přes transakce skupiny entity, ale stále mít vysoce škálovatelná.
> 
> 

Další rozhodnutí v požadovaném **PartitionKey** které se týkají jak bude vložit, aktualizovat a odstraňovat entity. Další informace najdete v tématu [návrh tabulky pro úpravu dat](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Optimalizace dotazů pro služby Table
Služby Table automaticky indexuje entity produktu pomocí **PartitionKey** a **RowKey** hodnot v jednom clusterovaný index, proto důvod, proč bodu dotazy jsou nejúčinnější používat. Však nejsou žádné indexy než tu, která na clusterovaný index na **PartitionKey** a **RowKey**.

Řada návrhů musí splňovat požadavky na povolení vyhledávání entit na základě několika kritérií. Například hledání zaměstnanec entity podle e-mailu, identifikační číslo zaměstnance nebo příjmení. Vzory popsané v [vzory návrhu tabulky](table-storage-design-patterns.md) adres tyto typy požadavek a popisují způsoby obcházet fakt, že služby Table nenabízí sekundární indexy:  

* [Vzor sekundární index Intra-partition](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -uložit více kopií každou entitu s využitím různých **RowKey** hodnoty (ve stejném oddílu) Chcete-li povolit rychlé a efektivní vyhledávání a alternativní pořadí řazení pomocí různých **RowKey** hodnoty.  
* [Vzor mezi oddíl sekundární index](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) -uložit více kopií každou entitu s využitím různých **RowKey** hodnoty v samostatných oddílů nebo v samostatné tabulky, které chcete povolit rychlé a efektivní vyhledávání a alternativní řazení objednávky pomocí různých **RowKey** hodnoty.  
* [Index entity vzor](table-storage-design-patterns.md#index-entities-pattern) -udržovat index entity umožňující efektivní hledání, které vrátí seznamy entit.  

## <a name="sorting-data-in-the-table-service"></a>Řazení dat ve službě Table
Služba Table vrací entity ve vzestupném pořadí podle **PartitionKey** a potom podle **RowKey**. Tyto klíče jsou hodnoty řetězce a aby správně řazení číselných hodnot, měli byste je převést na pevnou délkou a je odsadí nulami. Například, pokud hodnota id zaměstnance, můžete použít jako **RowKey** je celočíselná hodnota, bude třeba převést identifikační číslo zaměstnance **123** k **00000123**.  

Mnoho aplikací mít požadavky pro použití dat seřazeny v různém pořadí: například řazení zaměstnanci podle názvu nebo díky připojení ke službě data. Tyto vzory adres postup alternativní pořadí řazení pro vaše entity:  

* [Vzor sekundární index Intra-partition](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – ukládání více kopií každé entity pomocí různých hodnot RowKey (ve stejném oddílu) umožňující rychlé a efektivní vyhledávání a řazení alternativní řadí za použití různých hodnot RowKey.  
* [Vzor mezi oddíl sekundární index](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – ukládání více kopií každé entity pomocí různých hodnot RowKey v samostatné oddíly v samostatných tabulkách umožňující rychlé a efektivní vyhledávání a řazení alternativní řadí za použití různých hodnot RowKey .
* [Vzor protokolu poškozené databáze](table-storage-design-patterns.md#log-tail-pattern) -načíst *n* entity naposledy přidané do oddílu pomocí **RowKey** hodnotu, která seřadí zpětné datum a čas pořadí.  

## <a name="next-steps"></a>Další postup

- [Vzory návrhu tabulky](table-storage-design-patterns.md)
- [Modelování vztahů](table-storage-design-modeling.md)
- [Šifrování dat v tabulce](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)
