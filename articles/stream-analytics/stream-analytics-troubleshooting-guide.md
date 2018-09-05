---
title: Průvodce odstraňováním potíží pro Azure Stream Analytics
description: Tento článek popisuje postupy řešení potíží s vaší úlohy Azure Stream Analytics, připojení, vstupy, výstupy, dotazy a data.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: b1b5d0af3f2b149959bcb97ddaf29ba2fe1f4668
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702217"
---
# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Průvodce odstraňováním potíží pro Azure Stream Analytics

Řešení potíží Azure Stream Analytics může zdát složité úsilí na první pohled. Po dokončení práce s mnoha uživateli, jsme vytvořili tento průvodce vám pomůže zjednodušit proces a pokus-omyl o vstupů, výstupů, dotazy a funkce.

## <a name="troubleshoot-your-stream-analytics-job"></a>Řešení potíží s vaší úlohy Stream Analytics

Nejlepších výsledků dosáhnete při řešení potíží s vaší úlohy Stream Analytics použijte následující pokyny:

1.  Test připojení:
    - Ověření připojení ke vstupům a výstupům pomocí **Test připojení** tlačítko pro každý vstupní a výstupní.

2.  Zkontrolujte vstupní data:
    - Chcete-li ověřit, že se výstupní data přenášejí do centra událostí, použijte [Service Bus Exploreru](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) pro připojení k Azure Eventhub (Pokud je vstup Centrum událostí se používá).  
    - Použití [ **ukázková Data** ](stream-analytics-sample-data-input.md) pro každý vstupní tlačítko a stáhněte ukázková vstupní data.
    - Zkontrolujte ukázková data a porozumět struktuře dat: schéma a [datové typy](https://msdn.microsoft.com/library/azure/dn835065.aspx).

3.  Otestování dotazu:
    - Na **dotazu** kartu, použijte **testování** tlačítko otestujte dotaz a použijte stažené ukázková data do [otestujte dotaz](stream-analytics-test-query.md). Zkontrolujte případné chyby a pokuste se je opravit.
    - Pokud používáte [ **Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx), ověřte, že všechny události mají časové razítko větší, než [úlohy počáteční čas](stream-analytics-out-of-order-and-late-events.md).

4.  Ladění dotazu:
    - Znovu sestavte dotaz od jednoduchých příkazů select složitější agregace pomocí kroků. Vybudujte logiku dotazu krok za krokem, použijte [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx) klauzule.
    - Použití [SELECT INTO](stream-analytics-select-into.md) ladění kroky dotazu.

5.  Vylučte běžné nástrahy, jako například:
    - A [ **kde** ](https://msdn.microsoft.com/library/azure/dn835048.aspx) klauzule v dotazu vyfiltrovala všechny události, brání generován žádný výstup.
    - A [ **PŘETYPOVÁNÍ** ](https://msdn.microsoft.com/azure/stream-analytics/reference/cast-azure-stream-analytics) selže, což způsobí selhání úlohy funkce. Aby se zabránilo selhání přetypování typu, použijte [ **TRY_CAST** ](https://msdn.microsoft.com/azure/stream-analytics/reference/try-cast-azure-stream-analytics) místo.
    - Při použití funkcí okna, počkejte po dobu celé okno výstup z dotazu.
    - Časové razítko pro události předchází časem spuštění úlohy, a proto se události vypustí.

6.  Použijte řazení událostí:
    - Pokud všechny předchozí kroky fungovaly správně, přejděte **nastavení** okna a vyberte [ **řazení událostí**](stream-analytics-out-of-order-and-late-events.md). Ověřte, že tato zásada je nakonfigurovaný pro co dává smysl ve vašem scénáři. Tato zásada je *není* použitý při použití **testování** tlačítko a otestujte dotaz. Tento výsledek je jedním z rozdílů mezi testováním v prohlížeči a spuštěním úlohy v produkčním prostředí.

7.  Ladění pomocí metrik:
    - Pokud jste obdrželi žádný výstup po očekávané době trvání (na základě dotazu), zkuste následující:
        - Podívejte se na [ **monitorování metrik** ](stream-analytics-monitoring.md) na **monitorování** kartu. Protože se agregují hodnoty, metriky jsou zpožděné o několik minut.
            - Pokud vstupní události > 0, úlohy je možné číst vstupní data. Nejsou-li vstupní události > 0, pak:
                - Chcete-li zjistit, zda zdroj dat obsahuje platná data, zkontrolujte pomocí [Service Bus Exploreru](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Tato kontrola platí, pokud úloha používá jako vstup Centrum událostí.
                - Zkontrolujte, zda je formát serializace dat a kódování dat podle očekávání.
                - Pokud úloha používá centra událostí, zkontrolujte, zda je text zprávy *Null*.
            - Pokud chyby převodu dat > 0 a tato hodnota roste, může platit následující:
                - Úloha nemusí být možné deserializovat události.
                - Schéma událostí nemusí odpovídat definovaná nebo očekávanému schématu událostí v dotazu.
                - Datové typy některých polí v události nemusí odpovídat očekávání.
            - Pokud běhové chyby > 0, znamená to, že úloha může přijímat data, ale generuje chyby při zpracování dotazu.
                - Chcete-li najít chyby, přejděte na [protokoly auditu](../azure-resource-manager/resource-group-audit.md) a filtrujte *se nezdařilo* stav.
            - Pokud situací > 0 a výstupní události = 0, znamená to, že je splněna jedna z následujících akcí:
                - Zpracování dotazu mělo za výsledek nulu výstupních událostí.
                - Události nebo jejich pole může být poškozený, což vede k nulový výstup po zpracování dotazů.
                - Úlohu se nepodařilo vložit data do [výstupní jímky](stream-analytics-select-into.md) důvodů připojení nebo ověřování.
        - Ve všech výše uvedených chyb případech zprávy provozních protokolů vysvětlují další podrobnosti (včetně, co se děje), s výjimkou případů, kdy logika dotazu vyfiltrovala všechny události. Pokud zpracování několika událostí generuje chyby, Stream Analytics zaznamená první tři chybové zprávy stejného typu během 10 minut do provozních protokolů. Potom potlačí další identické chyby a zobrazí se zpráva, která čte "Chybám dochází příliš rychle a že potlačují se."

8. Ladění pomocí auditování a diagnostické protokoly:
    - Použití [protokoly auditu](../azure-resource-manager/resource-group-audit.md)a filtr k identifikaci a ladit chyby.
    - Použití [úlohy diagnostické protokoly](stream-analytics-job-diagnostic-logs.md) k identifikaci a ladit chyby.

9. Prozkoumejte výstupy:
    - Pokud je stav úlohy *systémem*, v závislosti na době trvání stanovené v dotazu, zobrazí se výstup ve zdroji dat jímky.
    - Pokud nevidíte výstupy, které se chystáte konkrétní typ výstupu, přesměruje je to typ výstupu, který není tak složitý, jako je Azure Blob. Pomocí Průzkumníka služby Storage, zkontrolujte, zda lze zobrazit ve výstupu. Kromě toho ověřte, zda omezení výstup brání data od jejich obdržení.

10. Použití analýzy toku dat s diagram metriky úlohy:
    - Chcete-li analyzovat tok dat a identifikovat problémy, použijte [diagram úloh s metrikami](stream-analytics-job-diagram-with-metrics.md).

11. Otevření případu podpory:
    - Nakonec pokud selžou všechny ostatní postupy, otevřete případ podpory Microsoftu s použitím ID předplatného, který obsahuje vaši úlohu.

## <a name="get-help"></a>Podpora

Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
