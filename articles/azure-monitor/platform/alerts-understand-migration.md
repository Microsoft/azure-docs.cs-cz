---
title: Pochopit, jak funguje dobrovolné migračního nástroje Azure Monitor výstrah
description: Pochopit, jak funguje nástroj pro migraci výstrahy a řešit problémy.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 9d872a6d753a206dcfb03761e50e5854db4f146e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071592"
---
# <a name="understand-how-the-migration-tool-works"></a>Pochopit, jak funguje nástroj pro migraci

Jako [jsme oznámili už dřív](monitoring-classic-retirement.md), klasických upozornění ve službě Azure Monitor se vyřazuje v září 2019 (byla původně 2019. července). Migrační nástroj je dostupný na webu Azure Portal pro zákazníky používající klasický pravidla upozornění a který chcete spustit migrace sami.

Tento článek vysvětluje, jak funguje nástroj pro migraci dobrovolná. Popisuje také nápravná opatření pro některé běžné problémy.

> [!NOTE]
> Datum vyřazení pro migraci klasických upozornění byla z důvodu zpoždění při uvádění nástroj pro migraci [rozšířená tak, aby 31. srpna 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) od původně oznámené data 30. června 2019.

## <a name="which-classic-alert-rules-can-be-migrated"></a>Která klasického upozornění pravidla je možné migrovat?

Přestože nástroj můžete migrovat téměř všechny klasické pravidel upozornění, existují některé výjimky. Následující pravidla upozornění se nemigruje s použitím nástroje (nebo při automatické migraci v září 2019):

- Klasické pravidla upozornění na metriky hosta virtuálního počítače (Windows nebo Linuxem). Zobrazit [pokyny k opětovnému vytváření těchto pravidel upozornění v nové upozornění na metriky](#guest-metrics-on-virtual-machines) dále v tomto článku.
- Klasické pravidla upozornění na metriky klasického úložiště. Zobrazit [pokyny pro monitorování účtů klasického úložiště](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Klasické pravidla upozornění na některé metriky účtu úložiště. Zobrazit [podrobnosti](#storage-account-metrics) dále v tomto článku.

Pokud vaše předplatné má tato pravidla classic, musíte je migrovat ručně. Protože neposkytujeme automatické migrace, všechny existující, klasického upozornění metrik z těchto typů budou nadále fungovat až do roku 2020 dne. Toto rozšíření nabízí vám čas přesunout do nové výstrahy. Však žádné nové výstrahy classic je možné vytvořit po srpnu 2019.

### <a name="guest-metrics-on-virtual-machines"></a>Metrik hosta na virtuálních počítačích

Než budete moct vytvořit nové upozornění na metriky týkající se metrik hosta, metrik hosta se musí odeslat na úložiště vlastních metrik Azure monitoru. Postupujte podle těchto pokynů a povolte jímka Azure monitoru v nastavení diagnostiky:

- [Zapnutí metrik hosta pro virtuální počítače s Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Povolení metrik hosta pro virtuální počítače s Linuxem](collect-custom-metrics-linux-telegraf.md)

Po dokončení těchto kroků můžete vytvořit nové upozornění na metriky týkající se metrik hosta. A po vytvoření nové upozornění na metriky, můžete odstranit upozornění classic.

### <a name="storage-account-metrics"></a>Metriky účtu úložiště

S výjimkou oznámení na tyto metriky se dají migrovat všechny klasických upozornění na účty úložiště:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

Klasické výstraha pravidla na procenta metriky se musí migrovat na základě [mapování mezi metriky staré a nové úložiště](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Bude nutné prahové hodnoty odpovídajícím způsobem upravit, protože nové metriky, které jsou k dispozici je některý z absolutní.

Klasické pravidla upozornění na AnonymousThrottlingError, SASThrottlingError a ThrottlingError musí rozdělit na dvě nové výstrahy, protože neexistuje žádný kombinované metriku, která poskytuje stejné funkce. Prahové hodnoty bude třeba ho upravit odpovídajícím způsobem.

## <a name="rollout-phases"></a>Fáze uvedení

Nástroj pro migraci zavádí ve fázích pro zákazníky, které používají klasický pravidla upozornění. Vlastníci předplatného obdrží e-mail, až bude předplatné připravené k migraci pomocí nástroje.

> [!NOTE]
> Protože nástroj je zavádění ve fázích, může se zobrazit, že většina vašich předplatných ještě nejsou připravené k migraci v raných fázích.

Aktuálně podmnožinu předplatných je označen jako připravený pro migraci. Obsahuje podmnožinu těchto předplatných, které mají pouze na následující typy prostředků classic pravidla upozornění. Podpora pro další typy prostředků bude přidána v budoucích fází.

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

Každý uživatel, který má předdefinovaná role Přispěvatel monitorování na úrovni předplatného můžete aktivovat migrace. Migraci můžete aktivovat také uživatelé, kteří mají vlastní roli s následujícími oprávněními:

- \* / čtení
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-problems-and-remedies"></a>Běžné problémy a náhrad

Poté co [aktivovat migrace](alerts-using-migration-tool.md), obdržíte e-mail na adresy, které jste zadali, které vás upozorní, že se migrace dokončí, nebo pokud není nutné provádět žádnou akci od vás. Tato část popisuje některé běžné problémy a řešení problémů s nimi.

### <a name="validation-failed"></a>Ověření se nezdařilo

Z důvodu některých nedávné změny classic pravidla výstrah v rámci vašeho předplatného není možné migrovat předplatné. Tento problém je dočasný. Migraci můžete restartovat po stavu migrace přesune zpět **připravený pro migraci** za několik dnů.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Zásady nebo obor zámku nám brání migrace vašich pravidel

Jako součást migrace se vytvoří nová upozornění metrik a nové skupiny akcí a pak se odstraní classic pravidla upozornění. Je však zásad nebo obor zámku nám brání vytváření prostředků. V závislosti na zámek zásad nebo oboru nelze migrovat některá nebo všechna pravidla. Tento problém lze vyřešit dočasně odebráním zámku oboru nebo zásad a aktivuje migrovat znovu.

## <a name="next-steps"></a>Další postup

- [Jak používat nástroj pro migraci](alerts-using-migration-tool.md)
- [Příprava na migraci](alerts-prepare-migration.md)
