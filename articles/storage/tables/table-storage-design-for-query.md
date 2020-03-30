---
title: Návrh úložiště tabulek Azure pro dotazy | Dokumenty společnosti Microsoft
description: Návrh tabulek pro dotazy v úložišti Azure Table.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 41a588ddc0c1be8014a84d8fe181013d8566f68d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457643"
---
# <a name="design-for-querying"></a>Návrh pro dotazování
Řešení stolních služeb mohou být intenzivní pro čtení, psaní intenzivní nebo jejich kombinace. Tento článek se zaměřuje na věci, které je třeba mít na paměti při navrhování služby Table pro efektivní podporu operací čtení. Návrh, který efektivně podporuje operace čtení, je obvykle také efektivní pro operace zápisu. Existují však další aspekty, které je třeba mít na paměti při navrhování pro podporu operací zápisu, popsané v článku [Návrh pro úpravu dat](table-storage-design-for-modification.md).

Dobrým výchozím bodem pro návrh řešení služby Table service, které vám umožní efektivně číst data, je dotaz "Jaké dotazy bude aplikace muset provést, aby načetla data, která potřebuje ze služby Table Service?"  

> [!NOTE]
> Se službou Table service je důležité, aby byl návrh správně uveden dopředu, protože později je obtížné a nákladné jej změnit. Například v relační databázi je často možné řešit problémy s výkonem jednoduše přidáním indexů do existující databáze: to není možnost se službou Table.  
> 
> 

Tato část se zaměřuje na klíčové problémy, které je třeba řešit při navrhování tabulek pro dotazování. Témata uvedená v této části zahrnují:

