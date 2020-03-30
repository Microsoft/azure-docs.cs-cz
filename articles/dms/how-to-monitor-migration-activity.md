---
title: Sledování aktivity migrace – služba migrace databáze Azure
description: Naučte se ke sledování aktivity migrace pomocí služby Migrace databáze Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 31b49cdd9e0e5569981b2a0b0c6efcab7239e019
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648508"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Sledování aktivity migrace pomocí služby Migrace databáze Azure
V tomto článku se dozvíte, jak sledovat průběh migrace na úrovni databáze i tabulky.

## <a name="monitor-at-the-database-level"></a>Monitor na úrovni databáze
Chcete-li sledovat aktivitu na úrovni databáze, zobrazte okno na úrovni databáze:

![Blade na úrovni databáze](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Výběrem hypertextového odkazu databáze se zobrazí seznam tabulek a jejich průběh migrace.

V následující tabulce jsou uvedena pole v okně na úrovni databáze a jsou popsány různé hodnoty stavu přidružené k jednotlivým polím.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Název pole</strong></th>
      <th><strong>Dílčí stav pole</strong></th>
      <th><strong>Popis</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Stav aktivity</strong></td>
      <td>Spuštěno</td>
      <td>Aktivita migrace je spuštěna.</td>
    </tr>
    <tr>
      <td>Úspěch</td>
      <td>Migrace aktivita proběhla bez problémů.</td>
    </tr>
    <tr>
      <td>Došlo k chybě</td>
      <td>Migrace se nezdařila. V části Podrobnosti o migraci vyberte odkaz Zobrazit podrobnosti o chybě , který se zobrazí v části Podrobnosti o migraci.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Stav</strong></td>
      <td>Initializing</td>
      <td>DMS nastavuje migrační kanál.</td>
    </tr>
    <tr>
      <td>Spuštěno</td>
      <td>Kanál DMS je spuštěn a provádí migraci.</td>
    </tr>
    <tr>
      <td>Dokončit</td>
      <td>Migrace byla dokončena.</td>
    </tr>
    <tr>
      <td>Failed</td>
      <td>Migrace se nezdařila. Kliknutím na podrobnosti o migraci zobrazíte chyby migrace.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Informace o migraci</strong></td>
      <td>Zahájení migračního kanálu</td>
      <td>DMS nastavuje migrační kanál.</td>
    </tr>
    <tr>
      <td>Úplné načítání dat probíhá</td>
      <td>DMS provádí počáteční zatížení.</td>
    </tr>
    <tr>
      <td>Připraveno pro cutover</td>
      <td>Po dokončení počátečního zatížení dms označí databázi jako připravenou k přerušení řezu. Uživatel by měl zkontrolovat, zda data zachycena na kontinuální synchronizaci.</td>
    </tr>
    <tr>
      <td>Všechny použité změny</td>
      <td>Počáteční zatížení a průběžná synchronizace jsou dokončeny. K tomuto stavu dochází také po úspěšném přeškrtání databáze.</td>
    </tr>
    <tr>
      <td>Zobrazit podrobnosti o chybě</td>
      <td>Kliknutím na odkaz zobrazíte podrobnosti o chybě.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Doba trvání</strong></td>
      <td>Není dostupné.</td>
      <td>Celkový čas od inicializování aktivity migrace na dokončenou migraci nebo chyba migrace.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Monitor na úrovni tabulky – Rychlý souhrn
Chcete-li sledovat aktivitu na úrovni tabulky, zobrazte okno na úrovni tabulky. Horní část okna zobrazuje podrobný počet řádků migrovaných při plném zatížení a přírůstkových aktualizacích. 

V dolní části okna jsou uvedeny tabulky a stručný přehled průběhu migrace.

![Čepel na úrovni stolu - rychlé shrnutí](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

Následující tabulka popisuje pole zobrazená v podrobnostech na úrovni tabulky.

| Název pole        | Popis       |
| ------------- | ------------- |
| **Úplné zatížení dokončeno**      | Počet dokončených tabulek úplného načtení dat. |
| **Plné zatížení ve frontě**      | Počet tabulek zařazených do fronty pro plné zatížení.      |
| **Plné zatížení** | Počet tabulek se nezdařil.      |
| **Přírůstkové aktualizace**      | Počet aktualizací sběru dat změn (CDC) v řádcích použitých na cíl. |
| **Přírůstkové břitové destičky**      | Počet vložky CDC v řádcích aplikovaných na cíl.      |
| **Přírůstkové odstranění** | Počet CDC odstraní v řádcích aplikovaných na cíl.      |
| **Čekající změny**      | Počet CDC v řádcích, které stále čekají na použití na cíl. |
| **Použité změny**      | Součet aktualizací CDC, vloží a odstraní v řádcích aplikovaných na cíl.      |
| **Tabulky v chybovém stavu** | Počet tabulek, které jsou ve stavu chyby během migrace. Některé příklady, které tabulky mohou přejít do chybového stavu, jsou, když jsou v cíli identifikovány duplikáty nebo data nejsou kompatibilní načítání v cílové tabulce.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Monitor na úrovni tabulky – podrobný souhrn
Existují dvě karty, které zobrazují průběh migrace v úplném načtení a přírůstkové synchronizaci dat.
    
![Karta Plné zatížení](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Karta Přírůstkové synchronizace dat](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

Následující tabulka popisuje pole zobrazená v průběhu migrace na úrovni tabulky.

| Název pole        | Popis       |
| ------------- | ------------- |
| **Stav – synchronizace**      | Je spuštěna nepřetržitá synchronizace. |
| **Vložit**      | Počet vložky CDC v řádcích aplikovaných na cíl.      |
| **Aktualizace** | Počet aktualizací CDC v řádcích použitých na cíl.      |
| **Odstranit**      | Počet CDC odstraní v řádcích aplikovaných na cíl. |
| **Celkem použito**      | Součet aktualizací CDC, vloží a odstraní v řádcích aplikovaných na cíl. |
| **Chyby dat** | V této tabulce došlo k chybám dat. Některé příklady chyb jsou *511: Nelze vytvořit řádek o velikosti %d, který je větší než přípustná maximální velikost řádku %d, 8114: Chyba při převodu datového typu %ls na %ls.*  Zákazník by se měl dotazovat z dms_apply_exceptions tabulky v cíli Azure, aby zobces viděl podrobnosti o chybě.    |

> [!NOTE]
> CDC hodnoty Vložit, Aktualizovat a odstranit a Celkem použité může snížit při přechodu databáze nebo migrace je restartován.

## <a name="next-steps"></a>Další kroky
- Přečtěte si pokyny k migraci v [Příručce pro migraci databáze](https://datamigration.microsoft.com/)společnosti Microsoft .