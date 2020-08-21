---
title: Migrace na monitor připojení (Preview) z Network Performance Monitor
titleSuffix: Azure Network Watcher
description: Přečtěte si, jak migrovat na monitor připojení (Preview) z Network Performance Monitor.
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
ms.openlocfilehash: 69dbb1dd4017c5acf9c195f5104741caee38c2b7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701592"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>Migrace na monitor připojení (Preview) z Network Performance Monitor

Testy můžete migrovat z Network Performance Monitor na nové a vylepšené monitorování připojení (Preview) v jednom kliknutím a s nulovými výpadky. Pokud se o výhodách chcete dozvědět víc, můžete si přečíst téma [monitorování připojení (Preview)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview) .

>[!NOTE]
> Do monitorování připojení (Preview) se dají migrovat jenom testy z monitorování připojení služby.
>

## <a name="key-points-to-note"></a>Klíčové body k označení

* Místní agenti a nastavení brány firewall budou fungovat tak, jak jsou. Nevyžadují se žádné změny. Agenti Log Analytics nainstalovanou v Azure Virtual Machines je potřeba nahradit rozšířením Network Watcher
* Existující testy budou namapovány na monitorování připojení (Preview)-> testovací skupiny > testovacího formátu. Uživatelé můžou kliknout na *Upravit* a zobrazit a upravit vlastnosti nového monitorování připojení a stáhnout šablonu, aby se změny zobrazily v monitorování připojení a odesílaly je prostřednictvím Azure Resource Manager.
* Agenti odesílají data do obou Log Analytics pracovní prostor a metriky.
* Data monitorování
    * Data v Log Analytics – veškerá předběžná migrace dat pokračuje v pracovním prostoru, ve kterém je NPM nakonfigurovaný v tabulce NetworkMonitoring. Po migraci se data přecházejí do tabulky NetworkMonitoring a tabulky ConnectionMonitor_CL ve stejném pracovním prostoru. Po zakázání testů z NPM budou data uložena pouze v ConnectionMonitor_CL tabulce.
    * Výstrahy, řídicí panely a integrace založené na protokolu – budete muset ručně upravit dotazy založené na nové ConnectionMonitor_CL tabulky. Pomocí tohoto odkazu můžete také znovu vytvořit výstrahy v metrikách. Možnost migrovat výstrahy založené na protokolech v tabulce NetworkMonitoring na výstrahy založené na metrikách automaticky jako součást migrace bude brzy k dispozici.
    
## <a name="prerequisites"></a>Předpoklady

*   Ujistěte se, že je v předplatném a oblasti pracovního prostoru Log Analytics povolená možnost Network Watcher.
*   Virtuální počítače Azure s nainstalovanými agenty Log Analytics se musí povolit s rozšířením Network Watcher.

## <a name="steps-to-migrate-tests-from-network-performance-monitor-to-connection-monitor-preview"></a>Postup migrace testů z Network Performance Monitor na monitorování připojení (Preview)

1.  Klikněte na monitorování připojení, přejděte na migrace testů z NPM a migrujte testy do monitorování připojení (Preview).

    ![Snímek obrazovky, který ukazuje migraci testů z NPM do monitorování připojení, Preview](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1.  Vyberte předplatné, pracovní prostor a funkci NPM, kterou chcete migrovat. V současné době můžete migrovat pouze testy z monitorování připojení služby.  
1.  Kliknutím na importovat migrujete testy.
1.  Po zahájení migrace dojde k následujícím změnám: 
    1. Vytvoří se nový prostředek monitorování připojení.
        1. Vytvoří se jedno monitorování připojení na oblast a předplatné. V případě testů s místními agenty má nový název sledování připojení formát <workspaceName> _"místní". V případě testů s agenty Azure má nový název sledování připojení formát <workspaceName> _<Azure_region_name>
        1. Data monitorování se teď ukládají do stejného Log Analytics pracovního prostoru, ve kterém je povolený NPM, v nové tabulce nazvané Connectionmonitor_CL Table. 
        1. Název testu se přenese do názvu skupiny testů. Popis testu nebude migrován.
        1. Zdrojové a cílové koncové body jsou vytvořeny a použity v vytvořené testovací skupině. Pro místní agenty jsou koncové body pojmenovány ve formátu <workspaceName> _"koncový bod"_ <FQDN of on-premises machine> . V případě Azure platí, že pokud migrace testů obsahuje agenty, budete muset povolit agenty a znovu provést migraci.
        1. Cílový port a interval zjišťování se přesunou do konfigurace testu, konkrétně "TC"_ <testname> a "TC"_ <testname> _ "AppThresholds". V závislosti na hodnotách portů je protokol nastavený. Prahové hodnoty úspěšnosti a jiné volitelné vlastnosti jsou ponechány prázdné.
    1. NPM není zakázaný. Migrované testy tedy nadále odesílají data do tabulky NetworkMonitoring a také ConnectionMonitor_CL tabulce. Tento krok zajistí, že nebudou ovlivněny stávající výstrahy a integrace založené na protokolu. Migrace výstrah na základě protokolu v tabulce NetworkMonitoring do výstrah založených na metrikách automaticky v rámci migrace bude k dispozici brzo.
    1. Nově vytvořené monitorování připojení se zobrazí v monitorování připojení (Preview).
1.  Po migraci budete muset testy v NPM ručně zakázat. Dokud to neuděláte, bude se vám nadále účtovat stejná částka. Při zakazování NPM se ujistěte, že jste znovu vytvořili výstrahy pro ConnectionMonitor_CL tabulku, nebo použijete metriky. Také se ujistěte, že všechny externí integrace, jako jsou řídicí panely v Power BI Grafana, integraci se systémy SIEM, budou muset být migrovány do tabulky ConnectionMonitor_CL


## <a name="next-steps"></a>Další kroky

* Přečtěte si [, jak migrovat z monitorování připojení do monitorování připojení (Preview)](migrate-to-connection-monitor-preview-from-connection-monitor.md) .
* Zjistěte [, jak vytvořit monitorování připojení (Preview) pomocí Azure Portal](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
