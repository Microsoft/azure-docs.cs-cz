---
title: Obnovení Azure SQL database ve víceklientské aplikace SaaS | Dokumentace Microsoftu
description: Zjistěte, jak obnovení jednoho tenanta SQL database po nechtěného odstranění dat
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 4059b0f979e7e6856905f1759129167d62d7b5f5
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274424"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Obnovení jednoho tenanta u SaaS aplikace databází na tenanta

Model databáze na tenanta usnadňuje obnovení jednoho tenanta do předchozího bodu v čase, aniž by to ovlivnilo ostatní tenanty.

V tomto kurzu se dozvíte, dva způsoby zotavení dat:

> [!div class="checklist"]
> * Obnovení databáze do paralelní databáze (vedle sebe).
> * Obnovení databáze na místě a nahraďte existující databázi.

|||
|:--|:--|
| Obnovit do paralelní databáze | Tento vzor lze použít pro úlohy, jako je kontrola, auditování a dodržování předpisů umožňující tenanta ke kontrole jejich data z dřívějšího bodu. Aktuální databáze tenanta zůstává online a beze změny. |
| Obnovit na místě | Tento model se obvykle používá k obnovení tenanta k dřívějšímu bodu, poté, co tenanta neúmyslně odstraní nebo poškození dat. Původní databáze je provést offline a nahradit obnovenou databází. |
|||

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasazení SaaS aplikace Wingtip. Nasazení za méně než pět minut najdete v tématu [nasazení a zkoumání SaaS aplikace Wingtip aplikace](saas-dbpertenant-get-started-deploy.md).
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v tématu [Začínáme s Azure Powershellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Úvod do principu obnovení tenanta SaaS

Existují dva jednoduché způsoby pro obnovení dat jednotlivých tenantů. Protože jsou od sebe navzájem izolované databáze tenantů, obnovení jednoho tenanta nemá žádný vliv na data žádným jiným tenantem společnosti. Funkce (PITR) bod v čase obnovení Azure SQL Database se používá v oba vzorky. PITR vždy vytvoří novou databázi.

* **Obnovit paralelně**: V první vzor je vytvořena nová databáze paralelní spolu s aktuální databáze tenanta. Tenant je pak udělen přístup jen pro čtení k obnovené databázi. Obnovená data můžete zkontrolovat a potenciálně použita přepsat aktuální hodnoty data. Záleží jen na návrháře aplikací určíte, jak klient přistupuje k obnovené databázi a jaké možnosti pro obnovení jsou k dispozici. Klientovi zkontrolujte svá data na dřívějšího bodu umožňuje jednoduše může být vše, co se v některých scénářích vyžaduje.

* **Obnovit na místě**: Druhý vzor je užitečné, pokud byl při ztrátě nebo poškození dat, a klienta chce obnovit k dřívějšímu bodu. Tenant se používá offline při obnovení databáze. Odstraní původní databáze a přejmenování obnovené databáze. Řetězce zálohy je původní databáze zůstala přístupná po odstranění, takže můžete obnovit databázi k dřívějšímu bodu v čase, v případě potřeby.

Pokud databáze používá [aktivní geografickou replikaci](sql-database-active-geo-replication.md) a obnovení paralelně, doporučujeme vám zkopírovat všechna požadovaná data z obnovené kopie do původní databáze. Pokud je původní databázi nahradit obnovenou databází, musíte znovu nakonfigurovat a provést opakovanou synchronizaci geografickou replikaci.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získání skriptů aplikace databáze na tenanta SaaS aplikace Wingtip Tickets

Wingtip Tickets SaaS Multitenant databázové skripty a zdrojový kód aplikace, které jsou k dispozici v [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Pokyny ke stažení a odblokování SaaS aplikace Wingtip Tickets skriptů, najdete v článku [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Než začnete

Když se vytvoří databáze, může trvat 10 až 15 minut, než je k dispozici pro obnovení z první úplná záloha. Pokud jste právě nainstalovali aplikaci, můžete potřebovat Počkejte několik minut, než se pokusíte tento scénář.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulovat tenanta nechtěného odstranění dat

Abychom si předvedli tyto scénáře obnovení, nejprve "nechtěného" odstraňte událost v jedné z databází tenantů. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Otevřete aplikaci události ke kontrole aktuální události

1. Otevřete Centrum událostí (http://events.wtp.&lt; uživatel&gt;. trafficmanager.net) a vyberte **Contoso koncertní Hall**.

   ![Centrum akcí](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Posuňte se seznam událostí a poznamenejte si poslední události v seznamu.

   ![Zobrazí se poslední události](media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>"Nechtěného" odstranit poslední události

1. V prostředí PowerShell ISE otevřete... \\Learning Modules\\provozní kontinuitu a zotavení po havárii\\RestoreTenant\\*Demo-RestoreTenant.ps1*a nastavte následující hodnotu:

   * **$DemoScenario** = **1**, *odstranit poslední události (s žádné prodej lístků)*.
2. Stisknutím klávesy F5 spusťte skript a odstranit poslední události. Zobrazí se následující potvrzující zpráva:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Otevře se stránka Contoso události. Posuňte se dolů a ověřte, že událost je pryč. Pokud události je stále v seznamu, vyberte **aktualizovat** a ověřte, že zmizí.
   ![Odebrat poslední události](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Obnovení databáze tenanta paralelně s produkční databází

V tomto cvičení obnoví databázi Contoso koncertní Hall do bodu v čase před odstraněním události. Tento scénář předpokládá, že chcete zkontrolovat odstraněná data v databázi paralelní.

 *Obnovení TenantInParallel.ps1* skript vytvoří paralelní tenanta databázi s názvem *ContosoConcertHall\_staré*, s položkou paralelní katalogu. Tento model obnovení je nejvhodnější pro obnovení z menších únikem. Také můžete použít tento model Pokud je potřeba zkontrolovat data auditování účely nebo dodržování předpisů. Je doporučený postup při použití [aktivní geografickou replikaci](sql-database-active-geo-replication.md).

1. Dokončení [simulovat tenanta nechtěného odstranění dat](#simulate-a-tenant-accidentally-deleting-data) oddílu.
2. V prostředí PowerShell ISE otevřete... \\Learning Modules\\provozní kontinuitu a zotavení po havárii\\RestoreTenant\\_Demo-RestoreTenant.ps1_.
3. Nastavte **$DemoScenario** = **2**, *obnovení tenanta paralelně*.
4. Stisknutím klávesy F5 spusťte skript.

Skript obnoví databáze tenantů do bodu v čase před odstraněním události. Obnovení databáze do nové databáze s názvem _ContosoConcertHall\_staré_. Odstranit katalog metadat, která existuje v obnovené databázi a pak je databáze přidaných do katalogu pomocí klíče se vytvářejí na základě *ContosoConcertHall\_staré* název.

Ukázkový skript v prohlížeči otevře stránku události pro tuto novou databázi tenanta. Poznámka: z adresy URL ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` , že tato stránka zobrazuje data z obnovené databáze kde *_old* se přidá k názvu.

Posuňte se události uvedené v prohlížeči k potvrzení, že se obnovila události odstraní v předchozí části.

Vystavení obnovené tenanta jako další tenant, s vlastní události aplikace, je pravděpodobné, aby, jak poskytnout přístup klienta k obnovená data. Slouží k objasnění model obnovení. Obvykle poskytují přístup jen pro čtení k původní data a zachovat obnovené databáze za definované období. Ve vzorku, můžete odstranit položku obnovené tenanta, až dokončíte spuštěním _odebrat obnovit tenanta_ scénář.

1. Nastavte **$DemoScenario** = **4**, *odebrat obnovit tenanta*.
2. Stisknutím klávesy F5 spusťte skript.
3. *ContosoConcertHall\_staré* položku je nyní odstranit z katalogu. Zavřete stránku události pro tohoto tenanta v prohlížeči.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Obnovení tenanta na místě a nahraďte existující databáze tenantů

V tomto cvičení obnoví Contoso koncertní Hall tenanta do bodu před odstraněním události. *Obnovení TenantInPlace* obnoví databáze tenantů do nové databáze skriptu a odstraní původní. Tento model obnovení je nejvhodnější pro obnovení z závažné poškození a tenanta může být nutné přizpůsobit významné datové ztráty.

1. V prostředí PowerShell ISE, otevřete **Demo-RestoreTenant.ps1** souboru.
2. Nastavte **$DemoScenario** = **5**, *obnovení tenanta na místě*.
3. Stisknutím klávesy F5 spusťte skript.

Skript obnoví databáze tenantů do bodu před odstraněním události. Nejprve trvá tenanta organizace Contoso koncertní offline aby se zabránilo dalším aktualizace. Paralelní databáze se vytvoří pomocí obnovení z bodu obnovení. S časovým razítkem název obnovené databáze zajistit, aby že název databáze není v konfliktu s existující název databáze tenanta. V dalším kroku se odstraní staré databáze tenanta a přejmenování obnovené databáze na původní název databáze. Nakonec Hall Contoso spolupracuje se znovu přepne online pro povolení přístupu aplikace k obnovené databázi.

Úspěšně jste obnovili databáze do bodu v čase před odstraněním události. Když **události** otevře se stránka, zkontrolujte, zda byla obnovena poslední události.

Po obnovení databáze trvá jiného 10 až 15 minut, než se bude první úplná záloha je k dispozici pro obnovení z znovu.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Obnovení databáze do paralelní databáze (vedle sebe).
> * Obnovení databáze na místě.

Zkuste [schéma databáze tenanta spravovat](saas-tenancy-schema-management.md) kurzu.

## <a name="additional-resources"></a>Další zdroje informací:

* [Další kurzy, které staví na aplikace SaaS aplikace Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Přehled kontinuity se službou Azure SQL Database](sql-database-business-continuity.md)
* [Další informace o zálohování SQL Database](sql-database-automated-backups.md)
