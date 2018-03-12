---
title: "Migrovat existující Azure datového skladu do úložiště úrovně premium | Microsoft Docs"
description: "Pokyny k migraci existující datový sklad storage úrovně premium"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: barbkess
editor: 
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 11/29/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 1e216da55a4c425fe112215464cdedb59c8db585
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="migrate-your-data-warehouse-to-premium-storage"></a>Migrace na storage úrovně premium datového skladu
Azure SQL Data Warehouse nedávno zaveden [storage úrovně premium pro vyšší výkon, předvídatelnost][premium storage for greater performance predictability]. Existující datových skladů aktuálně na standardní úložiště lze nyní přenést do úložiště úrovně premium. Můžete využít výhod Automatická migrace, nebo pokud chcete řídit, kdy k migraci (které zahrnují výpadky), můžete provést migraci sami.

Pokud máte více než jeden datový sklad, použijte [automatickou migraci plánu] [ automatic migration schedule] k určení, kdy ho bude migrována také.

## <a name="determine-storage-type"></a>Stanovení typů úložiště
Pokud jste vytvořili datový sklad před následující daty, jsou právě používá standardní úložiště.

| **Oblast** | **Vytvořené před tímto datem datového skladu** |
|:--- |:--- |
| Austrálie – východ |1. ledna 2018 |
| Čína – východ |1. listopadu 2016 |
| Čína – sever |1. listopadu 2016 |
| Německo – střed |1. listopadu 2016 |
| Německo – severovýchod |1. listopadu 2016 |
| Indie – západ |1. února 2018 |
| Japonsko – západ |1. února 2018 |
| Střed USA – sever |10 od listopadu 2016 |

## <a name="automatic-migration-details"></a>Automatická migrace podrobnosti
Ve výchozím nastavení, jsme bude databázi migrujte pro vás od 18:00:00 do 6:00:00 ve vaší oblasti místní čas během [automatickou migraci plánu][automatic migration schedule]. Během migrace budou existující datový sklad nepoužitelné. Migrace bude trvat přibližně za jednu hodinu za terabajt úložiště za datového skladu. Vám nebude nic účtováno během jakékoli její části automatickou migraci.

> [!NOTE]
> Po dokončení migrace datového skladu bude zpět online a dá se použít.
>
>

Společnost Microsoft provádí následující kroky k dokončení migrace (ty nevyžadují žádné zapojení z vaší strany). V tomto příkladu Představte si, že vaše existující datový sklad na standardní úložiště je aktuálně s názvem "MyDW."

1. Microsoft přejmenuje "MyDW" na "MyDW_DO_NOT_USE_ [časové razítko]."
2. Microsoft pozastaví "MyDW_DO_NOT_USE_ [časové razítko]." Během této doby je převzat zálohu. Pokud jsme dojde k potížím během tohoto procesu může se zobrazit více zastaví a obnoví.
3. Microsoft vytvoří nový datový sklad s názvem "MyDW" na storage úrovně premium ze zálohy pořízené v kroku 2. "MyDW" se zobrazí až po dokončení obnovení.
4. Po obnovení je dokončeno, "MyDW" vrátí do stejné jednotky datového skladu a stav (pozastaven nebo aktivní) aby byla před migrací.
5. Po dokončení migrace Microsoft odstraní "MyDW_DO_NOT_USE_ [časové razítko]".

> [!NOTE]
> Jako součást migrace se nepřenesou následující nastavení:
>
> * Auditování na úrovni databáze musí být znovu zapnout.
> * Pravidla brány firewall na úrovni databáze musí být znovu přidat. Ovlivněné nejsou pravidla brány firewall na úrovni serveru.
>
>

### <a name="automatic-migration-schedule"></a>Automatická migrace plánu
Automatické migrace dojít od 18:00:00 do 6:00:00 (místní čas na oblast) během následujícího plánu výpadku.

