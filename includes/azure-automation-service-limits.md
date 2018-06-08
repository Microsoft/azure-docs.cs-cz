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
ms.openlocfilehash: 6b6e4afa7c8b18c8ce9af8c6abd371b4321e3343
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34852037"
---
| Prostředek | Maximální omezení |Poznámky|
| --- | --- |---|
| Maximální počet nové úlohy, které se dají odeslat každých 30 sekund na účtu Automation (bez naplánovaná úloh) |100 |Pokud tento limit, se setkají, následných žádostí k vytvoření úlohy se nezdaří. Klient obdrží odpověď s chybou.|
| Maximální počet souběžných úloh spuštěných na stejnou instanci doba účet Automation (bez naplánovaná úloh) |200 |Pokud tento limit, se setkají, následných žádostí k vytvoření úlohy se nezdaří. Klient obdrží odpověď s chybou.|
| Maximální počet modulů, které lze importovat každých 30 sekund na účtu Automation |5 ||
| Maximální velikost modulu |100 MB ||
| Čas spuštění úlohy - úroveň Free |500 minut za předplatné za kalendářní měsíc ||
| Maximální množství místa na disku povoleno za izolovaného prostoru**<sup>1</sup>** |1 GB |Platí pro pouze Azure izolovaných prostorů|
| Maximální množství paměti na izolovaném prostoru**<sup>1</sup>** |400 MB |Platí pro pouze Azure izolovaných prostorů|
| Maximální počet soketů sítě povolené za izolovaného prostoru**<sup>1</sup>** |1000 |Platí pro pouze Azure izolovaných prostorů|
| Maximální počet účtů Automation v předplatném. |Bez omezení ||
|Maximální počet souběžných úloh, která se spustit pro jeden Hybrid Runbook Worker|50 ||

**<sup>1</sup>**  izolovaném prostoru je sdílený prostředí, které mohou být využívána více úloh, úloh pomocí stejné karanténě jsou vázány omezení prostředků izolovaného prostoru.
