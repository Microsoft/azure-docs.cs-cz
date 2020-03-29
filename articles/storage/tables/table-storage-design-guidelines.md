---
title: Pokyny pro návrh tabulky úložiště Azure | Dokumenty společnosti Microsoft
description: Navrhněte si službu azure table service tak, aby efektivně podporovala operace čtení.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: d056d29469ad9a60fceeee307aca3c0e1319283c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61269829"
---
# <a name="guidelines-for-table-design"></a>Pokyny pro návrh tabulek

Navrhování tabulek pro použití se službou tabulky úložiště Azure se velmi liší od aspekty návrhu pro relační databázi. Tento článek popisuje pokyny pro navrhování řešení služby Table service pro efektivní čtení a zápis efektivní.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Navrhněte řešení table service tak, aby bylo efektivní pro čtení

* ***Návrh pro dotazování v aplikacích s vysokým i pro čtení.*** Při navrhování tabulek přemýšlejte o dotazech (zejména těch citlivých na latenci), které provedete, než začnete přemýšlet o tom, jak budete aktualizovat entity. To obvykle vede k efektivní a výkonné řešení.  
* ***Zadejte partitionkey a RowKey v dotazech.*** *Bodové dotazy,* jako jsou tyto, jsou nejúčinnější dotazy na služby tabulky.  
* ***Zvažte uložení duplicitních kopií entit.*** Úložiště tabulek je levné, takže zvažte uložení stejné entity vícekrát (s různými klíči), abyste povolili efektivnější dotazy.  
* ***Zvažte denormalizaci dat.*** Ukládání stolů je levné, takže zvažte denormalizaci dat. Například uložte souhrnné entity tak, aby dotazy na agregovaná data potřebovaly pouze pro přístup k jedné entitě.  
* ***Použijte hodnoty složeného klíče.*** Jediné klíče, které máte, jsou **PartitionKey** a **RowKey**. Pomocí hodnot složených klíčů můžete například povolit alternativní přístupové cesty s klíčem k entitám.  
* ***Použijte projekci dotazu.*** Množství dat, která přenášíte po síti, můžete snížit pomocí dotazů, které vyberou pouze pole, která potřebujete.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Navrhněte řešení table service tak, aby bylo efektivní pro zápis  

* ***Nevytvářejte horké oddíly.*** Vyberte klíče, které vám umožní rozložit požadavky na více oddílů v libovolném okamžiku.  
* ***Vyhněte se špičkám v provozu.*** Vyhlaďte provoz po přiměřenou dobu a vyhněte se špičkám v provozu.
* ***Nemusí nutně vytvořit samostatnou tabulku pro každý typ entity.*** Pokud požadujete atomické transakce napříč typy entit, můžete uložit tyto typy více entit ve stejném oddílu ve stejné tabulce.
* ***Zvažte maximální propustnost, které musíte dosáhnout.*** Musíte si být vědomi cílů škálovatelnosti pro službu Table service a ujistěte se, že váš návrh nezpůsobí jejich překročení.  

Při čtení této příručky uvidíte příklady, které všechny tyto zásady uvedou do praxe. 

## <a name="next-steps"></a>Další kroky

- [Způsoby návrhu tabulek](table-storage-design-patterns.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrování dat tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)
