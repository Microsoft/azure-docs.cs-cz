---
title: zahrnout soubor
description: zahrnout soubor
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: 61f82771e53ac9bb594484b29bb109a03cee674b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553467"
---
#### <a name="process-automation"></a>Automatizace procesů

| Prostředek | Maximální omezení |Poznámky|
| --- | --- |---|
| Maximální počet nových úlohách, které se dají odeslat každých 30 sekund na účet služby Azure Automation (nonscheduled úlohy) |100 |Při dosažení tohoto limitu, selhání následné žádosti k vytvoření úlohy. Klient obdrží odpověď.|
| Maximální počet souběžných úloh spuštěných na stejnou instanci času na účet služby Automation (nonscheduled úlohy) |200 |Při dosažení tohoto limitu, selhání následné žádosti k vytvoření úlohy. Klient obdrží odpověď.|
| Maximální velikost úložiště metadat úlohy pro období posledních 30 dnů | 10 GB (přibližně 4 miliony úlohy)|Při dosažení tohoto limitu, selhání následné žádosti k vytvoření úlohy. |
| Maximální počet modulů, které mohou být naimportovány každých 30 sekund na účet služby Automation |5 ||
| Maximální velikost modulu |100 MB ||
| Úlohy úrovně Free doby běhu |500 minut na předplatné za kalendářní měsíc ||
| Maximální množství místa na disku pro izolovaný prostor povolené<sup>1</sup> |1 GB |Platí pro pouze Azure karantény.|
| Maximální velikost paměti na sandboxu<sup>1</sup> |400 MB |Platí pro pouze Azure karantény.|
| Maximální počet povolených pro izolovaný prostor síťové sokety<sup>1</sup> |1 000 |Platí pro pouze Azure karantény.|
| Maximální povolené na sadu runbook runtime<sup>1</sup> |3 hodiny |Platí pro pouze Azure karantény.|
| Maximální počet účtů Automation v předplatném |Bez omezení ||
|Maximální počet souběžných úloh, které lze spustit na jednoho procesu Hybrid Runbook Worker|50 ||
| Velikost parametru úlohy maximální sady runbook   | 512 kB||
| Parametry maximální runbooku   | 50|Pokud překročíte limit parametr-50, můžete předat parametr řetězec JSON nebo XML a parsování se runbook.|
| Velikost datové části maximální webhooku |  512 kB|
| Maximální počet dní, které se uchovávají data úlohy|30 dní|
| Maximální velikost stavu pracovního postupu Powershellu |5 MB| Platí pro runbooky pracovních postupů Powershellu při vytváření kontrolních bodů pracovního postupu.|

<sup>1</sup>izolovaný prostor je sdíleném prostředí, které mohou být využívána více úloh. Úlohy, které používají stejné izolovaného prostoru zavazujete se k jejich omezení prostředků izolovaného prostoru.

#### <a name="change-tracking-and-inventory"></a>Change tracking a inventory

Následující tabulka uvádí omezení sledované položky na počítači pro řešení change tracking.

| **Prostředek** | **Limit**| **Poznámky** |
|---|---|---|
|File|500||
|Registr|250||
|Windows software|250|Nezahrnuje aktualizace softwaru.|
|Balíčky Linux|1,250||
|Služby|250||
|Démon|250||
