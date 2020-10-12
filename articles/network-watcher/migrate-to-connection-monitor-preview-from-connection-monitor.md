---
title: Migrace na monitor připojení (Preview) z monitorování připojení
titleSuffix: Azure Network Watcher
description: Přečtěte si, jak migrovat na monitorování připojení (Preview) z monitorování připojení.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: 05b42024529b8d9de3590488ecafbdf83283e007
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441812"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>Migrace na monitor připojení (Preview) z monitorování připojení

Stávající monitorování připojení můžete migrovat na nové, vylepšené monitorování připojení (Preview) jenom několika kliknutími a s nulovými výpadky. Další informace o výhodách najdete v tématu [monitorování připojení (Preview)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview).

## <a name="key-points-to-note"></a>Klíčové body k označení

Migrace pomáhá získat následující výsledky:

* Nastavení agenti a brány firewall fungují tak, jak je. Nejsou vyžadovány žádné změny. 
* Existující monitory připojení jsou namapovány na monitorování připojení (Preview) > testovací skupiny > formátu testu. Výběrem možnosti **Upravit**můžete zobrazit a upravit vlastnosti nového monitorování připojení, stáhnout šablonu, která provede změny v monitorování připojení a odeslat ji pomocí Azure Resource Manager. 
* Virtuální počítače Azure s rozšířením Network Watcher odesílají data do pracovního prostoru i do metriky. Monitorování připojení zpřístupňuje data prostřednictvím nové metriky (ChecksFailedPercent [Preview] a RoundTripTimeMs [Preview]) místo starých metrik (ProbesFailedPercent a AverageRoundtripMs). 
* Monitorování dat:
   * **Výstrahy**: automaticky se migrují na nové metriky.
   * **Řídicí panely a integrace**: vyžaduje ruční úpravu sady metrik. 
    
## <a name="prerequisites"></a>Požadavky

Pokud používáte vlastní pracovní prostor, ujistěte se, že je ve vašem předplatném a v oblasti pracovního prostoru Log Analytics povolená možnost Network Watcher. 

## <a name="migrate-the-connection-monitors"></a>Migrace monitorování připojení

1. Pokud chcete migrovat starší monitory připojení do novějšího, vyberte **monitorování připojení**a pak vyberte **migrovat monitory připojení**.

    ![Snímek obrazovky znázorňující migraci monitorování připojení do monitorování připojení (Preview)](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Vyberte své předplatné a monitory připojení, které chcete migrovat, a pak vyberte **migrovat vybrané**. 

Po několika kliknutích jste migrovali existující monitorování připojení do monitorování připojení (Preview). 

Nyní můžete přizpůsobit vlastnosti monitorování připojení (Preview), změnit výchozí pracovní prostor, stáhnout šablony a zkontrolovat stav migrace. 

Po zahájení migrace proběhne následující změny: 
* Prostředek Azure Resource Manager se změní na novější monitor připojení.
    * Název, oblast a odběr monitorování připojení zůstávají beze změny. ID prostředku není nijak ovlivněno.
    * V případě, že se monitorování připojení nepřizpůsobí, vytvoří se v předplatném a v oblasti monitorování připojení výchozí Log Analytics pracovní prostor. Tento pracovní prostor je místo, kde jsou uložená data monitorování. Data výsledků testů jsou také uložena v metrikách.
    * Každý test je migrován do testovací skupiny s názvem *defaultTestGroup*.
    * Zdrojové a cílové koncové body jsou vytvořeny a použity v nové testovací skupině. Výchozí názvy jsou *defaultSourceEndpoint* a *defaultDestinationEndpoint*.
    * Cílový port a interval pro zjišťování se přesunou do konfigurace testu s názvem *defaultTestConfiguration*. Protokol je nastaven na základě hodnot portů. Prahové hodnoty úspěšnosti a jiné volitelné vlastnosti jsou ponechány prázdné.
* Výstrahy metrik se migrují na výstrahy metriky monitorování připojení (Preview). Metriky se liší, tedy změnu. Další informace najdete v tématu [monitorování připojení k síti pomocí monitorování připojení (Preview)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#metrics-in-azure-monitor).
* Migrované monitorování připojení se už nezobrazuje jako řešení pro monitorování starších připojení. Jsou teď dostupné jenom pro použití v monitorování připojení (Preview).
* Všechna externí integrace, jako jsou například řídicí panely v Power BI a Grafana, a integrace s systémy SIEM (Security Information and Event Management), musí být migrovány ručně. Toto je jediný ruční krok, který musíte provést při migraci nastavení.

## <a name="next-steps"></a>Další kroky

Další informace o monitorování připojení (Preview) najdete v tématu:
* [Migrace z Network Performance Monitor na monitorování připojení (Preview)](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* [Vytvoření monitorování připojení (Preview) pomocí Azure Portal](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
