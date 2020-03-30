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
ms.openlocfilehash: d700dfcf5a7b6e9ada2a755335689ffa571e4c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334675"
---
#### <a name="process-automation"></a>Automatizace procesů

| Prostředek | Omezení |Poznámky|
| --- | --- |---|
| Maximální počet nových úloh, které lze odeslat každých 30 sekund na účet Azure Automation (neplánované úlohy) |100 |Po dosažení tohoto limitu se následné požadavky na vytvoření úlohy nezdaří. Klient obdrží odpověď na chybu.|
| Maximální počet souběžných spuštěných úloh ve stejné instanci času na účet automatizace (neplánované úlohy) |200 |Po dosažení tohoto limitu se následné požadavky na vytvoření úlohy nezdaří. Klient obdrží odpověď na chybu.|
| Maximální velikost úložiště metadat úlohy pro 30denní klouzavé období | 10 GB (přibližně 4 miliony pracovních míst)|Po dosažení tohoto limitu se následné požadavky na vytvoření úlohy nezdaří. |
| Maximální limit datového proudu úloh|1 MB|Jeden datový proud nemůže být větší než 1 MB.|
| Maximální počet modulů, které lze importovat každých 30 sekund na účet automatizace |5 ||
| Maximální velikost modulu |100 MB ||
| Doba běhu úlohy, úroveň Free |500 minut na předplatné za kalendářní měsíc ||
| Maximální povolené množství místa na disku v izolovaném prostoru<sup>1</sup> |1 GB |Platí pouze pro karantény zabezpečení Azure.|
| Maximální množství paměti dané izolovanému prostoru<sup>1</sup> |400 MB |Platí pouze pro karantény zabezpečení Azure.|
| Maximální povolený počet síťových soketů na izolované mši<sup>1</sup> |1 000 |Platí pouze pro karantény zabezpečení Azure.|
| Maximální povolený čas běhu na runbook<sup>1</sup> |3 hodiny |Platí pouze pro karantény zabezpečení Azure.|
| Maximální počet účtů automatizace v předplatném |Bez omezení ||
| Maximální počet hybridních skupin pracovních procesů na účet automatizace|4 000||
|Maximální počet souběžných úloh, které lze spustit na jednom hybridním pracovníkovi runbooku|50 ||
| Maximální velikost parametru úlohy runbooku   | 512 kilobitů||
| Maximální parametry runbooku   | 50|Pokud dosáhnete limitu 50 parametrů, můžete zadat řetězec JSON nebo XML parametru a analyzovat jej pomocí runbooku.|
| Maximální velikost datové části webového háku |  512 kilobitů|
| Maximální počet dní, po které jsou data úlohy zachována|30 dní|
| Maximální velikost stavu pracovního postupu prostředí PowerShell |5 MB| Platí pro runbooky pracovního postupu Prostředí PowerShell při vytváření kontrolních bodů pracovního postupu.|

<sup>1.</sup> Izolovaného prostoru je sdílené prostředí, které může používat více úloh. Úlohy, které používají stejnou oblast složky složky složky, jsou vázány omezeními prostředků izolovaného prostoru.

#### <a name="change-tracking-and-inventory"></a>Change Tracking a Inventory

V následující tabulce jsou uvedeny limity sledovaných položek pro sledování změn na počítač.

| **Zdrojů** | **Limit**| **Poznámky** |
|---|---|---|
|File|500||
|Registr|250||
|Software windows|250|Nezahrnuje aktualizace softwaru.|
|Balíčky linuxu|1,250||
|Služby|250||
|Daemon|250||

#### <a name="update-management"></a>Update Management

V následující tabulce jsou uvedena omezení pro správu aktualizací.

| **Zdrojů** | **Limit**| **Poznámky** |
|---|---|---|
|Počet počítačů na nasazení aktualizace|1000||