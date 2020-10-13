---
title: Řešení potíží se selháním modulu runtime pro Azure Data Lake Analytics U-SQL
description: Přečtěte si, jak řešit potíže s modulem runtime U-SQL.
services: data-lake-analytics
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 39b4a3bc5e5f70a5699f4fd84ec86dc61cf21483
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87475256"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Přečtěte si, jak řešit problémy s modulem runtime U-SQL kvůli změnám za běhu

Modul runtime Azure Data Lake U-SQL, včetně kompilátoru, Optimalizátoru a Správce úloh, je, který zpracovává váš kód U-SQL.

## <a name="choosing-your-u-sql-runtime-version"></a>Výběr verze modulu runtime U-SQL

Když odesíláte úlohy U-SQL ze sady Visual Studio, ADL SDK nebo portálu Azure Data Lake Analytics, bude vaše úloha používat aktuálně dostupný výchozí modul runtime. Nové verze modulu runtime U-SQL jsou vydávány pravidelně a zahrnují dílčí aktualizace a opravy zabezpečení.

Můžete také zvolit vlastní verzi modulu runtime; vzhledem k tomu, že chcete vyzkoušet novou aktualizaci, je nutné zůstat ve starší verzi modulu runtime nebo byly poskytnuty v případě nahlášeného problému s opravou hotfix, kde nelze čekat na běžnou novou aktualizaci.

> [!CAUTION]
> Výběr modulu runtime, který je jiný než výchozí, má potenciál rozdělit úlohy U-SQL. Použijte tyto další verze pouze pro testování.

Ve výjimečných případech může podpora Microsoftu připnout jinou verzi modulu runtime jako výchozí pro váš účet. Ujistěte se prosím, že jste Tento PIN kód znovu navrátili co nejdříve. Pokud zůstane připnuté k této verzi, platnost vyprší později.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Monitorování úloh verze modulu runtime U-SQL

V historii úloh v prohlížeči úloh sady Visual Studio nebo v historii úloh Azure Portal můžete zobrazit historii, které verze modulu runtime v minulých úlohách používala v historii úloh vašeho účtu.

1. V Azure Portal přejít na účet Data Lake Analytics.
2. Vyberte **Zobrazit všechny úlohy**. Zobrazí se seznam všech aktivních a nedávno dokončených úloh v účtu.
3. V případě potřeby můžete kliknout na tlačítko **Filtr** , což vám umožní najít úlohy podle **časového rozsahu**, **názvu úlohy**a hodnot **autora** .
4. Můžete zobrazit modul runtime použitý v dokončených úlohách.

![Zobrazení běhové verze minulé úlohy](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

Dostupné verze modulu runtime se v průběhu času mění. Výchozí modul runtime se vždy nazývá "výchozí" a v některých případech udržujeme alespoň předchozí modul runtime, který je k dispozici pro nejrůznější účely. Explicitně pojmenované moduly runtime se obecně řídí následujícím formátem (kurzíva se používají pro proměnné části a [] označuje volitelné části):

release_YYYYMMDD_adl_buildno [_modifier]

Například release_20190318_adl_3394512_2 znamená druhou verzi sestavení 3394512 běhové verze z března 18 2019 a release_20190318_adl_3394512_private znamená privátní sestavení stejné verze. Poznámka: datum se vztahuje na dobu, kdy se poslední vrácení se změnami povedlo pro danou verzi, a ne nutně na oficiální datum vydání.


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Řešení potíží s verzí modulu runtime U-SQL

Existují dva možné problémy s verzí modulu runtime, se kterými se můžete setkat:

1. Skript nebo některé uživatelské kódy mění chování z jedné verze na další. Tyto zásadní změny se většinou sdělují před časem a s publikací poznámky k verzi. Pokud narazíte na zásadní změnu, kontaktujte prosím podpora Microsoftu, abyste nahlásili toto porušení zásad (v případě, že ještě není dokumentováno), a odešlete úlohy do starší verze modulu runtime.

2. V případě, že jste připnuli k vašemu účtu a tento modul runtime byl po určitou dobu odstraněn, používali jste nevýchozí modul runtime buď explicitně, nebo implicitně. Pokud narazíte na chybějící moduly runtime, upgradujte prosím skripty, aby běžely s aktuálním výchozím modulem runtime. Pokud potřebujete další čas, kontaktujte prosím podpora Microsoftu

## <a name="see-also"></a>Viz také

- [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Správa Azure Data Lake Analytics pomocí Azure Portal](data-lake-analytics-manage-use-portal.md)
- [Monitorování úloh v Azure Data Lake Analytics pomocí Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
