---
title: Monitorování Synchronizace souborů Azurech vrstev cloudu | Microsoft Docs
description: Podrobnosti o metrikách, které se mají použít k monitorování zásad vaší vrstvy cloudu
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cbdde16ff214077d7fe4814c8841d805fe05c37c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796637"
---
# <a name="monitor-cloud-tiering"></a>Monitorování vrstvení cloudu
Existují dva způsoby, jak můžete monitorovat zásady cloudové vrstvy: okno Vlastnosti koncového bodu serveru a Azure Monitor.

## <a name="monitoring-via-server-endpoint"></a>Monitorování prostřednictvím koncového bodu serveru

Přihlaste se k [Azure Portal](https://portal.azure.com/)a potom přejděte do okna **Vlastnosti koncového bodu serveru** pro koncový bod serveru, který chcete monitorovat, a přejděte dolů k části věnované vrstvám cloudu. 

![Snímek obrazovky oddílu vrstvení cloudu ve vlastnostech koncového bodu serveru](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

**Úspora místa** je množství místa uloženého v rámci povolení vrstvení cloudu. Pokud je velikost místního svazku dostatečně velká, aby obsahovala všechna vaše data, budete mít 0% úspory místa. Pokud máte k dispozici 0% nebo úspory místa na disku, může to znamenat, že při současné velikosti místní mezipaměti není výhoda cloudové vrstvy. 

**Rychlost přístupů do mezipaměti** je procento souborů, které otevřete v místní mezipaměti. Rychlost přístupů do mezipaměti se počítá podle souborů otevřených přímo z mezipaměti nebo celkového počtu otevřených souborů. Míra přístupů do mezipaměti 100% znamená, že všechny soubory, které jste otevřeli během poslední hodiny, již byly v místní mezipaměti. Pokud vaším cílem je snížit výstup, znamená to, že vaše aktuální zásada funguje dobře.

> [!NOTE]
> Úlohy se vzory náhodného přístupu mají typicky nižší míry přístupů do mezipaměti. 

**Celková velikost (Cloud)** je velikost souborů, které byly synchronizovány do cloudu. 

**Velikost v mezipaměti (Server)** je celková velikost souborů na serveru, stahovaných i vrstvených. V některých případech může být velikost v mezipaměti větší než celková velikost souborů v cloudu. To může způsobit proměnné, jako je velikost clusteru svazku na serveru. Pokud je velikost v mezipaměti větší, než kolik potřebujete, zvažte zvýšení zásad pro volné místo svazku. 

Synchronizace souborů Azure slouží k **určení množství volného** místa na svazku, na kterém je koncový bod serveru zapnutý. Pokud je na stejném svazku více koncových bodů serveru, nejvyšší zásada volného místa svazku mezi koncovými body serveru se bude platit pro všechny koncové body serveru na tomto svazku. Pokud jsou například dva koncové body serveru na stejném svazku, jeden s 30% volného místa na svazku a druhý s 50% svazkem volného místa, platné zásady volného místa svazku pro oba koncové body serveru budou 50%.

**Aktuální volné místo svazku** je volné místo na svazku, které je aktuálně k dispozici na místním serveru. Pokud máte vysoký výstup, ale k dispozici je víc volného místa, než se zásada volného místa na svazku zahájí, zvažte zakázání zásad pro data. Další příčinou může být to, že v aktuálně vrstvených souborech je soubor s naposledy použitou velikostí větší než velikost volného místa na svazku, než se zásada zahájí. V takovém případě zvažte zvýšení velikosti místního svazku. 

## <a name="monitoring-via-azure-monitor"></a>Monitorování prostřednictvím Azure Monitor

Přejít do **služby synchronizace úložiště** a vybrat **metriky** na straně navigace. 

Existují tři různé metriky, pomocí kterých můžete monitorovat výstup přímo z vrstev cloudu:

- Velikost odvolání při vyvolání vrstvy cloudu
    - Toto je celková velikost vrácených dat v bajtech a lze ji použít k identifikaci množství dat, která se mají odvolat.
- Velikost odvolání při vrstvení cloudu podle aplikace
    - Toto je celková velikost vrácených dat v bajtech aplikací a lze ji použít k identifikaci toho, co data znovu volá.
- Propustnost volání při navracení cloudových vrstev
    - Tím se měří, jak rychle se data odvolají v bajtech, a měla by se používat, pokud máte obavy o výkon. 

Pokud chcete, aby se grafy zobrazovaly lépe, zvažte použití možnosti **Přidat filtr** a **použít rozdělení**.
 
Podrobnosti o různých typech metrik pro Synchronizace souborů Azure a způsobu jejich použití najdete v tématu [monitorování synchronizace souborů Azure](file-sync-monitoring.md).

Podrobnosti o tom, jak používat metriky, najdete v tématu [Začínáme s Azure Průzkumník metrik.](../../azure-monitor/essentials/metrics-getting-started.md).

Pokud chcete změnit zásady cloudové vrstvy, přečtěte si téma [Výběr zásad clouding](file-sync-choose-cloud-tiering-policies.md).

## <a name="next-steps"></a>Další kroky

* [Plánování nasazení Synchronizace souborů Azure](file-sync-planning.md)