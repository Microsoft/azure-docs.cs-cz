---
title: Správa databází upraveným cloudu | Microsoft Docs
description: Použijte službu úlohy elastické databáze pro spuštění skriptu napříč skupinou databází.
metakeywords: azure sql database elastic databases
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 5e2c233ec631f6a3e57d2203a9678b42f909a885
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646081"
---
# <a name="managing-scaled-out-cloud-databases"></a>Správa databází upraveným cloudu
Ke správě upraveným horizontálně dělené databází, **úlohy elastické databáze** funkce (preview) umožňuje spolehlivě spuštění skriptu jazyka Transact-SQL (T-SQL) napříč skupinou databází, včetně:

* vlastní kolekce databáze (vysvětlení níže)
* všechny databáze [elastického fondu](sql-database-elastic-pool.md)
* Sada horizontálního oddílu (vytvořený [klientské knihovny pro elastické databáze](sql-database-elastic-database-client-library.md)). 

## <a name="documentation"></a>Dokumentace
* [Nainstalujte komponenty úlohy elastické databáze](sql-database-elastic-jobs-service-installation.md). 
* [Začínáme s úlohami elastické databáze](sql-database-elastic-jobs-getting-started.md).
* [Vytvářet a spravovat úlohy pomocí prostředí PowerShell](sql-database-elastic-jobs-powershell.md).
* [Vytvoření a správa škálovaný databází SQL Azure](sql-database-elastic-jobs-getting-started.md)

**Elastické databáze úlohy** právě zákazníka hostovaná Cloudová služba Azure, která umožňuje provádění ad-hoc a naplánované úlohy správy, které se nazývají **úlohy**. S úlohami můžete snadno a spolehlivě spuštění skriptů jazyka Transact-SQL k provedení operace správy spravovat velké skupiny databáze SQL Azure. 

![Služba úlohy elastické databáze][1]

## <a name="why-use-jobs"></a>Proč používat úlohy?
**Správa**

Jednoduše proveďte změny schématu, Správa přihlašovacích údajů, aktualizace dat odkaz, shromažďování dat výkonu nebo kolekce telemetrie klienta (zákazníka).

**Sestavy**

Agregovaná data z kolekce databáze SQL Azure do jedné cílové tabulky.

**Snížení režie**

Za normálních okolností se musí připojit k každou databázi nezávisle aby bylo možné spustit příkazy jazyka Transact-SQL nebo provádět další úlohy správy. Úlohu zpracovává úlohu přihlášení pro každou databázi v cílové skupině. Můžete také definovat, udržovat a zachovat Transact-SQL skriptů provést napříč skupinou databází SQL Azure.

**Monitorování účtů**

Úlohy spusťte skript a stavu spuštění pro každou databázi protokolu. Získáte také automatické opakování, když dojde k selhání.

**Flexibilita**

Definovat vlastní skupiny databází SQL Azure a plány pro spuštění úlohy.

> [!NOTE]
> Na portálu Azure je k dispozici omezená sada funkcí, které jsou omezené na elastické fondy SQL Azure. Získat přístup v úplné sadě stávající funkčnost pomocí rozhraní API prostředí PowerShell.
> 
> 

## <a name="applications"></a>Aplikace
* Provádění úloh správy, jako je nasazení nové schéma.
* Aktualizujte data produktu informace společné napříč všechny databáze. Nebo plány automatické aktualizace každý den v týdnu po hodinách.
* Opětovné sestavení indexů pro zlepšení výkonu dotazů. Nové sestavení se dá nakonfigurovat k provedení celé kolekci databáze pravidelně, například mimo špičku.
* Shromážděte výsledky dotazu ze sady databází do centrální tabulky na základě průběžné. Výkon dotazů můžete průběžně spustit a nakonfigurovat tak, aby aktivační událost má provádět další úlohy.
* Spusťte delší spuštěné dotazy zpracování dat napříč velkou sadou databází, například kolekce telemetrie zákazníka. Výsledky se shromažďují do jedné cílové tabulky pro další analýzu.

