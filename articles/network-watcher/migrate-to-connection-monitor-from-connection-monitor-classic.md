---
title: Migrace na monitorování připojení z monitorování připojení
titleSuffix: Azure Network Watcher
description: Přečtěte si, jak migrovat na monitorování připojení z monitorování připojení.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: eed68ff7e6b002e7a5985b336686b646a1a9acee
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522943"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor-classic"></a>Migrace na monitorování připojení z monitoru připojení (klasický)

Stávající monitorování připojení můžete migrovat na nové, vylepšené monitorování připojení jenom několika kliknutími a s nulovými výpadky. Další informace o výhodách najdete v tématu [monitorování připojení](./connection-monitor-overview.md).

## <a name="key-points-to-note"></a>Klíčové body k označení

Migrace pomáhá získat následující výsledky:

* Nastavení agenti a brány firewall fungují tak, jak je. Nejsou vyžadovány žádné změny. 
* Existující monitory připojení jsou namapovány na monitorování připojení > testovací skupiny > formátu testu. Výběrem možnosti **Upravit** můžete zobrazit a upravit vlastnosti nového monitorování připojení, stáhnout šablonu, která provede změny v monitorování připojení a odeslat ji pomocí Azure Resource Manager. 
* Virtuální počítače Azure s rozšířením Network Watcher odesílají data do pracovního prostoru i do metriky. Monitorování připojení zpřístupňuje data prostřednictvím nové metriky (ChecksFailedPercent a RoundTripTimeMs) místo starých metrik (ProbesFailedPercent a AverageRoundtripMs). 
* Monitorování dat:
   * **Výstrahy**: automaticky se migrují na nové metriky.
   * **Řídicí panely a integrace**: vyžaduje ruční úpravu sady metrik. 
    
## <a name="prerequisites"></a>Předpoklady

Pokud používáte vlastní pracovní prostor, ujistěte se, že je ve vašem předplatném a v oblasti pracovního prostoru Log Analytics povolená možnost Network Watcher. 

## <a name="migrate-the-connection-monitors"></a>Migrace monitorování připojení

1. Pokud chcete migrovat starší monitory připojení do novějšího, vyberte **monitorování připojení** a pak vyberte **migrovat monitory připojení**.

    ![Snímek obrazovky znázorňující migraci monitorování připojení do monitorování připojení](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Vyberte své předplatné a monitory připojení, které chcete migrovat, a pak vyberte **migrovat vybrané**. 

Po několika kliknutích jste migrovali existující monitorování připojení do monitorování připojení. 

Nyní můžete přizpůsobit vlastnosti sledování připojení, změnit výchozí pracovní prostor, stáhnout šablony a zkontrolovat stav migrace. 

Po zahájení migrace proběhne následující změny: 
* Prostředek Azure Resource Manager se změní na novější monitor připojení.
    * Název, oblast a odběr monitorování připojení zůstávají beze změny. ID prostředku není nijak ovlivněno.
    * V případě, že se monitorování připojení nepřizpůsobí, vytvoří se v předplatném a v oblasti monitorování připojení výchozí Log Analytics pracovní prostor. Tento pracovní prostor je místo, kde jsou uložená data monitorování. Data výsledků testů jsou také uložena v metrikách.
    * Každý test je migrován do testovací skupiny s názvem *defaultTestGroup*.
    * Zdrojové a cílové koncové body jsou vytvořeny a použity v nové testovací skupině. Výchozí názvy jsou *defaultSourceEndpoint* a *defaultDestinationEndpoint*.
    * Cílový port a interval pro zjišťování se přesunou do konfigurace testu s názvem *defaultTestConfiguration*. Protokol je nastaven na základě hodnot portů. Prahové hodnoty úspěšnosti a jiné volitelné vlastnosti jsou ponechány prázdné.
* Výstrahy metrik se migrují na výstrahy monitorování připojení výstrahy metrik. Metriky se liší, tedy změnu. Další informace najdete v tématu [monitorování připojení k síti pomocí monitorování připojení](./connection-monitor-overview.md#metrics-in-azure-monitor).
* Migrované monitorování připojení se už nezobrazuje jako řešení pro monitorování starších připojení. Nyní jsou k dispozici pro použití pouze v monitorování připojení.
* Všechna externí integrace, jako jsou například řídicí panely v Power BI a Grafana, a integrace s systémy SIEM (Security Information and Event Management), musí být migrovány ručně. Toto je jediný ruční krok, který musíte provést při migraci nastavení.

## <a name="next-steps"></a>Další kroky

Další informace o monitorování připojení najdete v těchto tématech:
* [Migrace z Network Performance Monitor na monitorování připojení](/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor)
* [Vytvoření monitorování připojení pomocí Azure Portal](./connection-monitor-create-using-portal.md)