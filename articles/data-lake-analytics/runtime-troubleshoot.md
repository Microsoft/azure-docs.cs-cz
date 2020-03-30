---
title: Řešení potíží s chybami modulu runtime Azure Data Lake Analytics U-SQL
description: Přečtěte si, jak řešit selhání modulu runtime U-SQL.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648451"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Zjistěte, jak řešit selhání modulu runtime U-SQL kvůli změnám modulu runtime

Běhový modul Azure Data Lake U-SQL, včetně kompilátoru, optimalizátoru a správce úloh, je to, co zpracovává váš kód U-SQL.

## <a name="choosing-your-u-sql-runtime-version"></a>Výběr runtime verze U-SQL

Při odesílání úloh U-SQL z Visual Studia, ADL SDK nebo portálu Azure Data Lake Analytics, vaše úloha bude používat aktuálně dostupné výchozí modul runtime. Nové verze modulu runtime U-SQL jsou vydávány v pravidelných intervalech a zahrnují menší aktualizace a opravy zabezpečení.

Můžete také zvolit vlastní verzi runtime; Buď proto, že chcete vyzkoušet novou aktualizaci, potřebujete zůstat ve starší verzi runtime nebo jste dostali opravu hotfix pro nahlášený problém, kde nemůžete čekat na pravidelnou novou aktualizaci.

> [!CAUTION]
> Výběr modulu runtime, který se liší od výchozího má potenciál přerušit vaše u-SQL úlohy. Tyto další verze používejte pouze pro testování.

Ve výjimečných případech může podpora společnosti Microsoft připnout jako výchozí verzi runtime pro váš účet. Ujistěte se, že jste tento kolík co nejdříve vrátili. Pokud k této verzi zůstanete připnuti, vyprší její platnost později.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Sledování úloh U-SQL runtime verze

Historii, kterou verze za běhu vaše minulé úlohy použily v historii úloh vašeho účtu, najdete v prohlížeči úloh Visual Studia nebo v historii úloh portálu Azure.

1. Na webu Azure portal přejděte na svůj účet Data Lake Analytics.
2. Vyberte **Zobrazit všechny úlohy**. Zobrazí se seznam všech aktivních a nedávno dokončených úloh v účtu.
3. Případně můžete klepnout na **filtr,** abyste našli úlohy podle **časového rozsahu**, **názvu úlohy**a hodnot **autora.**
4. Můžete zobrazit za běhu použitý v dokončených úlohách.

![Zobrazení runtime verze minulé úlohy](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

Dostupné verze runtime se v průběhu času mění. Výchozí doba runtime se vždy nazývá "výchozí" a po určitou dobu udržujeme alespoň předchozí runtime k dispozici a z různých důvodů zpřístupníme speciální runtime. Explicitně pojmenované runtimes obecně postupujte podle následujícího formátu (kurzíva se používají pro proměnné části a [] označuje volitelné části):

release_YYYYMMDD_adl_buildno[_modifier]

Například release_20190318_adl_3394512_2 znamená druhou verzi sestavení 3394512 runtime verze z března 18 2019 a release_20190318_adl_3394512_private znamená soukromé sestavení stejné verze. Poznámka: Datum se vztahuje k datu, kdy byla provedena poslední kontrola pro toto vydání, a ne nutně oficiální datum vydání.

Následují aktuálně dostupné verze runtime.

- release_20190318_adl_3394512
- release_20190318_adl_5832669 aktuální výchozí nastavení
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Poradce při potížích s verzí modulu runtime u-SQL

Existují dva možné problémy s verzí runtime, se kterými se můžete setkat:

1. Skript nebo nějaký uživatelský kód mění chování z jedné verze na další. Tyto změny jsou obvykle sdělovány předem se zveřejněním poznámek k verzi. Pokud narazíte na takovou změnu, obraťte se na podporu společnosti Microsoft a nahlaste toto chování (v případě, že ještě nebyl dokumentován) a odešlete úlohy proti starší verzi runtime.

2. Jste používali non-výchozí runtime explicitně nebo implicitně, když byl připnutý k vašemu účtu a že runtime byl odebrán po určité době. Pokud narazíte na chybějící runtime, upgradujte skripty tak, aby běžely s aktuálním výchozím runtime. Pokud potřebujete více času, obraťte se na podporu společnosti Microsoft.

## <a name="see-also"></a>Viz také

- [Azure Data Lake Analytics – přehled](data-lake-analytics-overview.md)
- [Správa Azure Data Lake Analytics pomocí portálu Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorování úloh v Azure Data Lake Analytics pomocí portálu Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
