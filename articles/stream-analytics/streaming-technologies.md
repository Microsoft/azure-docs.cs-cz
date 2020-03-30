---
title: Vyberte si řešení pro zpracování datových proudů v reálném čase v Azure
description: Přečtěte si, jak vybrat správnou analýzu v reálném čase a technologii zpracování datových proudů pro sestavení aplikace v Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 2146b1bd782aba5d98729a2d37d956744e469ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75860244"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Vyberte si v Azure technologii analýzy a zpracování datových proudů v reálném čase

Pro analýzy a zpracování datových proudů v Azure je k dispozici několik služeb. Tento článek obsahuje informace, které potřebujete k rozhodnutí, která technologie je nejvhodnější pro vaši aplikaci.

## <a name="when-to-use-azure-stream-analytics"></a>Kdy použít Azure Stream Analytics

Azure Stream Analytics je doporučená služba pro analýzu streamů v Azure. Je určen pro širokou škálu scénářů, které zahrnují, ale nejsou omezeny na:

* Řídicí panely pro vizualizaci dat
* [Výstrahy v](stream-analytics-set-up-alerts.md) reálném čase z časových a prostorových vzorů nebo anomálií
* Extrakce, transformace, načítání (ETL)
* [Vzor získávání událostí](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Přidání úlohy Azure Stream Analytics do vaší aplikace je nejrychlejší způsob, jak zprovoznit analýzy streamování v Azure pomocí jazyka SQL, který už znáte. Azure Stream Analytics je služba práce, takže nemusíte trávit čas správou clusterů a nemusíte se starat o prostoje s 99,9% sla na úrovni úlohy. Fakturace se také provádí na úrovni úloh, takže náklady na spuštění jsou nízké (jedna jednotka streamování), ale škálovatelné (až 192 jednotek streamování). Je mnohem nákladově efektivnější spustit několik úloh Stream Analytics než spuštění a údržbu clusteru.

Azure Stream Analytics má bohaté prostředí inas uvidového prostředí. Můžete okamžitě využít následující funkce bez dalšího nastavení:

* Vestavěné časové operátory, jako jsou [okenní agregáty](stream-analytics-window-functions.md), časová spojení a časové analytické funkce.
* Nativní [vstupní](stream-analytics-add-inputs.md) a [výstupní](stream-analytics-define-outputs.md) adaptéry Azure
* Podpora pomalu se [měnících referenčních dat](stream-analytics-use-reference-data.md) (označovaná také jako vyhledávací tabulky), včetně spojení s geoprostorovými referenčními daty pro geofencing.
* Integrovaná řešení, jako je [detekce anomálií](stream-analytics-machine-learning-anomaly-detection.md)
* Více časových oken ve stejném dotazu
* Schopnost skládat více časových operátorů v libovolných sekvencích.
* Latence od začátku do konce pod 100 ms od vstupu přicházejícího do event hubů až po výstupní přistání v event hubech, včetně zpoždění sítě z a do event hubů, s trvalou vysokou propustností

## <a name="when-to-use-other-technologies"></a>Kdy použít jiné technologie

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Chcete psát UDFs, UDA a vlastní deserializers v jiném jazyce než JavaScript nebo C #

Azure Stream Analytics podporuje uživatelem definované funkce (UDF) nebo uživatelem definované agregace (UDA) v Jazyce JavaScript pro úlohy v cloudu a C# pro úlohy IoT Edge. C# uživatelem definované deserializers jsou také podporovány. Pokud chcete implementovat deserializátor, UDF nebo UDA v jiných jazycích, jako je například Java nebo Python, můžete použít Strukturované streamování Spark. Můžete také spustit Event Hubs **EventProcessorHost** na vlastních virtuálních počítačích provádět libovolné zpracování streamování.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Vaše řešení je v prostředí s více cloudy nebo v místním prostředí.

Azure Stream Analytics je proprietární technologie Microsoftu a je dostupná jenom v Azure. Pokud potřebujete, aby vaše řešení bylo přenosné napříč cloudy nebo v místním prostředí, zvažte technologie s otevřeným zdrojovým kódem, jako je strukturované streamování Spark nebo Storm.

## <a name="next-steps"></a>Další kroky

* [Vytvoření úlohy Stream Analytics pomocí portálu Azure](stream-analytics-quick-create-portal.md)
* [Vytvoření úlohy Stream Analytics pomocí Azure PowerShellu](stream-analytics-quick-create-powershell.md)
* [Vytvoření úlohy Stream Analytics pomocí Sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Vytvoření úlohy Stream Analytics pomocí kódu Sady Visual Studio](quick-create-vs-code.md)
