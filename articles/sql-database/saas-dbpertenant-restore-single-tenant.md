---
title: Obnovení databáze Azure SQL v víceklientské aplikace SaaS | Microsoft Docs
description: Postup obnovení databáze SQL jednoho klienta po omylem odstraňování dat
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 319195ceb98b5b760d4d92055faa6e09c8fb0e3d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644344"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Obnovení jednoho klienta s aplikací SaaS databáze za klienta

Model databáze za klienta snadno obnovit jednoho klienta do předchozího bodu v čase bez ovlivnění jiných klientů.

V tomto kurzu zjistíte, dva vzory obnovení dat:

> [!div class="checklist"]

> * Obnovení databáze do paralelní databáze (vedle sebe).
> * Obnovení databáze na místě, nahraďte existující databáze.


|||
|:--|:--|
| Obnovit do paralelní databáze | Tento vzor slouží pro úlohy, jako je kontrola, auditování a dodržování předpisů umožňující klienta ke kontrole svá data z dřívějšího bodu. Aktuální databáze klienta zůstane online a beze změny. |
| Obnovit na místě | Tento vzor se obvykle používá k obnovení klienta do dřívějšího bodu, poté, co klient omylem odstraní nebo poškození dat. Původní databáze je provést v režimu offline a nahradí obnovenou databázi. |
|||

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Adresář Wingtip SaaS aplikace je nasazená. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s aplikací Wingtip SaaS](saas-dbpertenant-get-started-deploy.md).
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v tématu [Začínáme s Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Úvod do vzory obnovení klienta SaaS

Existují dva jednoduché vzory pro obnovení dat jednoho klienta. Protože jsou od sebe navzájem oddělené klienta databáze, obnovení jednoho klienta nemá žádný vliv na data žádným jiným klientem na. Funkci Azure SQL Database bodu v době obnovení (Možnosti PITR) se používá v obou vzory. Možnosti PITR vždycky vytvoří novou databázi.   

* **Obnovení paralelně**: V prvním vzoru, je vytvořena nová databáze paralelní spolu s aktuální databáze klienta. Klient je pak poskytnut přístup jen pro čtení k obnovené databáze. Obnovená data můžete zkontrolovat a potenciálně používá k přepsání aktuálních hodnot data. Je to na návrháře aplikace k určení, jak klient přistupuje k obnovené databáze a jaké možnosti pro obnovení jsou k dispozici. Jednoduše klientovi zkontrolujte svá data v dřívějšího bodu umožňuje může být všechno, co je potřeba v některých scénářích. 

* **Obnovit na místě**: vzoru druhý je užitečné, pokud byl ztrátě nebo poškození dat, a chce vrátit do dřívějšího bodu klienta. Klient se pořídí režimu offline za běhu byla databáze obnovena. Původní databáze, se odstraní a obnovené databáze je přejmenována. Řetěz záloh původní databáze zůstane přístupná po odstranění, abyste mohli obnovit databázi do dřívějšího bodu v čase, v případě potřeby.

Pokud se databáze používá [geografická replikace](sql-database-geo-replication-overview.md) a obnovují souběžně, doporučujeme, zkopírujte všechna požadovaná data z obnovené zkopírujte do původní databáze. Pokud je původní databáze nahradit obnovenou databázi, budete muset překonfigurovat a poté opakujte synchronizaci geografická replikace.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získat skripty databáze za klienta aplikace SaaS Wingtip lístky

Adresář Wingtip lístky SaaS víceklientské databázové skripty a zdrojový kód aplikace, které jsou k dispozici v [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Pokyny ke stažení a odblokování skripty Wingtip lístky SaaS, najdete v části [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Než začnete

Vytvoření databáze, může trvat 10 až 15 minut, než je k dispozici pro obnovení z první úplné zálohování. Pokud jste právě nainstalovali aplikaci, možná budete muset Počkejte několik minut, než se pokusíte tento scénář.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulovat klienta omylem odstraňování dat

K předvedení tyto scénáře obnovení, nejprve "omylem" odstraníte události v jedné z databází klienta. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Otevřete aplikaci události ke kontrole aktuální události

1. Otevřete Centrum událostí (http://events.wtp.&lt; uživatele&gt;. trafficmanager.net) a vyberte **Contoso vzájemné součinnosti Hall**.

   ![Centra událostí](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Posuňte se seznam událostí a poznamenejte si poslední událost v seznamu.

   ![Zobrazí se poslední událost](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="accidentally-delete-the-last-event"></a>"Omylem" odstranit poslední události

1. V prostředí PowerShell ISE otevřete... \\Learning moduly\\kontinuity podnikových procesů a zotavení po havárii\\RestoreTenant\\*ukázku RestoreTenant.ps1*a nastavte následující hodnotu:

   * **$DemoScenario** = **1**, *odstranit poslední událost (s žádné prodeje lístků)*.
2. Stisknutím klávesy F5 spusťte skript a odstranit poslední událost. Zobrazí se následující potvrzující zpráva:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Otevře se stránka události Contoso. Přejděte dolů a ověřte, zda je událost pryč. Pokud tato událost je stále v seznamu, vyberte **aktualizovat** a ověřte, že přešel.

   ![Odebrat poslední událost](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Obnovení databáze klienta paralelně s provozní databáze

Toto cvičení obnoví databázi Hall vzájemné součinnosti Contoso do bodu v čase před událost byla odstraněna. Tento scénář předpokládá, že chcete zkontrolovat odstraněná data v databázi paralelní.

 *Obnovení TenantInParallel.ps1* skript vytvoří paralelní klienta databáze s názvem *ContosoConcertHall\_staré*, s položkou paralelní katalogu. Tento vzor obnovení je nejvhodnější pro obnovení před ztrátou dat menší. Také můžete použít tento vzor Pokud potřebujete posoudit data pro dodržování předpisů nebo auditování. Je doporučeným přístupem, když používáte [geografická replikace](sql-database-geo-replication-overview.md).

1. Dokončení [simulovat klienta omylem odstraní data](#simulate-a-tenant-accidentally-deleting-data) části.
2. V prostředí PowerShell ISE otevřete... \\Učení moduly\\kontinuity podnikových procesů a zotavení po havárii\\RestoreTenant\\_ukázku RestoreTenant.ps1_.
3. Nastavit **$DemoScenario** = **2**, *obnovení klienta paralelně*.
4. Pokud chcete spustit skript, stisknutím klávesy F5.

Skript obnoví databázi klienta k bodu v čase před odstranit událost. Obnovení databáze pro novou databázi s názvem _ContosoConcertHall\_staré_. Je Odstraněná katalogu metadata, která existuje v této obnovené databáze a pak je databáze přidaných do katalogu pomocí klíče zkonstruován z *ContosoConcertHall\_staré* název.

Ukázkový skript se otevře stránka události pro tuto novou databázi klienta v prohlížeči. Poznámka: z adresy URL ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` této stránce se zobrazují data z obnovené databáze kde *_old* se přidá k názvu.

Posuňte se události uvedené v prohlížeči potvrďte, že byla obnovena události odstranit v předchozí části.

Vystavení obnovené klienta jako další klienta, s vlastní aplikace události je nepravděpodobné, že se, jak poskytnout přístup klientů k obnovená data. Slouží k objasnění vzoru obnovení. Obvykle poskytnout přístup jen pro čtení k původní data a zachovat obnovenou databázi za definované období. V ukázce můžete odstranit položku obnovené klienta po dokončíte spuštěním _odebrat obnovit klienta_ scénář.

1. Nastavit **$DemoScenario** = **4**, *odebrat obnovit klienta*.
2. Pokud chcete spustit skript, stisknutím klávesy F5.
3. *ContosoConcertHall\_staré* položka je nyní odstraněny z katalogu. Zavřete stránku události pro tohoto klienta v prohlížeči.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Obnovení klienta v místě, nahraďte existující databáze klienta

Toto cvičení obnoví Contoso vzájemné součinnosti Hall klienta k bodu před událost byla odstraněna. *Obnovení TenantInPlace* skriptu obnoví databázi klienta pro novou databázi a odstraní původní. Tento vzor obnovení je nejvhodnější pro obnovení z vážné poškození dat. a aby dokázala pojmout větší ztrátě dat. může mít klienta.

1. V prostředí PowerShell ISE, otevřete **ukázku RestoreTenant.ps1** souboru.
2. Nastavit **$DemoScenario** = **5**, *obnovení klienta na místě*.
3. Pokud chcete spustit skript, stisknutím klávesy F5.

Skript obnoví databázi klienta k bodu před událost byla odstraněna. Nejprve trvá klientovi Contoso vzájemné součinnosti Hall režimu offline. aby se zabránilo dalším aktualizace. Paralelní databáze je pak vytvořena obnovení z bodu obnovení. Obnovenou databázi jmenuje s časovým razítkem a ujistěte se, že název databáze není v konfliktu s existující databáze název klienta. V dalším kroku staré databáze klienta se odstraní a obnovené databáze je přejmenována na původní název databáze. Nakonec Contoso vzájemné součinnosti Hall je uvést do režimu online pro povolení přístupu aplikace k obnovené databáze.

Úspěšně jste obnovili databázi do bodu v čase před událost byla odstraněna. Když **události** otevře se stránka, zkontrolujte, zda byla obnovena poslední události.

Po obnovení databáze trvá jiné 10 až 15 minut, než je k dispozici pro obnovení z znovu první úplné zálohování. 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]

> * Obnovení databáze do paralelní databáze (vedle sebe).
> * Obnovení databáze na místě.

Zkuste [schéma databáze klienta spravovat](saas-tenancy-schema-management.md) kurzu.

## <a name="additional-resources"></a>Další zdroje informací:

* [Další kurzy, které staví na adresář Wingtip SaaS aplikace](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Přehled kontinuity podnikových procesů s Azure SQL Database](sql-database-business-continuity.md)
* [Další informace o zálohování databáze SQL](sql-database-automated-backups.md)
