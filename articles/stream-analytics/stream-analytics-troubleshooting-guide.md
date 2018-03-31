---
title: Průvodci odstraňováním potíží Azure Stream Analytics | Microsoft Docs
description: Řešení potíží s vaší úloze Stream Analytics
keywords: řešení potíží s Průvodce
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 87e8b99cb2774966ce22c76e1afed6a754749e9d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Průvodce řešením potíží pro Azure Stream Analytics

Řešení potíží Azure Stream Analytics můžete zobrazí jako komplexní úsilí na první pohled. Po dokončení práce s mnoha uživateli, vytvořili jsme Tato příručka ke zjednodušení procesu a odebrat průběhu o vstupy, výstupy, dotazů a funkce.

## <a name="troubleshoot-your-stream-analytics-job"></a>Řešení potíží s vaší úloze Stream Analytics

Chcete-li dosáhnout nejlepších výsledků při řešení potíží úlohu služby Stream Analytics použijte následující pokyny:

1.  Test připojení:
    - Ověření připojení k vstupů a výstupů pomocí **Test připojení** tlačítko pro každý vstup a výstup.

2.  Zkontrolujte vstupní data:
    - Chcete-li ověřit, že je vstupních dat odesílaných do centra událostí, použijte [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) k připojení do centra událostí Azure (Pokud se používá Centrum událostí vstupu).  
    - Použití [ **ukázková Data** ](stream-analytics-sample-data-input.md) tlačítko pro každý vstupní a stáhněte si vstupní ukázková data.
    - Zkontrolujte ukázková data pochopit tvaru dat: schéma a [datové typy](https://msdn.microsoft.com/library/azure/dn835065.aspx).

3.  Otestování dotazu:
    - Na **dotazu** pomocí **testování** tlačítko otestujte dotaz a použijte stažené ukázková data [otestujte dotaz](stream-analytics-test-query.md). Zkontrolujte případné chyby a pokus o opravte je.
    - Pokud používáte [ **Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx), ověřte, že události větší než časová razítka [čas spuštění úlohy](stream-analytics-out-of-order-and-late-events.md).

4.  Ladění dotazu:
    - Znovu sestavte dotaz progresivně z jednoduchého příkazu select složitější agregace pomocí kroků. Chcete-li vytvářet logice dotazu krok za krokem, použijte [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx) klauzule.
    - Použití [SELECT INTO](stream-analytics-select-into.md) k ladění kroky dotazu.

5.  Eliminovat běžné nástrahy, jako například:
    - A [ **kde** ](https://msdn.microsoft.com/library/azure/dn835048.aspx) klauzule dotazu odfiltrovat všechny události, brání generován žádný výstup.
    - Při použití funkce okno, počkejte, než po dobu trvání celé okno zobrazíte výstup z dotazu.
    - Časové razítko pro události předchází čas spuštění úlohy, a proto jsou vyřazována události.

6.  Použijte řazení událostí:
    - Pokud všechny předchozí kroky fungovala správně, přejděte na **nastavení** a vyberte [ **řazení událostí**](stream-analytics-out-of-order-and-late-events.md). Ověřte, že je tato zásada povolena pro co smysl ve vašem scénáři. Tato zásada je *není* použít při použití **testování** tlačítko a otestujte dotaz. Tento výsledek je jeden rozdíl mezi testování v prohlížeči a spustit úlohu v produkčním prostředí.

7.  Ladění pomocí metriky:
    - Pokud žádný výstup získáte po očekávané trvání (založené na dotazu), zkuste následující postup:
        - Podívejte se na [ **monitorování metriky** ](stream-analytics-monitoring.md) na **monitorování** kartě. Protože hodnoty nejsou agregovány, metriky zpožděn několik minut.
            - Pokud vstupní události > 0, úloha se bude moct číst vstupní data. Pokud události vstup není pak > 0:
                - Chcete-li zjistit, zda má platný datový zdroj dat, zkontrolujte pomocí [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Tato kontrola platí, pokud úloha používá jako vstup centra událostí.
                - Zkontrolujte, zda je formát serializace dat a dat kódování podle očekávání.
                - Pokud úloha používá centra událostí, zkontrolujte, zda text zprávy je *Null*.
            - Pokud chyb při převodu dat > 0 a stoupající, může platit následující:
                - Úloha nemusí být možné deserializovat události.
                - Schéma událost nemusí odpovídat schéma definované nebo se očekává událostí v dotazu.
                - Datové typy některých polí události nemusí odpovídat očekávání.
            - Pokud chyby za běhu > 0, znamená, že úloha může přijímat data, ale generuje chyby při zpracování dotazu.
                - Chyby, najdete [protokoly auditu](../azure-resource-manager/resource-group-audit.md) a filtrujte *se nezdařilo* stavu.
            - Pokud InputEvents > 0 a OutputEvents = 0, znamená, že je hodnota true, jednu z následujících:
                - Zpracování dotazu mělo za výsledek nulu výstupních událostí.
                - Události nebo jeho pole může být poškozený, což vede k nulové výstup po zpracování dotazu.
                - Úlohu se nepodařilo vložit data do [výstupní jímku](stream-analytics-select-into.md) důvodů připojení nebo ověřování.
        - Ve všech případech výše uvedené chyby zprávy protokolu operací popisují další podrobnosti (včetně, co se děje), s výjimkou případů, kdy logice dotazu odfiltrovat všechny události. Pokud zpracování více událostí generuje chyby, Stream Analytics zaznamená do 10 minut do protokolů operací první tři chybové zprávy stejného typu. Potlačí další identické chyby se zprávou, který čte "Chyby se děje příliš rychle že tyto potlačeno."

8. Ladění pomocí auditu a diagnostické protokoly:
    - Použití [protokoly auditu](../azure-resource-manager/resource-group-audit.md)a filtr k určení a ladění chyb.
    - Použití [úlohy diagnostické protokoly](stream-analytics-job-diagnostic-logs.md) k určení a ladění chyb.

9. Zkontrolujte výstupy:
    - Když stav úlohy je *systémem*, v závislosti na dobu, která je stanoveno v dotazu, uvidíte výstup ve zdroji dat jímky.
    - Pokud nevidíte výstupů, které se chystáte konkrétní výstupní typ, přesměruje je to typ výstupu, který je méně složitých, jako je například objektu Blob Azure. Pomocí Průzkumníka úložiště, zkontrolujte, zda se zobrazí výstup. Kromě toho ověřte, zda omezení omezení výstup brání data přijímání.

10. Tok dat analysis pomocí úlohy diagram metriky:
    - Chcete-li analyzovat tok dat a identifikovat problémy, použijte [diagram úlohy s metriky](stream-analytics-job-diagram-with-metrics.md).

11. Otevřete případu podpory:
    - Navíc pokud selžou všechny ostatní postupy, otevřete případu podpory společnosti Microsoft pomocí ID odběru, která obsahuje úlohu.

## <a name="get-help"></a>Podpora

Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