| **Oblast** | **Odhadované datum** | **Odhadované datum ukončení** |
|:--- |:--- |:--- |
| Austrálie – východ |19. března 2018 |20 března 2018 |
| Čína – východ |Již migrovali |Již migrovali |
| Čína – sever |Již migrovali |Již migrovali |
| Německo – střed |Již migrovali |Již migrovali |
| Německo – severovýchod |Již migrovali |Již migrovali |
| Indie – západ |19. března 2018 |20 března 2018 |
| Japonsko – západ |19. března 2018 |20 března 2018 |
| Střed USA – sever |Již migrovali |Již migrovali |

## <a name="self-migration-to-premium-storage"></a>Vlastní migrace na storage úrovně premium
Pokud chcete řídit, když dojde k odstávka, můžete použít následující kroky k migraci existující datový sklad na standardní úložiště do úložiště úrovně premium. Pokud zvolíte tuto možnost, musíte vlastní migrace dokončit před zahájením Automatická migrace v této oblasti. To zajistí, že zabránění nebezpečí automatickou migraci způsobuje konflikt (odkazovat [automatickou migraci plánu][automatic migration schedule]).

### <a name="self-migration-instructions"></a>Pokyny k migraci vlastní
Pro migraci datového skladu, použijte zálohování a obnovení funkce. Očekává se, že část obnovení migrace trvat přibližně jednu hodinu za terabajt úložiště za datového skladu. Pokud chcete použít stejný název po dokončení migrace, postupujte [kroky pro přejmenování během migrace][steps to rename during migration].

1. [Pozastavení] [ Pause] datového skladu. 
2. [Obnovit] [ Restore] z vaší poslední snímek.
3. Odstraňte existující datový sklad na standardní úložiště. **Pokud tento krok, vám bude účtována pro obě datových skladů.**

> [!NOTE]
>
> Při obnovování datového skladu, ověřte, že dojde k nejnovější bod obnovení, která je k dispozici po datového skladu byla pozastavena.
>
> Jako součást migrace se nepřenesou následující nastavení:
>
> * Auditování na úrovni databáze musí být znovu zapnout.
> * Pravidla brány firewall na úrovni databáze musí být znovu přidat. Ovlivněné nejsou pravidla brány firewall na úrovni serveru.
>
>

#### <a name="rename-data-warehouse-during-migration-optional"></a>Přejmenování datového skladu během migrace (volitelné)
Dvě databáze na stejného logického serveru nemůžou mít stejný název. SQL Data Warehouse nyní podporuje funkce přejmenování datového skladu.

V tomto příkladu Představte si, že vaše existující datový sklad na standardní úložiště je aktuálně s názvem "MyDW."

1. Přejmenujte "MyDW" pomocí následujícího příkazu ALTER DATABASE. (V tomto příkladu jsme budete přejmenujte ji "MyDW_BeforeMigration.")  Tento příkaz zastaví všechny stávající transakce a je třeba provést v hlavní databázi proběhla úspěšně.
   ```
   ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
   ```
2. [Pozastavení] [ Pause] "MyDW_BeforeMigration." 
3. [Obnovit] [ Restore] z vaší poslední snímek novou databázi s názvem dříve (například "MyDW").
4. Odstranit "MyDW_BeforeMigration." **Pokud tento krok, vám bude účtována pro obě datových skladů.**


## <a name="next-steps"></a>Další postup
Změny do úložiště úrovně premium máte také zvýšením počtu objektů blob soubory databáze v základní Architektura datového skladu. Pokud chcete maximalizovat výkon výhody této změny, znovu sestavte vaše Clusterované indexy columnstore pomocí následujícího skriptu. Skript funguje tak, že některé z vašich existujících dat vynucení na další objekty BLOB. Pokud nepodniknete žádnou akci, data se přirozeně znovu distribuovat časem jako načtení více dat do tabulek.

Pokud narazíte na potíže s datovým skladem, [vytvořit lístek podpory] [ create a support ticket] a referenční dokumentace "migrace na storage úrovně premium" jako možnou příčinu.

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: quickstart-scale-compute-portal.md
[mediumrc role]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com
