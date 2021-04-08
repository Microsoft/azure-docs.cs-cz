---
title: Obnovení databáze ve víceklientské aplikaci SaaS
description: Přečtěte si, jak obnovit Azure SQL Database jednoho tenanta po nechtěném odstranění dat.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 88496a39b0186cefb7c64e227530b5d73e693094
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780474"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Obnovení jednoho tenanta s aplikací SaaS Database na tenanta
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Model databáze na tenanta usnadňuje obnovení jednoho tenanta k dřívějšímu bodu v čase, aniž by to ovlivnilo ostatní klienty.

V tomto kurzu se seznámíte se dvěma vzory obnovení dat:

> [!div class="checklist"]
> * Obnovení databáze do paralelní databáze (vedle sebe).
> * Obnovte databázi na místě a nahraďte stávající databázi.

| Vzor | Description |
|:--|:--|
| Obnovení do paralelní databáze | Tento model se dá použít pro úlohy, jako je například kontrola, auditování a dodržování předpisů, aby mohl tenant prozkoumat data z dřívějšího bodu. Aktuální databáze tenanta zůstane online a nezměněná. |
| Obnovení na místě | Tento model se obvykle používá k obnovení tenanta do dřívějšího bodu, poté, co tenant omylem odstraní nebo poškodí data. Původní databáze je mimo řádek a nahrazuje obnovenou databází. |
|||

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Aplikace Wingtip SaaS je nasazená. K nasazení během méně než pěti minut si přečtěte téma [nasazení a prozkoumání aplikace Wingtip SaaS](./saas-dbpertenant-get-started-deploy.md).
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v tématu [Začínáme s Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Úvod ke vzorům obnovení tenanta SaaS

Existují dva jednoduché vzory pro obnovení dat jednotlivých klientů. Vzhledem k tomu, že databáze tenantů jsou od sebe vzájemně izolované, obnovení jednoho tenanta nemá žádný vliv na data ostatních klientů. V obou vzorcích se používá funkce Azure SQL Databaseho bodu v čase obnovení (PITR). PITR vždy vytvoří novou databázi.

* **Paralelní obnovení**: v prvním vzoru se vytvoří nová paralelní databáze společně s aktuální databází klienta. Tenant pak bude mít k obnovené databázi přístup jen pro čtení. Obnovená data lze zkontrolovat a potenciálně použít k přepsání aktuálních hodnot dat. K určení toho, jak tenant přistupuje k obnovené databázi a jaké možnosti pro obnovení, je k dispozici návrhář aplikace. V některých scénářích může být vše, co je potřeba, jenom v případě, že je možné, aby tenant zkontroloval svá data ve starším bodě.

* **Obnovení na místě**: Druhý vzor je užitečný, pokud došlo ke ztrátě nebo poškození dat a tenant se chce vrátit k dřívějšímu bodu. Klient je při obnovení databáze mimo řádek vypnut. Původní databáze je odstraněna a obnovená databáze je přejmenována. Řetězec zálohy původní databáze zůstane po odstranění přístupný, takže v případě potřeby můžete databázi obnovit do dřívějšího bodu v čase.

Pokud databáze používá [aktivní geografickou replikaci](active-geo-replication-overview.md) a souběžné obnovování, doporučujeme zkopírovat požadovaná data z obnovené kopie do původní databáze. Pokud nahradíte původní databázi obnovenou databází, je nutné překonfigurovat a znovu synchronizovat geografickou replikaci.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získání skriptů aplikace Wingtip Tickets SaaS Database – pro klienty

V úložišti GitHubu [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) jsou k dispozici skripty SaaS víceklientské databáze a zdrojový kód aplikace Wingtip Tickets. Postup pro stažení a odblokování SaaSch lístků Wingtip Tickets najdete v tématu [Obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Než začnete

Když je databáze vytvořena, může trvat 10 až 15 minut, než bude k dispozici první úplná záloha pro obnovení z. Pokud jste právě nainstalovali aplikaci, může být nutné počkat několik minut, než se pokusíte tento scénář vyzkoušet.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulace neúmyslného odstranění dat u tenanta

Chcete-li předvést tyto scénáře obnovení, nejprve "omylem" odstraňte událost v jedné z databází tenanta. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Otevřete aplikaci události a zkontrolujte aktuální události.

1. Otevřete centrum událostí ( http://events.wtp.&lt ; User &gt; . trafficmanager.NET) a vyberte Contoso IoT **hala**.

   ![Centrum událostí](./media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Posuňte se do seznamu událostí a poznamenejte si poslední událost v seznamu.

   ![Zobrazí se poslední událost.](./media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>"Omylem" odstraníte poslední událost

1. V prostředí PowerShell ISE otevřete... \\ Výukové moduly \\ pro provozní kontinuitu a zotavení po havárii \\ RestoreTenant \\ *Demo-RestoreTenant.ps1* a nastavte následující hodnotu:

   * **$DemoScenario**  =  **1**, *odstranit poslední událost (bez prodeje lístku)*.
2. Stisknutím klávesy F5 spusťte skript a odstraňte poslední událost. Zobrazí se následující potvrzovací zpráva:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Otevře se stránka pro události společnosti Contoso. Posuňte se dolů a ověřte, že událost zmizela. Pokud je událost stále v seznamu, vyberte **aktualizovat** a ověřte, že je pryč.
   ![Poslední událost odebrána](./media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Paralelní obnovení databáze tenanta pomocí provozní databáze

Toto cvičení obnoví databázi společnosti Contoso v rámci databáze Contoso v čase před odstraněním události. V tomto scénáři se předpokládá, že chcete zkontrolovat Odstraněná data v paralelní databázi.

 Skript *Restore-TenantInParallel.ps1* vytvoří paralelní databázi tenanta s názvem *ContosoConcertHall \_ Old* s položkou paralelního katalogu. Tento vzor obnovení se nejlépe hodí pro obnovu z menší ztráty dat. Tento model můžete použít také v případě, že potřebujete zkontrolovat data pro účely dodržování předpisů nebo auditování. Je to doporučený postup, pokud používáte [aktivní geografickou replikaci](active-geo-replication-overview.md).

1. Dokončete [simulaci oddílu data neúmyslně odstranit klienta](#simulate-a-tenant-accidentally-deleting-data) .
2. V prostředí PowerShell ISE otevřete... \\ Výukové moduly \\ pro provozní kontinuitu a zotavení po havárii \\ RestoreTenant \\ _Demo-RestoreTenant.ps1_.
3. Nastavte **$DemoScenario**  =  **2**, *paralelně obnovte tenanta*.
4. Pokud chcete skript spustit, stiskněte klávesu F5.

Skript obnoví databázi tenanta k určitému bodu v čase před odstraněním události. Databáze je obnovena do nové databáze s názvem _ContosoConcertHall \_ Old_. Metadata katalogu, která existují v této obnovené databázi, se odstraní a pak se do katalogu přidá databáze pomocí klíče vytvořeného z *ContosoConcertHall \_ starého* názvu.

Ukázkový skript otevře stránku události pro tuto novou databázi tenanta v prohlížeči. Poznámka z adresy URL ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` , kterou tato stránka zobrazuje data z obnovené databáze, do které se přidají *_old* do názvu.

Posuňte události uvedené v prohlížeči, abyste potvrdili, že se událost Odstraněná v předchozí části obnovila.

Vystavení obnoveného tenanta jako dalšího tenanta s vlastní aplikací pro události nezpůsobí, že poskytnete tenantovi přístup k obnoveným datům. Slouží k ilustraci vzoru obnovení. Většinou udělíte původní data přístup jen pro čtení a uložíte obnovenou databázi za definované období. V ukázce můžete odstranit obnovenou položku tenanta, až budete hotovi, spuštěním scénáře _Odebrat obnoveného tenanta_ .

1. Nastavte **$DemoScenario**  =  **4**, *odeberte obnoveného tenanta*.
2. Pokud chcete skript spustit, stiskněte klávesu F5.
3. *\_ Stará položka ContosoConcertHall* je nyní odstraněna z katalogu. Zavřete stránku události pro tohoto tenanta v prohlížeči.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Obnovte klienta na místě a nahraďte stávající databázi tenanta.

Toto cvičení obnoví tenanta společnosti Contoso v inhalaes do bodu před odstraněním události. Skript *Restore-TenantInPlace* obnoví databázi tenanta do nové databáze a odstraní původní. Tento vzor obnovení se nejlépe hodí pro obnovení ze závažných poškození dat a klient bude muset vyhovět významné ztrátě dat.

1. V prostředí PowerShell ISE otevřete soubor **Demo-RestoreTenant.ps1** .
2. Nastavte **$DemoScenario**  =  **5** a *obnovte klienta na místě*.
3. Pokud chcete skript spustit, stiskněte klávesu F5.

Skript obnoví databázi tenanta do bodu před odstraněním události. Před tím, než se dovede k dalším aktualizacím, nejprve vezme klienta contoso v rámci tenanta ve firmě contoso. Pak se vytvoří paralelní databáze obnovením z bodu obnovení. Obnovená databáze má název s časovým razítkem, aby se zajistilo, že název databáze není v konfliktu s názvem existující databáze klienta. V dalším kroku se stará databáze tenanta odstraní a obnovená databáze se přejmenuje na původní název databáze. Nakonec se do online režimu dovede online, aby aplikace mohla přistupovat k obnovené databázi.

Úspěšně jste obnovili databázi k určitému bodu v čase před odstraněním události. Po otevření stránky **události** potvrďte, že poslední událost byla obnovena.

Po obnovení databáze trvá znovu dalších 10 až 15 minut, než bude k dispozici první úplná záloha pro obnovení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Obnovení databáze do paralelní databáze (vedle sebe).
> * Obnovte databázi na místě.

Vyzkoušejte kurz [Správa schématu databáze tenanta](saas-tenancy-schema-management.md) .

## <a name="additional-resources"></a>Další zdroje informací

* [Další kurzy, které se vytvářejí v aplikaci Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Přehled provozní kontinuity se službou Azure SQL Database](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [Informace o SQL Database zálohování](automated-backups-overview.md)