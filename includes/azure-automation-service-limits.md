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
ms.openlocfilehash: abe7668ed16ddd5cc1b247207d3e80f7c1118137
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50964540"
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
| Maximální počet dnů, které se uchovávají data úlohy|30 dní|

**<sup>1</sup>**  izolovaný prostor je sdíleném prostředí, které mohou být využívána více úloh a úloh pomocí stejné izolovaného prostoru zavazujete se k jejich omezení prostředků izolovaného prostoru.
