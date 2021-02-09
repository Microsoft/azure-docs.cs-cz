---
title: Migrace na monitorování připojení z Network Performance Monitor
titleSuffix: Azure Network Watcher
description: Přečtěte si, jak migrovat na monitorování připojení z Network Performance Monitor.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 0bb46c17ece9a38d9f1e10c79a4b026efa0ece4c
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833792"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migrace na monitorování připojení z Network Performance Monitor

> [!IMPORTANT]
> Od 1. července 2021 nebudete moci přidat nové testy do existujícího pracovního prostoru nebo povolit nový pracovní prostor v Network Performance Monitor. Můžete pokračovat v používání testů vytvořených před 1. července 2021. Pokud chcete minimalizovat přerušení služby na vaše aktuální úlohy, migrujte testy z Network Performance Monitor na nové monitorování připojení v Azure Network Watcher před 29. února 2024.

Testy můžete migrovat z Network Performance Monitor (NPM) na nové, vylepšené monitorování připojení jediným kliknutím a s nulovými výpadky. Další informace o výhodách najdete v tématu [monitorování připojení](./connection-monitor-overview.md).


## <a name="key-points-to-note"></a>Klíčové body k označení

Migrace pomáhá získat následující výsledky:

* Místní agenti a nastavení brány firewall fungují tak, jak jsou. Nejsou vyžadovány žádné změny. Log Analytics agenti, kteří jsou nainstalováni na virtuálních počítačích Azure, je třeba nahradit rozšířením Network Watcher.
* Existující testy jsou namapovány na monitorování připojení > testovací skupiny > formátu testu. Výběrem možnosti **Upravit** můžete zobrazit a upravit vlastnosti nového monitorování připojení, stáhnout šablonu, která v něm provede změny, a odeslat šablonu prostřednictvím Azure Resource Manager.
* Agenti odesílají data do pracovního prostoru Log Analytics i do metriky.
* Monitorování dat:
   * **Data v Log Analytics**: před migrací zůstanou data v pracovním prostoru, ve kterém je npm nakonfigurovaný v tabulce NetworkMonitoring. Po migraci přejde data do tabulky NetworkMonitoring a do tabulky ConnectionMonitor_CL ve stejném pracovním prostoru. Po zakázání testů v NPM jsou data uložena pouze v tabulce ConnectionMonitor_CL.
   * **Výstrahy založené na protokolech, řídicí panely a integrace**: dotazy je nutné ručně upravit na základě nové ConnectionMonitor_CL tabulky. Postup opětovného vytvoření výstrah v metrikách najdete v tématu [monitorování připojení k síti pomocí monitorování připojení](./connection-monitor-overview.md#metrics-in-azure-monitor).
    
## <a name="prerequisites"></a>Požadavky

* Ujistěte se, že ve vašem předplatném a oblasti pracovního prostoru Log Analytics je povolená možnost Network Watcher.
* Virtuální počítače Azure s nainstalovanými agenty Log Analytics musí být povolené s rozšířením Network Watcher.

## <a name="migrate-the-tests"></a>Migrace testů

Chcete-li migrovat testy z Network Performance Monitor na monitorování připojení, postupujte následovně:

1. V Network Watcher vyberte **monitorování připojení** a pak vyberte kartu **MIGROVAT testy z npm** . 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Migrace testů z Network Performance Monitor na monitorování připojení" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. V rozevíracích seznamech vyberte své předplatné a pracovní prostor a pak vyberte funkci NPM, kterou chcete migrovat. 
1. Vyberte **importovat** a migrujte testy.

Po zahájení migrace proběhne následující změny: 
* Vytvoří se nový prostředek monitorování připojení.
   * Vytvoří se jedno monitorování připojení na oblast a předplatné. Pro testy s místními agenty je název monitoru nového připojení formátovaný jako `<workspaceName>_"on-premises"` . Pro testy s agenty Azure je název nového monitorování připojení formátovaný jako `<workspaceName>_<Azure_region_name>` .
   * Data monitorování se teď ukládají do stejného Log Analytics pracovního prostoru, ve kterém je povolený NPM, v nové tabulce nazvané Connectionmonitor_CL. 
   * Název testu se přenese jako název testovací skupiny. Popis testu není migrován.
   * Zdrojové a cílové koncové body jsou vytvořeny a použity v nové testovací skupině. Pro místní agenty jsou koncové body formátovány jako `<workspaceName>_"endpoint"_<FQDN of on-premises machine>` . Pokud migrace testů obsahuje agenty, kteří nepoužívají, je nutné v Azure povolit agenty a znovu provést migraci.
   * Cílový port a interval zjišťování jsou přesunuty do konfigurace testu s názvem *TC_ \<testname>* a *TC_ \<testname> _AppThresholds*. Protokol je nastaven na základě hodnot portů. Prahové hodnoty úspěšnosti a jiné volitelné vlastnosti jsou ponechány prázdné.
* NPM není zakázané, takže migrované testy můžou dál posílat data do tabulek NetworkMonitoring a ConnectionMonitor_CL. Tento přístup zajišťuje, že existující výstrahy a integrace založené na protokolu nebudou mít vliv na.
* Nově vytvořené monitorování připojení je viditelné v monitorování připojení.

Po dokončení migrace nezapomeňte:
* Ručně zakažte testy v NPM. Dokud to neuděláte, bude se vám za ně účtovat i nadále. 
* Když NPM zakážete, znovu vytvořte upozornění v tabulce ConnectionMonitor_CL nebo použijte metriky. 
* Migrujte všechna externí integrace do ConnectionMonitor_CL tabulky. Příklady externích integrací jsou řídicí panely v Power BI a Grafana a integrace se systémy SIEM (Security Information and Event Management).


## <a name="next-steps"></a>Další kroky

Další informace o monitorování připojení najdete v těchto tématech:
* [Migrace z monitoru připojení (Classic) na monitorování připojení](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Vytvoření monitorování připojení pomocí Azure Portal](./connection-monitor-create-using-portal.md)
