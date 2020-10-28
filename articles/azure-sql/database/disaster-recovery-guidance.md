---
title: Zotavení po havárii
description: Naučte se, jak obnovit databázi z oblasti výpadku nebo selhání regionálního datového centra pomocí Azure SQL Database aktivní geografické replikace a možnosti geografického obnovení.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/21/2019
ms.openlocfilehash: e4a6802829d230cebc460df1409b7655534a5b8e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782990"
---
# <a name="restore-your-azure-sql-database-or-failover-to-a-secondary"></a>Obnovení Azure SQL Database nebo převzetí služeb při selhání sekundárním
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database nabízí následující možnosti pro zotavení po výpadku:

- [Aktivní geografická replikace](active-geo-replication-overview.md)
- [Skupiny automatického převzetí služeb při selhání](auto-failover-group-overview.md)
- [Geografické obnovení](recovery-using-backups.md#point-in-time-restore)
- [Redundantní databáze v zóně](high-availability-sla.md)

Další informace o scénářích kontinuity podnikových aplikací a funkcích, které tyto scénáře podporují, najdete v tématu [Kontinuita podnikových aplikací](business-continuity-high-availability-disaster-recover-hadr-overview.md).

> [!NOTE]
> Pokud používáte databáze nebo fondy nadbytečná zóny nebo Pro důležité obchodní informace, proces obnovení je automatizovaný a zbytek tohoto materiálu neplatí.
>
> U primárních i sekundárních databází je potřeba, aby měly stejnou úroveň služby. Také se důrazně doporučuje vytvořit sekundární databázi se stejnou výpočetní velikostí (DTU nebo virtuální jádra) jako primární. Další informace najdete v tématu [upgrade nebo downgrading jako primární databáze](active-geo-replication-overview.md#upgrading-or-downgrading-primary-database).
>
> Pomocí jedné nebo několika skupin převzetí služeb při selhání můžete spravovat převzetí služeb při selhání více databází.
> Pokud přidáte existující relaci geografické replikace do skupiny převzetí služeb při selhání, ujistěte se, že je geograficky sekundární nakonfigurovaná se stejnou úrovní služeb a výpočetní velikostí jako primární. Další informace najdete v tématu [použití skupin automatického převzetí služeb při selhání k zajištění transparentního a koordinovaného převzetí služeb při selhání více databází](auto-failover-group-overview.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Příprava na případ výpadku

V případě úspěchu s obnovením do jiné oblasti dat pomocí skupin pro převzetí služeb při selhání nebo geograficky redundantního zálohování je potřeba připravit server v jiném výpadku datového centra, aby se stal novým primárním serverem, pokud by to bylo nutné, a měly by se dobře definovat popsané a testované kroky, aby se zajistilo hladké obnovení. Tyto přípravné kroky zahrnují:

- Identifikujte Server v jiné oblasti, který se stane novým primárním serverem. V případě geografického obnovení se jedná o server v [spárované oblasti](../../best-practices-availability-paired-regions.md) pro oblast, ve které se nachází vaše databáze. Tím se eliminují dodatečné náklady na provoz během geografického obnovení.
- Identifikujte a volitelně definujte pravidla brány firewall IP na úrovni serveru potřebná pro uživatele, kteří budou mít přístup k nové primární databázi.
- Určete, jak budete přesměrovat uživatele na nový primární server, například změnou připojovacích řetězců nebo změnou položek DNS.
- Identifikujte a volitelně Vytvořte přihlašovací údaje, které se musí nacházet v hlavní databázi na novém primárním serveru, a ujistěte se, že tato přihlášení mají v hlavní databázi příslušná oprávnění, pokud existují. Další informace najdete v tématu [SQL Database Security po zotavení po havárii](active-geo-replication-security-configure.md) .
- Identifikujte pravidla výstrah, která je potřeba aktualizovat, aby se namapovala na novou primární databázi.
- Dokumentuje konfiguraci auditování v aktuální primární databázi.
- Provedení postupu [zotavení po havárii](disaster-recovery-drills.md). Chcete-li simulovat výpadek geografického obnovení, můžete zdrojovou databázi odstranit nebo přejmenovat a způsobit tak selhání připojení aplikace. Chcete-li simulovat výpadky pomocí skupin převzetí služeb při selhání, můžete zakázat webovou aplikaci nebo virtuální počítač připojený k databázi nebo převzetí služeb při selhání databáze a způsobit tak selhání připojení aplikace.

## <a name="when-to-initiate-recovery"></a>Kdy spustit obnovení

Operace obnovení má vliv na aplikaci. Vyžaduje změnu připojovacího řetězce nebo přesměrování SQL pomocí DNS a může způsobit trvalou ztrátu dat. Proto by měl být proveden pouze v případě výpadku, který je pravděpodobně naposledy delší než cíl doby obnovení vaší aplikace. Když se aplikace nasadí do produkčního prostředí, měli byste pravidelně monitorovat stav aplikace a pomocí následujících datových bodů vyhodnotit, že se obnovení opravňuje:

1. Trvalá Chyba připojení z aplikační vrstvy k databázi.
2. Azure Portal zobrazuje výstrahu týkající se incidentu v oblasti s rozsáhlým dopadem.

> [!NOTE]
> Pokud používáte skupiny převzetí služeb při selhání a zvolíte automatické převzetí služeb při selhání, proces obnovení je automatizovaný a transparentní pro aplikaci.

V závislosti na vaší tolerance vaší aplikace na výpadky a možné obchodní odpovědnosti můžete zvážit následující možnosti obnovení.

K získání nejnovějšího geograficky replikovaného bodu obnovení použijte [databázi Get obnovitelné databáze](/previous-versions/azure/reference/dn800985(v=azure.100)) ( *LastAvailableBackupDate* ).

## <a name="wait-for-service-recovery"></a>Počkat na obnovení služby

Azure Teams funguje usilovně k tomu, aby co nejrychleji obnovil dostupnost služby, ale v závislosti na hlavní příčině může trvat hodiny nebo dny.  Pokud vaše aplikace může tolerovat významné výpadky, můžete jednoduše počkat na dokončení obnovení. V takovém případě není nutná žádná akce s vaší částí. Aktuální stav služby můžete zobrazit na našem [řídicím panelu Azure Service Health](https://azure.microsoft.com/status/). Po obnovení této oblasti se obnoví dostupnost vaší aplikace.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Převzetí služeb při selhání geograficky replikovaným sekundárním serverem ve skupině převzetí služeb při selhání

Pokud by výpadky vaší aplikace mohly vést k obchodním zodpovědnostem, měli byste používat skupiny převzetí služeb při selhání. Umožňuje aplikaci rychle obnovit dostupnost v jiné oblasti v případě výpadku. Kurz najdete v tématu [implementace geograficky distribuované databáze](geo-distributed-application-configure-tutorial.md).

Chcete-li obnovit dostupnost databází, je třeba zahájit převzetí služeb při selhání sekundárním serverem pomocí jedné z podporovaných metod.

Pro převzetí služeb při selhání geograficky replikovanou sekundární databází použijte jedno z následujících pokynů:

- [Převzetí služeb při selhání geograficky replikovaným sekundárním serverem pomocí Azure Portal](active-geo-replication-configure-portal.md)
- [Převzetí služeb při selhání sekundárním serverem pomocí PowerShellu](scripts/setup-geodr-and-failover-database-powershell.md)
- [Převzetí služeb při selhání sekundárním serverem pomocí jazyka Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Obnovení pomocí geografického obnovení

Pokud výpadek vaší aplikace nevede k obchodním zodpovědnostem, můžete použít [geografickou obnovu](recovery-using-backups.md) jako metodu pro obnovení databází aplikace. Vytvoří kopii databáze z poslední geograficky redundantní zálohy.

## <a name="configure-your-database-after-recovery"></a>Konfigurace databáze po obnovení

Pokud k zotavení z výpadku používáte geografické obnovení, musíte zajistit, aby připojení k novým databázím bylo správně nakonfigurováno, aby bylo možné obnovit normální funkci aplikace. Toto je kontrolní seznam úkolů pro přípravu obnovené databáze.

### <a name="update-connection-strings"></a>Aktualizace připojovacích řetězců

Vzhledem k tomu, že se obnovená databáze nachází na jiném serveru, musíte aktualizovat připojovací řetězec aplikace tak, aby odkazoval na tento server.

Další informace o změně připojovacích řetězců najdete v příslušném vývojovém jazyce pro [knihovnu připojení](connect-query-content-reference-guide.md#libraries).

### <a name="configure-firewall-rules"></a>Konfigurace pravidel brány firewall

Je nutné zajistit, aby pravidla brány firewall nakonfigurovaná na serveru a v databázi odpovídala nastavením nakonfigurovaným na primárním serveru a primární databázi. Další informace najdete v tématu [Postup: Konfigurace nastavení brány firewall (Azure SQL Database)](firewall-configure.md).

### <a name="configure-logins-and-database-users"></a>Konfigurace přihlašovacích údajů a uživatelů databáze

Musíte zajistit, aby všechny přihlašovací údaje, které vaše aplikace používá, existovaly na serveru, který je hostitelem obnovené databáze. Další informace najdete v tématu [Konfigurace zabezpečení pro geografickou replikaci](active-geo-replication-security-configure.md).

> [!NOTE]
> Během postupu zotavení po havárii byste měli nakonfigurovat a otestovat pravidla brány firewall serveru a přihlášení (a jejich oprávnění). Tyto objekty na úrovni serveru a jejich konfigurace nemusí být k dispozici během výpadku.

### <a name="setup-telemetry-alerts"></a>Nastavení výstrah telemetrie

Musíte zajistit, aby byla stávající nastavení pravidla upozornění aktualizována tak, aby se namapovala na obnovenou databázi a na jiný server.

Další informace o pravidlech upozornění databáze najdete v tématu [příjem oznámení o výstrahách](../../azure-monitor/platform/alerts-overview.md) a [sledování Service Health](../../service-health/service-notifications.md).

### <a name="enable-auditing"></a>Povolit auditování

Pokud je pro přístup k databázi vyžadováno auditování, je nutné povolit auditování po obnovení databáze. Další informace najdete v tématu [auditování databáze](../../azure-sql/database/auditing-overview.md).

## <a name="next-steps"></a>Další kroky

- Další informace o Azure SQL Database automatizovaných zálohách najdete v tématu [SQL Database automatizované zálohy](automated-backups-overview.md) .
- Další informace o scénářích pro návrh a obnovení provozní kontinuity najdete v tématu [scénáře kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md) .
- Další informace o použití automatizovaných záloh pro obnovení najdete v tématu [obnovení databáze ze zálohy iniciované službou](recovery-using-backups.md) .