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
ms.openlocfilehash: e95f6fdff164a6f5f9d4af4f19b1876d1483a70c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038709"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migrace na monitorování připojení z Network Performance Monitor

> [!IMPORTANT]
> Od 1. července 2021 nebudete moci přidat nové testy do existujícího pracovního prostoru nebo povolit nový pracovní prostor s Network Performance Monitor. Můžete pokračovat v používání testů vytvořených před 1. července 2021. Pokud chcete minimalizovat přerušení služby na vaše aktuální úlohy, migrujte testy z Network Performance Monitor na nové monitorování připojení v Azure Network Watcher před 29. února 2024.

Testy můžete migrovat z Network Performance Monitor (NPM) na nové, vylepšené monitorování připojení jediným kliknutím a s nulovými výpadky. Další informace o výhodách najdete v tématu [monitorování připojení](./connection-monitor-overview.md).


## <a name="key-points-to-note"></a>Klíčové body k označení

Migrace pomáhá získat následující výsledky:

* Místní agenti a nastavení brány firewall fungují tak, jak jsou. Nejsou vyžadovány žádné změny. Log Analytics agenti, kteří jsou nainstalováni na virtuálních počítačích Azure, je třeba nahradit [rozšířením Network Watcher](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).
* Existující testy jsou namapovány na monitorování připojení > testovací skupiny > formátu testu. Výběrem možnosti **Upravit** můžete zobrazit a upravit vlastnosti nového monitorování připojení, stáhnout šablonu, která v něm provede změny, a odeslat šablonu prostřednictvím Azure Resource Manager.
* Agenti odesílají data do pracovního prostoru Log Analytics i do metriky.
* Monitorování dat:
   * **Data v Log Analytics**: před migrací zůstanou data v pracovním prostoru, ve kterém je npm nakonfigurovaný v tabulce NetworkMonitoring. Po migraci přejde data do tabulky NetworkMonitoring, tabulky NWConnectionMonitorTestResult a tabulky NWConnectionMonitorPathResult ve stejném pracovním prostoru. Po zakázání testů v NPM budou data uložena pouze v tabulce NWConnectionMonitorTestResult a tabulce NWConnectionMonitorPathResult.
   * **Výstrahy založené na protokolech, řídicí panely a integrace**: dotazy musíte ručně upravit v závislosti na nové tabulce NWConnectionMonitorTestResult a tabulce NWConnectionMonitorPathResult. Postup opětovného vytvoření výstrah v metrikách najdete v tématu [monitorování připojení k síti pomocí monitorování připojení](./connection-monitor-overview.md#metrics-in-azure-monitor).
    
## <a name="prerequisites"></a>Požadavky

* Ujistěte se, že ve vašem předplatném a oblasti pracovního prostoru Log Analytics je povolená možnost Network Watcher. 
* V případě, že se virtuální počítač Azure patřící do jiné oblasti nebo předplatného, než je Log Analytics pracovního prostoru, používá jako koncový bod, ujistěte se, že pro toto předplatné a oblast jsou povolené Network Watcher.   
* Virtuální počítače Azure s nainstalovanými agenty Log Analytics musí být povolené s rozšířením Network Watcher.

## <a name="migrate-the-tests"></a>Migrace testů

Chcete-li migrovat testy z Network Performance Monitor na monitorování připojení, postupujte následovně:

1. V Network Watcher vyberte **monitorování připojení** a pak vyberte kartu **MIGROVAT testy z npm** . 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Migrace testů z Network Performance Monitor na monitorování připojení" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. V rozevíracích seznamech vyberte své předplatné a pracovní prostor a pak vyberte funkci NPM, kterou chcete migrovat. 
1. Vyberte **importovat** a migrujte testy.

Po zahájení migrace proběhne následující změny: 
* Vytvoří se nový prostředek monitorování připojení.
   * Vytvoří se jedno monitorování připojení na oblast a předplatné. Pro testy s místními agenty je název monitoru nového připojení formátovaný jako `<workspaceName>_"workspace_region_name"` . Pro testy s agenty Azure je název nového monitorování připojení formátovaný jako `<workspaceName>_<Azure_region_name>` .
   * Data monitorování se teď ukládají do stejného Log Analytics pracovního prostoru, ve kterém je povolený NPM, v nových tabulkách s názvem tabulka NWConnectionMonitorTestResult a tabulce NWConnectionMonitorPathResult. 
   * Název testu se přenese jako název testovací skupiny. Popis testu není migrován.
   * Zdrojové a cílové koncové body jsou vytvořeny a použity v nové testovací skupině. Pro místní agenty jsou koncové body formátovány jako `<workspaceName>_<FQDN of on-premises machine>` .
   * Cílový port a interval zjišťování jsou přesunuty do konfigurace testu s názvem `TC_<protocol>_<port>` a `TC_<protocol>_<port>_AppThresholds` . Protokol je nastaven na základě hodnot portů. Pro protokol ICMP jsou konfigurace testu pojmenovány jako `TC_<protocol>` a `TC_<protocol>_AppThresholds` . Prahové hodnoty úspěšnosti a další volitelné vlastnosti, pokud jsou operace migrovány, jinak jsou ponechány prázdné.
   * Pokud migrace testů obsahuje agenty, kteří nepoužívají, je nutné povolit agenty a znovu provést migraci.
* NPM není zakázané, takže migrované testy můžou dál posílat data do tabulky NetworkMonitoring, tabulky NWConnectionMonitorTestResult a NWConnectionMonitorPathResult. Tento přístup zajišťuje, že existující výstrahy a integrace založené na protokolu nebudou mít vliv na.
* Nově vytvořené monitorování připojení je viditelné v monitorování připojení.

Po dokončení migrace nezapomeňte:
* Ručně zakažte testy v NPM. Dokud to neuděláte, bude se vám za ně účtovat i nadále. 
* Když NPM zakážete, znovu vytvořte upozornění v tabulkách NWConnectionMonitorTestResult a NWConnectionMonitorPathResult nebo použijte metriky. 
* Migrujte všechna externí integrace do tabulek NWConnectionMonitorTestResult a NWConnectionMonitorPathResult. Příklady externích integrací jsou řídicí panely v Power BI a Grafana a integrace se systémy SIEM (Security Information and Event Management).


## <a name="next-steps"></a>Další kroky

Další informace o monitorování připojení najdete v těchto tématech:
* [Migrace z monitoru připojení (Classic) na monitorování připojení](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Vytvoření monitorování připojení pomocí Azure Portal](./connection-monitor-create-using-portal.md)
