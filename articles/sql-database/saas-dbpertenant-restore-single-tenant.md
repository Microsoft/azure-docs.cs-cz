---
title: Obnovení databáze ve víceklientské aplikaci SaaS
description: Zjistěte, jak obnovit databázi SQL jednoho klienta po náhodném odstranění dat
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 12/04/2018
ms.openlocfilehash: a54e8e5629f6f8ad688b6fe11bbf50fc038095bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73818534"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Obnovení jednoho klienta pomocí aplikace SaaS pro databázi na klienta

Model databáze na tenanta usnadňuje obnovení jednoho klienta do předchozího bodu v čase bez ovlivnění ostatních klientů.

V tomto kurzu se naučíte dva vzory obnovení dat:

> [!div class="checklist"]
> * Obnovení databáze do paralelní databáze (vedle sebe).
> * Obnovte databázi na místě a nahrazte existující databázi.

|||
|:--|:--|
| Obnovení do paralelní databáze | Tento vzor lze použít pro úkoly, jako je kontrola, auditování a dodržování předpisů, aby klient mohl kontrolovat svá data z dřívějšího bodu. Aktuální databáze klienta zůstane online a beze změny. |
| Obnovení na místě | Tento vzor se obvykle používá k obnovení klienta do dřívějšího bodu poté, co klient omylem odstraní nebo poškodí data. Původní databáze je vyřazena z řádku a nahrazena obnovenou databází. |
|||

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Aplikace Wingtip SaaS je nasazená. Informace o nasazení za méně než pět minut najdete v [tématu Nasazení a prozkoumání aplikace Wingtip SaaS](saas-dbpertenant-get-started-deploy.md).
* Je nainstalované prostředí Azure PowerShell. Podrobnosti [najdete v tématu Začínáme s Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Úvod do vzorů obnovení klienta SaaS

Existují dva jednoduché vzory pro obnovení dat jednotlivého klienta. Vzhledem k tomu, že databáze tenantů jsou izolované od sebe navzájem, obnovení jednoho klienta nemá žádný vliv na data jiného klienta. Funkce Azure SQL Database point-in-time-restore (PITR) se používá v obou vzorcích. PITR vždy vytvoří novou databázi.

* **Obnovení paralelně**: V prvním vzoru je vytvořena nová paralelní databáze vedle aktuální databáze klienta. Klient je pak přidělen přístup jen pro čtení k obnovené databázi. Obnovená data mohou být zkontrolována a potenciálně použita k přepsání aktuálních hodnot dat. Je na návrháři aplikací určit, jak klient přistupuje k obnovené databázi a jaké možnosti pro obnovení jsou k dispozici. Jednoduše povolení tenanta zkontrolovat jejich data v dřívějším bodě může být vše, co je požadováno v některých scénářích.

* **Obnovení na místě**: Druhý vzor je užitečný, pokud byla data ztracena nebo poškozena a klient se chce vrátit k dřívějšímu bodu. Tenant je přijata off line, zatímco databáze je obnovena. Původní databáze je odstraněna a obnovená databáze je přejmenována. Řetězec zálohování původní databáze zůstane přístupný po odstranění, takže můžete v případě potřeby obnovit databázi do dřívějšího bodu v čase.

Pokud databáze používá [aktivní geografickou replikaci](sql-database-active-geo-replication.md) a obnovení paralelně, doporučujeme zkopírovat všechna požadovaná data z obnovené kopie do původní databáze. Pokud nahradíte původní databázi obnovenou databází, je třeba znovu nakonfigurovat a znovu synchronizovat geografickou replikaci.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získejte skripty aplikace SaaS pro jednotlivé klienty wingtip tickets

Wingtip Vstupenky SaaS Víceklientské databáze skripty a zdrojový kód aplikace jsou k dispozici v [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub úložiště. Postup stažení a odblokování skriptů Wingtip Tickets SaaS naleznete v [obecných pokynech](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Než začnete

Při vytvoření databáze může trvat 10 až 15 minut, než je k dispozici obnovení první úplné zálohy. Pokud jste právě nainstalovali aplikaci, budete muset počkat několik minut, než se pokusíte tento scénář.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulace náhodného odstranění dat klienta

Chcete-li demonstrovat tyto scénáře obnovení, nejprve "omylem" odstranit událost v jedné z databází klienta. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Otevření aplikace Události pro kontrolu aktuálních událostí

1. Otevřete Centrumhttp://events.wtp.&ltudálostí&gt;( ;uživatel .trafficmanager.net) a vyberte **Contoso Concert Hall**.

   ![Centrum události](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Posuňte seznam událostí a poznamenejte si poslední událost v seznamu.

   ![Zobrazí se poslední událost](media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>"Náhodně" odstranit poslední událost

1. V prostředí PowerShell ISE otevřete ... \\Výukové\\moduly Kontinuita podnikání\\a\\zotavení po havárii obnoveníTenant*Demo-RestoreTenant.ps1*a nastavte následující hodnotu:

   * **$DemoScenario** = **1**, Odstranit poslední *událost (bez prodeje vstupenek).*
2. Stisknutím klávesy F5 spusťte skript a odstraňte poslední událost. Zobrazí se následující potvrzovací zpráva:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Otevře se stránka Události contoso. Posuňte se dolů a ověřte, zda je událost pryč. Pokud je událost stále v seznamu, vyberte **Aktualizovat** a ověřte, že je pryč.
   ![Poslední odebraná událost](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Obnovení databáze klienta paralelně s produkční databází

Toto cvičení obnoví databázi koncertní síně Contoso do bodu v čase před odstraněním události. Tento scénář předpokládá, že chcete zkontrolovat odstraněná data v paralelní databázi.

 Skript *Restore-TenantInParallel.ps1* vytvoří paralelní databázi klienta s názvem *ContosoConcertHall\_old*s položkou paralelního katalogu. Tento vzor obnovení je nejvhodnější pro obnovení z menší ztráty dat. Tento vzor můžete také použít, pokud potřebujete zkontrolovat data pro účely dodržování předpisů nebo auditování. Je to doporučený přístup při použití [aktivní geografické replikace](sql-database-active-geo-replication.md).

1. [Dokončete simulovat klienta omylem odstranění dat](#simulate-a-tenant-accidentally-deleting-data) části.
2. V prostředí PowerShell ISE otevřete ... \\Výukové\\moduly Kontinuita podnikání\\a\\zotavení po havárii ObnoveníTenant_Demo-RestoreTenant.ps1_.
3. Nastavit **$DemoScenario** = **2**, *Obnovit klienta paralelně*.
4. Skript spustíte stisknutím klávesy F5.

Skript obnoví databázi klienta do bodu v čase před odstraněním události. Databáze je obnovena do nové databáze s názvem _\_ContosoConcertHall old_. Metadata katalogu, která existují v této obnovené databázi, jsou odstraněna a potom je databáze přidána do katalogu pomocí klíče vytvořeného ze starého názvu *ContosoConcertHall.\_*

Ukázkový skript otevře stránku událostí pro tuto novou databázi klienta ve vašem prohlížeči. Poznámka z ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` adresy URL, která zobrazuje data z obnovené databáze, kde je k názvu *přidána _old.*

Posunutím událostí uvedených v prohlížeči potvrďte, že událost odstraněná v předchozí části byla obnovena.

Vystavení obnoveného klienta jako dalšího klienta s vlastní aplikací Events pravděpodobně nebude způsob, jakým poskytnete přístup klienta k obnoveným datům. Slouží k ilustraci vzoru obnovení. Obvykle udělit jen pro čtení přístup ke starým datům a zachovat obnovenou databázi po definovanou dobu. V ukázce můžete odstranit obnovenou položku klienta po dokončení spuštěním scénáře _Odebrat obnovený klient._

1. Nastavte **$DemoScenario** = **4**, *Odebrat obnoveného klienta*.
2. Skript spustíte stisknutím klávesy F5.
3. Stará položka *ContosoConcertHall\_* je nyní odstraněna z katalogu. Zavřete stránku událostí pro tohoto klienta v prohlížeči.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Obnovení klienta na místě, nahrazení existující databáze klienta

Toto cvičení obnoví nájemce koncertní síně Contoso do bodu před odstraněním události. Skript *Restore-TenantInPlace* obnoví databázi klienta do nové databáze a odstraní originál. Tento model obnovení je nejvhodnější pro obnovení z poškození dat závažné a klient může mít k uložení významné ztráty dat.

1. V prostředí PowerShell ISE otevřete soubor **Demo-RestoreTenant.ps1.**
2. Nastavte **$DemoScenario** = **5**, *Obnovit klienta na místě*.
3. Skript spustíte stisknutím klávesy F5.

Skript obnoví databázi klienta do bodu před odstraněním události. Nejprve se Contoso Koncertní sál nájemce off-line, aby se zabránilo dalším aktualizacím. Paralelní databáze je potom vytvořena obnovením z bodu obnovení. Obnovená databáze je pojmenována s časovým razítkem, aby se ujistil, že název databáze není v konfliktu s existujícím názvem databáze klienta. Dále je odstraněna stará databáze klienta a obnovená databáze je přejmenována na původní název databáze. A konečně, Contoso Koncertní síň je uvedena do režimu online, aby aplikace umožnila přístup k obnovené databázi.

Databázi jste úspěšně obnovili do bodu v čase před odstraněním události. Po otevření stránky **Události** potvrďte, že byla obnovena poslední událost.

Po obnovení databáze trvá dalších 10 až 15 minut, než bude k dispozici první úplná záloha, ze které bude znovu možné obnovit.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Obnovení databáze do paralelní databáze (vedle sebe).
> * Obnovte databázi na místě.

Vyzkoušejte kurz [Správa schématu databáze klienta.](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>Další zdroje

* [Další výukové programy, které vycházejí z aplikace Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Přehled provozní kontinuity se službou Azure SQL Database](sql-database-business-continuity.md)
* [Informace o zálohách databáze SQL](sql-database-automated-backups.md)
