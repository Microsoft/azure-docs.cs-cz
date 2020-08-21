---
title: Migrace na monitor připojení (Preview) z monitorování připojení
titleSuffix: Azure Network Watcher
description: Přečtěte si, jak migrovat na monitorování připojení (Preview) z monitorování připojení.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: ddf6e326df876229d32ef15983f76879836f1fca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701595"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>Migrace na monitor připojení (Preview) z monitorování připojení

Existující monitorování připojení můžete migrovat na nové a vylepšené monitorování připojení (Preview) v jednom kliknutím a s nulovými výpadky. Pokud se o výhodách chcete dozvědět víc, můžete si přečíst téma [monitorování připojení (Preview)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview) .

## <a name="key-points-to-note"></a>Klíčové body k označení

* Nastavení agenti a brány firewall budou fungovat tak, jak je (nevyžaduje se dotykové ovládání). 
* Existující monitory připojení budou namapovány na monitorování připojení (Preview)-> testovací skupina-> formát testu. Uživatelé můžou kliknout na *Upravit* a zobrazit a upravit vlastnosti nového monitorování připojení a stáhnout šablonu, aby se změny zobrazily v monitorování připojení a odesílaly je prostřednictvím Azure Resource Manager. 
* Virtuální počítače Azure s rozšířením Network Watcher odesílají data do pracovního prostoru i metrik. Monitory připojení zpřístupňují data prostřednictvím nové metriky (ChecksFailedPercent (Preview) a RoundTripTimeMs (Preview)) namísto zastavování staré metriky (ProbesFailedPercent a AverageRoundtripMs). 
* Data monitorování
    * Výstrahy – budou migrovány na nové metriky jako součást migrace.
    * Řídicí panely a integrace – sady metrik budete muset ručně upravit. 
    
## <a name="prerequisites"></a>Předpoklady

Pokud používáte vlastní pracovní prostor, ujistěte se, že je v předplatném a oblasti pracovního prostoru Log Analytics povolená možnost Network Watcher. 

## <a name="steps-to-migrate-from-connection-monitor-to-connection-monitor-preview"></a>Postup migrace z monitorování připojení do monitorování připojení (Preview)

1. Klikněte na monitorování připojení, přejděte na migrace monitorování připojení a migrujte monitorování připojení ze starší verze do novějšího řešení.

    ![Snímek obrazovky s informacemi o migraci monitorování připojení do monitorování připojení verze Preview](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Vyberte monitorování předplatného a připojení a klikněte na migrovat vybrané. V jednom kliknutím migrujte existující monitorování připojení do monitorování připojení (Preview). 
1. Můžete přizpůsobit vlastnosti sledování připojení, změnit výchozí pracovní prostor, stáhnout šablonu a ověřit stav migrace. 
1. Po zahájení migrace dojde k následujícím změnám: 
    1. Azure Resource Manager změny prostředků do novějšího monitorování připojení
        1. Název, oblast a odběr monitorování připojení zůstávají beze změny. Proto neexistuje žádný vliv na ID prostředku.
        1. Pokud není přizpůsobený, vytvoří se v oblasti a předplatném monitorování připojení výchozí Log Analytics pracovní prostor. Tento pracovní prostor je místo, kde se budou ukládat data monitorování. Data výsledků testů budou také uložena v metrikách.
        1. Každý test je migrován do testovací skupiny s názvem * defaultTestGroup *
        1.  Zdrojové a cílové koncové body jsou vytvořeny a použity v vytvořené testovací skupině. Výchozí názvy jsou *defaultSourceEndpoint* a *defaultDestinationEndpoint* .
        1. Cílový port a interval pro zjišťování se přesunou do konfigurace testu s názvem *defaultTestConfiguration*. V závislosti na hodnotách portů je protokol nastavený. Prahové hodnoty úspěšnosti a jiné volitelné vlastnosti jsou ponechány prázdné.
    1. Výstrahy metriky se migrují na výstrahy metriky monitorování připojení (Preview). Metriky se liší <link to metric section in the doc> , takže změna
    1. Migrace migrovaných připojení se nezobrazí ve starším řešení nástroje pro monitorování připojení, budou teď dostupné jenom pro použití v monitorování připojení (Preview).
    1. Všechna externí integrace, jako jsou řídicí panely v Power BI Grafana, integrace se systémy SIEM, bude muset uživatel migrovat přímo. Toto je jediný ruční krok, který uživatel potřebuje k provedení migrace jeho nastavení.

## <a name="next-steps"></a>Další kroky

* Naučte [se migrovat z Network Performance Monitor na monitorování připojení (Preview)](migrate-to-connection-monitor-preview-from-network-performance-monitor.md) .
* Zjistěte [, jak vytvořit monitorování připojení (Preview) pomocí Azure Portal](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
