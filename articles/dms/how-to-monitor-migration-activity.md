---
title: Monitorování aktivity migrace – Azure Database Migration Service
description: Naučte se používat Azure Database Migration Service k monitorování aktivity migrace.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: dc9123735ef65bec89e99a14d25cba881be306fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91297466"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Monitorování aktivity migrace pomocí Azure Database Migration Service
V tomto článku se dozvíte, jak monitorovat průběh migrace na úrovni databáze i na úrovni tabulky.

## <a name="monitor-at-the-database-level"></a>Monitorování na úrovni databáze
Chcete-li monitorovat aktivitu na úrovni databáze, zobrazte okno na úrovni databáze:

![Okno na úrovni databáze](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Kliknutím na hypertextový odkaz databáze zobrazíte seznam tabulek a průběh migrace.

V následující tabulce jsou uvedena pole v okně na úrovni databáze a popisuje různé stavové hodnoty, které jsou k nim přidruženy.

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
      <td>Aktivita migrace je spuštěná.</td>
    </tr>
    <tr>
      <td>Úspěšný</td>
      <td>Migrace se úspěšně zdařila bez problémů.</td>
    </tr>
    <tr>
      <td>Došlo chybě</td>
      <td>Migrace se nezdařila. V části Podrobnosti o migraci pro úplnou chybovou zprávu vyberte odkaz Zobrazit podrobnosti o chybě.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Stav</strong></td>
      <td>Initializing</td>
      <td>DMS nastavuje kanál migrace.</td>
    </tr>
    <tr>
      <td>Spuštěno</td>
      <td>Kanál DMS je spuštěný a provádí migraci.</td>
    </tr>
    <tr>
      <td>Dokončit</td>
      <td>Migrace se dokončila.</td>
    </tr>
    <tr>
      <td>Neúspěšný</td>
      <td>Migrace se nezdařila. Kliknutím na podrobnosti o migraci zobrazíte chyby migrace.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Podrobnosti o migraci</strong></td>
      <td>Inicializuje se kanál migrace.</td>
      <td>DMS nastavuje kanál migrace.</td>
    </tr>
    <tr>
      <td>Probíhá úplné zatížení dat</td>
      <td>DMS provádí počáteční zatížení.</td>
    </tr>
    <tr>
      <td>Připraveno pro přímou migraci</td>
      <td>Po dokončení počátečního načtení bude DMS označit databázi jako připravenou pro přímou migraci. Uživatel by měl kontrolovat, jestli se data při průběžné synchronizaci zachytila.</td>
    </tr>
    <tr>
      <td>Všechny změny aplikovány</td>
      <td>Počáteční zatížení a průběžná synchronizace jsou dokončeny. K tomuto stavu dochází také po úspěšném přímou migraci databáze.</td>
    </tr>
    <tr>
      <td>Zobrazit podrobnosti o chybě</td>
      <td>Kliknutím na odkaz zobrazíte podrobnosti o chybě.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Doba trvání</strong></td>
      <td>–</td>
      <td>Celková doba z migrační aktivity migrace do migrace byla dokončena nebo došlo k chybě migrace.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Monitorování na úrovni tabulky – rychlý přehled
Chcete-li monitorovat aktivitu na úrovni tabulky, zobrazte okno na úrovni tabulky. V horní části okna se zobrazuje podrobný počet řádků migrovaných v části úplné zatížení a přírůstkové aktualizace. 

V dolní části okna jsou uvedené tabulky a zobrazí se rychlý souhrn průběhu migrace.

![Okno na úrovni tabulky – rychlý souhrn](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

Následující tabulka popisuje pole zobrazená v podrobnostech na úrovni tabulky.

| Název pole        | Popis       |
| ------------- | ------------- |
| **Úplné načtení dokončeno**      | Počet tabulek dokončil úplné načtení dat. |
| **Úplné načtení ve frontě**      | Počet tabulek, které jsou zařazeny do fronty pro úplné načtení.      |
| **Úplné načtení zatížení** | Počet tabulek se nezdařil.      |
| **Přírůstkové aktualizace**      | Počet aktualizací Change Data Capture (CDC) v řádcích použitých na cíl |
| **Přírůstkové vkládání**      | Počet vkládání funkce CDC v řádcích použitých na cíl      |
| **Přírůstková odstranění** | Počet odstranění CDC v řádcích použitých na cíl      |
| **Probíhající změny**      | Počet CDC v řádcích, které stále čekají na použití pro cíl. |
| **Použité změny**      | Celkový počet aktualizací CDC, vkládání a odstranění v řádcích použitých pro cíl      |
| **Tabulky v chybovém stavu** | Počet tabulek, které jsou ve stavu Error během migrace. Některé příklady, které mohou tabulky přejít do chybového stavu, jsou v případě, že jsou v cíli zjištěny duplicity nebo data nejsou kompatibilní při načítání v cílové tabulce.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Monitorování na úrovni tabulky – podrobný souhrn
K dispozici jsou dvě karty, které znázorňují průběh migrace v plném zatížení a přírůstkové synchronizaci dat.
    
![Karta úplné načtení](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Karta pro přírůstkové synchronizace dat](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

Následující tabulka popisuje pole zobrazená v průběhu migrace na úrovni tabulky.

| Název pole        | Popis       |
| ------------- | ------------- |
| **Stav – synchronizace**      | Průběžná synchronizace je spuštěná. |
| **Insert**      | Počet vkládání funkce CDC v řádcích použitých na cíl      |
| **Aktualizace** | Počet aktualizací CDC v řádcích použitých na cíl      |
| **Odstranit**      | Počet odstranění CDC v řádcích použitých na cíl |
| **Celkem použito**      | Celkový počet aktualizací CDC, vkládání a odstranění v řádcích použitých pro cíl |
| **Chyby dat** | Počet chyb dat, které byly v této tabulce provedeny. Některé příklady chyb jsou *511: nelze vytvořit řádek o velikosti% d, což je více než maximální povolená velikost řádku% d, 8114: Chyba při převodu datového typu% ls na% ls.*  Zákazník by měl dotaz z dms_apply_exceptions tabulky v cíli Azure zobrazit podrobnosti o chybě.    |

> [!NOTE]
> Hodnoty CDC v případě vložení, aktualizace a odstranění a celkem se můžou snížit, když je databáze přímou migraci nebo se restartuje migrace.

## <a name="next-steps"></a>Další kroky
- Přečtěte si pokyny k migraci v [Průvodci migrací databáze](https://datamigration.microsoft.com/)společnosti Microsoft.