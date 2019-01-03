---
title: Monitorování migrace pomocí Azure Database Migration Service | Dokumentace Microsoftu
description: Naučte se používat Azure Database Migration Service monitorování migrace.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/14/2018
ms.openlocfilehash: a7fc48c149d488ab7860513b617b8a5806b1feb6
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721151"
---
# <a name="monitor-migration-activity"></a>Monitorování aktivity migrace
V tomto článku se dozvíte, jak můžete sledovat průběh migrace na úrovni databáze i na úrovni tabulky.

## <a name="monitor-at-the-database-level"></a>Monitorování na úrovni databáze
Monitorování na úrovni databáze, zobrazte okno na úrovni databáze:

![Okno na úrovni databáze](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Výběrem hypertextového odkazu databáze zobrazí v seznamu tabulek a jejich průběh migrace.

V následující tabulce jsou uvedena pole, v okně databáze a popisuje různé hodnoty stavu spojené s jednotlivými.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Název pole</strong></th>
      <th><strong>Podřízený stav pole</strong></th>
      <th><strong>Popis</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Stav aktivity</strong></td>
      <td>Spuštěno</td>
      <td>Aktivit migrace běží.</td>
    </tr>
    <tr>
      <td>Úspěch</td>
      <td>Migrace byla úspěšná bez problémů.</td>
    </tr>
    <tr>
      <td>S chybou</td>
      <td>Migrace se nezdařila. Výběrem odkazu "Podrobnosti o chybě naleznete v tématu" v části Podrobnosti o migraci kompletní chybové zprávy.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Stav</strong></td>
      <td>Inicializace</td>
      <td>DMS je nastavení se kanál migrace.</td>
    </tr>
    <tr>
      <td>Spuštěno</td>
      <td>DMS kanálu je spuštěné a provedení migrace.</td>
    </tr>
    <tr>
      <td>Dokončit</td>
      <td>Migrace byla dokončena.</td>
    </tr>
    <tr>
      <td>Selhalo</td>
      <td>Migrace se nezdařila. Klikněte na podrobnosti o migraci zobrazíte chyby při migraci.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Podrobnosti o migraci</strong></td>
      <td>Zahajuje se kanál migrace</td>
      <td>DMS je nastavení se kanál migrace.</td>
    </tr>
    <tr>
      <td>Probíhá úplné načtení dat</td>
      <td>DMS provádí počátečním načtení.</td>
    </tr>
    <tr>
      <td>Připravené pro přímou migraci</td>
      <td>Po dokončení počáteční načtení DMS označí databáze jako připravené pro přímou migraci. Uživatele zaškrtněte, pokud data zachytila průběžné synchronizace.</td>
    </tr>
    <tr>
      <td>Všechny změny použity</td>
      <td>Počátečním načtení a průběžná synchronizace se dokončí. Tento stav také vyvolá se po databáze je úspěšně přímou migraci.</td>
    </tr>
    <tr>
      <td>Zobrazit podrobnosti o chybě</td>
      <td>Kliknutím na odkaz zobrazíte podrobnosti o chybě.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Doba trvání</strong></td>
      <td>neuvedeno</td>
      <td>Celkový čas z aktivity migrace během inicializace migrace dokončeny nebo došlo k chybě migrace.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Monitorování na úrovni tabulky – stručný přehled
Monitorování na úrovni tabulek, zobrazení okna úrovni tabulky. Horní části okna zobrazuje že podrobné počet řádků migrovat v plném zatížení a přírůstkové aktualizace. 

Dolní části okna obsahuje seznam tabulek a zobrazuje rychlý přehled průběh migrace.

![Okno úrovni tabulky – stručný přehled](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

Následující tabulka popisuje pole zobrazená v úrovni tabulky Podrobnosti.

| Název pole        | Popis       |
| ------------- | ------------- |
| **Úplné načtení dokončeno**      | Počet tabulek dokončení úplné načtení dat. |
| **Úplné načtení ve frontě**      | Počet tabulek byla zařazena do fronty pro úplné načtení.      |
| **Úplné načtení** | Počet tabulek se nezdařilo.      |
| **Přírůstkové aktualizace**      | Počet změn dat zachycení aktualizace (CDC) v řádcích použitý k cíli. |
| **Přírůstkové vložení**      | Počet CDC vloží řádků, které jsou použity k cíli.      |
| **Přírůstkové odstraní** | Počet CDC odstraní v řádcích použitý k cíli.      |
| **Čekající změny**      | Počet CDC v řádcích, které stále čekají na získání použitý k cíli. |
| **Provedené změny**      | Celkový počet CDC aktualizací, vkládání a odstranění řádků použitý k cíli.      |
| **Tabulky v chybovém stavu** | Počet tabulek, které jsou ve stavu "Chyba" během migrace. Jsou některé příklady, které tabulky můžete přejít do chybového stavu, když existují duplicitní identifikované v cílové nebo data ještě nejsou kompatibilní, načítají se v cílové tabulce.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Monitorování na úrovni tabulky – podrobný souhrn
Existují dvě karty, které v plném zatížení a přírůstková synchronizace dat zobrazit průběh migrace.
    
![Karta úplné načtení](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Karta synchronizace dat](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

Následující tabulka popisuje pole zobrazená v průběhu migrace úrovně tabulky.

| Název pole        | Popis       |
| ------------- | ------------- |
| **Stav - Probíhá synchronizace**      | Průběžná synchronizace probíhá. |
| **Vložit**      | Počet CDC vloží řádků, které jsou použity k cíli.      |
| **Aktualizace** | Počet CDC aktualizace řádků použitý k cíli.      |
| **Odstranění**      | Počet CDC odstraní v řádcích použitý k cíli. |
| **Celkově použito**      | Celkový počet CDC aktualizací, vkládání a odstranění řádků použitý k cíli. |
| **Data chyby** | Počet chyb dat došlo v této tabulce. Tady je několik příkladů chyb *511: Nelze vytvořit řádek velikosti %d, což je více než povolená maximální velikost řádku % d, 8114: Při převodu datového typu %ls. % ls.*  Zákazník musí dotaz z dms_apply_exceptions tabulky v Azure cílové zobrazíte podrobnosti o chybě.    |

> [!NOTE]
> CDC hodnoty Insert, Update a Delete a celkový počet použít může snížit, pokud je přímou migraci databáze nebo byl restartován migrace.

## <a name="next-steps"></a>Další postup
- Přečtěte si pokyny k migraci v Microsoftu [Průvodce migrací databází](https://datamigration.microsoft.com/).