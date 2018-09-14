---
title: zahrnout soubor
description: zahrnout soubor
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 04/05/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: 34cae9172d9b024bd6866742d39d82ad496bfc52
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45570460"
---
| Prostředek | Maximální omezení |Poznámky|
| --- | --- |---|
| Maximální počet nových úlohách, které můžete odeslat každých 30 sekund na účet služby Automation (bez naplánované úlohy) |100 |Pokud tento limit se přístupů, následné žádosti k vytvoření úlohy k chybě. Klient obdrží odpověď.|
| Maximální počet souběžných úloh spuštěných na stejnou instanci času na účet služby Automation (bez naplánované úlohy) |200 |Pokud tento limit se přístupů, následné žádosti k vytvoření úlohy k chybě. Klient obdrží odpověď.|
| Maximální počet modulů, které mohou být naimportovány každých 30 sekund na účet služby Automation |5 ||
| Maximální velikost modulu |100 MB ||
| Čas spuštění úlohy – úroveň Free |500 minut na předplatné za kalendářní měsíc ||
| Maximální množství místa na disku pro izolovaný prostor povolené  **<sup>1</sup>** |1 GB |Platí pro pouze Azure karantény|
| Maximální velikost paměti na sandboxu  **<sup>1</sup>** |400 MB |Platí pro pouze Azure karantény|
| Maximální počet povolených pro izolovaný prostor síťové sokety  **<sup>1</sup>** |1000 |Platí pro pouze Azure karantény|
| Maximální povolené na sadu runbook runtime  **<sup>1</sup>** |3 hodiny |Platí pro pouze Azure karantény|
| Maximální počet účtů Automation v rámci předplatného |Bez omezení ||
|Maximální počet souběžných úloh, která spustíte ve službě jednoho procesu Hybrid Runbook Worker|50 ||
| Úloha Runbooku maximální velikost parametry   | 512 kb||
| Maximální počet parametrů   | 50|Můžete předat parametr řetězec JSON nebo XML a analyzovat pomocí sady runbook, pokud dosáhnete limitu 50 parametr|
| Maximální velikost datové části webhooku |  512 kb|

**<sup>1</sup>**  izolovaný prostor je sdíleném prostředí, které mohou být využívána více úloh a úloh pomocí stejné izolovaného prostoru zavazujete se k jejich omezení prostředků izolovaného prostoru.
