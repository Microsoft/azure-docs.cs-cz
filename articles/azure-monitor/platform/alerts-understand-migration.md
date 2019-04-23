---
title: Pochopit, jak dobrovolné výstrahy migračního nástroje v Azure Monitor funguje
description: Pochopit, jak funguje nástroj pro migraci výstrahy a řešení potíží, pokud narazíte na problémy.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: a45a0cff606bc854924d5da0841b26e1cb9031bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60347596"
---
# <a name="understand-how-the-migration-tool-works"></a>Pochopit, jak funguje nástroj pro migraci

Jako [jsme oznámili už dřív](monitoring-classic-retirement.md), klasických upozornění ve službě Azure Monitor se vyřazuje v červenci 2019. Nástroj pro migraci k aktivaci migrace dobrovolně je k dispozici na webu Azure portal a zavádí pro zákazníky, kteří používají klasický pravidla upozornění.

Tento článek vás provede fungování nástroj pro migraci dobrovolná. Popisuje také nápravy pro některé běžné problémy.

## <a name="which-classic-alert-rules-can-be-migrated"></a>Která klasického upozornění pravidla je možné migrovat?

Přestože téměř všechna pravidla upozornění classic je možné migrovat pomocí nástroje, existují některé výjimky. Následující pravidla upozornění se nemigruje pomocí nástroje (nebo při automatické migraci v červenci 2019)

- Klasické pravidla upozornění na metriky hosta virtuálního počítače (Windows nebo Linuxem). [Přečtěte si pokyny o tom, jak znovu vytvořit tato pravidla výstrah ve službě nové upozornění na metriky](#guest-metrics-on-virtual-machines)
- Klasické pravidla upozornění na metriky klasického úložiště. [Přečtěte si pokyny k monitorování vaše účty klasického úložiště.](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)
- Klasické pravidla upozornění na některé metriky účtu úložiště. [Podrobnosti níže](#storage-account-metrics)

Pokud má vaše předplatné tyto klasické pravidla, zbývající pravidla se budou migrovat, ale tato pravidla se musí migrovat ručně. Nemůžeme poskytovat automatické migrace, všechny takové existující klasického upozornění metrik bude práce do června 2020 poskytnout vám čas přesunout do nové výstrahy. Však žádné nové výstrahy classic je možné vytvořit příspěvek dne 2019.

### <a name="guest-metrics-on-virtual-machines"></a>Metrik hosta na virtuálních počítačích

Aby bylo možné vytvořit nové upozornění na metriky týkající se metrik hosta, metrik hosta potřebovat k odeslání do úložiště Azure Monitor vlastní metriky. Postupujte podle níže uvedených pokynů a povolte jímka Azure monitoru v nastavení diagnostiky.

- [Zapnutí metrik hosta pro virtuální počítače s Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Povolení metrik hosta pro virtuální počítače s Linuxem](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)

Po dokončení výše uvedené kroky se dají vytvořit nové upozornění na metriky na metrik hosta. Jakmile se znovu vytvoří nové upozornění na metriky se dá odstranit upozornění classic.

### <a name="storage-account-metrics"></a>Metriky účtu úložiště

Je možné migrovat všechny klasických upozornění na účty úložiště s výjimkou těchto upozornění na následujících metrikách:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

Klasické pravidla upozornění na procenta metriky se musí migrovat na základě [mapování mezi metriky staré a nové úložiště](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Bude nutné prahové hodnoty odpovídajícím způsobem změnit, protože nové metriky, které jsou k dispozici je některý z absolutní.

Klasické pravidla upozornění AnonymousThrottlingError a SASThrottlingError budou muset možné rozdělit na dvě nové výstrahy jako by zde není žádná kombinované metriku, která poskytuje stejné funkce. Bude nutné prahové hodnoty odpovídajícím způsobem upravit.

## <a name="roll-out-phases"></a>Fáze zavedení

Nástroj pro migraci zavádí ve fázích pro zákazníky, které používají klasický pravidla upozornění. **Vlastníci předplatného** obdrží e-mail, až bude předplatné připravené k migraci pomocí nástroje.

> [!NOTE]
> Jak nástroj je zavádění ve fázích, v počátečních fázích, může se zobrazit, že většina vašich předplatných ještě nejsou připravené k migraci.

Aktuálně **dílčí** předplatných, která **pouze** mít classic pravidla upozornění na následující typy jsou označeny jako připravené k migraci prostředků. Podpora pro další typy prostředků bude přidána v budoucích fází.

- Microsoft.apimanagement/service
- Microsoft.batch/batchaccounts
- Microsoft.cache/redis
- Microsoft.datafactory/datafactories
- Microsoft.dbformysql/servers
- Microsoft.dbforpostgresql/servers
- Microsoft.eventhub/namespaces
- Microsoft.logic/workflows
- Microsoft.network/applicationgateways
- Microsoft.network/dnszones
- Microsoft.network/expressroutecircuits
- Microsoft.network/loadbalancers
- Microsoft.network/networkwatchers/connectionmonitors
- Microsoft.network/publicipaddresses
- Microsoft.network/trafficmanagerprofiles
- Microsoft.network/virtualnetworkgateways
- Microsoft.search/searchservices
- Microsoft.servicebus/namespaces
- Microsoft.streamanalytics/streamingjobs
- Microsoft.timeseriesinsights/environments
- Microsoft.web/hostingenvironments/workerpools
- Microsoft.web/serverfarms
- Microsoft.web/sites

## <a name="who-can-trigger-the-migration"></a>Kdo může aktivovat migrace?

Každý uživatel, který má předdefinovaná role z **Přispěvatel monitorování** na předplatné úrovně budete moct aktivovat migrace. Uživatelé s vlastní role se stejnými následující oprávnění, mohou aktivovat také migrace:

- * / čtení
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-issues-and-remediations"></a>Běžné problémy a jejich náprav

Jakmile [aktivovat migrace](alerts-using-migration-tool.md), použijeme e-mailové adresy uvedené upozornění na dokončení migrace nebo pokud je akce dělat. Následující část popisuje některé běžné problémy a jejich řešení

### <a name="validation-failed"></a>Ověření se nepovedlo

Z důvodu některých nedávné změny classic pravidla výstrah v rámci vašeho předplatného není možné migrovat předplatné. Jedná se o dočasný problém. Migraci můžete restartovat, jakmile se stav migrace přesune zpět **připravený pro migraci** za několik dnů.

### <a name="policyscope-lock-preventing-us-from-migrating-your-rules"></a>Obor zásady/lock nám brání migrace vašich pravidel

V rámci migrace se vytvoří nová upozornění metrik a nové skupiny akcí a klasické pravidla upozornění se odstraní (Jakmile se vytvoří nová pravidla). Je však zásad nebo obor zámku nám brání vytváření prostředků. V závislosti na zámek zásad nebo oboru nelze migrovat některá nebo všechna pravidla. Můžete tento problém vyřešit tak, že odeberete zámek/zásady oboru dočasně a aktivovat migrovat znovu.

## <a name="next-steps"></a>Další postup

- [Jak používat nástroj pro migraci](alerts-using-migration-tool.md)
- [Příprava na migraci](alerts-prepare-migration.md)
