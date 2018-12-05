---
title: Správa databází s horizontálním navýšením kapacity | Dokumentace Microsoftu
description: Úlohy služby elastic database použijte ke spuštění skriptu v rámci skupiny databází.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 10/22/2018
ms.openlocfilehash: 9647522f4b3990d065f292f05934b8d19c691454
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865518"
---
# <a name="managing-scaled-out-cloud-databases"></a>Správa databází s horizontálním navýšením kapacity

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]

**Úlohy elastic Database** hostované zákazníků Azure Cloudová služba, která umožňuje spouštět ad-hoc a naplánované úlohy správy, které se nazývají **úlohy**. S úlohami můžete snadno a spolehlivě spravovat velké skupiny databází Azure SQL pomocí jazyka Transact-SQL skriptů k provádění operací správy.

Chcete-li správa horizontálně dělených databází s horizontálním navýšením kapacity, **úlohy elastické databáze** funkce (preview) umožňuje spolehlivě spustit skript jazyka Transact-SQL (T-SQL) napříč skupinou databází, včetně:

- kolekce uživatelem definovaných databází (vysvětleno níže)
- všechny databáze ve službě [elastického fondu](sql-database-elastic-pool.md)
- nastavení horizontálními oddíly (vytvořené pomocí [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md)).

Další informace o konceptu horizontálně dělené databáze najdete v tématu [horizontálního dělení databáze systému SQL Server](https://blog.pythian.com/sharding-sql-server-database/).

## <a name="documentation"></a>Dokumentace

- [Nainstalujte součásti úlohy elastické databáze](sql-database-elastic-jobs-service-installation.md).
- [Začínáme s úlohami Elastic Database](sql-database-elastic-jobs-getting-started.md).
- [Vytváření a Správa úloh pomocí Powershellu](sql-database-elastic-jobs-powershell.md).
- [Vytvářet a spravovat škálovaná databází Azure SQL](sql-database-elastic-jobs-getting-started.md)

![Služba úloh elastických databází][1]

## <a name="why-use-jobs"></a>Proč používat úlohy

### <a name="manage"></a>Spravovat

Snadno proveďte změny ve schématu, Správa přihlašovacích údajů, aktualizace referenčních dat, shromažďování údajů o výkonu nebo shromažďování telemetrických dat tenanta (zákazníka).

### <a name="reports"></a>Reports

Agregace dat z kolekce databází Azure SQL do jedné cílové tabulky.

### <a name="reduce-overhead"></a>Snížení režie

Pokud chcete spouštět příkazy jazyka Transact-SQL nebo provádět jiné úlohy správy, za normálních okolností se musíte připojit ke každé databázi zvlášť. Úloha zajišťuje přihlášení ke každé databázi v cílové skupině. Můžete také definovat, spravovat a uchovávat skripty Transact-SQL, které se spustí pro skupinu databází Azure SQL.

### <a name="accounting"></a>Účetnictví

Úlohy, spusťte skript a protokolovat stav spuštění pro každou databázi. Pokud dojde k selhání, provedete se automatické opakování.

### <a name="flexibility"></a>Flexibilita

Můžete definovat vlastní skupiny databází Azure SQL a plány spouštění úloh.

> [!NOTE]
> Na webu Azure Portal je k dispozici pouze s omezenou sadou funkcí, které jsou omezené na elastické fondy SQL Azure. Pomocí rozhraní API prostředí PowerShell pro přístup k úplné sadě funkcí aktuální.

## <a name="applications"></a>Aplikace

- Provádění úloh správy, jako je nasazení nové schéma.
- Aktualizujte odkaz na data – informace o produktech běžné napříč všemi databázemi. Nebo plány automatické aktualizace každý den v týdnu po hodinách.
- Vylepšení výkonu dotazů díky opětovnému sestavení indexů. Obnovení je možné nakonfigurovat ke spuštění v kolekci databází opakovaného, například během hodiny mimo špičku.
- Průběžné shromažďování výsledků dotazů ze sady databází do centrální tabulky. Výkonové dotazy je možné spouštět průběžně a nakonfigurovat tak, aby aktivovaly další úlohy, které se mají provést.
- Spouštění dlouhotrvajících dotazů na zpracování dat pro velkou sadu databází, například shromažďování telemetrických dat uživatelů. Výsledky se pro účely další analýzy shromažďují do jedné cílové tabulky.

## <a name="elastic-database-jobs-end-to-end"></a>Úlohy elastic Database: začátku do konce

1. Nainstalujte **úlohy elastické databáze** komponenty. Další informace najdete v tématu [úlohy instalace elastické databáze](sql-database-elastic-jobs-service-installation.md). Pokud se instalace nezdaří, přečtěte si téma [odinstalace](sql-database-elastic-jobs-uninstall.md).
2. Pomocí rozhraní API prostředí PowerShell pro přístup k více funkcí, například vytvoření uživatelem definovaných databáze kolekcí, přidání plány a/nebo shromažďování sad výsledků. Použití portálu pro jednoduché instalaci a vytváření a monitorování úloh omezen na spuštění proti **elastického fondu**.
3. Vytvoření zašifrované přihlašovací údaje pro provádění úloh a [přidejte uživatele (nebo rolí) pro každou databázi ve skupině](sql-database-security-overview.md).
4. Vytvoření idempotentní skriptu T-SQL, které se dají spustit pro každou databázi ve skupině.
5. Postupujte podle těchto kroků můžete vytvořit úlohy pomocí webu Azure portal: [vytváření a správa úlohy elastické databáze](sql-database-elastic-jobs-create-and-manage.md).
6. Nebo pomocí skriptů prostředí PowerShell: [vytvořit a spravovat úlohy elastické databáze SQL Database pomocí Powershellu (preview)](sql-database-elastic-jobs-powershell.md).

## <a name="idempotent-scripts"></a>Idempotentní skripty

Skripty musí být [idempotentní](https://en.wikipedia.org/wiki/Idempotence). Jednoduše řečeno "idempotentní" znamená, že v případě, že skript uspěje a znovu spusťte, stejného výsledku dochází. Skript může selhat kvůli přechodným problémům se sítí. V takovém případě se úloha automaticky pokusí znovu skript spustit tolikrát, kolikrát je uvedeno v předvolbách, a pak přestane. Skriptu idempotentní má stejný výsledek, i v případě, že byl úspěšně spuštěn dvakrát.

Jednoduchou taktikou je před vytvořením objektu otestovat, jestli už neexistuje.  

```sql
    IF NOT EXIST (some_object)
    -- Create the object
```

Skript se také musí být schopný úspěšně provést. Dosáhne toho tím, že logicky testuje podmínky a reaguje na případné podmínky, které najde.

## <a name="failures-and-logs"></a>Chyby a protokoly

Pokud skript selže po několika pokusech, úloha zaznamená chybu a pokračuje. Po skončení úlohy (tj. běh před všechny databáze ve skupině), můžete zkontrolovat svůj seznam neúspěšných pokusů o přihlášení. Protokoly poskytují podrobnosti o ladění chybného skriptů.

## <a name="group-types-and-creation"></a>Skupiny typů a vytvoření

Existují dva typy skupin:

1. Sady horizontálních oddílů
2. Vlastní skupiny

Skupiny sady horizontálních oddílů jsou vytvořeny pomocí [nástrojů Elastic Database](sql-database-elastic-scale-introduction.md). Při vytváření skupiny nastavení horizontálními oddíly, jsou databáze přidat nebo odebrat ze skupiny automaticky. Například nový horizontální oddíl bude automaticky ve skupině při přidání do mapy horizontálních oddílů. Úloha pak dají spustit pro skupinu.

Vlastní skupiny, na druhé straně jsou pevně definovány. Musíte explicitně přidat nebo odebrat databáze z vlastní skupiny. Pokud databáze ve skupině, bude vynechána. úlohy se pokusí pro spuštění skriptu na databázi, což vede k případnému selhání. Skupiny vytvořené pomocí webu Azure portal momentálně jsou vlastní skupiny.

## <a name="components-and-pricing"></a>Komponenty a ceny

Následující součásti společně vytvářejí služby Azure Cloud, která umožňuje provádění ad-hoc Správce úloh. Součásti instalace a konfigurace automaticky během instalace, v rámci vašeho předplatného. Služby můžete identifikovat všechny mají stejnou automaticky generovaný název. Název je jedinečný a se skládá z předponu "edj" následované 21 náhodně generovaných znaků.

- Cloudové služby Azure

  Úlohy elastic database (preview) je dodávána jako hostované zákazníků Azure cloudové služby se provede spuštění úloh. Z portálu je nasazení služby a je hostovaná ve vašem předplatném Microsoft Azure. Výchozí hodnota nasazení spouští služba s minimálně dvěma rolemi pracovních procesů pro zajištění vysoké dostupnosti. Výchozí velikost každé role pracovního procesu (ElasticDatabaseJobWorker) se používá v instanci A0. Ceny najdete v tématu [ceny Cloud services](https://azure.microsoft.com/pricing/details/cloud-services/).

- Azure SQL Database

  Služba používá označované jako Azure SQL Database **databáze správy** k uložení všech metadat úlohy. Výchozí úroveň služby je S0. Ceny najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

- Azure Service Bus

  Azure Service Bus je pro koordinaci práce v rámci cloudové služby Azure. Zobrazit [ceník služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

- Azure Storage

  Účet služby Azure Storage se používá k ukládání diagnostický výstup protokolování v případě, že některý problém vyžaduje další ladění (viz [povolení diagnostiky v Azure Cloud Services a Virtual Machines](../cloud-services/cloud-services-dotnet-diagnostics.md)). Ceny najdete v tématu [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-elastic-database-jobs-work"></a>Jak fungují úlohy elastické databáze

1. Azure SQL Database je určena **databáze správy** které ukládá všechna data metadata a stav.
2. Ovládací prvek databázi přistupuje **služba úloh** ke spuštění a sledování úloh provádět.
3. Dva různé role komunikaci s databází ovládacího prvku:
   - Kontroler: Určuje, jaké úlohy, vyžadují úkoly provádět požadované úlohy a opakování neúspěšných úloh tak, že vytvoříte nové úlohy.
   - Provádění úloh úkolu: Provádí úkoly úlohy.

### <a name="job-task-types"></a>Typy úloh úlohy

Existuje více typů úloh, které vykonávají spuštění úlohy:

- ShardMapRefresh

  Dotazy k určení všechny databáze používané jako horizontálními oddíly mapy horizontálních oddílů
- ScriptSplit

  Rozdělí skript napříč 'Přejít' příkazy do dávek
- ExpandJob

  Vytvoří podřízené úlohy pro každou databázi z úlohy, které cílí na skupinu databází
- ScriptExecution

  Provede skript na konkrétní databázi pomocí přihlašovacích údajů definovaných
- Dacpac

  Platí pro konkrétní databázi pomocí přihlašovacích údajů pro konkrétní DACPAC

## <a name="end-to-end-job-execution-work-flow"></a>Úloha začátku do konce provádění pracovního postupu

1. Pomocí portálu nebo rozhraní API prostředí PowerShell, úlohy je vložen do **databáze správy**. Žádosti o provádění úlohy skriptu jazyka Transact-SQL pro skupinu databází pomocí konkrétní přihlašovací údaje.
2. Kontroler identifikuje nová úloha. Úlohy se vytvářejí a rozdělit skript a obnovit databáze skupiny. A konečně nová úloha se vytvářejí a rozbalte úlohy a vytvářet nové podřízené úlohy, ve kterém je každá podřízená úloha zadané pro spuštění skriptu jazyka Transact-SQL pro jednotlivé databáze ve skupině.
3. Kontroler identifikuje vytvořený podřízené úlohy. Pro každou úlohu kontroleru vytvoří a spustí úlohu pro spuštění skriptu pro databázi.
4. Po dokončení všech úloh, aktualizuje kontroleru úlohy do dokončeného stavu.
   Kdykoli během provádění úlohy je možné rozhraní API prostředí PowerShell zobrazit aktuální stav provádění úlohy. Vrácené rozhraní API Powershellu všechny časy jsou reprezentovány ve standardu UTC. V případě potřeby lze inicializovat žádost o zrušení k zastavení úlohy.

## <a name="next-steps"></a>Další postup

[Nainstalujte součásti](sql-database-elastic-jobs-service-installation.md), pak [vytvořit a přidat do protokolu pro každou databázi ve skupině databází](sql-database-manage-logins.md). Abyste pochopili, vytvoření úlohy a správu, najdete v článku [vytváření a správa úlohy elastické databáze](sql-database-elastic-jobs-create-and-manage.md). Viz také [Začínáme s úlohami Elastic Database](sql-database-elastic-jobs-getting-started.md).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->
