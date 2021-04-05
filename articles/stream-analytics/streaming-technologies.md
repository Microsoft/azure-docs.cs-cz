---
title: Volba řešení pro zpracování datových proudů v Azure v reálném čase
description: Přečtěte si, jak zvolit správnou analýzu v reálném čase a technologii zpracování streamování pro sestavení vaší aplikace v Azure.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 4c10a91971357001723adcb783253c9867cf6d87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019053"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Volba analýzy v reálném čase a technologie zpracování streamování v Azure

K dispozici je několik služeb pro analýzy v reálném čase a zpracování streamování v Azure. Tento článek poskytuje informace, které potřebujete k rozhodnutí, kterou technologii nejlépe vyhovuje vaší aplikaci.

## <a name="when-to-use-azure-stream-analytics"></a>Kdy použít Azure Stream Analytics

Azure Stream Analytics je doporučená služba pro Stream Analytics v Azure. Je určený pro široké spektrum scénářů, které zahrnují, ale nejsou omezené na:

* Řídicí panely pro vizualizaci dat
* [Výstrahy](stream-analytics-set-up-alerts.md) v reálném čase z dočasného a prostorového vzoru nebo anomálií
* Extrakce, transformace, načítání (ETL)
* [Vzor zdroje událostí](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Přidání Azure Stream Analytics úlohy do aplikace je nejrychlejší způsob, jak pomocí jazyka SQL, který už znáte, analyzovat streamování v Azure v provozu. Azure Stream Analytics je služba úlohy, takže nemusíte ztrácet čas správou clusterů a nemusíte se starat o prostoje s 99,9% smlouvou SLA na úrovni úlohy. Fakturace se provádí taky na úrovni úlohy, která má nízké náklady na spuštění (jedna jednotka streamování), ale škálovatelná (až 192 jednotek streamování). Spuštění několika úloh Stream Analytics je mnohem efektivnější než spuštění a údržba clusteru.

Azure Stream Analytics má bohatě připravený zážitek. Bez dalších nastavení můžete hned využít tyto funkce:

* Předdefinované dočasné operátory, jako jsou například [okna agregace](stream-analytics-window-functions.md), dočasná spojení a dočasné analytické funkce.
* Nativní [vstupní](stream-analytics-add-inputs.md) a [výstupní](stream-analytics-define-outputs.md) adaptéry Azure
* Podpora pro pomalé změny [referenčních dat](stream-analytics-use-reference-data.md) (označují se také jako vyhledávací tabulky), včetně spojení s geoprostorové referenčními daty pro monitorování geografických zón.
* Integrovaná řešení, jako je [detekce anomálií](stream-analytics-machine-learning-anomaly-detection.md)
* Několik časových oken ve stejném dotazu
* Možnost vytvářet více dočasných operátorů v libovolných sekvencích.
* V části 100-MS koncová latence ze vstupu přicházejících na Event Hubs, do výstupního odpočívadla v Event Hubs, včetně zpoždění sítě od a do Event Hubs, při trvalé vysoké propustnosti

## <a name="when-to-use-other-technologies"></a>Kdy použít jiné technologie

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Chcete zapisovat UDF, Uda a vlastní deserializace v jiném jazyce než JavaScript nebo C. #

Azure Stream Analytics podporuje uživatelsky definované funkce (UDF) nebo uživatelsky definované agregace (UDA) v JavaScriptu pro cloudové úlohy a jazyky C# pro IoT Edge úlohy. Jsou podporovány také uživatelsky definované deserializace v jazyce C#. Pokud chcete implementovat deserializaci, UDF nebo UDA v jiných jazycích, jako je Java nebo Python, můžete použít strukturované streamování Sparku. Můžete také spustit Event Hubs **EventProcessorHost** na svých vlastních virtuálních počítačích a dělat tak libovolné zpracování libovolného streamování.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Vaše řešení je v cloudu nebo v místním prostředí.

Azure Stream Analytics je proprietární technologie Microsoftu a je dostupná jenom v Azure. Pokud potřebujete, aby vaše řešení bylo přenosné napříč cloudy nebo místně, zvažte Open Source technologie, jako je například strukturovaný streamování Sparku nebo vyplavení.

## <a name="next-steps"></a>Další kroky

* [Vytvoření Stream Analytics úlohy pomocí Azure Portal](stream-analytics-quick-create-portal.md)
* [Vytvoření úlohy Stream Analytics pomocí Azure PowerShellu](stream-analytics-quick-create-powershell.md)
* [Vytvoření úlohy Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Vytvoření Stream Analytics úlohy pomocí Visual Studio Code](quick-create-visual-studio-code.md)