## <a name="elastic-database-jobs-end-to-end"></a>Úlohy elastické databáze: začátku do konce
1. Nainstalujte **úlohy elastické databáze** součásti. Další informace najdete v tématu [úlohy instalace elastické databáze](sql-database-elastic-jobs-service-installation.md). Pokud se instalace nezdaří, přečtěte si téma [postup odinstalace](sql-database-elastic-jobs-uninstall.md).
2. Pomocí rozhraní API prostředí PowerShell pro přístup k další funkce, například vytváření vlastní databázi kolekce, přidání plány nebo shromažďování sad výsledků. Pomocí portálu pro vytvoření nebo monitorování úloh omezené na vůči a jednoduché instalace **elastický fond**. 
3. Vytvoření zašifrované přihlašovací údaje pro provádění úlohy a [přidejte uživatele (nebo rolí) pro každou databázi ve skupině](sql-database-security-overview.md).
4. Vytvořte idempotent skriptu T-SQL, který lze spustit na každou databázi ve skupině. 
5. Postupujte podle těchto kroků můžete vytvořit úlohy pomocí portálu Azure: [vytváření a Správa úloh elastické databáze](sql-database-elastic-jobs-create-and-manage.md). 
6. Nebo použití skriptů prostředí PowerShell: [vytvořit a spravovat úlohy elastické databáze databáze SQL pomocí prostředí PowerShell (preview)](sql-database-elastic-jobs-powershell.md).

