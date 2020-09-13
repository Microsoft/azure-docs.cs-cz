---
title: Migrace na monitor připojení (Preview) z Network Performance Monitor
titleSuffix: Azure Network Watcher
description: Přečtěte si, jak migrovat na monitor připojení (Preview) z Network Performance Monitor.
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
ms.openlocfilehash: dcbb82c1315e6150ddcfadbb52b2976447329b87
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441829"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>Migrace na monitor připojení (Preview) z Network Performance Monitor

Testy můžete migrovat z Network Performance Monitor (NPM) na nové, vylepšené monitorování připojení (Preview) jediným kliknutím a s nulovými výpadky. Další informace o výhodách najdete v tématu [monitorování připojení (Preview)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview).

>[!NOTE]
> Do monitorování připojení (Preview) se dají migrovat jenom testy z monitorování připojení služby.
>

## <a name="key-points-to-note"></a>Klíčové body k označení

Migrace pomáhá získat následující výsledky:

* Místní agenti a nastavení brány firewall fungují tak, jak jsou. Nejsou vyžadovány žádné změny. Log Analytics agenti, kteří jsou nainstalováni na virtuálních počítačích Azure, je třeba nahradit rozšířením Network Watcher.
* Existující testy jsou namapovány na monitorování připojení (Preview) > testovací skupiny > formátu testu. Výběrem možnosti **Upravit**můžete zobrazit a upravit vlastnosti nového monitoru připojení (Preview), stáhnout šablonu, která v něm provede změny, a odeslat šablonu prostřednictvím Azure Resource Manager.
* Agenti odesílají data do pracovního prostoru Log Analytics i do metriky.
* Monitorování dat:
   * **Data v Log Analytics**: před migrací zůstanou data v pracovním prostoru, ve kterém je npm nakonfigurovaný v tabulce NetworkMonitoring. Po migraci přejde data do tabulky NetworkMonitoring a do tabulky ConnectionMonitor_CL ve stejném pracovním prostoru. Po zakázání testů v NPM jsou data uložena pouze v tabulce ConnectionMonitor_CL.
   * **Výstrahy založené na protokolech, řídicí panely a integrace**: dotazy je nutné ručně upravit na základě nové ConnectionMonitor_CL tabulky. Postup opětovného vytvoření výstrah v metrikách najdete v tématu [monitorování připojení k síti pomocí monitorování připojení (Preview)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#metrics-in-azure-monitor).
    
## <a name="prerequisites"></a>Požadavky

* Ujistěte se, že ve vašem předplatném a oblasti pracovního prostoru Log Analytics je povolená možnost Network Watcher.
* Virtuální počítače Azure s nainstalovanými agenty Log Analytics musí být povolené s rozšířením Network Watcher.

## <a name="migrate-the-tests"></a>Migrace testů

Chcete-li migrovat testy z Network Performance Monitor na monitorování připojení (Preview), postupujte takto:

1. V Network Watcher vyberte **monitorování připojení**a pak vyberte kartu **MIGROVAT testy z npm** . 

    ![Snímek obrazovky s podoknem migrace testů z NPM v Network Watcher | Monitorování připojení (Preview).](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1. V rozevíracích seznamech vyberte své předplatné a pracovní prostor a pak vyberte funkci NPM, kterou chcete migrovat. V současné době můžete testy migrovat pouze z monitorování připojení služby.  
1. Vyberte **importovat** a migrujte testy.

Po zahájení migrace proběhne následující změny: 
* Vytvoří se nový prostředek monitorování připojení.
   * Vytvoří se jedno monitorování připojení na oblast a předplatné. Pro testy s místními agenty je název monitoru nového připojení formátovaný jako `<workspaceName>_"on-premises"` . Pro testy s agenty Azure je název nového monitorování připojení formátovaný jako `<workspaceName>_<Azure_region_name>` .
   * Data monitorování se teď ukládají do stejného Log Analytics pracovního prostoru, ve kterém je povolený NPM, v nové tabulce nazvané Connectionmonitor_CL. 
   * Název testu se přenese jako název testovací skupiny. Popis testu není migrován.
   * Zdrojové a cílové koncové body jsou vytvořeny a použity v nové testovací skupině. Pro místní agenty jsou koncové body formátovány jako `<workspaceName>_"endpoint"_<FQDN of on-premises machine>` . Pokud migrace testů obsahuje agenty, kteří nepoužívají, je nutné v Azure povolit agenty a znovu provést migraci.
   * Cílový port a interval zjišťování jsou přesunuty do konfigurace testu s názvem *TC_ \<testname> * a *TC_ \<testname> _AppThresholds*. Protokol je nastaven na základě hodnot portů. Prahové hodnoty úspěšnosti a jiné volitelné vlastnosti jsou ponechány prázdné.
* NPM není zakázané, takže migrované testy můžou dál posílat data do tabulek NetworkMonitoring a ConnectionMonitor_CL. Tento přístup zajišťuje, že existující výstrahy a integrace založené na protokolu nebudou mít vliv na.
* Nově vytvořené monitorování připojení je viditelné v monitorování připojení (Preview).

Po dokončení migrace nezapomeňte:
* Ručně zakažte testy v NPM. Dokud to neuděláte, bude se vám za ně účtovat i nadále. 
* Když NPM zakážete, znovu vytvořte upozornění v tabulce ConnectionMonitor_CL nebo použijte metriky. 
* Migrujte všechna externí integrace do ConnectionMonitor_CL tabulky. Příklady externích integrací jsou řídicí panely v Power BI a Grafana a integrace se systémy SIEM (Security Information and Event Management).


## <a name="next-steps"></a>Další kroky

Další informace o monitorování připojení (Preview) najdete v tématu:
* [Migrace z monitorování připojení do monitorování připojení (Preview)](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* [Vytvoření monitorování připojení (Preview) pomocí Azure Portal](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
