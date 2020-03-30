---
title: Zotavení po havárii
description: Zjistěte, jak obnovit databázi z výpadku nebo selhání regionálního datového centra pomocí aktivní geografické replikace Azure SQL Database a možností geografického obnovení.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 06/21/2019
ms.openlocfilehash: d28edd28dcbe31bfe63c2d0a9c3e975967efef04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256377"
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Obnovení databáze Azure SQL nebo převzetí služeb při selhání na sekundární

Azure SQL Database nabízí následující funkce pro obnovení z výpadku:

- [Aktivní geografická replikace](sql-database-active-geo-replication.md)
- [Skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md)
- [Geografické obnovení](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Zónově redundantní databáze](sql-database-high-availability.md)

Další informace o scénářích kontinuity provozu a funkcích, které tyto scénáře podporují, naleznete [v tématu Kontinuita provozu](sql-database-business-continuity.md).

> [!NOTE]
> Pokud používáte zónově redundantní databáze nebo fondy Premium nebo Business Critical, proces obnovení je automatizovaný a zbytek tohoto materiálu se nepoužije.

> [!NOTE]
> Primární i sekundární databáze musí mít stejnou úroveň služby. Důrazně se také doporučuje, aby sekundární databáze byla vytvořena se stejnou velikostí výpočetních prostředků (DTU nebo virtuálních jader) jako primární. Další informace naleznete v [tématu Upgrade nebo downgrading jako primární databáze](sql-database-active-geo-replication.md#upgrading-or-downgrading-primary-database).

> [!NOTE]
> Pomocí jedné nebo několika skupin převzetí služeb při selhání můžete spravovat převzetí služeb při selhání více databází.
> Pokud přidáte existující vztah geografické replikace do skupiny převzetí služeb při selhání, ujistěte se, že geograficky sekundární je nakonfigurován se stejnou úrovní služby a výpočetní velikost jako primární. Další informace naleznete [v tématu Použití skupin s automatickým převzetím služeb při selhání k povolení transparentního a koordinovaného převzetí služeb při selhání více databází](sql-database-auto-failover-group.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Připravte se na případ výpadku

Chcete-li úspěšně obnovit do jiné oblasti dat pomocí skupin s podporou převzetí služeb při selhání nebo geograficky redundantních záloh, musíte připravit server v jiném výpadku datového centra, aby se stal novým primárním serverem v případě potřeby, a také mít dobře definované kroky zdokumentované a aby se zajistilo hladké zotavení. Tyto přípravné kroky zahrnují:

- Identifikujte databázový server SQL v jiné oblasti, aby se stal novým primárním serverem. Pro geografické obnovení se obvykle jedná o server ve [spárované oblasti](../best-practices-availability-paired-regions.md) pro oblast, ve které je databáze umístěna. To eliminuje dodatečné náklady na provoz během operací geografické obnovení.
- Identifikujte a volitelně definujte pravidla brány firewall IP na úrovni serveru, která jsou potřebná pro přístup uživatelů k nové primární databázi.
- Určete, jak chcete přesměrovat uživatele na nový primární server, například změnou připojovacích řetězců nebo změnou položek DNS.
- Identifikujte a volitelně vytvořte přihlášení, která musí být přítomna v hlavní databázi na novém primárním serveru, a ujistěte se, že tato přihlášení mají příslušná oprávnění v hlavní databázi, pokud existuje. Další informace naleznete v tématu [sql database security after disaster recovery](sql-database-geo-replication-security-config.md)
- Identifikujte pravidla výstrah, která je třeba aktualizovat, aby byla mapována na novou primární databázi.
- Dokumentovat konfiguraci auditování v aktuální primární databázi
- Proveďte [cvičení zotavení po havárii](sql-database-disaster-recovery-drills.md). Chcete-li simulovat výpadek pro geografické obnovení, můžete odstranit nebo přejmenovat zdrojovou databázi způsobit selhání připojení aplikace. Chcete-li simulovat výpadek pomocí skupin s podporou převzetí služeb při selhání, můžete zakázat webovou aplikaci nebo virtuální počítač připojený k databázi nebo převzetí služeb při selhání databáze způsobit selhání připojení aplikace.

## <a name="when-to-initiate-recovery"></a>Kdy zahájit obnovu

Operace obnovení má vliv na aplikaci. Vyžaduje změnu připojovacího řetězce SQL nebo přesměrování pomocí služby DNS a může vést ke ztrátě trvalých dat. Proto by mělo být provedeno pouze v případě, že výpadek je pravděpodobné, že trvat déle, než je cíl obnovení vaší aplikace. Když je aplikace nasazena do produkčního prostředí, měli byste provádět pravidelné sledování stavu aplikace a použít následující datové body k tvrzení, že obnovení je oprávněné:

1. Trvalé selhání připojení z aplikační vrstvy do databáze.
2. Portál Azure zobrazuje výstrahu o incidentu v oblasti s širokým dopadem.

> [!NOTE]
> Pokud používáte skupiny převzetí služeb při selhání a zvolili jste automatické převzetí služeb při selhání, je proces obnovení pro aplikaci automatizovaný a transparentní.

V závislosti na toleranci aplikace k prostojům a možné obchodní odpovědnosti můžete zvážit následující možnosti obnovení.

Pomocí [funkce Získat obnovitelnou databázi](https://msdn.microsoft.com/library/dn800985.aspx) *(LastAvailableBackupDate)* získáte nejnovější bod obnovení geograficky replikovaného.

## <a name="wait-for-service-recovery"></a>Počkejte na obnovení služby

Týmy Azure usilovně pracují na obnovení dostupnosti služeb co nejrychleji, ale v závislosti na hlavní příčině může trvat hodiny nebo dny.  Pokud vaše aplikace může tolerovat významné prostoje, můžete jednoduše počkat na dokončení obnovení. V takovém případě není nutná žádná akce z vaší strany. Aktuální stav služby najdete na našem [řídicím panelu stavu služby Azure](https://azure.microsoft.com/status/). Po obnovení oblasti je obnovena dostupnost vaší aplikace.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Převzetí služeb při selhání na geograficky replikovaný sekundární server ve skupině převzetí služeb při selhání

Pokud prostoje vaší aplikace může mít za následek obchodní odpovědnost, měli byste používat skupiny převzetí služeb při selhání. Umožňuje aplikaci rychle obnovit dostupnost v jiné oblasti v případě výpadku. Kurz naleznete v [tématu Implementace geograficky distribuované databáze](sql-database-implement-geo-distributed-database.md).

Chcete-li obnovit dostupnost databází, je třeba zahájit převzetí služeb při selhání na sekundární server pomocí jedné z podporovaných metod.

Pomocí jednoho z následujících pokynů přetekovat převzetí služeb při selhání do geograficky replikované sekundární databáze:

- [Převzetí služeb při selhání na geograficky replikovaný sekundární server pomocí portálu Azure](sql-database-geo-replication-portal.md)
- [Převzetí služeb při selhání na sekundární server pomocí prostředí PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [Převzetí služeb při selhání na sekundární server pomocí transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Obnovení pomocí geografického obnovení

Pokud prostoje vaší aplikace nevede k obchodní odpovědnosti, můžete použít [geografické obnovení](sql-database-recovery-using-backups.md) jako metodu k obnovení databáze aplikací. Vytvoří kopii databáze z jeho nejnovější geograficky redundantní zálohy.

## <a name="configure-your-database-after-recovery"></a>Konfigurace databáze po obnovení

Pokud používáte geografické obnovení obnovit z výpadku, musíte se ujistit, že připojení k nové databáze je správně nakonfigurován tak, aby normální funkce aplikace lze obnovit. Toto je kontrolní seznam úkolů, které vám pomohou připravit obnovenou databázi.

### <a name="update-connection-strings"></a>Aktualizace připojovacích řetězců

Vzhledem k tomu, že obnovená databáze je umístěna na jiném serveru, je třeba aktualizovat připojovací řetězec aplikace tak, aby ukazoval na tento server.

Další informace o změně připojovacích řetězců naleznete v příslušném vývojovém jazyce [pro knihovnu připojení](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Konfigurace pravidel brány firewall

Musíte se ujistit, že pravidla brány firewall nakonfigurovaná na serveru a v databázi odpovídají pravidlům, která byla nakonfigurována na primárním serveru a primární databázi. Další informace naleznete v [tématu How to: Configure Firewall Settings (Azure SQL Database)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Konfigurace přihlášení a uživatelů databáze

Musíte se ujistit, že všechny přihlašovací údaje používané vaší aplikací existují na serveru, který je hostitelem obnovené databáze. Další informace naleznete [v tématu Konfigurace zabezpečení pro geografickou replikaci](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Měli byste nakonfigurovat a otestovat pravidla brány firewall serveru a přihlášení (a jejich oprávnění) během cvičení zotavení po havárii. Tyto objekty na úrovni serveru a jejich konfigurace nemusí být k dispozici během výpadku.

### <a name="setup-telemetry-alerts"></a>Nastavení upozornění na telemetrii

Je třeba se ujistit, že stávající nastavení pravidel výstrahy jsou aktualizovány tak, aby mapovaly na obnovenou databázi a jiný server.

Další informace o pravidlech upozornění databáze naleznete v [tématu Příjem oznámení o výstražných upozorněních](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) a [sledování stavu služby](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Povolit auditování

Pokud auditování je nutné pro přístup k databázi, je třeba povolit auditování po obnovení databáze. Další informace naleznete v [tématu Auditing databáze](sql-database-auditing.md).

## <a name="next-steps"></a>Další kroky

- Další informace o automatickém zálohování azure sql database najdete v tématu [automatické zálohování databáze SQL Database.](sql-database-automated-backups.md)
- Další informace o scénářích návrhu a obnovení kontinuity provozu najdete [v tématu Scénáře kontinuity](sql-database-business-continuity.md)
- Další informace o použití automatických záloh pro obnovení naleznete [v tématu obnovení databáze ze záloh iniciovaných službou.](sql-database-recovery-using-backups.md)