* [Jak vaše volba PartitionKey a RowKey ovlivňuje výkon dotazu](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Výběr vhodného klíče PartitionKey](#choosing-an-appropriate-partitionkey)
* [Optimalizace dotazů pro službu Table](#optimizing-queries-for-the-table-service)
* [Řazení dat ve službě Tabulka](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Jak vaše volba PartitionKey a RowKey ovlivňuje výkon dotazu
Následující příklady předpokládají, že služba tabulky ukládá entity zaměstnanců s následující strukturou (většina příkladů vyneche vlastnost **Časové razítko** pro přehlednost):  

| *Název sloupce* | *Datový typ* |
| --- | --- |
| **PartitionKey** (název oddělení) |Řetězec |
| **RowKey** (ID zaměstnance) |Řetězec |
| **Jméno** |Řetězec |
| **Lastname** |Řetězec |
| **Věk** |Integer |
| **Emailaddress** |Řetězec |

Článek [Přehled úložiště tabulky Azure](table-storage-overview.md) popisuje některé klíčové funkce služby Azure Table, které mají přímý vliv na návrh dotazu. Výsledkem jsou následující obecné pokyny pro navrhování dotazů služby Table Service. Všimněte si, že syntaxe filtru použitá v následujících příkladech je z rozhraní REST API služby Table Service, další informace naleznete v [tématu Entity dotazu](https://docs.microsoft.com/rest/api/storageservices/Query-Entities).  

* ***Bodový dotaz*** je nejúčinnější vyhledávání k použití a doporučuje se použít pro vyhledávání ve vysokém objemu nebo vyhledávání, které vyžadují nejnižší latenci. Takový dotaz můžete použít indexy k vyhledání jednotlivé entity velmi efektivně zadáním **PartitionKey** a **RowKey** hodnoty. Příklad: $filter=(PartitionKey eq 'Sales') a (RowKey eq '2')  
* Druhým nejlepším je ***dotaz rozsahu,*** který používá **PartitionKey** a filtruje na rozsah **RowKey** hodnoty vrátit více než jednu entitu. Hodnota **PartitionKey** identifikuje konkrétní oddíl a hodnoty **RowKey** identifikují podmnožinu entit v tomto oddílu. Příklad: $filter=PartitionKey eq 'Sales' a RowKey ge 'S' a RowKey lt 'T'  
* Třetí nejlepší je ***prohledávač oddílů,*** který používá **PartitionKey** a filtruje na jinou vlastnost, která není klíčem, a která může vrátit více než jednu entitu. Hodnota **PartitionKey** identifikuje konkrétní oddíl a hodnoty vlastností vyberte pro podmnožinu entit v tomto oddílu. Příklad: $filter=PartitionKey eq 'Sales' a LastName eq 'Smith'  
* ***Prohledávač tabulky*** neobsahuje **partitionkey** a je velmi neefektivní, protože prohledává všechny oddíly, které tvoří tabulku zase pro všechny odpovídající entity. Provede prohledávač tabulky bez ohledu na to, zda filtr používá **řádek .** Příklad: $filter=LastName eq 'Jones'  
* Dotazy, které vracejí více entit vrátit je seřazeny v **PartitionKey** a **RowKey** pořadí. Chcete-li se vyhnout uchýlení entity v klientovi, zvolte **RowKey,** který definuje nejběžnější pořadí řazení.  

Všimněte si, že použití "**nebo**" k určení filtru založeného na hodnotách **RowKey** vede k prohledání oddílu a není považováno za dotaz na oblast. Proto byste se měli vyhnout dotazům, které používají filtry, jako jsou: $filter=PartitionKey eq 'Sales' a (RowKey eq '121' nebo RowKey eq '322')  

Příklady kódu na straně klienta, které používají knihovnu klienta úložiště ke spuštění efektivních dotazů, naleznete v tématu:  

* [Spuštění bodového dotazu pomocí klientské knihovny úložiště](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Načtení více entit pomocí LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projekce na straně serveru](table-storage-design-patterns.md#server-side-projection)  

Příklady kódu na straně klienta, který dokáže zpracovat více typů entit uložených ve stejné tabulce, najdete v tématu:  

* [Práce s heterogenními typy entit](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Výběr vhodného klíče PartitionKey
Vaše volba **PartitionKey** by měla vyvážit potřebu povolit použití EGTs (pro zajištění konzistence) proti požadavku na distribuci entit mezi více oddílů (pro zajištění škálovatelné řešení).  

V jednom extrému můžete uložit všechny entity v jednom oddílu, ale to může omezit škálovatelnost vašeho řešení a zabrání službě table service možnost požadavky na vyrovnávání zatížení. Na druhé straně extrémní můžete uložit jednu entitu na oddíl, který by byl vysoce škálovatelný a který umožňuje službu table service požadavky na vyrovnávání zatížení, ale které by vám zabránily v použití transakcí skupiny entit.  

Ideální **PartitionKey** je ten, který umožňuje používat efektivní dotazy a který má dostatek oddílů k zajištění škálovatelnosti vašeho řešení. Obvykle zjistíte, že vaše entity budou mít vhodnou vlastnost, která distribuuje entity přes dostatečné oddíly.

> [!NOTE]
> Například v systému, který ukládá informace o uživatelích nebo zaměstnancích, může být UživatelID dobrým partitionkey. Můžete mít několik entit, které používají dané Uživatelské ID jako klíč oddílu. Každá entita, která ukládá data o uživateli, je seskupena do jednoho oddílu, takže tyto entity jsou přístupné prostřednictvím transakcí skupiny entit, zatímco jsou stále vysoce škálovatelné.
> 
> 

Existují další důležité informace ve vašem výběru **PartitionKey,** které se vztahují k způsobu vložení, aktualizace a odstranění entit. Další informace naleznete v [tématu Návrh tabulek pro úpravy dat](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Optimalizace dotazů pro službu Table
Služba Table Service automaticky indexuje entity pomocí hodnot **PartitionKey** a **RowKey** v jednom seskupeném indexu, proto je důvod, proč jsou dotazy bodů nejúčinnější. Neexistují však žádné indexy než v clusterovaném indexu na **PartitionKey** a **RowKey**.

Mnoho návrhů musí splňovat požadavky, aby bylo možné vyhledat entity na základě více kritérií. Například vyhledání entit zaměstnanců na základě e-mailu, id zaměstnance nebo příjmení. Vzory popsané v [vzorcích návrhu tabulky](table-storage-design-patterns.md) řeší tyto typy požadavků a popisují způsoby práce kolem skutečnosti, že služba Table neposkytuje sekundární indexy:  

* [Vzor sekundárního indexu uvnitř oddílu](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – uložte více kopií každé entity pomocí různých hodnot **RowKey** (ve stejném oddílu), abyste povolili rychlé a efektivní vyhledávání a alternativní pořadí řazení pomocí různých hodnot **RowKey.**  
* [Vzor sekundárního indexu mezi oddíly](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – uložte více kopií každé entity pomocí různých hodnot **RowKey** v samostatných oddílech nebo v samostatných tabulkách, abyste povolili rychlé a efektivní vyhledávání a alternativní pořadí řazení pomocí různých hodnot **RowKey.**  
* [Vzorek entit indexu](table-storage-design-patterns.md#index-entities-pattern) – udržovat entity indexu, aby bylo možné povolit efektivní vyhledávání, které vrací seznamy entit.  

## <a name="sorting-data-in-the-table-service"></a>Řazení dat ve službě Tabulka
Služba Table vrátí entity seřazené ve vzestupném pořadí na základě **partitionkey** a potom podle **řádku.** Tyto klávesy jsou řetězcové hodnoty a aby bylo zajištěno, že číselné hodnoty se řadí správně, měli byste je převést na pevnou délku a umístit je nulami. Například pokud id zaměstnance hodnota, kterou používáte jako **RowKey** je celá hodnota, měli byste převést id zaměstnance **123** na **00000123**.  

Mnoho aplikací má požadavky na použití dat seřazených v různých objednávkách: například řazení zaměstnanců podle jména nebo podle data spojení. Následující vzory se týkají alternativního řazení objednávek pro entity:  

* [Vzor sekundárního indexu uvnitř oddílu](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – uložte více kopií každé entity pomocí různých hodnot RowKey (ve stejném oddílu), abyste povolili rychlé a efektivní vyhledávání a alternativní pořadí řazení pomocí různých hodnot RowKey.  
* [Vzor sekundárního indexu mezi oddíly](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – uložte více kopií každé entity pomocí různých hodnot RowKey v samostatných oddílech v samostatných tabulkách, abyste povolili rychlé a efektivní vyhledávání a alternativní pořadí řazení pomocí různých hodnot RowKey.
* [Vzor ocasu protokolu](table-storage-design-patterns.md#log-tail-pattern) - načíst *n* entity naposledy přidané do oddílu pomocí **RowKey** hodnotu, která seřadí v obráceném pořadí data a času.  

## <a name="next-steps"></a>Další kroky

- [Způsoby návrhu tabulek](table-storage-design-patterns.md)
- [Modelování relací](table-storage-design-modeling.md)
- [Šifrování dat tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)