## <a name="idempotent-scripts"></a>Idempotent skriptů
Musí být skripty [idempotent](https://en.wikipedia.org/wiki/Idempotence). Jednoduše řečeno "idempotent" znamená, že v případě, že skript uspěje a znovu spusťte, stejný výsledek dojde. Skript se pravděpodobně nezdaří z důvodu přechodný síťový problémy. V takovém případě úlohy bude automaticky opakovat spouštění skriptu přednastavené počet časů před desisting. Skript idempotent má stejný výsledek i v případě, že byl úspěšně spuštěn dvakrát. 

Jednoduché cílem je otestovat existenci objekt před jejím vytváření.  

    IF NOT EXIST (some_object)
    -- Create the object 
    -- If it exists, drop the object before recreating it.

Podobně skriptu musí být úspěšně provést logicky pro testování a zamezením jakékoliv podmínky, které najde.

## <a name="failures-and-logs"></a>Chyby a protokoly
Pokud skript selže po několika pokusech, úloha zapíše chybu a pokračuje. Po skončení úlohy (tj. spustit pro všechny databáze ve skupině), můžete zkontrolovat její seznam neúspěšných pokusů o přihlášení. Protokoly poskytují podrobnosti k ladění skriptů vadný. 

## <a name="group-types-and-creation"></a>Skupiny typy a vytvoření
Existují dva typy skupin: 

1. Nastaví horizontálního oddílu
2. Vlastní skupiny

Horizontálního oddílu sady skupiny jsou vytvořeny pomocí [nástroje elastické databáze](sql-database-elastic-scale-introduction.md). Když vytvoříte skupinu sadu horizontálního oddílu, jsou databáze přidat nebo odebrat ze skupiny automaticky. Například nové horizontálního oddílu bude automaticky ve skupině při přidání mapu horizontálního oddílu. Úlohu můžete pak spusťte proti skupině.

Vlastní skupiny, na druhé straně jsou pevně definované. Musíte explicitně přidat nebo odebrat databáze z vlastní skupiny. Pokud databáze ve skupině je vynechána, úloha se pokusí spustit skript v databázi, což vede k jeho případnému selhání. Skupiny vytvořené pomocí portálu Azure aktuálně jsou vlastní skupiny. 

## <a name="components-and-pricing"></a>Součásti a ceny
Následující součásti spolupracují na vytvoření Azure cloudové služby, která umožňuje ad-hoc provádění úloh správy. Součásti instalace a konfigurace automaticky během instalace, v rámci vašeho předplatného. Služby můžete určit, jak spolu mají stejný název automaticky generovaný. Název jedinečný a skládá se z předponu "edj" následované 21 náhodně generovaných znaků.

* **Cloudovou službu Azure**: úlohy elastické databáze (preview) je dodávána jako zákazník hostované služby Azure Cloud k provádění požadovaných úkolů. Z portálu je nasazení služby a je hostovaná v rámci vašeho předplatného Microsoft Azure. Výchozí hodnota nasadit service běží s minimálně dvěma rolí pracovního procesu pro vysokou dostupnost. Výchozí velikost jednotlivých rolí pracovního procesu (ElasticDatabaseJobWorker) běží na instanci A0. Ceny, najdete v části [ceny služby Azure Cloud services](https://azure.microsoft.com/pricing/details/cloud-services/). 
* **Azure SQL Database**: služba používá označované jako Azure SQL Database **řízení databáze** všechny úlohy metadat. Výchozí úroveň služby je S0. Ceny, najdete v části [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).
* **Azure Service Bus**: Azure Service Bus je pro koordinaci práce v rámci služby Azure Cloud Service. V tématu [Service Bus ceny](https://azure.microsoft.com/pricing/details/service-bus/).
* **Úložiště Azure**: Azure Storage účet se používá k ukládání výstup diagnostiky protokolování v případě, že problém vyžaduje další ladění (viz [povolení diagnostiky Azure Cloud Services a virtuálních počítačů](../cloud-services/cloud-services-dotnet-diagnostics.md)). Ceny, najdete v části [Azure Storage – ceny](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-elastic-database-jobs-work"></a>Jak fungují úlohy elastické databáze
1. Je určený k Azure SQL Database **řízení databáze** které ukládá všechna data metadata a stavu.
2. Databáze řízení přístupu **úlohy služby** můžou spouštět a sledovat úlohy provést.
3. Dva různé role komunikaci s databází ovládacího prvku: 
   * Řadič: Určuje, jaké úlohy vyžadují úlohy musíte provést požadovanou úlohu a opakovaných pokusů neúspěšných úloh tak, že vytvoříte nové úlohy.
   * Provádění úloh úkolu: Provádí úkoly úlohy.

### <a name="job-task-types"></a>Typy úloh úloh
Existují různé druhy úlohy, které provádějí spouštění úloh:

* ShardMapRefresh: Mapování horizontálních k určení všech databází použít jako horizontálních oddílů dotazuje
* ScriptSplit: Rozdělí mezi tlačítko Přejít' příkazy do dávek skriptu
* ExpandJob: Vytváří podřízené úlohy pro každou databázi z úlohy, která je cílena skupinu databází
* ScriptExecution: Spustí skript na konkrétní databázi pomocí přihlašovacích údajů definovaných
* Dacpac: Platí pro konkrétní databázi pomocí konkrétní přihlašovací údaje DACPAC

## <a name="end-to-end-job-execution-work-flow"></a>Spuštění úlohy začátku do konce pracovní flow
1. Pomocí portálu nebo rozhraní API prostředí PowerShell, úlohy budou vložena do **řízení databáze**. Úloha požaduje spuštění skriptu jazyka Transact-SQL pro skupinu databází pomocí konkrétní přihlašovací údaje.
2. Kontroler identifikuje novou úlohu. Úlohy se vytváří a provést rozdělit skript a k aktualizaci databáze této skupiny. Nakonec novou úlohu je vytvořen a provést rozbalte úlohy a vytvořit novou podřízenou tam, kde je zadán každé podřízené úlohy ke spuštění skriptu jazyka Transact-SQL pro jednotlivé databáze ve skupině úloh.
3. Kontroler identifikuje vytvořené podřízené úlohy. Pro každou úlohu řadičem vytvoří a spustí úkol pro spuštění skriptu pro databázi. 
4. Po dokončení úlohy všechny, aktualizuje řadičem stavu dokončení úlohy. 
   Kdykoli během provádění úlohy rozhraní API prostředí PowerShell slouží k zobrazení aktuálního stavu provádění úlohy. Všechny časy vrácený rozhraní API prostředí PowerShell jsou reprezentované ve standardu UTC. V případě potřeby k zastavení úlohy lze inicializovat žádost o zrušení. 

## <a name="next-steps"></a>Další postup
[Nainstalujte součásti](sql-database-elastic-jobs-service-installation.md), pak [vytvořit a přidat do protokolu pro každou databázi ve skupině databází](sql-database-manage-logins.md). Abyste pochopili, úlohy vytváření a správu, najdete v části [vytvářením a správou úloh elastické databáze](sql-database-elastic-jobs-create-and-manage.md). Viz také [Začínáme s úlohami elastické databáze](sql-database-elastic-jobs-getting-started.md).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->


