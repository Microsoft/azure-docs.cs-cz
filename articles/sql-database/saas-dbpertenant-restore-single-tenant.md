---
title: "Obnovení databáze Azure SQL v aplikaci SaaS víceklientské | Microsoft Docs"
description: "Postup obnovení databáze SQL jeden klienty po omylem odstraňování dat"
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 05/10/2017
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 7ae8bcb6172d9f9d56c531e149635434057fc2af
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Obnovení jednoho klienta s databází na každého klienta aplikace SaaS

Databáze pro každý model díky klienta je snadno provedete obnovení jednoho klienta do předchozího bodu v čase bez dopadu na ostatních klientů.

V tomto kurzu zjistíte, dva vzory obnovení dat:

> [!div class="checklist"]

> * Obnovení databáze do paralelní databáze (-souběžného)
> * Obnovení databáze na místě, nahraďte existující databáze


|||
|:--|:--|
| **Obnovit do paralelní databáze** | Tento vzor slouží ke kontrole, auditování, dodržování předpisů, umožňující klienta ke kontrole svá data z dřívějšího bodu atd.  Aktuální databáze klienta zůstane online a beze změny. |
| **Obnovit na místě** | Tento vzor se obvykle používá k obnovení klienta do dřívějšího bodu, poté, co klient omylem odstraní nebo poškození dat. Původní databáze je uveden do režimu offline a nahradit obnovenou databázi. |
|||

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Adresář Wingtip SaaS aplikace je nasazená. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s Wingtip SaaS aplikace](saas-dbpertenant-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Úvod do vzory obnovení klienta SaaS

Existují dva jednoduché vzory pro obnovení dat jednoho klienta. Protože jsou od sebe navzájem oddělené klienta databáze, obnovení jednoho klienta nemá žádný vliv na data žádným jiným klientem na.  Funkci SQL Database bodu v době obnovení (Možnosti PITR) se používá v obou vzory.  Možnosti PITR vždycky vytvoří novou databázi.   

V prvním vzoru **obnovit paralelně**, je vytvořena nová databáze paralelní spolu s aktuální databáze klienta. Klient je pak poskytnut přístup jen pro čtení k obnovené databáze. Obnovená data můžete zkontrolovat a potenciálně používá k přepsání aktuálních hodnot data. Je to na návrháře aplikace k určení, jak klient přistupuje k obnovené databáze a jaké možnosti pro obnovení jsou k dispozici. Jednoduše klientovi zkontrolujte svá data v dřívějšího bodu umožňuje může být všechno, co je potřeba v některých scénářích. 

Druhý vzor **obnovit na místě**, je užitečné, pokud byla ztracena nebo poškozená data, a chce vrátit do dřívějšího bodu klienta.  Klient je převedeny do režimu offline, když byla databáze obnovena. Původní databáze, se odstraní a obnovené databáze je přejmenována. Řetěz záloh původní databáze zůstane přístupná po odstranění, což umožňuje v případě potřeby obnovit databázi do dřívějšího bodu v čase.

Pokud se databáze používá [geografická replikace](sql-database-geo-replication-overview.md) a obnovují souběžně, doporučujeme, abyste kopírování všechna požadovaná data z obnovené kopie do původní databáze. Pokud je původní databáze nahradit obnovenou databázi, budete muset překonfigurovat a poté opakujte synchronizaci geografická replikace.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získat aplikační skripty Wingtip lístky SaaS databáze za klienta

Adresář Wingtip lístky SaaS víceklientské databázové skripty a zdrojový kód aplikace, které jsou k dispozici v [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty Wingtip lístky SaaS.

## <a name="before-you-start"></a>Než začnete

Vytvoření databáze, může trvat 10 až 15 minut, než je k dispozici pro obnovení z první úplné zálohování.  Pokud jste nainstalovali pouze jenom aplikace, kterou budete muset Počkejte několik minut, než se pokusíte tento scénář.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulovat klienta omylem odstraňování dat

K předvedení tyto scénáře obnovení nejprve *'omylem* odstranit události v jedné z databází klienta. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Otevřete aplikaci události ke kontrole aktuální události

1. Otevřete *události rozbočovače* (http://events.wtp.&lt; Uživatel&gt;. trafficmanager.net) a klikněte na tlačítko **Contoso vzájemné součinnosti Hall**:

   ![centrum akcí](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

1. Posuňte se seznam událostí a poznamenejte si poslední událost v seznamu:

   ![poslední událost](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="accidentally-delete-the-last-event"></a>'Omylem odstranit poslední události

1. Otevřete... \\Učení moduly\\kontinuity podnikových procesů a zotavení po havárii\\RestoreTenant\\*ukázku RestoreTenant.ps1* v *prostředí PowerShell ISE*a nastavte následující hodnotu:
   * **$DemoScenario** = **1**, odstranit poslední událost (s žádné prodeje lístků).
1. Stiskněte klávesu **F5** a spusťte skript odstranit poslední událost. Byste měli vidět následující potvrzující zpráva:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. Otevře se stránka události Contoso. Posuňte se dolů a ověřte, zda že je událost pryč. Pokud tato událost je stále v seznamu, klikněte na tlačítko Aktualizovat a ověřte, zda že je pryč.

   ![poslední událost](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Obnovení databáze klienta paralelně s provozní databáze

Toto cvičení obnoví databázi Hall vzájemné součinnosti Contoso do bodu v čase před událost byla odstraněna. V tomto scénáři se předpokládá, že chcete jenom zkontrolovat odstraněná data v databázi paralelní.

 *Obnovení TenantInParallel.ps1* skript vytvoří paralelní klienta databáze s názvem *ContosoConcertHall\_staré*, s položkou paralelní katalogu. Tento vzor obnovení je nejvhodnější pro obnovení z menší únikem, nebo pokud potřebujete posoudit data pro dodržování předpisů nebo účely auditování. Je také o doporučený postup při použití [geografická replikace](sql-database-geo-replication-overview.md).

1. Dokončení [simulovat klienta omylem odstraní data](#simulate-a-tenant-accidentally-deleting-data) části.
1. V *prostředí PowerShell ISE*, otevřete... \\Učení moduly\\kontinuity podnikových procesů a zotavení po havárii\\RestoreTenant\\_ukázku RestoreTenant.ps1_.
1. Nastavit **$DemoScenario** = **2**, *obnovení klienta paralelně*.
1. Stisknutím klávesy **F5** spusťte skript.

Skript obnoví databázi klienta k bodu v čase před odstranit událost. Obnovení databáze do nové databáze s názvem _ContosoConcertHall\_staré_. Je Odstraněná katalogu metadata, která existuje v této obnovené databáze a pak je databáze přidaných do katalogu pomocí klíče zkonstruován z *ContosoConcertHall\_staré* název.

Ukázkový skript se otevře stránka události pro tuto novou databázi klienta v prohlížeči. Poznámka: z adresy URL: ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` , tato stránka se zobrazuje data z obnovené databáze kde *_old* se přidá k názvu.

Posuňte se události uvedené v prohlížeči potvrďte, že byla obnovena události odstranit v předchozí části.

Všimněte si, že vystavení obnovené klienta jako další klienta, s vlastní aplikace události je nepravděpodobné, že se, jak by poskytují klientovi přístup k obnovení dat, ale slouží k objasnění vzoru obnovení. Ve skutečnosti by pravděpodobně poskytnout přístup jen pro čtení k původní data a zachovat tato obnovená databáze za definované období. V ukázce můžete odstranit položku obnovené klienta po dokončení spuštěním _odebrat obnovit klienta_ scénář.

1. Nastavit **$DemoScenario** = **4**, *odebrat obnovit klienta*
1. **Spuštění** **pomocí** **F5**
1. *ContosoConcertHall\_staré* položka je nyní odstraněny z katalogu. Pokračujte a zavřete stránku události pro tohoto klienta v prohlížeči.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Obnovení klienta v místě, nahraďte existující databáze klienta

Toto cvičení obnoví Contoso vzájemné součinnosti Hall klienta k bodu před událost byla odstraněna. *Obnovení TenantInPlace* skriptu obnoví databázi klienta pro novou databázi a odstraní původní.  Tento vzor obnovení je nejvhodnější pro obnovení z poškození závažné dat jako může být důležité datových ztrát klienta bude muset přizpůsobit.

1. Otevřete **ukázku RestoreTenant.ps1** souboru v integrovaném Skriptovacím prostředí PowerShell
1. Nastavit **$DemoScenario** = **5**, *obnovení klienta na místě*.
1. Spuštění pomocí **F5**.

Skript obnoví databázi klienta k bodu před událost byla odstraněna. Aby se zabránilo další aktualizace trvá nejprve Contoso vzájemné součinnosti Hall klienta do offline režimu. Paralelní databáze je pak vytvořena obnovení z bodu obnovení.  Obnovenou databázi jmenuje s časovým razítkem zajistit, že název databáze není v konfliktu s existující databáze název klienta. V dalším kroku staré databáze klienta se odstraní a obnovené databáze je přejmenována na původní název databáze. Nakonec Contoso vzájemné součinnosti Hall je uvést do režimu online pro povolení přístupu aplikace k obnovené databáze.

Úspěšně jste obnovili databázi do bodu v čase před událost byla odstraněna. Otevře stránku události, takže potvrďte poslední událost byla obnovena.

Všimněte si, že po obnovení databáze bude trvat dalších 10 až 15 minut před první úplné zálohování je k dispozici pro obnovení z znovu. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]

> * Obnovení databáze do paralelní databáze (-souběžného)
> * Obnovení databáze na místě

[Správa schématu databáze klienta](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>Další zdroje informací:

* [Další kurzy, které stavět na adresář Wingtip SaaS aplikace](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Přehled kontinuity podnikových procesů s Azure SQL Database](sql-database-business-continuity.md)
* [Další informace o zálohování databáze SQL](sql-database-automated-backups.md)
